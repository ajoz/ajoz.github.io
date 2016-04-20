---
layout: post
date: 30-12-2006
author: Andrzej Jóźwiak
title: Hasła i PAM (Pluggable Authentication Modules)
tags: freebsd unix security shell
disqus: true
---

W jaki sposób zapewnić skuteczną ochronę zasobów systemu komputerowego? Można ów system ogrodzić, oddzielić od „sieci” i postawić przy nim wartownika. Jednak rozwiązaniem mniej kosztownym niż strażnik, który udostępnia każdemu użytkownikowi systemu terminal dostępowy, jest ścisła i „niezawodna” identyfikacja.

Identyfikacja ta ma zapewnić ochronę przed nieautoryzowanym dostępem, sprawdzić, czy są użytkownicy potrzebujący uzyskać zasoby systemu oraz w jaki sposób chcą je wykorzystać. W idealnej sytuacji jakakolwiek identyfikacja mająca ochraniać dane nie byłaby konieczna. Jednak, gdy administrator musi borykać się z problemami „złośliwych” programów, słabo wyszkolonych użytkowników, włamywaczy szukających cennych danych, jak i tych, którzy chcieliby zniszczyć system, powinien mieć narzędzia i metody pozwalające na kontrolę dostępu do zasobów systemu komputerowego.

