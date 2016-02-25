---
layout: post
date: 17-02-2007
author: Andrzej Jóźwiak
title: FreeBSD - przydatne polecenia
tags: freebsd unix shell commands
disqus: true
---

W systemie FreeBSD istnieje wiele prostych lecz bardzo przydatnych narzędzi i poleceń zaprojektowanych tak aby praca z systemem była jak najłatwiejsza. Nieważne jest jak długo pracuje się z systemem z rodziny Unix zawsze znajdzie się nowe skróty i sposoby wykonywania swoich codziennych zadań efektywniej.

Uwielbiam koncepcję wirtualnych terminali i zazwyczaj pracuję na 8 wraz z otwartą sesją X Window. W typowej sytuacji mam pracującą sesję PPP, kolejny terminal z otwartym klientem e-mail, kilka terminali z otwartymi stronami podręcznika systemowego man, jeszcze inny terminal gdzie wydaję polecenia jako root, i jeszcze jeden gdzie wydaje polecenia jako zwykły użytkownik już pewnie łapiecie obraz sytuacji. Wraz ze wzrastającą funkcjonalnością wzrasta zakłopotanie. Staram się używać kilku poleceń które pozwalają mi ogarnąć powstający niepożądek.

Jeżeli zapomnę na którym terminalu zostawiłem otwartą stronę manuala, klawisz PrintScrn pozwoli mi wędrować po terminalach w rosnącej kolejności. Jeżeli nie mam uruchomionej sesji X Window, mogę przeglądać zawartość terminali 1 - 8 w nieskończoność. Jeżeli jednak działam także na uruchomionej sesji X, podczas zmieniania terminali za pomocą PrintScrn zatrzymam się na terminalu 9, czyli serwerze X.

Jeżeli chciałbym się dowiedzieć na jakim terminalu pracuję mogę wydać polecenie `tty`:

```
$ tty
/dev/ttyv4
```

Zauważmy, że to jest defacto terminal 5, jako że są one numerowane od 0. Jeżeli opuszczę ten terminal, kombinacja klawiszy `Alt-F5` pozwoli mi do niego powrócić.

Jeżeli chcę się dowiedzieć jako kto jestem zalogowany w danym terminalu, używam polecenia `whoami`:

```
$ whoami
andrey
```
Jeżeli chcę się dowiedzieć kto jest zalogowany na dowolnym terminalu korzystam z polecenia `who`:

```
$ who
andrey          ttyv0   Feb  9 00:45
andrey          ttyv1   Feb  9 00:45
andrey          ttyv2   Feb  9 00:45
andrey          ttyv3   Feb  9 00:45
andrey          ttyv4   Feb  9 00:45
andrey          ttyv5   Feb  9 00:45
andrey          ttyv6   Feb  9 00:45
andrey          ttyv7   Feb  9 00:45
```

Trzeba zauważyć różnicę pomiędzy `who` i `whoami`. Na ttyv1, zalogowałem się jako andrey, potem zostałem root-em. Różnicę łatwo jest zaobserwować na poniższym listingu:

```
$ whoami
andrey
$ who
andrey      ttyv0     Feb   9 00:45
$ su
Password:
Feb   9 00:50:01  freebsd su: andrey to root on /dev/ttyv0
# who
andrey      ttyv0     Feb   9 00:45
# whoami
root
```

Jeżeli zapomnę w jakim katalogu aktualnie pracuję, używam `pwd`:

```
$ pwd
/usr/home/andrey
```

Dobra rada: Nigdy nie twórz lub usuwaj plików bądź katalogów zanim nie sprawdzisz za pomocą pwd gdzie rzeczywiście aktualnie pracujesz.

Jeżeli straciliśmy rachubę upływającego czasu:

```
$ date
Fri Feb  9 00:58:46 CET 2007
```

Jeżeli chcemy sie przyjrzeć całemu miesiącowi wydajemy polecenie:

```
$ cal
    February 2007
Su Mo Tu We Th Fr Sa
             1  2  3
 4  5  6  7  8  9 10
11 12 13 14 15 16 17
18 19 20 21 22 23 24
25 26 27 28
```

