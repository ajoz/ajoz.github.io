---
layout: post
date: 06-02-2007
author: Andrzej Jóźwiak
title: Sudo - podstawy
tags: freebsd unix shell sudo security
disqus: true
---

Sudo jest programem suid root, który udostępnia kontrolę nad komendami, które muszą być wydane jako administrator przez zwykłych użytkowników. Pobiera komendę jaką chce się wykonać i porównuje ze swoja wewnętrzną listą pozwoleń. Jeżeli pozowolenia te umożliwiają danemu użytkownikowi wykonać komendę, `sudo` wykonuje komendę za niego. Administrator (root) może wykonywać komendy jako dowolny użytkownik, sudo może wykonywać komedny jako jakikolwiek dowolny użytkownik systemu.

Po odpowiedniej konfiguracji administrator systemu, może pozwolić użytkownikowi wywoływać komendy jako inny dowolny użytkownik. Sudo jest więc potężnym narzędziem mogącym pozwolić lub zabronić użycia prawie każdego zestawu komend. Ze względu na dużą elastyczność w tworzeniu pozwoleń, dokumentacja sudo odstrasza wielu nowych użytkoników.

Poza wymienionymi korzyściami wynikającymi z tego, iż nie musimy logować się na konto `root`, wynikają inne, między innymi taka, że każda komenda wydana za pomocą sudo jest logowana i dzięki temu można sprawdzić kto wykonał zmiany i jakie. Zle skonfigurowany program sudo, może być równie niebezpieczny jak samo udzielanie hasła root-a potrzebującym tego użytkownikom.

Sudo składa się z 3 części, pierwszą jest właściwe polecenie `sudo`, z którego będą korzystać użytkownicy. Następny jest plik konfiguracyjny programu `/etc/sudoers`. W pliku tym określone jest na co polecenie sudo może zezwolić konkretnym użytkownikom. Ostatnią częścią jest polecenie `visudo`, które pozwala edycję pliku konfiguracyjnego bez ryzyka uszkodzenia go.

Jeżeli składnia pliku `sudoers` jest nieprawidłowa, program `sudo` nie zadziała. Jeżeli polegasz na `sudo` w celu uzyskania dostępu do pliku `sudoers`, uszkodzenie go może zablokować twój dostęp do systemu a konkretnie do części administracyjnej. `Visudo` zabezpiecza częściowo przed taką sytuacją.

