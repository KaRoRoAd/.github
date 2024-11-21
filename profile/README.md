<details>
   <summary>
      Konfiguracja dwóch kont na GitHubie
   </summary>

# 1. Utwórz klucze SSH dla każdego konta
   Każde konto GitHub wymaga osobnego klucza SSH, aby można było się nim uwierzytelniać.

## Generowanie klucza SSH

### Dla pierwszego konta (prywatne):
```bash
ssh-keygen -t ed25519 -C "twoj_email1@domena.com"
```
Gdy poprosi o nazwę pliku, zapisz go np. jako:
```bash
~/.ssh/id_ed25519_osobiste
```

### Dla drugiego konta (służbowe / uczelniane):
```bash
ssh-keygen -t ed25519 -C "sXXXXX@pjwstk.edu.pl"
```
Zapisz go np. jako:
```bash
~/.ssh/id_ed25519_pjatk
```

### Dodanie kluczy do agenta SSH

Aktywuj klucze:
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_osobiste
ssh-add ~/.ssh/id_ed25519_pjatk
```

### Dodaj klucze do GitHuba

1. Skopiuj klucze publiczne:
   - **Osobisty**:
     ```bash
     cat ~/.ssh/id_ed25519_osobiste.pub
     ```
   - **Służbowy**:
     ```bash
     cat ~/.ssh/id_ed25519_pjatk.pub
     ```
2. Wklej odpowiedni klucz w ustawieniach SSH klucza na GitHubie dla każdego konta:
   - [Dodaj klucz SSH](https://github.com/settings/keys)

---

## 2. Konfiguracja `~/.ssh/config`

Aby `git` wiedział, którego klucza używać, skonfiguruj plik `~/.ssh/config`:
```bash
Host github.com-osobisty
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_osobiste

Host github.com-pjatk
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_pjatk
```

---

## 3. Konfiguracja globalna i lokalna Git

### Ustaw jedno konto jako globalne:
```bash
git config --global user.name "Twoje Imię (Osobiste)"
git config --global user.email "twoj_email1@domena.com"
```

### Dla repozytoriów służbowych, nadpisz lokalne ustawienia:
W katalogu repozytorium służbowego:
```bash
git config user.name "Twoje Imię (Służbowe)"
git config user.email "sXXXX2@pjwstk.edu.pl"
```

---

## 4. Praca z dwoma kontami

Podczas klonowania repozytorium upewnij się, że używasz odpowiedniego aliasu z pliku `~/.ssh/config`:

- **Dla konta osobistego**:
  ```bash
  git clone git@github.com-osobisty:username/repo.git
  ```
- **Dla konta służbowego**:
  ```bash
  git clone git@github.com-pjatk:username/repo.git
  ```

---

## 5. Sprawdzenie konfiguracji

W repozytorium możesz sprawdzić, które konto jest używane:
```bash
git config user.name
git config user.email
```
</details>


<details>
   <summary>
      GitFlow
   </summary>

## GUI
- [Sourcetree](https://www.sourcetreeapp.com/) - klient dla OSX/Windowsa, który jest wyśmienitą nakładką graficzną dla gita
  
## Gałęzie
Pierwsza rzecz, którą należy zrobić, aby wdrożyć model Git Flow, to odpowiednio uporządkować gałęzie w swoim repozytorium. W tym przypadku będziemy operować na pięciu rodzajach branchy:

- __master__ – produkcyjna wersja aplikacji. Do tego brancha będziemy mergować tylko te zmiany, które już zostały wydane na produkcję oraz krytyczne hotfixy.
- __hotfix__ – jedyna gałąź bazująca ma masterze. To właśnie ona służy do szybkiego naprawiania krytycznych błędów występujących na produkcji.
- __develop__ – gałąź ta jest „nieoficjalnym” masterem podczas pracy. Z tego brancha programiści tworzą swoje gałęzie robocze i do niego mergują (rebase-ują) swoją pracę. Gdy praca nad wszystkimi funkcjonalnościami w danym etapie jest gotowa, branch ten jest mergowany do gałęzi „master”.
- __gałęzie robocze (features / feature branch)__ – na tych gałęziach pracujemy na co dzień i tworzymy nowe funkcjonalności.
</details>
