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
      Priorytetyzacja wymagań
   </summary>

   ## Priorytetyzacja wymagań MoSCoW w projekach IT
   MoSCoW to technika priorytetyzacji wykorzystywana podczas zarządzania projektami oraz przy tworzeniu analiz biznesowych projektów informatycznych. Metoda MoSCoW pozwala wypracować wzajemne zrozumienie pomiędzy wszystkimi uczestnikami  (interesariuszami) projektu i taki też jest jej cel.

   Gdy wszystkie wymagania mają tę samą wysoką wagę, to żadne z nich nie jest ważniejsze od pozostałych. Należy nadać odpowiednie priorytety, tak aby jak najwcześniej pojawiły się funkcjonalności, które przyniosą jak największe korzyści biznesowe.

   Każde wymaganie funkcjonalne ma określony priorytet MoSCoW. Wymagania definiuje się w dokumencie opisującym zakres funkcjonalny systemu wraz z kryteriami akceptacyjnymi. Zgodnie z tą metodologią zadania przypisywane są do kategorii M (Must), S (Should), C (Could) i W (Won’t), od których pochodzi nazwa modelu MoSCoW.

- __Must have__ - Priorytet M (must) opisuje krytyczne wymagania, które muszą być spełnione, bo bez nich cały projekt nie zakończy się powodzeniem. Wymienione poniżej wymagania oznaczone symbolami S, C i W można zrealizować w przyszłych fazach cyklu życia projektu.

- __Should have__ - Priorytet S (should) opisuje wymagania, które powinny być spełnione, jednak bez których projekt może funkcjonować.

- __Could have__ - Priorytet C (could) reprezentuje wymagania, które dobrze, gdyby były, jednak nie są konieczne do funkcjonowania projektu.

- __Won’t have__ - Priorytet W (won’t) to zakres reprezentujący wymagania, których wiemy, że w danym projekcie nie będzie. Rzadziej wykorzystywany w celu dobitnego podkreślenia braku danej funkcjonalności. Domyślnie, jeśli wymaganie nie jest ustalone, to należy uznać, że nie będzie realizowane.

</details>

