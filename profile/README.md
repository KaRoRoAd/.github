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
      GitHub Flow
   </summary>

## GUI
- [Sourcetree](https://www.sourcetreeapp.com/) - klient dla OSX/Windowsa, który jest wyśmienitą nakładką graficzną dla gita
  
## Gałęzie
Pierwsza rzecz, którą należy zrobić, aby wdrożyć model [GitHub Flow](https://www.frontstack.pl/blog/praca-z-git-github-flow), to odpowiednio uporządkować gałęzie w swoim repozytorium. W tym przypadku będziemy operować na dwóch rodzajach branchy:

- __main__ –  na tym branchu znajduje się produkcyjna i aktualna wersja aplikacji. Gałąź ta jest zawsze gotowa do tego, aby w razie potrzeby mogła być deployowana na serwery produkcyjne. To z tej gałęzi programiści będą tworzyć swoje branche i do tej gałęzi mergują swoje zmiany.
- __gałęzie robocze (features / feature branch)__ – na tych gałęziach tworzymy nowe funkcjonalności oraz naprawiamy wszelkiego rodzaju błędy w aplikacji.

  ## Flow
1. Stworzenie brancha
   Naszą pracę zawsze zaczynamy wychodząc z master brancha. To w masterze znajduje się przetestowany i działający kod. Dzięki temu wiemy, że pracujemy zawsze na aktualnej wersji aplikacji.
2. Praca nad nową funkcjonalnością
   W momencie, gdy jesteśmy już na swoim branchu, możemy zacząć pracę nad zadaniem. Regularnie commitujemy swoją pracę i nie zapominamy o tym, aby aktualizować swoją gałąź o nowe commity z mastera.
3. Pull Request
   Gdy skończymy już nasze zadanie i chcemy je umieścić w masterze, tworzymy tzw. Pull Request. Powiedzieliśmy sobie wcześniej, że master jest gałęzią, z której w każdym momencie możemy wykonać deploy na produkcję. W takim przypadku kod tam się znajdujący musi być dokładnie zweryfikowany przez pozostałych członków zespołu. Pull Request jest miejscem, gdzie możemy dokładnie przedyskutować wprowadzone przez nas rozwiązania.
   W przypadku, gdy podczas omawiania PR-a wyjdą jakieś błędy, które wymagają poprawki, to poprawiamy je na naszej gałęzi roboczej. Wszystkie przyszłe commity, które wyślemy do zdalnego repozytorium, będą aktualizowały już raz utworzony PR.
4. Merge i deploy
   Gdy nasze zmiany zostały już szczegółowo skonsultowane z pozostałymi członkami zespołu, przyszedł czas na pokazanie ich światu. Merge zaakceptowanego PR-a do mastera i deploy mastera. Sposób trochę mniej bezpieczny, ale dzięki temu główna gałąź projektu zostaje szybko zaktualizowana o nowe zmiany. Taki proces również dużo łatwiej zautomatyzować za pomocą narzędzi CI/CD. Wszelkie zmiany w masterze mogą powodować automatyczny deploy na produkcję. Problemy wynikłe po deployu naprawiamy kolejnym PR-em. Jeżeli coś pójdzie naprawdę bardzo źle, to mamy przecież Gita – revert bądź reset mogą okazać się pomocne 😉

</details>

<details>
   <summary>
      Priorytetyzacja wymagań MoSCoW w projektach IT
   </summary>
   
   MoSCoW to technika priorytetyzacji wykorzystywana podczas zarządzania projektami oraz przy tworzeniu analiz biznesowych projektów informatycznych. Metoda MoSCoW pozwala wypracować wzajemne zrozumienie pomiędzy wszystkimi uczestnikami  (interesariuszami) projektu i taki też jest jej cel.

   Gdy wszystkie wymagania mają tę samą wysoką wagę, to żadne z nich nie jest ważniejsze od pozostałych. Należy nadać odpowiednie priorytety, tak aby jak najwcześniej pojawiły się funkcjonalności, które przyniosą jak największe korzyści biznesowe.

   Każde wymaganie funkcjonalne ma określony priorytet MoSCoW. Wymagania definiuje się w dokumencie opisującym zakres funkcjonalny systemu wraz z kryteriami akceptacyjnymi. Zgodnie z tą metodologią zadania przypisywane są do kategorii M (Must), S (Should), C (Could) i W (Won’t), od których pochodzi nazwa modelu MoSCoW.

- __Must have__ - Priorytet M (must) opisuje krytyczne wymagania, które muszą być spełnione, bo bez nich cały projekt nie zakończy się powodzeniem. Wymienione poniżej wymagania oznaczone symbolami S, C i W można zrealizować w przyszłych fazach cyklu życia projektu.

- __Should have__ - Priorytet S (should) opisuje wymagania, które powinny być spełnione, jednak bez których projekt może funkcjonować.

- __Could have__ - Priorytet C (could) reprezentuje wymagania, które dobrze, gdyby były, jednak nie są konieczne do funkcjonowania projektu.

- __Won’t have__ - Priorytet W (won’t) to zakres reprezentujący wymagania, których wiemy, że w danym projekcie nie będzie. Rzadziej wykorzystywany w celu dobitnego podkreślenia braku danej funkcjonalności. Domyślnie, jeśli wymaganie nie jest ustalone, to należy uznać, że nie będzie realizowane.

</details>
