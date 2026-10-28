1.

{"status":"ok"}

172.17.0.1 - - [28/Oct/2026 21:53:51] "GET /health HTTP/1.1" 200

2.

docker run -d --name backend --network breach-net -p 5000:5000 -v ./data/leaked-sha1.txt:/app/data/leaked-sha1.txt:ro backend:v1
7961c7fb02a6ca9990ac4c1192f1950dcf49bb7d8dbf34a07a03e705d44fff6e

docker run --rm --network breach-net healthcheck-probe
backend: OK

docker run --rm healthcheck-probe
backend: UNREACHABLE

docker stop backend

docker run --rm --network breach-net healthcheck-probe
backend: UNREACHABLE

dlaczego bez --network nazwa backend nie działa, choć backend jest uruchomiony?

Nie działa, ponieważ DNS działa jedynie w sieciach zdefiniowanych przez użytkownika. Jest błąd połączenia z nazwą

3. 

docker exec backend cat /tmp/notatka.txt
notatka

docker exec backend cat /tmp/notatka.
cat: /tmp/notatka.: No such file or directory

docker run --rm -v breach-dane:/dane alpine sh -c "date >> /dane/historia.txt; cat /dane/historia.txt"
Wed Oct 28 22:06:57 UTC 2026
Wed Oct 28 22:07:09 UTC 2026

Plik trafił do ulotnej pamięci, dlatego zapamiętał co się znajduje w pliku
Plik historia.txt przetrwał usunięcie bo trafil do wolumenu
Tak lista przecieków by przetrwała, bo została podmontowana