lub jeżeli chcemy sprawdzić jaki był dzień wybuchu 2 wojny światowej

```
$ cal 01 1939
    January 1939
Su Mo Tu We Th Fr Sa
 1  2  3  4  5  6  7
 8  9 10 11 12 13 14
15 16 17 18 19 20 21
22 23 24 25 26 27 28
29 30 31
```

Jeżeli chcesz zadziwić przyjaciół i znajomych proponuję wpisać:

```
$ cal 09 1752
   September 1752
Su Mo Tu We Th Fr Sa
       1  2 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29 30
```

Aby przejrzeć historię wykonywanych poleceń wystaczy wydać polecenie `h`. Trzeba tutaj zauważyć, że nie ma programu o nazwie `h`, lecz jest to alias dla polecenia `history`. Łatwo sie o tym przekonać przeszukując plik `~/.cshrc` zawierający ustawienia dla powłoki tcsh/csh jeżeli z niej korzystamy:

```
$ grep "alias h" .cshrc
alias h         history 25
```

Wracając do wyników zwracanych przez polecenie history:

```
28  1:40    grep "alias h" .cshrc
    29  1:40    date > ~/data
    30  1:40    cal > ~/kalendarz
    31  1:40    h
```

Nie lubie przepisywać wyników poleceń z ekranu znacznie łatwiej jest z nich korzystać przy pomocy przekierowania wyjścia do pliku. W tym celu stosujemy ">" po nazwie polecenia. Konwencja stosowania tego sposobu jest bardzo prosta:

```
polecenie > plik
```

Wyobraźmy sobie sytuację w której musimy napisać na listę dyskusyjną ponieważ mamy pewien problem z systemem. Osoba chcąca nam pomoc prosi o listing z `uname -a`, `dmesg` oraz `fstab`. Nic prostszego więc:

```
uname -a > /usr/home/andrey/help
dmesg >> /usr/home/andrey/help
more /etc/fstab >> /usr/home/andrey/help
```

Przyjżyjmy się teraz użytym poleceniom. Użyłem tylko raz ">" ponieważ tworzyłem nowy plik i nie obchodziło mnie czy nadpiszę już istniejący. Plik zostałby utworzony gdybym użył ">>" ale oszczędziłem sobie naciśnięcia klawisza. Musiałem wykorzystać ">>" aby zapisac wynik polecenia dmesg i zawartość fstab nie nadpisując już zachowanych danych z uname -a. Pamiętać należy, że przekierowywać mogę tylko wyniki poleceń nie mogę próbować tego samego z plikami. Mam na myśli w sposób:

```
$ fstab >> /usr/home/andrey/help
fstab: Command not found:
```

Wszystko pięknie ale co zrobić aby zamiast 3 poleceń wydać tylko jedno, oszczędzając tym samym sobie pisania ścieżki do pliku docelowego. Jeżeli spróbujemy w następujący sposób:

```
$ uname -a dmesg more /etc/fstab >> /usr/home/andrey/help
usage: uname [-amnrsv]
```

Coż niemiła niespodzianka. Można skorzystać z separatora poleceń ";", tak jak poniżej:

```
$ uname -a; dmesg; more /etc/fstab >> /usr/home/andrey/help
```

Jednak to nie jest to czego oczekiwaliśmy, na terminalu zostaną wypisane wszystkie informacje pochodzące od poleceń `uname` i `dmesg`, zaś do pliku zostanie zapisana zawartość fstab. Widać jednak, że jesteśmy na dobrej drodze. Rozsądek podpowiada, że powinniśmy podpowiedzieć powłoce iż chcemy, odpowiedzi wszystkich poleceń zapisane do pliku. Stosuje się do tego nawiasy "()", które pogrupują polecenia:

```
$ (uname -a; dmesg; more /etc/fstab) >> /usr/home/andrey/help
```

FreeBSD ma standardowo wiele użytecznych narzędzi do znajdywania informacji. Wszystko zależy, od tego co chcemy znaleźć. Jeżeli potrzebujemy znaleźć program, korzystamy z whereis:

