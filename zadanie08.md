Zad. 1

Nieudane uruchomienie: https://github.com/jakubstefanski/breach-checker/actions/runs/45711276294/job/139546027731
Linia błędu z logu: Tag must be paired, missing: [ </h1> ], start tag match failed [ <h1> ] on line 14. (tag-pair)


Kolejne: https://github.com/jakubstefanski/breach-checker/actions/runs/45711794073

W tej wersji pipeline'u plik docker-compose.ci.yml nadpisuje obrazy dla obu usług (backend i frontend), a job verify pobiera z rejestru GHCR oba wcześniej opublikowane obrazy z tagiem tego commita i uruchamia je bez budowania lokalnego, dzięki czemu testuje dokładnie te artefakty, które trafią na produkcję. We wcześniejszej wersji pipeline'u z rejestru pobierany był wyłącznie obraz backend, natomiast frontend był budowany na nowo z lokalnego katalogu ./frontend podczas działania verify, więc nie weryfikowano opublikowanego obrazu frontendu.


Zad. 2

Nieudane uruchomienie: https://github.com/jakubstefanski/breach-checker/actions/runs/45713030538/job/139552778852
Kolejne, zielone uruchomienie: https://github.com/jakubstefanski/breach-checker/actions/runs/45713541869/job/139554513735

Po nieudanym uruchomieniu tag latest backendu nadal wskazywał na poprzedni, sprawdzony i działający obraz z wcześniejszego zielonego uruchomienia, ponieważ job promote został pominięty, a zepsuty obraz został opublikowany wyłącznie pod tagiem skrótu commita. Ma to kluczowe znaczenie dla kogoś, kto pobiera obraz bez podawania tagu, ponieważ chroni przed przypadkowym pobraniem i uruchomieniem niesprawnego obrazu, który nie przeszedł weryfikacji.

Zad. 3


PS C:\Users\Jakub\metody_lab\breach-checker> docker pull ghcr.io/jakubstefanski/breach-checker-backend:latest
latest: Pulling from jakubstefanski/breach-checker-backend
36aafc2aede6: Pull complete 
30cf67d7101c: Pull complete 
83a14706aa6d: Pull complete 
8916f27adbd8: Pull complete 
6a9915d857be: Pull complete 
96ac437f0119: Pull complete 
286d8c291daa: Pull complete 
770edaa8edf6: Pull complete 
e3e428093f5e: Pull complete 
Digest: sha256:57a610144d70e6cea1a40a877d0bfe8e048b8f9bee19d97c8d206c8e5fceb1c3
Status: Downloaded newer image for ghcr.io/jakubstefanski/breach-checker-backend:latest
ghcr.io/jakubstefanski/breach-checker-backend:latest
PS C:\Users\Jakub\metody_lab\breach-checker> docker pull ghcr.io/jakubstefanski/breach-checker-backend:b1e9bfcf6a4446d8a752a4ec4b3888f098820722
b1e9bfcf6a4446d8a752a4ec4b3888f098820722: Pulling from jakubstefanski/breach-checker-backend
Digest: sha256:57a610144d70e6cea1a40a877d0bfe8e048b8f9bee19d97c8d206c8e5fceb1c3
Status: Downloaded newer image for ghcr.io/jakubstefanski/breach-checker-backend:b1e9bfcf6a4446d8a752a4ec4b3888f098820722
ghcr.io/jakubstefanski/breach-checker-backend:b1e9bfcf6a4446d8a752a4ec4b3888f098820722
PS C:\Users\Jakub\metody_lab\breach-checker> docker tag ghcr.io/jakubstefanski/breach-checker-backend:latest ghcr.io/jakubstefanski/breach-checker-backend:test-push
PS C:\Users\Jakub\metody_lab\breach-checker> docker push ghcr.io/jakubstefanski/breach-checker-backend:test-push
The push refers to repository [ghcr.io/jakubstefanski/breach-checker-backend]
e3e428093f5e: Layer already exists
30cf67d7101c: Layer already exists
286d8c291daa: Layer already exists
83a14706aa6d: Layer already exists
6a9915d857be: Layer already exists
96ac437f0119: Layer already exists
36aafc2aede6: Layer already exists
770edaa8edf6: Layer already exists
8916f27adbd8: Layer already exists
error from registry: unauthenticated: User cannot be authenticated with the token provided.


Identyczne digesty obu tagów pokazują, że oba wskazują na dokładnie ten sam, zweryfikowany obraz. Polecenie docker pull zadziałało bez logowania, ponieważ pakiet ma ustawioną widoczność publiczną, natomiast docker push zostało odrzucone, gdyż publikacja i modyfikacja obrazów zawsze wymaga uwierzytelnienia. Właśnie do tego w kroku 1 służył token z zakresem write:packages — uwierzytelniał użytkownika i dawał uprawnienia do wypychania (zapisu) obrazów do rejestru GHCR.
