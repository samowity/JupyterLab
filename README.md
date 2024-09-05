# JupyterLab

Repozytorium do instalacji i dostosowania serwera JupyterLab w kontenerze Docker.
Na podstawie [Jupyter Docker Stacks](https://jupyter-docker-stacks.readthedocs.io/en/latest/index.html).

Dostosowanie obejmuje:
- instalację dodatkowych programów apt z pliku _requirements/apt.txt_ (w tym różnych wersji pythona);
- instalację dodatkowych kerneli dla każdej z wersji pythona, w dedykowanych środowiskach wirtualnych i z zainstalowanymi modułami z plików _requirements/python3.XX.txt_.


## Instalacja

```bash
docker compose -f ./docker-compose.yaml up -d
```

Parametry takie jak:
  - adres IP i port
  - hasło
  - lokalizację katalogu z danymi użytkownika
  - lokalizacje katalogu z modułami użytkownika

można ustawić poprzez zmienne środowiskowe.
Nazwy wymaganych zmiennych i ich wartości domyślne znajduja się w pliku _.env_.

Po instalacji może być konieczna zmiana właściciela współdzielonych katalogów na hoście:
```bash
sudo chown -R $(id -u):$(id -g) .JupyterLab
```
## Moduły użytkownika

Do instalacji modułów użytkownika przeznaczony jest katalog _user_modules_ współdzielony pomiędzy hostem a kontenerem.
By dodawać moduły niewyposażone w instalator wymagane jest utworzenie podkatalogu __user_modules/python3.XX/pth/_ i umieszczenie ich w nim.

## Dodawanie środowisk wirtualnych i kerneli

By dodać środowisko wirtualne _Pythona 3.12_ o nazwie __nowy_venv__ i kernel dla niego należy w kontenerze w konsoli:

```bash
cd ~/venvs
python3.12 -m venv nowy_venv
source nowy_venv/bin/activate
python -m pip install ipykernel
python -m ipykernel install --user --name nowy_venv --display-name "NOWY"
deactivate
```

Instalacja modułów:
```bash
source ~/venvs/nowy_venv/bin/activate
pip install pandas
deactivate
```

Dodanie folderu na moduły użytkownika bez instalatorów:

```bash
mkdir --parents ~/user_modules/nowy_venv/pth
echo "$HOME/user_modules/nowy_venv/pth" >> ~/venvs/nowy_venv/lib/python3.12/site-packages/py_modules.pth
echo 'Katalog na moduły bez instalatora dla kernela "NOWY".' >> ~/user_modules/nowy_venv/pth/INFO.txt
```

## Moduły apt
Logowanie do kontenera jako __root__
```bash
docker exec -it -u root jupyterlab bash
```

Instalacja poprzez apt:
```bash
apt update
apt install -y nano
```

Instalacja __wine32__:
```bash
dpkg --add-architecture i386
apt update
apt install -y wine32
```

