Zad. 1

Nieudane uruchomienie: https://github.com/jakubstefanski/breach-checker/actions/runs/45711276294/job/139546027731
Linia błędu z logu: Tag must be paired, missing: [ </h1> ], start tag match failed [ <h1> ] on line 14. (tag-pair)


Kolejne: https://github.com/jakubstefanski/breach-checker/actions/runs/45711794073

W tej wersji pipeline'u plik docker-compose.ci.yml nadpisuje obrazy dla obu usług (backend` i `frontend), a job verify pobiera z rejestru GHCR oba wcześniej opublikowane obrazy z tagiem tego commita i uruchamia je bez budowania lokalnego, dzięki czemu testuje dokładnie te artefakty, które trafią na produkcję. We wcześniejszej wersji pipeline'u z rejestru pobierany był wyłącznie obraz backend, natomiast frontend był budowany na nowo z lokalnego katalogu ./frontend podczas działania verify, więc nie weryfikowano opublikowanego obrazu frontendu.


Zad. 2

Nieudane uruchomienie: <WKLEJ_LINK_DO_CZERWONEGO_URUCHOMIENIA_ACTIONS>
Kolejne, zielone uruchomienie: <WKLEJ_LINK_DO_ZIELONEGO_URUCHOMIENIA_ACTIONS>

Po nieudanym uruchomieniu tag latest backendu nadal wskazywał na poprzedni, sprawdzony i działający obraz z wcześniejszego zielonego uruchomienia, ponieważ job promote został pominięty, a zepsuty obraz został opublikowany wyłącznie pod tagiem skrótu commita. Ma to kluczowe znaczenie dla kogoś, kto pobiera obraz bez podawania tagu (Docker domyślnie pobiera wtedy `:latest`), ponieważ chroni przed przypadkowym pobraniem i uruchomieniem niesprawnego obrazu, który nie przeszedł weryfikacji.