```
$ whereis ls
ls: /bin/ls
```

Polecenie whereis pozwoli nam znaleźć także aplikację w drzewie portów.

Jeżeli potrzebujemy znaleźć plik, możemy skorzystać z locate:

```
$ locate fstab
/etc/fstab
```

Inny przypadek to gdy znaleźliśmy coś ale nie wiemy co to jest korzystam z polecenia whatis:

```
$ whatis ls
ls(1) - list directory contents

$ whatis fstab
fstab(5) - static information about the filesystems
```

Zauważmy, że polecenie whatis załączy numer manuala w nawiasach, więc aby otrzymać dodatkowe informacje wystarczy:

```
$ man 1 ls
$ man 5 fstab
```

Lecz co jeżeli potrzebujemy znaleźć specyficzny fragment tekstu? Będzie do tego potrzebne narzędzie `grep`:

```
$ grep toCzegoPotrzebujesz nazwa_pliku
```

Bardzo powszechnym sposobem wykorzystania narzędzia grep jest "potokowanie" mu informacji zwracanych przez polecenia:

```
$ dmesg | grep CPU
CPU: AMD Athlon(tm) 64 Processor
```

Często napewno korzystać będziemy z narzędzia `pkg_add` które pozwala na dodawanie oprogramowania do systemu, pobieramy pakiety, której później zostaną zainstalowane. Narzędzie to można porównać znanego z różnych dystrybucji systemu Linux programu apt-get. Narzędzie to dokładnie określi jakie wydanie i wersje systemu posiadamy po czym pobierze odpowiednie paczki z serwera ftp freebsd.org. Przyjrzyjmy się kilku przykładom:

```
# pkg_add -v -r mc
```

Domyślnie wydanie tego polecenia pobierze odpowiednią paczkę z programem `mc` (midnight commander) z serwera [ftp.freebsd.org](ftp.freebsd.org). Jeżeli serwer jest zbyt obciążony powinniśmy wybrać inna lokalizację pakietów. Można to wykonać ustawiając zawartość zmiennej środowiskowej PACKAGEROOT, zmienna ta określa inną lokalizację serwera z którego będą pobierane pakiety.

```
# export PACKAGEROOT=ftp://ftp3.FreeBSD.org
# pkg_add -v -r mc
```