- [Modele identyfikacji](#modele-identyfikacji)
    - [Kategoria bycia](#kategoria-bycia)
    - [Kategoria posiadania](#kategoria-posiadania)
    - [Kategoria wiedzy](#kategoria-wiedzy)
- [Hasła](#hasla)
    - [Przyczyny stosowania haseł](#przyczyny-stosowania-hasel)
    - [Postać hasła](#postac-hasla)
- [Formaty plików „z hasłami”](#formaty-plikow-z-haslami)
    - [Linux](#linux)
    - [FreeBSD](#freebsd)
    - [Windows NT](#windowsnt)
    - [Zmiana hasła](#zmiana-hasla)
- [Ataki na systemy haseł](#ataki)
- [Inne modele identyfikacji](#inne-modele)
    - [Identyfikacja „wyzwanie – odpowiedź”](#wyzwanie-odpowiedz)
    - [Wykorzystanie kluczy publicznych](#klucze-publiczne)
- [PAM - Wprowadzenie](#pam-wprowadzenie)
    - [PAM - Cele](#pam-cele)
    - [PAM – Scenariusz wykorzystania](#pam-wykorzystanie)
    - [PAM - Moduły](#pam-moduly)
    - [PAM - Konfiguracja systemu](#pam-konfiguracja)
    - [PAM - /etc/pam.conf](#pam-plik-konfiguracyjny)
    - [PAM - /etc/pam.d](#pam-demon)
    - [PAM – Przykładowe wpisy konfiguracyjne](#pam-przyklady)
    - [PAM - Narzędzia i moduły](#pam-narzedzia-moduly)

### Modele identyfikacji
Identyfikacja bytu jakim może być: `osoba`, `host`, `terminal` inteligentny oraz program, może być rozpatrywana jako weryfikacja tego bytu w 3 kategoriach:

+ bycia;
+ posiadania;
+ wiedzy.

#### Kategoria bycia

Pierwsza kategoria nazywana jest także `user identificaction`. Każdy użytkownik w systemie cechuje się pewnymi unikalnymi właściwościami. W literaturze nazywa się je indywidualnymi cechami osobniczymi. Czy takie niepowtarzalne cechy mogą być podstawą identyfikacji?

Otóż jak dobrze wiemy odciski palców uznane są za niepowtarzalną `własność`, zatem sąd uznaje je za dowód nie do podważenia. Wymyślono więc urządzenia do identyfikacji linii papilarnych (`AFIM` – automated fingerprint identification machines), jednak maszyny takie są drogie a możliwość ich wykorzystania dość ograniczona.

Na podobnej zasadzie działają wszelkie systemy stwierdzające inne niepowtarzalne cechy jak badanie struktury siatkówki i tęczówki oka. Jednak mimo tego, iż prawdopodobieństwo błędnej akceptacji jest w ich przypadku naprawdę małe, wysoka cena specjalistycznych urządzeń dyskwalifikuje tą metodę do masowych zastosowań . Kolejne nie mniej ciekawe metody identyfikacji to badanie topografii dłoni lub geometrii twarzy. Wspomniane wyżej metody mają ważną zaletę, nie wiążą się z dość nieprzyjemnym zabiegiem jak np.: skanowanie gałki ocznej, ale ich prawdopodobieństwo błędnej identyfikacji jest znacznie większe niż w przypadku badania siatkówki. Systemy sprawdzające geometrie twarzy mogą służyć nie tylko jako medium autoryzujące, lecz mogą pomagać np.: w wyszukiwaniu terrorystów wśród pasażerów na lotnisku. Podpis odręczny jest sam w sobie pewnym zbiorem cech człowieka wynikającym z nawyków takich jak nacisk i kierunek prowadzenia pióra, styl pisma, w tym nachylenie liter, szybkość pisania itp. Podobna do tej metody może być identyfikacja na podstawie rytmu i tempa naciskania klawiszy. Jednak wiąże się to z wyposażeniem klawiatury w dodatkowe czujniki rejestrujące „parametry stukania w klawisze”. Aby ten opis metod identyfikacji był kompletny należy wspomnieć o identyfikacji na podstawie kodu DNA. Teoretycznie prawdopodobieństwo błędu jest tu równe zeru, ale obecnie metodę tę stosować można jedynie w specjalistycznych laboratoriach.

#### Kategoria posiadania

Wracając do kategorii weryfikacji podmiotu to druga z wymienionych, kategoria posiadania odnosi się do pewnych unikalnych przedmiotów. Przykładem może być karta chipowa lub magnetyczna chroniona hasłem dostępu. Przedmiot taki jest pewnym świadectwem autentyczności posiadacza, zatem wniosek jest prosty - użytkownik posiadający ów przedmiot nie powinien go udostępniać lub utracić.

#### Kategoria wiedzy

Trzecia ostatnia kategoria, kategoria wiedzy bazuje na założeniu, iż pewną sekretną informację zna jedynie uprawniony do tego podmiot. Ewentualne zgubienie takiej informacji skutkuje tym, że podmiot nie może zaświadczyć o swej autentyczności. Żaden ze sposobów identyfikacji nie daje stuprocentowego zabezpieczenia; hasło można podsłuchiwać podłączając się pod odpowiednią linię terminalu; przykładając komuś pistolet do skroni, napastnik może ukraść wspominaną już wcześniej kartę chipową, a co może wydawać się bardzo brutalne, za pomocą noża bandyta może pozbawić użytkownika palców! Ogólnie należy sobie uświadomić, że „im bardziej godna zaufania jest forma identyfikacji, tym bardziej wyrafinowana będzie metoda weryfikacji i tym bardziej agresywne zachowanie może przejawić napastnik”.

### Hasła
Hasła jest to najbardziej popularna technika weryfikacji użytkownika. Hasło podczas logowania podaje się aby system mógł zweryfikować, czy użytkownik jest tym za kogo się podaje. System sprawdza czy podane przez użytkownika hasło pasuje do żądanego konta. Jakąkolwiek dalszą pracę w systemie, użytkownik może podjąć, jeżeli wszystkie dane się zgadzają. W tej chwili nie ma systemu, który wyświetlałby wprowadzane przez użytkownika hasła. Jest to jedna z form zabezpieczeń przed niepowołanym przejęciem hasła tzw.: „shoulder surfing” (podglądanie przez ramię).

#### Przyczyny stosowania haseł
Po co więc używać haseł? Przecież w przypadku systemów „biurkowych” nie wymaga się stosowania haseł. Utrudniają one niejako pracę użytkownikom korzystającym ze wspólnego sprzętu (w tym także danych dyskowych). W przypadku grup badawczych, które pracowały nad Unixem było podobnie. „Wiele grup badawczych nie używało haseł dla użytkowników indywidualnych – często z tego samego powodu, z jakiego wstydzili się zamków w szufladach swoich biurek i w drzwiach swoich biur. W tych środowiskach zaufanie, szacunek i dobre obyczaje stanowiły silną broń przeciw kradzieżom i destrukcji.” Dzisiaj jesteśmy wstanie sobie wyobrazić co mógłby uczynić napastnik w tak niezabezpieczonym systemie. Hasła są jedną z form ochrony mającą przeciwdziałać (lub niestety tylko utrudniać) kradzieży danych, wyników, nieuczciwej konkurencji jak i niszczeniu danych przez przypadkowych napastników.

#### Postać hasła

Konkretna postać hasła jest wymuszana przez specyfikę urządzenia (systemu) z którego docelowo ma korzystać użytkownik. Dobrym przykładem może być klawiatura bankomatu, zawierająca jedynie klawisze cyfr, lub klawiatura telefonu. Wymuszają one stricte numeryczną postać haseł (w omawianym przypadku bankomatu najlepszym przykładem jest numer identyfikacji osobistej tzw. kod `PIN` – Personal Identification Number). Hasło nie powinno być zbyt długie aby przeciętny użytkownik mógł je zapamiętać, w praktyce wychodzi, że hasła powinny składać się z czterech do dziewięciu znaków.

W przypadku hasła n znakowego, wykorzystującego 26 liter (małe i duże litery nie są rozróżnialne) prawdopodobieństwo odgadnięcia hasła wynosi `26^n` (pod warunkiem, że założymy iż każde z `26^n` słów może wystąpić z jednakowym prawdopodobieństwem). Co się stanie gdy rozróżnimy wielkie i małe litery w haśle?

Posiądziemy dzięki temu `52^n` równie prawdopodobnych słów. Prawdopodobieństwo odgadnięcia hasła jeszcze bardziej zmaleje gdy dopuścimy w haśle obecność cyfr oraz wszystkich „drukowalnych” znaków specjalnych takich jak np.:

```
 „ , . ; : ‘ [ ] { } ( ) * & ^ % $ # @ !
```

 Istnieje jednak pewne niebezpieczeństwo związane z użyciem tych znaków w haśle, np.: `xdm` odfiltrowuje znaki specjalne (na myśli mam tutaj znaki: ^@, ^G, ^H, ^[ itp.). Powinno się także wystrzegać znaków mogących powodować interakcję z terminalem (np.: ^L), lub znaków: `\`, `#`, `@` które jeszcze w niektórych Unixach traktowane są jako odpowiednio: `escape`, `erase` i `kill`.

Czego unikać przy wyborze hasła? Przedstawić można oczywistą listę:

+ imion i nazwisk, także imion zwierząt domowych lub ksywek
+ nazwy używanego systemu, nazwy komputera w sieci
+ wszelkich łatwych do odnalezienia numerów (nr telefonu, nr rejestracyjny, data urodzenia)
+ nazw użytkowników
+ prostych wzorów, jak np. znany wszystkim „qwerty”

Jak zbudowane jest więc dobre hasło? Oto recepta:

+ małe i wielkie litery występujące jednocześnie
+ cyfry i znaki interpunkcyjne pomieszane z literami
+ przynajmniej siedmioznakowe
+ daje się szybko wpisać :D

Z doborem hasła wiąże się pewna historyjka.

Na jednej uczelni często zdarzało się, że studenci zmieniali hasła i nie mogli się później zalogować na swoje konta. Równie często zdarzały się próby umieszczania znaków sterujących w haśle, jak i pomyłka podczas tworzenia hasła uniemożliwiająca późniejsze zalogowanie się. Studenci przychodzili więc ze swoimi legitymacjami do administratorów uczelnianego systemu, gdzie zmieniano im hasła na zwrot: „ChangeMe” (Zmień mnie). Pracownicy instruowali studentów, aby na końcu korytarza w pomieszczeniu z terminalami zmienili swoje hasła. Po pewnym czasie jeden z pracowników postanowił uruchomić program łamiący hasła, z przerażeniem odkrył, że przeważająca większość studentów miała hasło „ChangeMe”. Miał je nawet jeden z pracowników uczelni. Od tej pory pracownicy i studenci zmieniali swoje hasła na miejscu.

Bezpieczeństwo określonego hasła maleje sukcesywnie wraz z jego kolejnym użyciem. W związku z tym trzeba ograniczyć jego ważność do określonego przedziału czasowego, typowo wahającego się między trzema tygodniami a trzema miesiącami. W ekstremalnym przypadku ograniczamy się do jednokrotnego użycia hasła. Systemy haseł jednokrotnego użycia opierają się na wygenerowaniu dla każdego z użytkowników listy haseł oraz określenia kolejności w jakiej poszczególne pozycje z wygenerowanej listy mają być używane.

Nawet najlepiej przygotowane hasło lub hasła nie będą bezpieczne jeżeli wybierzemy nieodpowiedni sposób przechowywania i zarządzania nimi. Należy zwrócić uwagę na kilka spraw:

+ Nie należy zapisywać hasła w postaci elektronicznej, chyba że hasło jest zaszyfrowane najlepiej jednostronną funkcją haszującą.
+ Nie używać haseł logowania jako haseł do różnych programów. Hasła na serwerach gier typu MUD są zarządzane i określona grupa ludzi ma do nich wgląd.
+ Nie używać tego samego hasła dla różnych komputerów zarządzanych przez różne organizacje.

Z ostatnim „nie” jest najwięcej problemów, przekonał się o tym Alec Muffet, autor programu "Crack", który opowiedział kiedyś taką historię: pewien student, przyjaciel Aleca, Bob odbywał podczas przerwy semestralnej praktyki w dużej firmie komputerowej. W weekendy wracał na uczelnię i grał w grę AberMUD (gra sieciowa) na komputerze Aleca. Jednym z obowiązków Boba w firmie, w której odbywał praktykę było zarządzanie systemem komputerowym. Firmie bardzo zależało na bezpieczeństwie swojego systemu, dlatego wszystkie hasła użytkowników były ciągami przypadkowych liter i cyfr nie mających razem żadnego sensu. Pewnego dnia Alec włączył hasła gry "AberMUD" do powstającej wersji słownika programu "Crack". Włączył te hasła dlatego, że były zapisane w jego komputerze w pliku w postaci niezaszyfrowanej. Gdy słownik był już wstępnie skompletowany Alec w celach testowych włączył go w systemie uczelnianym, okazało się, że program trafił na kilka haseł studenckich. Alec kazał swoim studentom zmienić hasła i zapomniał o całej sprawie. Gdy program "Crack" był już gotowy Alec przedstawił go i związane z nim pliki w Usenecie. Program został dość szybko rozpowszechniony, koniec końców trafił do Boba. Bob po uruchomieniu programu z przerażeniem odkrył iż program "Crack" wykrył jego hasło root. Morał z tej historii jest taki:

+ nigdy nie używaj haseł do swoich kont w innych aplikacjach
+ hasła powinny być przechowywane w zaszyfrowanej postaci

### Formaty plików „z hasłami”

Systemy z rodziny unix standardowo przechowują dane o kontach użytkowników takie jak: login i hasło w pliku `/etc/passwd`.
Nie jest to reguła bo np.: w przypadku wielu dystrybucji Linuksa dane o użytkownikach przechowywane są w dwóch plikach tekstowych:

+ `/etc/passwd` - zawiera m.in. `login`, `UID`, `GID`, pole `GECOS` (z ang. General Electric Comprehensive Operating System - informacje o użytkowniku np. imię i nazwisko),
+ `/etc/shadow` - zawiera m.in. zakodowane hasło, dane o dacie ważności konta i hasła. Są także dystrybucje, które wykorzystują tylko i wyłącznie plik /etc/passwd.

Podobnie sprawa się ma w przypadku systemu FreeBSD:

+ `/etc/passwd` - zawiera m.in. `login`, `UID`, `GID`, pole `GECOS` (informacje o użytkowniku np. imię i nazwisko),
+ `/etc/master.passwd` - zawiera dodatkowo m.in. zakodowane hasło, dane o dacie ważności konta i hasła, dodatkowo system ten posługuje się plikiem `/etc/pwd.db`, który ma za zadanie zwiększać bezpieczeństwo poufnych danych jakimi są hasła a jednocześnie ułatwić zarządzanie tymi danymi (jest to baza danych przebudowywana za każdym razem gdy zmieniane są informacje w plikach passwd i master.passwd).

#### Linux

Zaczniemy od przyjrzenia się plikom, które można spotkać w dystrybucjach Linuksa.

Jak już sobie wcześniej powiedzieliśmy ogólno dostępny (tylko do odczytu) plik passwd zawiera dane użytkowników dotyczące ich logowania się do systemu. Nie jest to jednak bezpieczne rozwiązanie, o czym zaraz sobie powiemy. Aby zwiększyć bezpieczeństwo stosowany jest plik shadow, który jest dostępny wyłącznie dla użytkownika root. Wiele dystrybucji narzuca odgórnie to, że system korzysta z plików passwd i shadow, dystrybucja Red HAT Linux pozwala na proste przełączanie się pomiędzy metodami za pomocą dwóch komend ( wydajemy je jako root ): `/usr/sbin/pwconv` Aby przejść do formatu passwd i shadow `/usr/sbin/pwunconv` Aby przejść do formatu tylko passwd Dokładna struktura pliku wygląda następująco (kolejne pola, dotyczące jednego konta):

+ Login użytkownika – 8 znaków
+ Zaszyfrowane hasło – jeżeli zamiast hasła użyty jest znak ‘x’, oznacza to iż wykorzystywany jest plik shadow
+ Id użytkownika
+ Id grupy do której należy użytkownik
+ GECOS (ogólne informacje o użytkowniku jak imię, nazwisko, często też wydział itp.)
+ Ścieżka do katalogu domowego użytkownika
+ Nazwa powłoki z której użytkownik będzie korzystał po zalogowaniu

Przykładowe wpisy w pliku passwd mogą wyglądać następująco:

```
andrey:x:561:561:Andrzej Jozwiak:/home/andrey:/bin/bash
ra88:x:562:561:Konrad Jojczyk:/home/ra88:/bin/bash
```

Plik shadow w systemie „Linux” służy „przesłonięciu” informacji znajdujących się w pliku `/etc/passwd`. Co oznacza przesłanianie? Plik `/etc/passwd` jako ogólnie dostępny może być źródłem sekretnych informacji. Intruz mając dostęp (nawet tylko chwilowy) do jednego z kont w systemie, po przechwyceniu pliku passwd, wchodzi w posiadanie nie tylko listy użytkowników systemu (listy potencjalnych ofiar ataku) oraz ich zaszyfrowanych haseł, ale także danych GECOS (niektóre firmy dbają o uzupełnianie tych danych). Mając plik z nazwami użytkowników i ich hasłami może w „zaciszu domowym” przeprowadzić ataki słownikowe na te konta porównując zaszyfrowane postacie odgadywanych haseł z tymi zapisanymi w pliku. Jak wiadomo wielu użytkowników korzysta z prostych haseł, co daje intruzowi większą gamę kont z których może próbować (po odgadnięciu hasła) uzyskać przywileje administratora (root). Zdarza się, że w celu przejęcia hasła włamywacz zadzwoni do pracownika, którego hasło chce poznać, przedstawi się jako administrator i poprosi o jego hasło (tzw. social engineering) Dokładna struktura pliku wygląda następująco (kolejne pola, dotyczące jednego konta)

Plik shadow w systemie „Linux” służy „przesłonięciu” informacji znajdujących się w pliku `/etc/passwd`. Co oznacza przesłanianie?
Plik `/etc/passwd` jako ogólnie dostępny może być źródłem sekretnych informacji. Intruz mając dostęp (nawet tylko chwilowy) do jednego z kont w systemie, po przechwyceniu pliku passwd, wchodzi w posiadanie nie tylko listy użytkowników systemu (listy potencjalnych ofiar ataku) oraz ich zaszyfrowanych haseł, ale także danych GECOS (niektóre firmy dbają o uzupełnianie tych danych). Mając plik z nazwami użytkowników i ich hasłami może w „zaciszu domowym” przeprowadzić ataki słownikowe na te konta porównując zaszyfrowane postacie odgadywanych haseł z tymi zapisanymi w pliku. Jak wiadomo wielu użytkowników korzysta z prostych haseł, co daje intruzowi większą gamę kont z których może próbować (po odgadnięciu hasła) uzyskać przywileje administratora (root). Zdarza się, że w celu przejęcia hasła włamywacz zadzwoni do pracownika, którego hasło chce poznać, przedstawi się jako administrator i poprosi o jego hasło (tzw. social engineering) Dokładna struktura pliku wygląda następująco (kolejne pola, dotyczące jednego konta):

+ Login – nazwa, za pomocą której użytkownik się loguje
+ Zaszyfrowane hasło – więcej o interpretacji łańcucha można przeczytać na stronie manuala (crypt (3))
+ Liczba dni od 1 stycznia 1970r, od kiedy hasło było ostatni raz zmieniane
+ Liczba dni, do kiedy hasło może zostać zmienione
+ Liczba dni przed momentem kiedy zmiana będzie wymagana
+ Data, po której hasło musi zostać zmienione
+ Liczba dni, jaka została do wygaśnięcia hasła, o czym jest ostrzegany użytkownik
+ Liczba dni, po wygaśnięciu hasła do wyłączenia konta
+ Liczba dni od 1 stycznia 1970r do chwili kiedy konto zostanie wyłączone

Aby „tandem” plików `/etc/passwd` i `/etc/shadow` mógł działać, plik shadow jest dostępny tylko i wyłącznie dla użytkownika root. Przykładowy wpis w pliku shadow może wyglądać następująco:

```
andrey:Ep6mckrOLChF.:10063:0:99999:7:::
```

Mimo iż plik shadow zapewnia nam pewien poziom względnego bezpieczeństwa, jest też kilka argumentów przemawiających za jego nie używaniem:

+ System nie posiada kont zwyczajnych użytkowników
+ System pracuje w sieci LAN i używa NIS (z ang. Network Information Services), aby pobrać lub dostarczyć login-y i hasła innym systemom w sieci.
+ System używa innych metod identyfikacji użytkowników, które nie wymagają pliku shadow

#### FreeBSD

W przypadku systemu FreeBSD mamy najczęściej do czynienia z parą plików `/etc/passwd` i `/etc/master.passwd`. Plik master.passwd może być odczytany tylko przez użytkownika root (czym przypomina plik shadow wykorzystywany w wielu dystrybucjach Linuksa), zawiera zbiór rekordów zakończonych nową linią. Po jednym rekordzie na użytkownika, zawierającym pola danych oddzielone znakiem „:”. Dokładna struktura pliku wygląda następująco ( kolejne pola dotyczące jednego konta ):

+ Login – nazwa, za pomocą której użytkownik się loguje
+ Zaszyfrowane hasło
+ Id użytkownika
+ Id grupy do której należy użytkownik
+ Klasa logowania użytkownika
+ Data wymaganej zmiany hasła
+ Data wygaśnięcia konta
+ GECOS ( ogólne informacje o użytkowniku jak imię, nazwisko, często też wydział etc. )
+ Ścieżka do katalogu domowego użytkownika
+ Nazwa powłoki z której użytkownik będzie korzystał po zalogowaniu

Plik `/etc/passwd` jest generowany na podstawie master.passwd za pomocą polecenia `pwd_mkdb`. Po operacji tej plik passwd jest pozbawiony informacji dotyczących klasy logowania, daty zmiany hasła oraz daty wygaśnięcia konta. Hasło w pliku passwd jest zastąpione znakiem „*”. Jeżeli hasło w pliku master.passwd zastąpione jest znakiem „*” oznacza to, że nikt nie może zalogować się na to konto za pomocą metod identyfikujących na bazie hasła. Może to zrobić używając metody wykorzystującej inteligentne karty lub metody biometryczne. Login nie powinien zawierać znaków { ‘-‘, ‘.’, ‘:’ }. Dwa pierwsze ponieważ wiele programów pocztowych może mieć (mogło) problem z przetwarzaniem takiej nazwy użytkownika, z kolei znak ‘:’ jest uważany za „historyczny” rozgranicznik pól w pliku passwd.

Klasy użytkowników opisane są w pliku `login.conf`, wspomniane klasy zawierają ustawienia konta, zasobów i środowiska.

#### Windows NT

Przejdźmy teraz na drugą stronę barykady i przyjrzyjmy się komercyjnemu systemowi jakim jest system MS Windows. Jako, że systemy Windows 98, 98Se oraz Me są już dość stare nie będziemy zajmować się ich zabezpieczeniami. Przyjrzymy się zatem elementom wprowadzonym w Windows NT i późniejszych (Windows 2000, 2003 i XP).

W systemach Windows z rodziny NT przechowywaniem haseł użytkowników zajmuje się SAM (z ang. Security Account Manager). Jest to baza danych, której format odpowiada formatowi plików rejestru.

Hasła są przechowywane w postaci zaszyfrowanej, używana jest jednostronna funkcja haszująca dająca względny poziom bezpieczeństwa. Zastosowano algorytm LM hash (LAN Manager hash), który jest odmianą algorytmu DES i służy do zaszyfrowania haseł nie dłuższych niż 15 znaków. Stosowany jest także protokół identyfikacji NTLM, który używa pochodnych algorytmu MD5.

Sam plik wydaje się bezpieczny ze względu, na to iż stosowana jest jednostronna funkcja haszująca oraz to, że nie ma do niego dostępu podczas pracy systemu. Plik jest zamknięty, nie jest możliwe usunięcie, skopiowanie, przeniesienie go lub zmiana jego nazwy. Dostęp do pamięci, gdzie załadowany jest plik SAM jest również ograniczony, dostęp posiada tylko grupa administracyjna. Hexedytor na tym pliku może zostać użyty wyłącznie, jeżeli został uruchomiony z przywilejami administracyjnymi. Z powyższego opisu wynika, iż dostęp do pliku można uzyskać przez bezpośredni odczyt dysku z poziomu innego systemu operacyjnego. Specjalnie do tego stworzono uruchamialną z płyty wersję programu ‘Ophcrack’, dołączoną do jednej z wielu dystrybucji LiveCD. Program pozwala na łamanie haseł zahaszowanych za pomocą LM hash. Od wersji 2.3 program podobno potrafi złamać 99.9% alfanumerycznych haseł nie składających się z więcej niż 14 znaków w kilka minut.

Przed Service Pack-iem 3 dla systemu Windows NT 4, możliwe było wyciągnięcie zaszyfrowanych haseł z rejestru w pracującym systemie. Później wprowadzono ‘SAM Lock Tool’ program o nazwie syskey.exe, który raz uruchomiony nie mógł być wyłączony (według Microsoftu). Program ten bronił dostępu do informacji o hasłach. Wraz z nadejściem systemu Windows 2000 program syskey.exe instalowany i uruchamiany jest domyślnie.

Jeżeli plik SAM zostanie skasowany np. z poziomu innego systemu operacyjnego, Windows NT zwyczajnie stworzy nowy z jednym kontem Administratora i jednym kontem Gościa, oba konta będą posiadały puste hasła. Tutaj należy wspomnieć, iż konto Gościa zostanie zablokowane.

Nie jest to jednak tak proste w przypadku Windows XP, jeżeli plik SAM zostanie skasowany w trakcie ładowania systemu pojawi się opis błędu:

```
bootup: lsass.exe - System Error
Security Accounts Manager initialization failed because of the
following error: A device attached to the system is not functioning.
Error Status: 0xC0000001
```

oraz propozycja restartowania systemu i uruchomienia go w trybie naprawczym. Istnieje metoda obejścia tego problemu ale nie jest ona przedmiotem tej pracy. Na koniec wspomnieć chyba trzeba, iż proces lsass.exe przechowuje hasło zalogowanego użytkownika systemu w postaci tekstowej w swojej przestrzeni adresowej. Jak tylko kolejna osoba loguje się do systemu, przestrzeń ta jest nadpisywana. Jako, że istnieje praktycznie znikoma szansa pojawienia się tych danych w pliku stronnicowania oraz potrzebne są przywileje administracyjne, aby dostać się do przestrzeni adresowej procesu lsass.exe, wydaje się, że hasła nawet w tej postaci są bezpieczne.

Jeżeli jednak system podłączony jest do sieci, możliwa jest następująca sytuacja: Za pomocą kilku ogólno dostępnych narzędzi (pobrać je można ze strony firmy sysinternals). Osoba posiadająca hasło Administratora, może wykonać następujące czynności:

```
C:\>pslist lsass \\192.168.0.49 -u administrator -p pass (pobiera pid procesu lsass)
C:\>psexec.exe \\192.168.0.49 -c pmdump.exe 220 foo.dat (220 = pid procesu lsass w trakcie tego testu)
C:\>move \\192.168.0.49\admin$\system32\foo.dat c:
```

Wystarczy otworzyć przygotowany plik `foo.dat` za pomocą edytora heksadecymalnego i poszukać ciągu ‘0E003F000001080000000000’, dane dotyczące loginu i hasła są przechowywane 20 bajtów za podanym ciągiem. Widać tutaj, iż administrator jest wstanie poznać hasła użytkowników omijając część modelu bezpieczeństwa.

#### Zmiana hasła

W większości systemów z rodziny UN*X hasło zmienia się za pomocą polecenia passwd. Program prosi o podanie starego hasła (ma to za zadanie uchronić przed niepowołaną zmianą hasła przez intruza jeżeli użytkownik zostawi swój terminal), potem użytkownik podaje nowe hasło i program zmienia zawartości plików `/etc/passwd` (ewentualnie `/etc/shadow`).

W dystrybucjach Linuksa istnieje możliwość manualnego edytowania plików, w systemie FreeBSD nie jest to zalecane, ponieważ system ten generuje także binarne pliki pwd.db na podstawie passwd i master.passwd.

W systemach Windows zarządzanie hasłami odbywa się przez narzędzie Zarządzanie komputerem znajdujące się w Narzędziach administracyjnych w Panelu sterowania.

### Ataki na systemy haseł

Każde hasło, nawet najsilniejsze może być skompromitowane. Skompromitowanie hasła oznacza poznanie go przez osobę do tego nieupoważnioną. Niebezpieczeństwo jest widoczne na każdym etapie wykorzystywania hasła. Zagrożenie rozpoczyna się już nawet w momencie wpisywania hasła – intruz może zajrzeć użytkownikowi przez ramię lub może wystarczy mu tylko analiza samych ruchów ramion?

Kolejną możliwością skompromitowania hasła jest przesłanie go niezabezpieczonym kanałem z terminala do hosta. Jeżeli intruz pozna podczas takiej transmisji hasło jednokrotne nie powinno mieć to dużego znaczenia dla bezpieczeństwa całego systemu. Gdy użytkownik posiada słabe hasło, hasło będące prostym słowem może narazić system na ataki słownikowe. Ataki te polegają na intensywnych próbach dopasowania znanych wzorców za pomocą przeszukiwania wyczerpującego. Wszelkie możliwości ataków wynikają ze słabości systemu haseł.

Istnieje szereg niedogodności korzystania z haseł jako metody uwierzytelniania:
+ W procesie identyfikacji hasło udowadniającego P (z ang. prover) staje się znane weryfikatorowi V (z ang. verifier). V może podszywać się dzięki temu pod P.
+ Jeżeli V nie identyfikuje się P, intruz I ( z ang. intruder ) może podszyć się pod V w celu pozyskania hasła P.
+ Jeżeli hasło jest niezależne od czasu, I może próbować ataków przez powtórzenie.

Relacje między P i V są wysoce asymetryczne. Aby wyeliminować problem przekazywania “sekretnej” informacji należy skorzystać z silnej identyfikacji podmiotów (o tym później). Zabezpieczenie się przed atakami przez powtórzenie wiąże się ze sposobem implementacji systemu uwierzytelniającego. Wprowadzenie zasady trzech prób logowania dziennie, oddalonych o określoną ilość czasu skutecznie może uniemożliwić lawinowe ataki przez wypróbowywanie kolejnych haseł.

### Inne modele identyfikacji

Identyfikacja na bazie wykorzystania hasła może być bardziej wyrafinowana. Istnieje szereg modeli identyfikacji wykorzystujących silną identyfikację podmiotów. Jedną z takich metod jest identyfikacja “wyzwanie – odpowiedź” (protokół negocjowania – lub często nazywany protokół “uścisku dłoni” - “handshaking protocol”).

#### Identyfikacja „wyzwanie – odpowiedź”

Podczas dialogu P i V nie wymieniają między sobą żadnych haseł. Zamiast tego wspólne hasło znane zarówno P i V wykorzystywane jest do generowania poprawnych odpowiedzi na tzw. wyzwania. W tym kontekście “wspólne hasło” pełni rolę klucza kryptograficznego. Protokół ten pozwala P i V upewnić się, że posiadają ten sam zestaw kluczy (haseł).

Protokół wygląda następująco:

+ zarówno P i V wybierają losowo dwie liczby ra i rb oraz korzystają z tego samego algorytmu szyfrującego.
+ najpierw P wysyła (faktycznie ujawnia) V swoją losową liczbę ra
+ w odpowiedzi V wysyła nazwę P, jego wyzwanie (ra) oraz własne wyzwanie (rb) zaszyfrowane za pomocą klucza k (znanego zarówno P i V)
+ po otrzymaniu, P odszyfrowuje wiadomość sprawdza poprawność nazwy i losowej liczb ra gdy wszystko się zgadza P ma pewność co do autentyczności V.
+ aby taka pewność co do autentyczności P miał V, P wysyła zaszyfrowaną wiadomość składającą się z nazwy P oraz losowej liczby rb
+ gdy V odszyfruje i sprawdzi poprawność liczby rb zyskuje pewność co do autentyczności P.

Model identyfikacji “wyzwanie-odpowiedź” może zostać przeprowadzony za pomocą kluczy publicznych.

#### Wykorzystanie kluczy publicznych

Jak dobrze wiadomo system szyfrowania z kluczami publicznymi może być zarówno użyty do ukrycia informacji jak i do weryfikacji jej autentyczności. Protokół można zastosować w celach uwierzytelniania następująco:

+ P wysyła do V swoje losowe wyzwanie ra
+ V podpisuje parę składającą się z losowego wyzwania ra i własnego losowego wyzwania rb swoim kluczem prywatnym kv
+ P za pomocą klucza publicznego Kv odszyfrowuje wiadomość V po czym podaje V jego losowe wyzwanie rb
+ Zweryfikować musi się teraz P wobec V wiec, V tworzy swoje losowe wyzwanie i wysyła je do P.

Proces ten jest lustrzanym odbiciem tego co przed chwilą opisaliśmy. W systemach dowodzenia z wiedzą zerową udowadniający P ma możliwość zademonstrowania wobec weryfikatora V znajomości pewnego sekretu bez ujawniania jakichkolwiek szczegółów.

Protokołów identyfikacyjnych z „wiedzą zerową” jest sporo, najpopularniejsze to:
+ protokół Fiata-Shamira
+ protokół Feigego, Fiata i Shamira
+ protokół Guillou i Quisquatera

Niektóre z nich bazują na tożsamości podmiotów i są szczególnie przydatne do celów identyfikacji za pomocą inteligentnych kart chipowych (z ang. smart cards) Omawianie ich wykracza jednak mocno poza tematykę tej pracy.

### PAM - Wprowadzenie

Skrót PAM pochodzi od „Pluggable Authentication Modules”. Jest to system autoryzacji utworzony przez firmę SUN na potrzeby systemu operacyjnego Solaris, dzięki udogodnieniom jakie wprowadzał od razu stał się bardzo ważnym rozszerzeniem mechanizmów bezpieczeństwa. Nie pozostało to niezauważone, ponieważ niedługo później PAM został zaadoptowany przez „Open Software Foundation” i jest obecnie wykorzystywany w wielu opensource’owych systemach. Jego standard jest opisany przez V. Samara i R. Schemersa w dokumencie OSF RFC 86.0. "Unified Login with Pluggable Authentication Modules (PAM)". Linux-PAM bo taką nazwę nosi implementacja platformy GNU/Linux stworzył Andrew G. Morgan (morgan@ftp.kernel.org). W porównaniu do oryginalnej implementacji posiada ona szereg rozszerzeń, takich jak modularna konfiguracja i możliwość hierarchicznej autentykacji.

PAM został utworzony, aby pokonać trudności związane z wprowadzaniem nowych mechanizmów identyfikacji. Każdy taki nowy mechanizm powodował konieczność przepisywania od nowa kodu odpowiedzialnego za przeprowadzenie identyfikacji (konieczność taka zachodziła w przypadku programów login, rlogin, passwd, telnet itp.). Mechanizmy PAM są implementowane jako niewidoczne dla aplikacji moduły. Sposoby uwierzytelniania mogą zostać skonfigurowane osobno dla każdej z nich, administrator systemu ma możliwość skonfigurowania naiwnego przyzwalania dostępu jak i paranoicznego połączenia skanowania siatkówki, próbkowania głosu i hasła jednorazowego. Wynika z tego, że system ten może być dowolnie skonfigurowany. PAM udostępnia także korzystanie z mechanizmów w stosie, czyli aplikacja może korzystać z uwierzytelniania przez kilka mechanizmów. Aby dokonać autentykacji użytkownika, aplikacja komunikuje się z Linux-PAM. System PAM określa na podstawie konfiguracji, odpowiedniej dla danej aplikacji sposób autentykacji i określa, które moduły i w jaki sposób będą realizowały autentykację. Po ustaleniu uprawnień PAM przesyła odpowiednie informacje o uprawnieniach użytkownika do aplikacji.

W dystrybucjach GNU/Linux biblioteka `libpam` znajduje się w katalogu `/lib`, a moduły, noszące nazwy zaczynające się od ‘pam_’ znajdują się w katalogu `/lib/security` lub `/lib64/security`, w niektórych implementacjach UNIX moduły PAM mogą być zlokalizowane w katalogu `/usr/lib/security`. Omawiany system jest na tyle elastyczny, że za jego pomocą programista może tworzyć programy całkowicie niezależne od samego sposobu uwierzytelniania korzystającego z nich użytkownika. Jego uniwersalność pozwala na opracowanie specjalnych metod autentykacji dla istniejących usług w sieciach komputerowych, a otwarta architektura umożliwia jego dowolną rozbudowę i wsparcie dla nowych technologii autentykacji. Innymi słowy, możliwe jest przełączanie pomiędzy rożnymi mechanizmami uwierzytelniania, bez potrzeby ponownego kompilowania aplikacji, a tym bardziej ponownego jej programowania.

Wiele systemów z rodziny „Linuks” nie jest domyślnie przystosowanych do współpracy z PAM, przez co po instalacji bibliotek wymagana jest rekompilacja wielu programów. System ten wykorzystywany jest w Debian/GNU, RedHat Linux (wersje 3.04 wzwyż), SuSE Linux oraz w systemie FreeBSD. Tutaj należy zauważyć, że ostatni z wymienionych systemów operacyjnych może korzystać zarówno z implementacji Linux-PAM (w szczególności wersje z gałęzi 4.x) lub własnej implementacji OpenPAM (wersje systemu z gałęzi 5.x i 6.x, nie oznacza to jednak, że administrator nie może skompilować Linux-PAM z drzewa portów).

#### PAM - Cele

Podczas projektowania systemu PAM, programiści postawili sobie następujące cele:

+ Administrator systemu powinien móc wybrać domyślny sposób uwierzytelnienia;
+ Administrator systemu powinien móc ustalić sposób autentykacji dla każdego programu z osobna;
+ Interakcja z użytkownikiem nie powinna być ograniczona przez PAM. Ważne jest by zarówno programy działające na konsoli (np. login) jak i aplikacje z graficznym interfejsem użytkownika (np. xdm, gdm lub kdm) mogły wykorzystywać PAM;
+ Administrator powinien mieć możliwość skonfigurowania aplikacji w taki sposób, aby mogła korzystać z kilku mechanizmów autentykacji jednocześnie;
+ Powinna być możliwość wykorzystania hasła podanego jednemu modułowi uwierzytelniającemu w następnym module (wspominany przez nas wcześniej stos modułów) bez ponownego wpisywania;
+ Moduł powinien móc korzystać z wiecej niż jednego hasła;
+ Elementy systemu nie powinny wymagać zmiany, gdy mechanizm identyfikacji zmieni się na inny;
+ Architektura powinna zapewniać „wtyczkowy” model dla identyfikacji, a także zarządzania hasłami, kontami i sesjami użytkownika;
+ PAM powinno spełniać wymagania stawiane obecnie używanym w systemie mechanizmom zapewniającym bezpieczeństwo.

Podczas projektowania wykluczono następujące zastosowania systemu PAM:

+ PAM pomaga tylko ustalić tożsamość użytkownika. Nie zajmuje się natomiast przekazywaniem tej tożsamości innym programom (np. jeśli użytkownik wprowadzi hasło do programu login, to program ftp uruchomiony później nadal będzie wymagał podania hasła);
+ PAM nie zajmuje się problemem przesyłania nie zakodowanych danych przez sieć (np. przesyłanie nie zakodowanego hasła przez klienta protokołu FTP);
+ PAM nie zajmuje się spójnością danych trzymanych w różnych miejscach (np. jeśli hasła trzymane w dwóch bazach danych były identyczne, to PAM nie zapewni automatycznej ich synchronizacji to zależy od konkretnego modułu).

#### PAM – Scenariusz wykorzystania

Poniżej przedstawiony jest przykładowy scenariusz wykorzystania systemu PAM:

+ Administrator instaluje w systemie oprogramowanie wykorzystujące PAM (np. sudo lub ssh);
+ Administrator instaluje w systemie moduły PAM, które chciałby wykorzystywać w aplikacjach (część modułów, np. pam_unix, jest w standardowym pakiecie zawierającym PAM);
+ Administrator konfiguruje PAM dla swoich aplikacji (np. sudo lub ssh ) „mówiąc” PAM z jakich modułów ma korzystać dana aplikacja do uwierzytelnienia;
+ Użytkownik chce skorzystać z aplikacji (np. su) i uruchamia ją;
+ Aplikacja prosi użytkownika o nazwę użytkownika (lub np. pobiera ją jako argument);
+ Aplikacja wywołuje funkcję pam_start z PAM API przekazując nazwę użytkownika oraz wskaźnik do funkcji „konwersacji” z użytkownikiem;
+ PAM decyduje jakie moduły powinny wziąć udział w autentykacji na podstawie pliku konfiguracyjnego tego programu;
+ Aplikacja wywołuje funkcję `pam_authenticate` żeby sprawdzić tożsamość użytkownika.
+ Moduły które są skonfigurowane dla tej aplikacji po kolei próbują sprawdzić tożsamość użytkownika i jeśli potrzebują komunikacji z użytkownikiem to wywołują funkcję konwersacji (przekazaną przez aplikację);
+ Po wykonaniu się odpowiednich modułów, do aplikacji zwracany jest wynik pozytywny (`PAM_SUCCESS`) lub negatywny (`PAM_AUTH_ERR` lub `PAM_ACCT_EXPIRED` lub `PAM_AUTHOK_ERR` – w zależności od kategorii modułu);
+ Aplikacja wykonuje się dalej w zależności od powodzenia autentykacji (np. kończy się z błędem w razie niepowodzenia);
+ Przed wyjściem, aplikacja wywołuje funkcję ‘pam_end’ z PAM API.

#### PAM - Moduły

System PAM ma cztery podstawowe grupy modułów, zarządzające czterema grupami zadań:

+ `auth` - autentykacją (z ang. authentication management). Moduł ten dostarcza dwie możliwości identyfikacji. Ustala czy użytkownik jest tym za kogo się podaje, żądając wpisania hasła lub posługując się innymi, mniej lub bardziej wyrafinowanymi metodami (np. badanie siatkówki oka). Moduł może przyznawać przynależność "grupową" - niezależnie od mechanizmu linuksowego opartego o `/etc/groups`
+ `account` - kontami (z ang. account management). Tego typu moduły odpowiedzialne są za zarządzanie kontami w sposób niezwiązany z autentykacją. Typowym przykładem jest uzależnianie dostępu do usługi w zależności od pory dnia, danego stanu zasobów (np. liczby zalogowanych użytkowników) lub też sposobu logowania się (np. logowanie na roota tylko z konsoli).
+ `session` - sesją (ang. session management). Zarządzanie sesją obejmuje działania, jakie system podejmuje przed otwarciem, czy zamknięciem sesji użytkownika. Działania te mogą obejmować rejestrowanie informacji o użytkowniku, modyfikacje środowiska lub montowanie odpowiednich systemów plików. Dotyczy to modułów związanych z zadaniami wykonywanymi przed i po uzyskaniu dostępu do usługi. Mogą to być zapisy do logów, montowania itp;
+ `password` - hasłami (ang. password management). Ten moduł jest odpowiedzialny za uaktualnianie baz danych o użytkownikach, zawierających ich hasła, dane osobowe, czy listy uprawnień.

#### PAM - Konfiguracja systemu

Konfiguracja PAM znajduje się w pliku /etc/pam.conf lub w plikach w katalogu `/etc/pam.d`. Ta druga wersja jest nowsza i wygodniejsza. Składnia plików konfiguracyjnych jest praktycznie w obydwóch przypadkach identyczna.

#### PAM - /etc/pam.conf

Plik zbudowany jest jako lista reguł. Każda reguła jest w osobnej linii i zakończona znakiem LF. Komentarze rozpoczynają się od znaku # i są kontynuowane do znaku nowej linii.

Każda reguła zbudowana jest ze zbioru oddzielonych spacją argumentów, pierwsze trzy mają wrażliwą składnię. service module control module-path module-arguments W przypadku plików w katalogu /etc/pam.d różnica jest tylko taka, że każdy plik odpowiada jednej usłudze. Nazwa pliku jest taka sama jak pole service, a linie w pliku zawierają pozostałe cztery pola. Każda linia odpowiada pojedynczemu modułowi PAM dla konkretnej usługi. Stosowanie plików konfiguracyjnych w katalogu /etc/pam.d jest rozwiązaniem bardziej uniwersalnym i zalecanym. Znaczenie wymienionych pól jest następujące:

+ `service` - oznacza typ usługi wymagającej metody autoryzacji, na przykład login, su, ftpd, Specjalną nazwą jest 'OTHER' odnoszącą się do usług niewyszczególnionych osobno;
+ `module` - jeden z opisanych powyżej czterech typów modułów: auth, account, session, password;
+ `control` - to pole definiuje reakcję systemu PAM na rezultat pracy modułu. Ten parametr określa, w jaki sposób biblioteka PAM będzie reagowała na sukces lub porażkę pojedynczego modułu.

Z historycznych względów dozwolone są następujące proste wartości:

+ `required` - zakończenie się niepowodzeniem takiego modułu spowoduje zwrócenie do systemu informacji o błędzie, ale dopiero, gdy wszystkie moduły na stosie zostaną wywołane. Innymi słowy pomyślna autentykacja przy pomocy tego modułu jest konieczna dla całego procesu autentykacji;
+ `requisite` - podobnie jak w przypadku required, ale kontrola przekazywana jest bezpośrednio do aplikacji; * sufficient - pomyślność autentykacji w przypadku tego modułu jest wystarczająca dla całego procesu uwierzytelniania;
+ `optional` - powodzenie lub porażka autoryzacji ma znaczenie jedynie, gdy moduł z tą flagą jest jedynym modułem na stosie związanym z daną usługą.

Ponieważ moduły mogą być ustawiane na stosie, potrzebne jest ustalenie ich względnej ważności (np.jedno wywołanie funkcji pam_authenticate() przez aplikację może pociągać za sobą wywołanie kilku modułów typu 'auth', przy czym wartość zwracana przez tą funkcje jest wypadkową działania wszystkich tych modułów razem). Kolejność wywołań modułów odpowiada kolejności ich występowania w pliku `/etc/pam.conf` (wcześniejsze wpisy najpierw). Dla bardziej skomplikowanych zastosowań pole control może mieć następującą składnię:

```
wartość1=działanie1 wartość2=działanie2 ...
```

Pole wartość oznacza jeden z około 30 różnych wartości zwracanych przez moduł, dotyczących przebiegu autentykacji, na przykład:

```
success, open_err, symbol_err, service_err, system_err, buf_err, perm_denied, auth_err, cred_insufficient, authinfo_unavail, user_unknown, maxtries, new_authtok_reqd, acct_expired, session_err, cred_unavail, cred_expired, cred_err, no_module_data, conv_err, authtok_err, authtok_recover_err, authtok_lock_busy, authtok_disable_aging, try_again, ignore, abort, authtok_expired, module_unknown, bad_item oraz default.
```

Pełna lista wartości zwracanych przez PAM znajduje się w `/usr/include/security/_pam_types.h`

Pole działanie może przyjąć wartość dodatniej liczby całkowitej n, oznaczającej skok o n kolejnych modułów na stosie PAM lub jedną z 6 wartości:

```
ignore, ok, done, bad, die, reset
```

Oznaczają one działanie, jakie biblioteka ma podjąć po otrzymaniu kodu o podanej wartości.

+ `module-path` - pełna ścieżka dostępu do danego modułu, najczęściej `lib/security/pam_nazwamodułu.so`.
+ `arguments` - opcjonalne argumenty dla modułu przekazywane mu przez linię poleceń i wpływające na jego pracę. Argumenty są różne dla konkretnych modułów. Oprócz tego jest grupa argumentów standardowych. Wszystkie moduły powinny przyjmować ogólne argumenty takie jak: `debug`, `no_warn`, `use_first_pass`, `try_first_pass`, `expose_account`.

+ `debug` - Prześlij informację debug do sysloga;
+ `no_warn` - Nie przesyłaj ostrzeżeń do aplikacji;
+ `use_first_pass` - Nie pytaj użytkownika o hasło. Zamiast tego użyj hasła otrzymanego przez poprzedni moduł - jeżeli to nie zadziała użytkownik nie zostanie zautentykowany (dotyczy to tylko modułów typu `auth` i `password`);
+ `try_first_pass` - Spróbuj autentykować na podstawie poprzedniego hasła. Jeśli to nie zadziała spytaj użytkownika o hasło.
+ `expose_account` - Udostępniaj informacje o koncie użytkownika.

#### PAM - /etc/pam.d

Stosowanie plików konfiguracyjnych w katalogu /etc/pam.d ma różne zalety, takie jak łatwiejsza rekonfiguracja, możliwość używania dowiązań symbolicznych dla tych samych metod autentykacji oraz ułatwione zarządzanie pakietami (każdy pakiet DEB, lub RPM może dodawać własny plik z metodą autentykacji).

#### PAM – Przykładowe wpisy konfiguracyjne

Poniżej przedstawione są przykładowe wpisy konfiguracyjne. Jeżeli system ma być uważany za zabezpieczony, musi mieć dobrze sformułowane wpisy ‘other’, dotyczące innych niewyszczegółowionych w plikach konfiguracyjnych aplikacji. Przedstawione poniżej wpisy można nazwać dość paranoicznymi ustawieniami ale jest to dość dobry start na sam początek konfiguracji systemu.:

```
#
# Plik konfiguracyjny PAM dla usługi other
#
auth required pam_deny.so
auth required pam_warn.so
account required pam_deny.so
account required pam_warn.so
password required pam_deny.so
password required pam_warn.so
session required pam_deny.so
session required pam_warn.so
```

Dzięki konfiguracji takiej jak powyżej, administrator zostanie powiadomiony za każdym razem, jeżeli aplikacja nie posiadająca swojej konfiguracji PAM zostanie uruchomiona. Na sam koniec części opisującej konfigurowanie PAM, należy wspomnieć, co stanie się jeżeli plik `/etc/pam.conf` zostanie „uszkodzony” lub usunięty (lub pliki `/etc/pam.d/*` zostaną usunięte). Odpowiedź jest prosta, administrator zostanie odcięty od systemu, jedyną możliwością naprawy będzie odzyskanie systemu z obrazu w kopii zapasowej danych lub uruchomienie systemu w trybie naprawczym (trybie pojedynczego użytkownika) i próba manualnej naprawy plików.

#### PAM - Narzędzia i moduły

O sile systemu PAM stanowi uniwersalne API umożliwiające rozwijanie różnych modułów autentykacji. W standardowej dystrybucji Linux-PAM jest ponad 20 różnych modułów. Oprócz tego dostępnych jest ponad 40 modułów dodatkowych dostępnych jako osobne pakiety. Listę tych wszystkich modułów, wraz z opisem można znaleźć na stronach projektu Linux-PAM. Poniższa lista zawiera krótkie informacje o niektórych wybranych modułach:

- pam_access - moduł udostępnia kontrolę dostępu na bazie listy zawierającej login, nazwę hosta oraz adres IP;
- pam_cracklib - eliminuje słabe hasła, poprzez sprawdzanie ich pod kątem słów z języka naturalnego;
- pam_debug - służy do kontroli działania stosu PAM;
- pam_deny - moduł używany aby zabronić jakiegokolwiek dostępu;
- pam_echo - służy do wypisywania komunikatów specjalnych dla użytkowników;
- pam_env - moduł pozwala na zmianę ustawień zmiennych środowiskowych
- pam_exec - moduł może zostać użyty do wykonania zewnętrznej komendy
- pam_filter - moduł ten ma być platformą pośredniczącą w dostępie do danych wymienionych przez użytkownika i aplikację;
- pam_ftp -obsługuje anonimowe ftp;
- pam_group - nie autoryzuje użytkownika lecz przydziela go do grup użytkowników;
- pam_lastlog - moduł pozwala na wyświetlenie informacji o ostatnim logowaniu użytkownika;
- pam_limits - ogranicza dostęp do zasobów systemowych dla sesji użytkownika;
- pam_listfile - reguluje dostęp na podstawie list użytkowników;
- pam_localuser - moduł pozwala na zalogowanie się tylko użytkownikom wyszczególnionym w pliku /etc/passwd;
- pam_mail - pozwala na powiadomienie użytkownika o nowej poczcie na koncie;
- pam_mkhomedir - moduł tworzy katalog domowy użytkownika wraz z rozpoczęciem jego sesji, jeżeli taki katalog nie istnieje. Pozwala to na przechowywanie danych dotyczących użytkowników w centralnej bazie danych (np.: NIS lub LDAP);
- pam_motd - moduł pozwala na wyświetlenie pliku MOTD – Message Of The Day;
- pam_nologin - moduł zapobiega logowaniu się użytkowników, jeżeli w systemie występuje plik /etc/nologin;
- pam_permit - moduł ten zawsze zezwala na dostęp - pam_securetty - moduł zezwala na logowanie na konto root tylko i wyłącznie, jeżeli logowanie jest wykonywane z terminala oznaczonego jako bezpieczny w pliku /etc/securetty;
- pam_shells - moduł zezwala na zalogowanie, jeżeli powłoka użytkownika znajduje się w pliku /etc/shells;
- pam_tally - realizuje odmowę dostępu w zależności od liczby nieudanych logowań;
- pam_time - moduł ogranicza dostęp do systemu na podstawie daty, pory dnia;
- pam_unix - realizuje standardowe uniksowe zarządzanie autentykacją;
- pam_userdb - moduł służy do sprawdzenia poprawności loginu i hasła na podstawie danych zawartych w bazie Berkeley DB;
- pam_warn - moduł logujący informacje do syslog.

Moduł pam_unix realizuje wszystkie funkcje związane z tradycyjnym modelem autentykacji w Uniksie, opartym na pliku /etc/passwd. Dzięki niemu można skonfigurować system używający PAM tak, aby naśladował działanie systemu haseł Uniksa. Występują także nie wypisane w tej krótkiej liście moduły dodatkowe umożliwiające wprowadzenie rozproszonego systemu autentykacji wykorzystującego sieć komputerową. Dostępne są moduły umożliwiające połączenia z siecią opartą na serwisach LDAP, NDS czy Samba lub powiązane z konkretną aplikacją np.: z serwerem Apache.
