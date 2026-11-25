Zad. 1

- Nieudane uruchomienie (`build-frontend` zatrzymane przez `htmlhint`): <WKLEJ_LINK_DO_CZERWONEGO_URUCHOMIENIA_ACTIONS>
- Linia błędu `htmlhint` z logu:
```
<WKLEJ_LINIE_BLEDU_HTMLHINT_Z_LOGU>
```
- Kolejne, zielone uruchomienie (po przywróceniu `</h1>`): <WKLEJ_LINK_DO_ZIELONEGO_URUCHOMIENIA_ACTIONS>

W tej wersji pipeline'u plik `docker-compose.ci.yml` nadpisuje obrazy dla obu usług (`backend` i `frontend`), a job `verify` pobiera z rejestru GHCR oba wcześniej opublikowane obrazy z tagiem tego commita i uruchamia je bez budowania lokalnego, dzięki czemu testuje dokładnie te artefakty, które trafią na produkcję. We wcześniejszej wersji pipeline'u z rejestru pobierany był wyłącznie obraz `backend`, natomiast `frontend` był budowany na nowo z lokalnego katalogu `./frontend` podczas działania `verify`, więc nie weryfikowano opublikowanego obrazu frontendu.
