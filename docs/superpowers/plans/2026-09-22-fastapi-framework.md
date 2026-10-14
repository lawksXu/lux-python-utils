# Lux Python Utils implementation plan

**Goal:** Set up an independently runnable Python 3 + FastAPI utilities service.

**Architecture:** Use one `lux_utils` package under `src/`, with a FastAPI application served by Uvicorn. Follow `lux-python-agent` conventions for packaging and developer tools.

**Tech stack:** Python 3.14, FastAPI 0.141.1, Uvicorn 0.53.0, pytest 9.1.1, HTTPX 0.28.1, Ruff 0.16.8, setuptools 84.

## Constraints

- Work only in `/Users/xuwenjie/lux-python-utils`.
- Install dependencies in `.venv`; use standard pip and venv.
- Keep direct dependency versions aligned with the sibling Python agent service.
- Add only a process liveness route: `GET /health` returns HTTP 200 and `{"status":"ok"}`.
- Keep the application free of speculative features and integrations.
- Do not initialize Git, create branches, or commit files.

## Task: Create and validate the framework

**Files:** `pyproject.toml`, `.python-version`, `.gitignore`, `src/lux_utils/__init__.py`, `src/lux_utils/main.py`, `tests/test_health.py`, `README.md`, and `AGENTS.md`.

**Interface:** `lux_utils.main:app` is the ASGI entry point. Local development uses port 8001.

- [x] Create package metadata, ignore rules, project guidance, and setup documentation.
- [x] Create `.venv` with `python3 -m venv .venv`, then install with `.venv/bin/python -m pip install -e '.[dev]'`.
- [x] Add an HTTP-level smoke test for `/health`; confirm it fails with HTTP 404 before the route exists.
- [x] Add the health route and confirm the same test passes.
- [x] Run `.venv/bin/python -m ruff check .`, `.venv/bin/python -m ruff format --check .`, and `.venv/bin/python -m pip check`.
- [x] Build a wheel with `.venv/bin/python -m pip wheel --no-deps --wheel-dir dist .`.
- [x] Start Uvicorn on loopback, verify `/health`, `/docs`, `/redoc`, and `/openapi.json`, and stop it afterward.
- [x] Review the created files and report the run command and verification results.

## Validation results

- Installed successfully into `.venv` using Python 3.14.3.
- The health test first failed with HTTP 404, then passed after the route was added.
- pytest: 1 passed. Ruff lint and format checks passed. `pip check` found no broken requirements.
- Built `dist/lux_python_utils-0.1.0-py3-none-any.whl` and verified that it contains the `lux_utils` package.
- Actual HTTP requests to all four routes returned 200 on `127.0.0.1:8001`.
- The smoke-test server completed application shutdown and exited after SIGTERM. Uvicorn intentionally re-raises the captured signal after graceful shutdown, so the test recognizes the expected `-SIGTERM` process return code.
