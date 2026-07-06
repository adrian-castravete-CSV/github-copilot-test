## Plan: Add FastAPI automated tests in tests/ directory

TL;DR - Add a standalone `tests/` suite for the FastAPI backend using pytest and FastAPI TestClient. Keep tests isolated by resetting the shared in-memory `activities` state before each test.

**Steps**
1. Create a new `tests/` directory.
2. Add `tests/conftest.py` with a `client` fixture for `fastapi.testclient.TestClient` and an `autouse` fixture that snapshots and restores `src.app.activities` before each test.
3. Add `tests/test_app.py` covering the backend endpoints using the Arrange-Act-Assert (AAA) pattern:
   - `GET /` redirects to `/static/index.html`
   - `GET /activities` returns the activity map
   - `POST /activities/{activity_name}/signup` succeeds for a new participant
   - signup returns `400` when the student is already signed up
   - signup returns `404` when the activity does not exist
   - `POST /activities/{activity_name}/unregister` succeeds for a registered student
   - unregister returns `400` when the student is not registered
   - unregister returns `404` when the activity does not exist
4. Update `requirements.txt` to include `pytest` so the test suite is installable with project dependencies.
5. Optionally update `pytest.ini` to set `testpaths = tests` and make the test directory explicit.

**Relevant files**
- `/workspaces/github-copilot-test/src/app.py` — source of the FastAPI app and in-memory state
- `/workspaces/github-copilot-test/requirements.txt` — add `pytest`
- `/workspaces/github-copilot-test/pytest.ini` — optionally scope tests to `tests/`
- `/workspaces/github-copilot-test/tests/conftest.py` — fixture setup for isolated testing
- `/workspaces/github-copilot-test/tests/test_app.py` — endpoint and behavior tests

**Verification**
1. Run `pytest` from the repo root and confirm the new test suite passes.
2. Confirm all tests execute under `tests/` and no existing app code is modified to support testing.
3. Validate that the in-memory `activities` dictionary is restored between tests so tests are independent.

**Decisions**
- Use `fastapi.testclient.TestClient` rather than live server requests, keeping tests fast and self-contained.
- Add `pytest` to requirements so tests are supported by the existing dependency file.
- Keep test coverage focused on API behavior rather than static file content.

**Further Considerations**
1. If the repo later grows and uses async endpoints, we can add `pytest-asyncio` and async client tests.
2. If the user wants broader test coverage, add negative cases for malformed request parameters and static file serving.
