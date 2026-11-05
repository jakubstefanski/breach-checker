Zad. 1

WSZYSTKO DZIAŁA
docker compose exec backend pytest -v
================================================= test session starts ==================================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 7 items

tests/test_check.py::test_leaked_hash_is_found PASSED                                                            [ 14%]
tests/test_check.py::test_unknown_hash_is_not_found PASSED                                                       [ 28%]
tests/test_check.py::test_missing_hash_field PASSED                                                              [ 42%]
tests/test_check.py::test_plaintext_password_is_rejected PASSED                                                  [ 57%]
tests/test_check.py::test_upper PASSED                                                                           [ 71%]
tests/test_check.py::test_json PASSED                                                                            [ 85%]
tests/test_check.py::test_length PASSED                                                                          [100%]

================================================== 7 passed in 0.14s ===================================================

1 NIE DZIAŁA
================================================= test session starts ==================================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 7 items

tests/test_check.py::test_leaked_hash_is_found PASSED                                                            [ 14%]
tests/test_check.py::test_unknown_hash_is_not_found PASSED                                                       [ 28%]
tests/test_check.py::test_missing_hash_field PASSED                                                              [ 42%]
tests/test_check.py::test_plaintext_password_is_rejected PASSED                                                  [ 57%]
tests/test_check.py::test_upper FAILED                                                                           [ 71%]
tests/test_check.py::test_json PASSED                                                                            [ 85%]
tests/test_check.py::test_length PASSED                                                                          [100%]

======================================================= FAILURES =======================================================
______________________________________________________ test_upper ______________________________________________________

client = <FlaskClient <Flask 'app'>>

    def test_upper(client):
        response = client.post("/check", json={"hash": LEAKED.upper()})
>       assert response.status_code == 200
E       assert 400 == 200
E        +  where 400 = <WrapperTestResponse streamed [400 BAD REQUEST]>.status_code

tests/test_check.py:27: AssertionError
=============================================== short test summary info ================================================
FAILED tests/test_check.py::test_upper - assert 400 == 200
============================================= 1 failed, 6 passed in 0.21s ==============================================

What's next:
    Debug this Compose error with Gordon → docker ai "help me fix this compose error"


Zad. 2

LINIA CURL:
HTTP 500

COMPOSE PS -A:
docker compose ps -a
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                     PORTS
breach-checker-backend-1    breach-checker-backend    "flask --app app run…"   backend    34 minutes ago   Exited (1) 3 seconds ago
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         34 minutes ago   Exited (0) 2 minutes ago
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   34 minutes ago   Up 33 minutes              0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

DOCKER INSPECT:
docker inspect --format '{{.State.Status}} {{.State.ExitCode}}' breach-checker-backend-1
exited 1

Zad. 3

PO POPRAWCE: 
 curl.exe -I http://localhost:8080/nginx.conf
HTTP/1.1 404 Not Found
Server: nginx/1.31.8
Date: Thu, 05 Nov 2026 15:31:16 GMT
Content-Type: text/html
Content-Length: 153
Connection: keep-alive

