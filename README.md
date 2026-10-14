# Lux Python Utils

Python utilities service for Lux. The current scope is a minimal [FastAPI](https://fastapi.tiangolo.com/tutorial/first-steps/) application served by Uvicorn.

## Requirements

- Python 3.14 or newer; `.python-version` selects the 3.14 series for version managers.
- `pip` and Python's built-in `venv` module.

## Local setup

Run from the project root:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -e '.[dev]'
```

`pyproject.toml` declares the package and pins direct dependencies. Runtime dependencies are FastAPI and Uvicorn. The `dev` extra adds pytest, HTTPX, and Ruff. Transitive dependencies are resolved by pip; there is no full dependency lock yet.

## Run

With the virtual environment activated:

```bash
python -m uvicorn lux_utils.main:app --reload --port 8001
```

The development server listens on `127.0.0.1:8001`, leaving port 8000 available for `lux-python-agent`. Uvicorn's `--host` and `--port` options change the bind address. Use `--reload` only during development.

- Health: <http://127.0.0.1:8001/health>
- Swagger UI: <http://127.0.0.1:8001/docs>
- ReDoc: <http://127.0.0.1:8001/redoc>
- OpenAPI schema: <http://127.0.0.1:8001/openapi.json>

`GET /health` returns HTTP 200 with `{"status":"ok"}`. It checks process liveness only and does not contact external services.

For a server without automatic reloading:

```bash
python -m uvicorn lux_utils.main:app --host 0.0.0.0 --port 8001
```

## Verify

```bash
python -m pytest
python -m ruff check .
python -m ruff format --check .
python -m pip check
```

The integration test exercises the health endpoint using HTTPX's ASGI transport. The transport does not run application lifespan; add explicit lifecycle coverage when startup hooks are introduced.

To build an installable wheel:

```bash
python -m pip wheel --no-deps --wheel-dir dist .
```

## Structure

```text
src/lux_utils/
  __init__.py
  main.py          # ASGI application and health endpoint
tests/
  test_health.py   # HTTP health check
pyproject.toml     # Package, dependencies, and tooling configuration
```

Add feature packages as utilities are requested. Business utilities, persistence, authentication, and external service integrations are outside this initial framework.
