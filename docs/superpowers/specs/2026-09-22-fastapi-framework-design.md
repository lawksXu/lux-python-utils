# Lux Python Utils framework design

## Scope

Create the Python utilities service requested by the user in `/Users/xuwenjie/lux-python-utils`. Match the existing `lux-python-agent` framework conventions while keeping the two services independently installable and runnable.

## Design

- Use the installed Python 3.14 interpreter and a project-local `.venv`.
- Declare package metadata, pinned direct dependencies, and tool configuration in `pyproject.toml`.
- Use `src/lux_utils/main.py` as the ASGI entry point, served by Uvicorn.
- Runtime dependencies are FastAPI and Uvicorn. Development dependencies are pytest, HTTPX, and Ruff, matching the sibling service's direct versions.
- `GET /health` returns HTTP 200 and `{"status":"ok"}` for process liveness without contacting external services.
- Retain FastAPI's default Swagger UI, ReDoc, and OpenAPI routes.
- Document local port 8001 so the agent service can keep using port 8000.
- Use FastAPI's default error handling. No utility business routes, databases, authentication, or provider integrations are part of this setup.
- Include a README, root `AGENTS.md`, and ignore rules for virtual environments, build artifacts, secrets, and nested `AGENTS.md` files.

## Tradeoffs

A `src/` package and editable installation match the sibling service and allow wheel packaging without path overrides. Standard `venv` and pip avoid requiring an additional environment manager. Direct dependencies are pinned; transitive versions are resolved by pip and are not fully locked.

## Verification

Install into the new virtual environment, exercise the health route through HTTPX's ASGI transport, run Ruff and `pip check`, build a wheel, and smoke-test the actual Uvicorn server's health and documentation routes. Stop the smoke-test server afterward. No Git branches or commits are requested.