<details>
   <summary>
      Wersjonowanie semantyczne - SemVer
   </summary>

   ## Wersjonowanie semantyczne 
   znane również jako [Semantic Versioning](https://semver.org/lang/pl/) albo SemVer. SemVer to standard wersjonowania oprogramowania. Numeracja wersji to sposób identyfikacji i odróżnienia różnych wersji oprogramowania. Każda nowa wersja otrzymuje unikalny numer, który pozwala nam śledzić i kontrolować zmiany. Dzięki numeracji wersji możemy stwierdzić, czy dana wersja oprogramowania jest aktualna, nowsza lub starsza. To szczególnie istotne przy wprowadzaniu zmian i udostępnianiu aktualizacji.
   
## Wersje składają się z trzech cyfr: X.Y.Z

- __Major__ - Pierwsza liczba – 18 – reprezentuje numer wersji głównej (major). Jej zwiększenie (np. z 17 do 18) wskazuje na wprowadzenie istotnych modyfikacji, które mogą wpływać na istniejące funkcje i wymagać dostosowania.\
__Przykład:__ Zmiana interfejsu użytkownika, nowe funkcjonalności, znaczne zmiany w strukturze kodu.

- __Minor__ - Druga cyfra – 2 – to numer wersji mniejszej (minor). Zwiększenie tego numeru (np. z 18.1 do 18.2) wskazuje na wprowadzenie nowych funkcji oprogramowania bez wpływu na istniejące.\
__Przykład:__ Dodanie nowego modułu, rozszerzenie istniejących funkcji, ulepszenie wydajności.

- __Patch__ - Trzecia cyfra – 0 – reprezentuje numer wersji poprawki (patch). Zwiększenie numeru wersji patch (np. z 18.2.0 do 18.2.1) wskazuje na wprowadzenie poprawek bez zmian w funkcjonalności.\
__Przykład:__ Naprawa znalezionych błędów, łatanie luk w zabezpieczeniach, poprawki interfejsu.

W świecie programowania istnieje wiele bibliotek i modułów, które pomagają w tworzeniu aplikacji. Zarządzanie zależnościami jest kluczowe, aby nasz projekt działał sprawnie i był aktualny.

## Deklarowanie zależności
   W celu określenia wymaganych bibliotek i modułów w aplikacji javascript, korzystamy z pliku konfiguracyjnego package.json. W pliku tym możemy zadeklarować nazwy zależności oraz określić szczególne, minimalne lub maksymalne wersje, które nasz projekt obsługuje.
```
"dependencies": {
   "reaact": "^18.2.0",
   "@reduxjs/toolkit": "~1.9.5",
   "axios": "1.4.0"
}
```
W powyższym przykładzie widzimy deklarację trzech zależności:
- react,
- @reduxjs/toolkit,
- axios.
Przy każdej zależności możemy określić operator wersji, który pomoże nam zarządzać aktualizacjami.

__Operator „^” (marchewka/caret)__ - Pozwala na aktualizację do najnowszej zgodnej zadeklarowanej wersji, zapewniając przy tym kompatybilność z nowymi funkcjonalnościami. Przykładowo, jeśli zadeklarujemy naszą bibliotekę React w następujący sposób „react”: „^18.1.0” to możemy być pewni, że podczas aktualizacji naszej aplikacji wersja biblioteki może być większa lub równa 18.1.0, ale też mniejsza od 19.0.0.

__Operator „~” (tylda)__ - Umożliwia aktualizację do najnowszej wersji poprawiającej błędy, zachowując zgodność z deklaracją wersji. Przykładowo nasza biblioteka do obsługi stanu aplikacji zadeklarowana następująco „@reduxjs/toolkit”: „~1.8.4” będzie aktualizowana do wersji większej lub równej 1.8.4, ale mniejszej niż 1.9.0.

## Aktualizowanie wersji
Jest takie magiczne polecenie, które nie wszystkim jest znane, ale znacząco ułatwia proces podbijania wersji. Gdy po wprowadzeniu poprawek chcesz podbić wersję, np. modułu npm, masz do wyboru dwie metody. Możesz to zrobić na piechotę, zmieniając ręcznie plik package.json, albo skorzystać z polecenia npm version.

Oto trzy przykłady, które pokazują, jak to działa w praktyce:
- __npm version patch__ – zwiększa numer wersji poprawki. Przykład: 1.0.0 -> 1.0.1.
- __npm version minor__ – zwiększa numer wersji mniejszej. Przykład: 1.0.0 -> 1.1.0.
- __npm version major__ – zwiększa numer wersji głównej. Przykład: 1.0.0 -> 2.0.0.

Polecenia te automatycznie aktualizują numer wersji w pliku package.json, a jako bonus tworzą nowy tag w repozytorium Git.


</details>

<details>
   <summary>
      Etapy wytwarzania oprogramowania
   </summary>

## 1. Requirement analysis (określenie wymagań)
Pierwszym etapem jest określenie wymagań. Definiowane jest przez doświadczonych specjalistów, którzy wspierają się badaniami rynku i analizą konkurencji. Informacje zebrane w czasie analiz są wykorzystywane do planowania całego projektu, przeprowadzenia studium wykonalności czy analiza ryzyka. Po zaplanowaniu projektu ruszamy do zdefiniowania wartości naszego produktu, również przez określenie jego grupy docelowej. Posiadając te informacje, architekci oprogramowania mogą „przenieść” je do wirtualnego świata, projektując funkcjonalności naszej aplikacji.\

__Przykładowe role:__ Project Manager, Product Manager, Business Analyst

## 2. Design (projekt)
Drugi etap wytwarzania oprogramowania przenosi nas do części związanej z tworzeniem naszego produktu. To tutaj zapadają decyzje związane z interfejsem użytkownika. To, jakie kolory wykorzystamy, jak zaprojektujemy wygląd aplikacji. Gdzie będzie wyświetlał się nagłówek i czy urozmaicimy go animacją? Jakie ruchy wykona użytkownik, kiedy uruchomi aplikację po raz pierwszy i w którym miejscu będzie szukał danej funkcji? Jak będzie wyglądał przycisk logowania i w którym miejscu się znajdzie? Na tym etapie wybrany zostanie również język programowania, który najlepiej będzie pasować do aplikacji. Omówimy też inne aspekty techniczne – takie jak bazy danych i serwery. Wszystkie te elementy muszą być ustalone odpowiednio wcześniej między innymi ze względu na wielkość aplikacji. Portal, który działa na ogromną skalę i odwiedza go dziennie ponad miliard użytkowników, zaprojektowany jest inaczej od aplikacji mobilnej spełniającej pojedyńczą funkcję. Przykładowo Facebook jest bardzo potężną aplikacją o setkach funkcjonalności – dlatego wiele technicznych aspektów różni go od np. strony internetowej dla małej agencji SEO, której główne funkcjonalności opierają się na kalkulatorze cen dla klientów i formularzu kontaktowym.\

__Przykładowe role:__ Architekt Aplikacji, UX, UI designer

## 3. Implementation (implementacja)
Faza implementacji to czas kiedy pełną parą zaczynamy pisanie programu! Jest to bardzo ważny etap tworzenia oprogramowania. Czasami będzie to projekt wykonywany zupełnie od zera z całkiem nowym kodem (wtedy mówimy o tzw. green field development), czasami można wykorzystać elementy z innych, istniejących aplikacji (wtedy mamy do czynienia z tzw. legacy code). Programiści specjalizują się w różnych językach programowania, dlatego możemy ich podzielić na tych front-endowych (JavaScrpit, HTML, CSS, React.js, Angular etc.) czy back-endowych (Java, Scala, C, C++, C#.net etc.). Czasem poszukiwani są także Full Stack Developerzy – czyli tacy, którzy potrafią kodować zarówno po stronie klienta, jak i administrować serwerem czy bazą danych. Każda aplikacja ma zdefiniowany stack technologiczny, który powinien być dopasowany do głównych założeń oprogramowania.\

__Przykładowe role:__ Software Engineer, Programista, Java Developer, Frontend Developer, C++ Developer itp.

## 4. Testing (testowanie)
Następnym krokiem jest faza testowania. To świetny moment do tego, żeby upewnić się, że system działa właściwie. Aplikacja musi zostać sprawdzona pod wieloma kątami – funkcjonalności, optymalizacji czy wydajności. Jest to szczególnie istotne, jeśli produkt ma działać na większą skalę. Musimy się również upewnić, że oprogramowanie spełnia wymogi bezpieczeństwa. Testerzy sprawdzą, czy funkcjonalności i interfejsy działają prawidłowo. Mogą korzystać do tego z testów manualnych (użytkownik testujący „klikając” w aplikacji) lub automatycznych (tester tworzy kod testujący aplikację).
Testowanie odbywa się również przez użytkowników, w czasie korzystania z oprogramowania. Dzięki informacjom zwrotnym produkt zawsze może być poprawiony i lepiej dopasowany do potrzeb. Często po wykryciu „buga” wracamy znów do poprzedniego kroku, i kod jest poprawiany.\

__Przykładowe role:__ QA Specialist, tester oprogramowania, tester manualny, tester automatyzujący

## 5. Deployment (wdrożenie)
Deployment to faza tworzenia oprogramowania, w której software jest gotowy do działania. Po dokładnym zaplanowaniu tego, jak będzie działać aplikacja, kodowaniu i upewnieniu się, że software pracuje jak należy, oprogramowanie „idzie na produkcję”. Co to w praktyce znaczy? Mówiąc najprościej – użytkownicy mogą już korzystać z aplikacji. Na początku tego etapu użytkownicy mogą wyłapywać jeszcze drobne błędy, które na bieżąco mogą być zgłoszone i korygowane.
Aplikacje, czy np. strony internetowe też się zmieniają i zostają ulepszane. Widać to na przykładzie znanego wszystkim Facebooka, który dziś wygląda inaczej niż 5 lat temu. Jest to naturalne, ponieważ technologia się rozwija, pojawiają nowe funkcjonalności, zmienia się wizerunek firm czy trendy w designie. Dzięki innowacjom rozwijają się też wszystkie inne produkty i usługi.

## 6. Maintenance (utrzymanie)
Ostatnią fazą jest faza utrzymania, czyli etap, kiedy aplikacja już działa. Korzystają z niej użytkownicy, widzimy, że spełnia swoje funkcje i jesteśmy zadowoleni z całego produktu. Nie możemy jednak stracić czujności – aplikacja wciąż może się zepsuć. W przypadku aplikacji webowych coś może funkcjonować źle na konkretnych przeglądarkach, software może też paść ofiarą złośliwego oprogramowania lub ataku hackera. Z czasem, kiedy z aplikacji będzie korzystać zbyt wielu użytkowników może pojawić się na przykład problem z obciążeniem serwerów. Wtedy trzeba to naprawić, wracając do działań podejmowanych na odpowiednim etapie cyklu rozwoju oprogramowania (w zależności od błędu/problemu). Często aplikacje nie będą wymagały dużych ingerencji ze strony programistów, ponieważ błędy będą dość proste w naprawie. Czasem wystarczy nawet wyjaśnić użytkownikowi w prosty sposób, jak sam może poradzić sobie z problemem. Wtedy wystarczy skorzystać z pomocy zespołów wspierających (supportowych).\

__Przykładowe role:__ Support Engineer, I linia wsparcia, II linia wsparcia, IT Support.

</details>

<details>
   <summary>
      Założenia projektowe
   </summary>

   <details>
      <summary>
         Metodologia Agile
      </summary>

   [Agile Manifesto](https://agilemanifesto.org/iso/pl/manifesto.html) W wyniku naszej pracy, zaczęliśmy bardziej cenić:

__Ludzi i interakcje__ od procesów i narzędzi\
__Działające oprogramowanie__ od szczegółowej dokumentacji\
__Współpracę z klientem__ od negocjacji umów\
__Reagowanie na zmiany__ od realizacji założonego planu.\

Oznacza to, że elementy wypisane po prawej są wartościowe,
ale większą wartość mają dla nas te, które wypisano po lewej.

[Założenia Manifestu](https://agilemanifesto.org/iso/pl/principles.html):
- Najwyższy priorytet ma dla nas zadowolenie klienta dzięki wczesnemu i ciągłemu wdrażaniu wartościowego oprogramowania.
- Bądźcie gotowi na zmiany wymagań nawet na późnym etapie jego rozwoju. Procesy zwinne wykorzystują zmiany dla zapewnienia klientowi konkurencyjności.
- Dostarczajcie funkcjonujące oprogramowanie często, w kilkutygodniowych lub kilkumiesięcznych odstępach. Im częściej, tym lepiej.
- Zespoły biznesowe i deweloperskie muszą ściśle ze sobą współpracować w codziennej pracy przez cały czas trwania projektu.
- Twórzcie projekty wokół zmotywowanych ludzi. Zapewnijcie im potrzebne środowisko oraz wsparcie i zaufajcie, że wykonają powierzone zadanie.
- Najbardziej efektywnym i wydajnym sposobem przekazywania informacji zespołowi deweloperskiemu i wewnątrz niego jest rozmowa twarzą w twarz.
- Działające oprogramowanie jest podstawową miarą postępu.
- Procesy zwinne umożliwiają zrównoważony rozwój. Sponsorzy, deweloperzy oraz użytkownicy powinni być w stanie utrzymywać równe tempo pracy.
- Ciągłe skupienie na technicznej doskonałości i dobrym projektowaniu zwiększa zwinność.
- Prostota – sztuka minimalizowania ilości koniecznej pracy – jest kluczowa.
- Najlepsze rozwiązania architektoniczne, wymagania i projekty pochodzą od samoorganizujących się zespołów.
- W regularnych odstępach czasu zespół analizuje możliwości poprawy swojej wydajności, a następnie dostraja i dostosowuje swoje działania do wyciągniętych wniosków.
   </details>
   

## Tablica Kanban
   Kanban to popularne ramy postępowania stosowane do wdrażania procesów programistycznych Agile i DevOps. Wymagają one informowania o potencjale wykonawczym w czasie rzeczywistym i zapewnienia pełnej przejrzystości pracy. Jednostki pracy są prezentowane w formie wizualnej na tablicy Kanban, umożliwiając członkom zespołu śledzenie stanu każdego elementu prac przez cały czas.

## 8 kroków do DevOps
   ![DevOps1](https://rolandliedtke.me/images/devopsGit.webp)
   ![DevOps2](https://rolandliedtke.me/images/devops3.png)
   ![DevOps3](https://rolandliedtke.me/images/devops2.png)
   ![DevOps4](https://rolandliedtke.me/images/devops1.webp)

### Krok 1 — Wybór komponentu
Pierwszym krokiem jest rozpoczęcie od małej skali. Wybierz komponent, który jest obecnie w fazie produkcji. Idealny komponent ma prostą bazę kodu z niewieloma zależnościami i minimalną infrastrukturą. Komponent ten będzie poligonem doświadczalnym, na którym zespół może przećwiczyć wdrażanie DevOps.

### Krok 2 — Przeanalizowanie możliwości zastosowania metodologii Agile takiej jak Scrum
DevOps często stosowane jest w połączeniu z metodologią pracy Agile, taką jak Scrum. Nie ma potrzeby wdrażania wszystkich rytuałów i praktyk związanych z metodą taką jak Scrum. Trzy elementy Scrum, które zasadniczo są łatwe do wdrożenia i szybko dostarczają wartość, to backlog, sprint i planowanie sprintu.

Zespół DevOps może dodawać i nadawać priorytety pracy w backlogu Scrum, a następnie wprowadzać podzbiór tej pracy do sprintu, czyli ustalonego czasu na ukończenie określonej części pracy. Planowanie sprintu polega na decydowaniu o tym, jakie zadania przechodzą z backlogu zaległości do następnego sprintu.

### Krok 3 — Użycie kontroli źródła opartego na GIT
Kontrola wersji to najlepsza praktyka DevOps, która umożliwia lepszą współpracę i skrócenie cykli wydawania. Narzędzia takie jak Bitbucket umożliwiają programistom udostępnianie, współpracę, scalanie i tworzenie kopii zapasowych oprogramowania.

Wybierz model tworzenia gałęzi. Zarys tej koncepcji przedstawiono w tym artykule. Przepływ GitHub jest doskonałym punktem wyjścia dla zespołów zaczynających pracę z Git, ponieważ łatwo go zrozumieć i wdrożyć. Często preferowanym rozwiązaniem jest tworzenie oprogramowania w oparciu o gałąź główną, ale wymaga ono większej dyscypliny i komplikuje pierwsze kroki z Git.

### Krok 4 — Zintegrowanie kontroli źródła ze śledzeniem pracy
Zintegruj narzędzie do kontroli źródła z narzędziem do śledzenia pracy. Dzięki temu, że w jednym miejscu można zobaczyć wszystko, co jest związane z danym projektem, programiści i menedżerowie zaoszczędzą sporo czasu. Poniżej znajduje się przykład zgłoszenia Jira z aktualizacjami z repozytorium kontroli źródła opartego na Git. Zgłoszenia Jira obejmują sekcję tworzenia oprogramowania, która łączy pracę wykonaną dla zgłoszenia Jira w kontroli źródła. To zgłoszenie miało jedną gałąź, sześć commitów, jeden pull request i jedną kompilację.

Zrzut ekranu przedstawiający integrację kontroli źródła ze śledzeniem pracy
Zagłębiając się w sekcję programistyczną zgłoszenia Jira, można znaleźć dodatkowe szczegóły. Karta commitów zawiera listę wszystkich commitów powiązanych ze zgłoszeniem Jira.

Zrzut ekranu przedstawiający integrację kontroli źródła ze śledzeniem pracy
W tej sekcji wymieniono wszystkie pull requesty powiązane ze zgłoszeniem Jira.

Zrzut ekranu przedstawiający integrację kontroli źródła ze śledzeniem pracy
Kod związany z tym zgłoszeniem Jira jest wdrażany we wszystkich środowiskach wymienionych w sekcji Wdrożenia. Integracje te zwykle działają poprzez dodanie identyfikatora zgłoszenia Jira — w tym przypadku IM-202 — w celu zatwierdzenia komunikatów i nazw gałęzi pracy związanej ze zgłoszeniem Jira.

Zrzut ekranu przedstawiający integrację kontroli źródła ze śledzeniem pracy
Dostępna jest karta kodu, która zawiera łącza do wszystkich repozytoriów kontroli źródła związanych z projektem. Pomaga to programistom znaleźć kod, nad którym muszą pracować, gdy przypisują się do zgłoszenia Jira.

Zrzut ekranu przedstawiający integrację kontroli źródła ze śledzeniem pracy

### Krok 5 — Napisanie testów
Pipeline'y CI/CD wymagają testów, aby sprawdzić, czy kod wdrożony w różnych środowiskach działa poprawnie. Zacznij od napisania testów jednostkowych dla kodu. Ambitnym celem jest 90 procent pokrycia kodu, ale gdy dopiero zaczynasz, jest to nierealne. Wyznacz niski poziom bazowy dla pokrycia kodu i stopniowo z biegiem czasu zwiększaj poprzeczkę dla pokrycia testów jednostkowych. W tym celu możesz dodać jednostki pracy do backlogu.

Stosuj programowanie sterowane testami podczas naprawiania błędów znalezionych w kodzie produkcyjnym. Kiedy znajdziesz błąd, napisz testy jednostkowe, testy integracyjne i/lub systemowe, które kończą się niepowodzeniem w środowiskach, w których ten błąd występuje. Następnie napraw błąd i zobacz, czy testy kończą się teraz powodzeniem. Ten proces w sposób naturalny pozwoli z czasem zwiększyć pokrycie kodu. Jeśli błąd został wychwycony w środowisku testowym lub przejściowym, testy dadzą Ci pewność, że kod działa poprawnie, gdy zostanie przeniesiony do produkcji.

Gdy zaczynasz od początku, ten krok jest co prawda pracochłonny, ale ważny. Testowanie pozwala zespołom zobaczyć wpływ zmian kodu na zachowanie systemu przed wdrożeniem tych zmian u użytkowników końcowych.

- __Testy jednostkowe__ sprawdzają, czy kod źródłowy jest poprawny i powinien być uruchamiany jako jeden z pierwszych kroków w pipelinie CI/CD. Deweloperzy powinni napisać testy dla zielonej ścieżki, problematycznych wejść i znanych przypadków narożnych. Podczas pisania testów programiści mogą symulować dane wyjściowe i oczekiwane dane wyjściowe.

- __Testy integracyjne__ pozwalają na sprawdzenie, czy dwa komponenty komunikują się ze sobą poprawnie. Można symulować dane wyjściowe i oczekiwane dane wyjściowe. Testy te są jednym z pierwszych kroków pipeline'u CI/CD przed wdrożeniem w dowolnym środowisku. Testy te zazwyczaj wymagają szerzej zakrojonych symulacji niż testy jednostkowe.

- __Testy systemowe__ sprawdzają kompleksową wydajność systemu i dają pewność, że system działa zgodnie z oczekiwaniami w każdym środowisku. Symuluj dane wejściowe, które może otrzymać komponent, i uruchom system. Następnie sprawdź, czy system zwraca wymagane wartości i poprawnie aktualizuje pozostałą część systemu. Testy te powinny być uruchamiane po wdrożeniu w każdym środowisku.

### Krok 6 — Tworzenie procesu CI/CD w celu wdrożenia komponentu
Podczas tworzenia pipeline'u CI/CD rozważ przeprowadzenie wdrożenia w wielu środowiskach. Kod zostanie zapisany na stałe, jeśli zespół buduje pipeline CI/CD, który jest wdrażany tylko do jednego środowiska. Ważne jest tworzenie pipeline'ów CI/CD dla infrastruktury i kodu. Zacznij od stworzenia pipeline'u CI/CD, aby wdrożyć niezbędną infrastrukturę w każdym środowisku. Następnie stwórz kolejny pipeline CI/CD, aby wdrożyć kod.

</details>

<details>
   <summary>
      Rich Picture - Wzbogacony Wizerunek
   </summary>

![Rich Picture](https://rolandliedtke.me/images/RichPicture.png)

</details>