Lista dostępnych serwerów lustrzanych z paczkami i plikami dla systemu FreeBSD znajduje się pod następującym adresem [http://mirrorlist.freebsd.org/FBSDsites.php](http://mirrorlist.freebsd.org/FBSDsites.php). Jako, że mieszkam w Polce po określeniu gdzie się znajduję i pakietów dla jakiej wersji oraz architektury szukam:

```
Performing Release Search: Country=POLAND Architecture=i386 Release=6.2
```

Najbliższym serwerm jest [ftp.pl.freebsd.org](ftp.pl.freebsd.org). Więc pobierając pakiety powinienem zastosować:

```
# export PACKAGEROOT=ftp://ftp.pl.freebsd.org
# pkg_add -v -r mc
```

Wiele osób narzeka na narzędzie `cvsup-without-gui` mówiąc, że korzystanie z niego nie jest zbyt łatwe. Chciałem pokazać alternatywę dla niego czyli program `portsnap`. Do standardowego wydania został dołączony we FreeBSD 6.0 i pozwala na szybkie i łatwe uaktualnianie drzewa portów w systemie. Aby mieć świeżutkie porty musimy wydać trzy polecenia:

Gdy pierwszy raz korzystamy z narzędzia portsnap lub nie mamy wogóle drzewa portów w systemie:

```
# portsnap fetch
```

Pobierze całe drzewo portów.

```
# portsnap extract
```

Wypakuje drzewo portów, potworzy odpowiednie pliki i katalogi. Gdy jednak mamy już drzewo portów lub wykonaliśmy już fetch i extract, każdą kolejną sesja z programem portsnap może wyglądać następująco:

```
# portsnap fetch
# portsnap update
```

Podczas instalowania programów z drzewa portów często otrzymujemy zaraz po wydaniu polecenia `make install clean`:

```
Vulnerability check disabled, database not found
```

Potrzebujemy więc narzędzia, które będzie sprawdzało bezpieczeństwo instalowanych portów, czy pojawiły się opisy ich luk bezpieczeństwa etc. Służy do tego narzędzie `portaudit`. Znajduje się ono na płycie instalacyjnej FreeeBSD 6.0 - 6.2 ale łatwo je przeoczyć, zresztą i tak lepiej mieć najświeższe oprogramowanie (oczywiście nie testowe).

```
# cd /usr/ports/security/portaudit; make install clean
```

Narzędzie portaudit tworzy odpowiedni skrypt powłoki w katalogu `/usr/local/etc/periodic/security/` w celu codziennej aktualizacji bazy danych zabezpieczeń. Pora zobaczyć `portaudit` w akcji:

```
# cd /usr/ports/security/sudo
# make install

===>  sudo-1.6.8.7 has known vulnerabilities:
=> sudo - local race condition vulnerability.
   Reference: &tt;http://www.FreeBSD.org/ports/portaudit/3bf157fa-
e1c6-11d9-b875-0001020eed82.html>
=> Please update your ports tree and try again.
*** Error code 1

Stop in /usr/ports/security/sudo.
```

FreeBSD wszystkie skrypty zarządzające usługami w katalogach `/etc/rc.d` i `/usr/local/etc/rc.d`. Z tych katalogów można uruchomić, zatrzymać dowolną z usług sieciowych i systemowych. Ogólna składnia wygląda następująco:

```
/etc/rc.d/service-name {start} {stop} {status} {reload} {forceXXX} {rcvar}
```

Kolejne opcje jakie można uzyć to:

* `start` : Uruchamia usługę
* `stop` : Zatrzymuje usługę
* `status` : Pobiera status usługi w znaczeniu czy jest uruchomiona czy nie
* `reload` : Przeładowuje usługę przydatne jeżeli wykonaliśmy zmiany w plikach konfiguracyjnych
* `forceXXX` : Aby zatrzymać, uruchomić lub restartować usługę bezwzględu na ustawienia w pliku /etc/rc.conf, polecenia powinny być poprzedzone prefiksem "force". Na przykład jeżeli chcemy uruchomić ponownie instancję sshd wykorzystujemy polecenie forcerestart (zastępujemy XXX wpisując start, stop, restart).
* `rcvar` : Pozwala na sprawdzenie czy usługą jest uruchamiana wraz ze startem systemu, czy znajduje się w pliku rc.conf.

Aby uruchomić usługę sshd:

```
# /etc/rc.d/sshd start
```

Aby zatrzymać usługę sshd

```
# /etc/rc.d/sshd stop
```

Aby uruchomić ponownie usługę sshd:

```
# /etc/rc.d/sshd restart
```

Wszytkie usługi w systemie FreeBSD zazwyczaj uruchamiane automatycznie są wyszczególnione w pliku `rc.conf`. Uruchomienie demona ssh jest tak proste jak dodanie poniższej linijki do pliku rc.conf:

```
sshd_enable="YES"
```

Aby zarządzac uruchamianymi podczas startu systemu usługami można skorzystać z narzędzia sysinstall jako root wydajemy polecenie `/usr/sbin/sysinstall` i w oknie programu w menu wybieramy kolejno:

```
Configure > Startup
```

Za pomocą klawisza spacji zaznaczamy które usługi mają być uruchomione a które nie.


I to by było na tyle. Artykuł został napisany jako tłumaczenie tekstu Dru Lavigne z serwisu [OnLamp.com](http://www.onlamp.com/pub/a/bsd/2000/08/09/FreeBSD_Basics.html), nie jest to jednak wierne tłumaczenie pozwoliłem sobie bowiem zmienić niektóre przykłady, zmienić komenatrze do zebranych informacji oraz dodać trochę rzeczy od siebie.
