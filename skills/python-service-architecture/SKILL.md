---
name: python-service-architecture
description: Structure Python service business logic, persistence, and integrations behind explicit boundaries. Use when designing or reviewing service-layer code, data access, or external clients.
---

# Python service architecture — repository & service layers

Replace `<app>` with the lowercase, one-word service package name, such as `prism`.

The layering is `api → service → repository`. Never skip a layer or import upward. This skill covers the service and repository layers; the HTTP layer is framework-specific (see `flask-api`).

## Service layer

- Keep transport handlers thin: validate and translate the request, call the application/service layer, and map expected errors to the transport contract.
- Put business rules in services or use cases with explicit inputs and outputs. They should not depend directly on request globals, framework objects, or secret/config lookup.
- Isolate database, cache, filesystem, and network I/O behind repositories, gateways, or adapters with clear contracts. Set explicit timeouts for outbound calls and surface actionable typed failures.
- Return domain objects or well-defined result types from lower layers, not framework response objects, raw cursors, or unvalidated payloads.
- Inject dependencies at composition boundaries so unit tests can use fakes. Keep transactions and consistency boundaries explicit when multiple writes form one unit of work.

## Repository layer

For services using this architecture, `<app>/repository/` is the only layer that communicates with databases, caches, or external services.

- In `repository/__init__.py`, define an `<App>Session(requests.Session)` that carries the JWT cookie or headers, `X-Calling-Service`, and outbound-call caching hooks. Define an `<App>Repository` dataclass that aggregates one repository per backend—such as `omniscience`, `dealcloud`, `db`, or `redis`—and constructs them from its session in `__post_init__`.
- Give each data source its own sub-package and keep its `models.py` there. Add `schemas.py` for Pydantic models used to parse external payloads. Small repositories may put functions in their `__init__.py`; split growing repositories into cohesive category modules, such as `execution/graphql.py` or `db/manager.py` and `db/models.py`, rather than a monolithic file.
- Keep database access in `repository/db/`: `manager.py` creates the engine with `pool_recycle` and `echo=APP_CONFIG.DEBUG`, exposes `scoped_session`, and provides a `transaction_scope()` context manager for multi-write units; `models.py` contains SQLAlchemy models, regenerable with `make schema`; `__init__.py` exposes a `DatabaseRepository` with typed static query methods.
- Repository methods return models or domain objects, never transport `Response` objects, raw cursors, or JSON strings. API and service layers do not issue raw queries.
- Set explicit timeouts on every outbound HTTP call. Surface typed repository errors rather than swallowing errors as `None`.

## Models

Use Pydantic `BaseModel` whenever data crosses an HTTP boundary, is persisted, or is parsed from an external system. Place these models in `<app>/models/` or `<domain>/models/`; a model is the type, validator, and serializer, replacing a separate dataclass and Marshmallow schema.

- Plain dataclasses are appropriate only for internal intermediate values that never leave the service layer.
- For SQLAlchemy response models, set `model_config = ConfigDict(from_attributes=True)` and use `HedgeOut.model_validate(orm_row)` while the session remains open, allowing lazy relationships to load. Convert in the other direction with `OrmModel(**payload.model_dump())`.
- Keep field and business validation on models with `field_validator` and `model_validator`, rather than scattering it across services.
- Never expose SQLAlchemy models directly through an API; return Pydantic response models instead.
- Keep custom JSON encoding for non-Pydantic returns, such as dates and `Decimal`, in one `<app>/encoder.py`; Pydantic models serialize themselves.

## Configuration

For Flask services without an established configuration pattern, use one Django-settings-like module: `<app>/configuration.py`. Import its singleton everywhere as:

```python
from <app>.configuration import APP_CONFIG
```

- Define a `Secret` class with [`environ-config`](https://environ-config.readthedocs.io) and the application prefix. Declare every environment-specific or sensitive value there so it can be overridden through `<APP>_…` environment variables. Call `dotenv.load_dotenv()` before `Secret.from_environ(os.environ)` so a local `.env` file works.
- Use `BaseConfig`, `TestConfig`, `DevelopmentConfig`, and `ProductionConfig(DevelopmentConfig)`, overriding only values that change. Production must set `DEBUG = False`, `TESTING = False`, and log level `INFO`.
- Expose a `Configuration(dict)` singleton named `APP_CONFIG`. Select `production`, `development`, or `test` from `APP_ENV`, defaulting to `test`, and raise `ConfigurationException` for an unknown value. Its dictionary shape must work directly with `app.config.from_object(APP_CONFIG)`.
- Keep `__version__` at the top of `configuration.py` for bumpversion. Define the `LOGGING` `dictConfig` there and apply it at import time with `logging.config.dictConfig(APP_CONFIG.LOGGING)`.
- Compute derived values—such as `SQLALCHEMY_DATABASE_URI`, Flyway URL and locations, and Redis flags—in configuration classes, never ad hoc in application code.
- Read `os.getenv` or `os.environ` only in this module. Give every new setting a safe local default so a fresh clone can run without `.env`.
- Hardcode test ports, database names, and credentials in `TestConfig` so tests are reproducible without environment setup.

Use `APP_ENV`, not Flask's deprecated `FLASK_ENV`, as the application environment selector.

## Versioning and releases

- Keep `__version__` in `<app>/configuration.py` as the single source of truth.
- Manage releases with `bumpversion`: configure `setup.cfg` to update `setup.py` and `configuration.py`, commit the version change, and create its release tag.
- Expose the running version at `GET /api/version`.
