Zad. 1
breach-checker-backend:prod (debian 13.7)
=========================================
Total: 49 (HIGH: 49, CRITICAL: 0)

breach-checker-backend:prod (debian 13.7)
=========================================
Total: 2 (HIGH: 2, CRITICAL: 0)

breach-checker-frontend:prod (alpine 3.24.4)
============================================
Total: 3 (HIGH: 3, CRITICAL: 0)

breach-checker-frontend:prod (alpine 3.24.4)
============================================
Total: 3 (HIGH: 3, CRITICAL: 0)

pull zmienił wynik tylko dla backend bo debian ma więcej poprawek, dla których nie ma jeszcze rozwiązań

Zad. 2

backend-1  | FileNotFoundError: [Errno 2] No usable temporary directory found in ['/tmp', '/var/tmp', '/usr/tmp', '/app']

PS C:\Users\BDiJS\nw-kurs\breach-checker> docker compose exec backend touch /etc/test
touch: cannot touch '/etc/test': Read-only file system

What's next:
    Debug this Compose error with Gordon → docker ai "help me fix this compose error"
PS C:\Users\BDiJS\nw-kurs\breach-checker> docker compose exec backend touch /tmp/test
PS C:\Users\BDiJS\nw-kurs\breach-checker> docker compose exec backend grep CapEff /proc/1/status
CapEff: 0000000000000000

PS C:\Users\BDiJS\nw-kurs\breach-checker> docker compose exec backend pytest
======================================== test session starts =========================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0
rootdir: /app
collected 7 items

tests/test_check.py .......                                                                    [100%]

========================================= 7 passed in 0.16s ==========================================

read_only chroni przed zmianą plików w kontenerze, cap_drop przed posiadaniem uprawnien roota oraz security_opt broni przed nadaniem sobie uprawnień. Read only różni się tym że ustanawia cały system plików w kontenerze na tylko do odczytu a user appuser tylko zmieniał użytkownika

Zad. 3

PS C:\Users\Jakub\mw-kurs\lab07-sekret> docker history sekret-demo:plik
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
9fe8983326f9   15 seconds ago   RUN /bin/sh -c rm /tmp/klucz.txt # buildkit     8.19kB    buildkit.dockerfile.v0
<missing>      15 seconds ago   RUN /bin/sh -c test -s /tmp/klucz.txt && ech…   4.1kB     buildkit.dockerfile.v0
<missing>      15 seconds ago   COPY klucz.txt /tmp/klucz.txt # buildkit        12.3kB    buildkit.dockerfile.v0
<missing>      13 days ago      CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      13 days ago      ADD alpine-minirootfs-3.22.8-x86_64.tar.gz /…   8.97MB    buildkit.dockerfile.v0

tajny-klucz-jakubs


Polecenie RUN rm usuwa plik jedynie w nowej warstwie obrazu, jednak fizycznie pozostaje on zapisany i możliwy do odzyskania z wcześniejszej warstwy historii, która została utworzona przez instrukcję COPY. Klucz należało przekazać bezpiecznie przy użyciu mechanizmu Docker BuildKit i montowania sekretów (opcja --secret oraz RUN --mount=type=secret...), dzięki czemu poufne dane są dostępne podczas budowania, ale nie zostają zapisane w warstwach ostatecznego obrazu.