`Visudo` zamyka plik sudoers tak, że tylko jedna osoba może edytować plik naraz. Później otwiera plik konfiguracyjny sudo w edytorze (vi jest domyślnym edytorem, ale visudo respektuje zawartość zmiennej środowiskowej `$EDITOR`. Kiedy opuszcza sie edytor `visudo` przetwarza plik i potwierdza czy w pliku nie ma żadnych błędów. Nie jest pewne, że konfiguracja zadziała zgodnie z tym co oczekujemy ale przynajmniej mamy pewność, że plik i wpisy w nim zawarte są zbudowane poprawnie.

W przypadku gdy `visudo` znajdzie błąd wypisze numer linii z błędem i zapyta o dalsze działanie.

```
  >>> sudoers file: syntax error, line 44 <<<
  What now?
Mamy trzy wyjścia w tej sytuacji:
- powrócić do edycji pliku (wciskamy 'e')
- zrezygnować z wprowadzanych zmian (wciskamy 'x')
- wymusić zapisanie pliku (wciskamy 'Q')
```

Konfiguracja `sudo` stanie się łatwa z chwilą w której zrozumiemy konstrukcję pliku `sudoers`. Podstawowa składnia pliku mimo wszystko jest bardzo prosta, każda reguła ma następujący format:

```
  nazwa_uzytkownika	host = polecenie
```

* `nazwa_uzytkownika` to nazwa użytkownika który może wykonać dane polecenie.
* `host` jest nazwą systemu, którego dana reguła dotyczy. Sudo bowiem skonstruowane jest w taki sposób aby można było używać jednego pliku sudoers w wielu systemach.
* `polecenie` jest to pole, które zawiera listę poleceń, których dotyczy reguła. Do każdego polecenia musi być dodana pełna ścieżka w innym przypadku sudo nie rozpozna polecenia. (Jest to zabezpieczenie przed spryciarzami zmieniającymi zawartość swojej zmiennej `$PATH` w celu uzyskania dostępu do polecenia pod zmienioną nazwą).

Sudo domyślnie nie zezwala na nic (jak Kononowicz :P), aby użytkownik mógł wykonać polecenie, administrator musi stworzyć regułę, która daje użytkonikowi pozowolenie na wykonanie go na danym host-cie. Zamiast podawać konkretne wartości pól: nazwa_uzytkownika, host i polecenie można zastąpić je słowem kluczowym `ALL`. Słowo to pozwala dopasować wszystkie możliwe opcje.

Dla przykładu ufam użytkownikowi ra88 i pozwalam mu wykonywać wszystkie polecenia na dowolnym z moich systemów.

```
  ra88    ALL = ALL
```

Jednak przyznanie młodszemu administratorowi takiej władzy jest mało prawdopodobne. Jako starszy administrator powinienem wiedzieć jakich poleceń potrzebuje ra88 aby wykonywać swoją pracę. Przypuśćmy, że ra88 nadzoruje serwer nazw, który jest wyłącznie tylko częścią całego systemu. Kontrolujemy faktyczną edycję plików stref za pomocą przywilejów grupowych, jednak to nie pomoże bowiem serwer nazw musi być włączany, przeładowywany lub zatrzymywany. Poniżej nadam ra88 przywilej uruchamiania demona serwera nazw ndc(8) (nameserver deamon controller).

```
  ra88    ALL = /usr/sbin/ndc
```

Jeżeli współdzielę ten plik między wieloma maszynami jest całkiem prawdopodobne, że wiele z nich nie ma uruchomionego programu serwera nazw. Poniżej ograniczę dla jakich maszyn ra88 może wykonać to polecenie.

```
  ra88    dns1 = /usr/sbin/ndc
```

Z drugiej strony ra88 jest także administratorem serwera poczty o nazwie 'mail'. Tamten serwer jest całkowicie pod jego pieczą i może wykonać na nim dowolne polecenie. Mogę ustawić całkowicie odrębne przywileje na serwerze 'mail' mimo wszystko cały czas korzystając z jednego pliku.

```
  ra88    dns1 = /usr/sbin/ndc
  ra88    mail = ALL
```

Możliwe jest wyspecyfikowanie wielu poleceń, w jednej linii rozdzielając je przecinkami. Poniżej chciałbym aby ra88 mógł kontrolować zarówno serwer nazw jak i montować dyski za pomocą polecenia mount.

```
   ra88    dns1 = /usr/sbin/ndc, /bin/mount
```

Można wyszczególnić w pliku `sudoers`, że użytkownik może wykonywać komendy jako inny użytkownik, w zamian użytkownika root. Dzieje się to przez umieszczenie danej nazwy użytkownika w nawiasach przed poleceniem. Przypuśćmy, że mamy ustawione iż serwer nazw wykonuje się jako użytkownik 'named' i wszystkie polecenia kontrolujące serwer musza być wykonane jako ten użytkownik.

```
  ra88    dns1 = (named) /usr/sbin/ndc
```

Każda z reguł w pliku sudoers musi się znajdować w pojedyńczej linii. Może to powodować, że linie będą bardzo długie, aby jednak przejść do nowej linii stosuje się znak '\' na końcu każdej niekompletnej reguły.

```
  ra88    server1	= /sbin/fdisk,/sbin/fsck,/sbin/kldload,\
      /sbin/newfs,/sbin/newfs_msdos,/sbin/mount
```

Teraz kiedy rozumiemy podstawy działania sudo, spójrzmy jak działa owo polecenie. Pierwszy raz kiedy korzysta się z sudo (przy somyślnych ustawieniach) program prosi o hasło. Należy podać hasło swojego konta (nie hasło root-a :P). Jeżeli podane hasło jest niepoprawne program wykpi twoje umiejętności pisania, zdolności umysłowe oraz krewnych i pozwoli wprowadzić hasło ponownie. Po trzech niepoprawnych próbach wprowadzenia hasła sudo zamyka się i potrzeba znowu wprowadzić polecenie.

Wraz z chwilą gdy wprowadzone zostanie poprawne hasło sudo zapisze czas, jeżeli zostanie uruchomione ponownie w przciągu 5 minut od ostatniego uruchomienia nie zapyta się o hasło. Jeżeli jednak minie 5 minut potrzebna jest reidentyfikacja.

Aby dowiedzieć sie jakie polecenia są dla nas wyznaczone przez administratora, wystarczy wydać polecenie `sudo -l`.

```
  # sudo -l
  Password:
  User andrey may run the following commands on this host:
      (root) ALL
```

Jeżeli istniałby większe restrykcje zostałby wyświetlone.

Za pomocą `sudo` można wykonywać skomplikowane polecenia zawierające swoje argumenty. Dobrym przykładem może być polecenie `tail -f` pozwalające oglądać koniec pliku dziennika i mieć nowe wpisy pojawiające się na ekranie.

```
  # sudo tail -f /var/log/secure
  Dec 22 13:24:19 dns1 sudo:  ra88 : TTY=ttyp0 ; PWD=/home/ra88 ;
           USER=root ; COMMAND=list
   Dec 22 13:30:03 dns1 sudo:  ra88 : TTY=ttyp0 ; PWD=/home/ra88 ;
           USER=root ; COMMAND=/usr/bin/tail -f /var/log/secure
  ...
```

Możemy się zdecydować wykonać polecenie jako dowolny inny użytkownik o ile mamy do tego przywileje. Dla przykładu przypuśćmy, że mamy aplikację bazodanową gdzie polecenia muszą być wydawane jako użytkownik bazy. Użytkownik bazy to 'operator' ma przeprowadzić wykonywanie kopii zapasowej.

```
  # sudo -u operator dump /dev/sd0s1
```

Sudo domyślnie zapisuje logi swojej pracy w pliku `/var/log/secure`. Każda z wiadomości w logu zawiera znacznik kiedy został użyty program sudo, nazwę użytkownika, katalog w którym sudo zostało użyte oraz wywołane polecenie.

W najgorszym przypadku dzięki temu dziennikowi można prześledzić kto wykonał zmiany mające wpływ na stabilność systemu.

```
  Dec 22 13:24:19 dns1 sudo:  ra88 : TTY=ttyp0 ; PWD=/home/ra88 ;
           USER=root ; COMMAND=/bin/rm /etc/passwd
```

Nie trudno jest sobie wyobrazić, że mamy pod kontrolą kilka serwerów z różnymi poziomami przywilejów dla użytkowników przez to plik sudoers w szybkim czasie może się zrobić trudny do czytania. Z tą sytuacją radzą sobie aliasy.

Alias musi zostać zdefiniowany najpierw zanim zostanie użyty wewnątrz jakiejś reguły przyznającej przywileje. Dlatego najczęściej wszystkie aliasy znajdują się na początku pliku sudoers. Każdy alias ma etykietę określającą jego typ oraz listę elementów, których jest aliasem.

Aliasy użytkowników zaiwerają określoną listę użytkowników, definiowane są za pomocą etykiety User_Alias.

```
  User_Alias	DNSADMINS = ra88,freq
```

Alias użytkowników `DNSADMINS` zawiera dwóch użytkowników ra88, freq.

Aliasy "uruchom jako" są specjalnym typem aliasów. Alias tego typu zawiera listę użytkowników jako których można wykonać polecenie. Wiele serwerów nazw może być uruchamianych jako użytkownik 'named'. Administrator DNS musi mieć możliwość wykonywania poleceń jako ten użytkownik prawdopodobnie potrzebny będzie odpowiedni alias "uruchom jako" aby to wykonać. W wielu przypadkach administrator systemu odpowiedzialny za program chciałby móc także wywoływać tworzenie kopii zapasowej systemu jako użytkownik 'operator'.

```
  Runas_Alias   APPADMIN = named,dbuser,operator
```

Alias host-ów jest poprostu listą maszyn. Alias taki posiada etykietę `Host_Alias`. Aliast host-ów może zostać zdefiniowany jako nazwa host-a, adres IP lub blok adresów sieciowych. (Jeżeli korzysta się z nazw host-ów, plik sudoers może być narażony na problemy z rozwiązywaniem nazwy DNS)

```
  Host_Alias   DNSSERVERS = dns1,dns2,dns3
  Host_Alias   SECURITYSERVERS = 192.168.1.254,192.168.113.254
  Host_Alias   COMPANYNETWORK = 192.168.1.0/16
```

Alias poleceń jest lista poleceń. Alias taki posiada etykietę `Cmnd_Alias`.

```
  Cmnd_Alias   BACKUPS = /bin/mt,/sbin/restore,/sbin/dump
```

Możliwe jest także aby alias odnosił się do wszystkich poleceń znajdujących się w jednym katalogu.

```
  Cmnd_Alias   DBCOMMANDS = /usr/home/dbuser/bin/*
```

Aby użyć stworzonych wcześniej aliasów należy wstawić je w miejsca w których byłby użyte nazwy użytkowników, nazwy poleceń lub nazwy host-ów. Poniżej chcemy zezwolić wszystkim użytkownikom wyszczególnionym w aliasie `DNSADMINS` na wykonanie dowolnego polcenie na dowolnym naszym serwerze.

```
  DNSADMINS   ALL = ALL
```

Przypuśćmy, że użytkownik ra88 musi zarządzać aplikacją uruchomioną jako konkretny użytkownik.

```
  ra88	ALL = (APPADMIN)DBCOMMANDS
```

Jako administrator zajmujący się programami, ra88 musi mieć możliwość uruchamiania procesu tworzenia kopii zapasowych. Stworzony został do tego specjalny alias APPOWNER, który posiada przywileje użytkownika 'operator'.

```
  ra88	ALL = (APPOWNER)DBCOMMANDS, (APPOWNER)BACKUPS
```

Jest to znacznie efektywniejszy sposob niż poniższy.

```
  ra88   ALL = (dbuser,operator)/usr/home/dbuser/bin/*,\
         (dbuser,operator)/bin/mt, (dbuser,operator)/sbin/restore,\
         (dbuser,operator)/sbin/dump
```

Niektóre z przywilejów nadane przez `sudo` są zbędne, np.: posiadanie przywileju uruchamiania jako użytkownik bazy danych przy tworzeniu kopii zapasowych. Mimo wszystko jest to ściślejsza kontrola niż przydzielenie dostępu do hasła root.

Sudo posiada także możliwość wyciągania faktycznych nazw grup w systemie i nadawania im określonych przywilejów. Nazwa grupy poprzedzona jest znakiem '%'.

```
  %wheel   ALL = ALL
```

Nie zostały przedstawione tutaj wszystkie możliwości sudo więcej danych można łatwo znaleźć w dokumentacji systemowej, wstęp ten jednak napewno znacząco ułatwi jej czytanie.
