1

[+] up 5/5
 ✔ Image breach-checker-frontend       Built                                                      2.6s
 ✔ Image breach-checker-backend        Built                                                      2.6s
 ✔ Container breach-checker-db-1       Healthy                                                    4.0s
 ✔ Container breach-checker-backend-1  Healthy                                                   34.8s
 ✔ Container breach-checker-frontend-1 Started                                                    0.3s

 NAME                        IMAGE                                                                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend                                                    "python app.py"          backend    42 seconds ago   Up 37 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine                                                        "docker-entrypoint.s…"   db         3 minutes ago    Up 3 minutes (healthy)    5432/tcp


2

Po insert:
{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
{"found":true}

Po down + up:
{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
{"found":true}

Po down -v + up:

{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}' | curl.exe -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '@-'
{"found":false}

Skrót przetrwał down, ponieważ dane zostały zapisane w wolumenie, który nie został usunięty, natomiast parametr -v usuwa dane bazy. 
Po -v w bazie są znowu 89 skróty z próbek, ponieważ baza pobiera dane z tego samego pliku co na początku

3.

docker compose ps
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED              STATUS                        PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    About a minute ago   Up About a minute (healthy)   5000/tcp
breach-checker-backend-2    breach-checker-backend    "python app.py"          backend    About a minute ago   Up About a minute (healthy)   5000/tcp
breach-checker-backend-3    breach-checker-backend    "python app.py"          backend    About a minute ago   Up About a minute (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         About a minute ago   Up About a minute (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   About a minute ago   Up 39 seconds                 0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

 docker compose exec frontend nslookup backend
Server:         127.0.0.11
Address:        127.0.0.11:53

Non-authoritative answer:
Name:   backend
Address: 172.19.0.3
Name:   backend
Address: 172.19.0.4
Name:   backend
Address: 172.19.0.5

Non-authoritative answer:

Przed restartem:

backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:37] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...

Po restarcie:

backend-2  | check: hash=bc454...
backend-2  | 172.19.0.6 - - [29/Oct/2026 15:20:30] "POST /check HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [29/Oct/2026 15:20:34] "GET /health HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:38] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.19.0.6 - - [29/Oct/2026 15:19:38] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [29/Oct/2026 15:20:04] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...

Frontend miał przypisany pierwszy backend do komunikacji i "nie wiedział" o pozostałych

Skalowanie byłoby niemożliwe, ponieważ wtedy ruch trafiałby tylko na jedną usługę bo ona miałaby przypisany typowo port 5000 (usługa by się nie uruchomiła)