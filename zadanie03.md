3.1
docker inspect --format '{{json .Config.Labels}}' jakubs-frontend:v1
{"autor":"Jakub Stefanski","maintainer":"Jakub Stefanski"}

docker inspect --format '{{json .Config.Labels}}' jakubs-frontend:latest
{"autor":"Jakub Stefanski","maintainer":"Jakub Stefanski"}

Warstwy na samym dole pochodzą z obrazu bazowego, warstwy stworzone przeze mnie znajdujące
się w moim dockerfile są na górze.  Niektóre warstwy mają 0B, ponieważ nie modyfikują i nie
dodają nowych plików kontenera tylko modyfikują metadane obrazu. Widać tylko moją etykietę
maintainer, ponieważ jest moja wartswa nadpisała warstwę bazową.

3.2
Tue Oct 20 21:25:15 UTC 2026
Tue Oct 20 21:25:15 UTC 2026

IMAGE   CREATED          STATUS          PORTS                                     NAMES
nowy    34 seconds ago   Up 33 seconds   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   test2
nowy    2 minutes ago    Up 2 minutes    0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   test

Czas różni się, ponieważ instrukcja run wykonuje się tylko raz - podczas budowania obrazu

3.3
root   /usr/share/nginx/html
Linia root wskazuje serwerowi nginx główny katalog, z którego ma serwować pliki dla użytkowników


3.4
docker exec blednyv2 ls /usr/share/nginx
dockerfile
html
index.html
script.js
style.css

docker exec blednyv2 ls /usr/share/nginx/html
50x.html
build.txt
index.html

Htmlhint sprawdzał plik index.html znajdujący się w ścieżce /usr/share/nginx/html, z powodu
błędnej ścieżki została otworzona strona ze ścieżki /usr/share/nginx

3.5

docker rmi breach-checker-frontend:latest
Untagged: breach-checker-frontend:latest

docker rmi breach-checker-frontend:v2
Untagged: breach-checker-frontend:v2
Deleted: sha256:b9fa9244b22651b185c9e9de3b49ce198e71cfcda7017353d8029a525b4d724e

Docker rmi faktycznie usuwa obraz jak nie posiada żadnego taga do niego

