---
layout: post
date: 19-10-2005
author: Andrzej Jóźwiak
title: Code::Blocks Studio - krótkie spojrzenie
tags: cpp c++ windows linux ide
disqus: true
---

Krótko i nie na temat... Przeglądając prasę w Empiku natrafiłem na nowy numer Software Dewelopera, a w nim na krótką wzmiankę o młodziutkim jeszcze IDE dla C/C++ nazywającym się Code::Blocks. Z krótkiego opisu wynikało, iż jest ono warte zainteresowania. Chciałbym w tym miejscu podzielić się z wami moimi wrażeniami. A więc z czym mamy do czynienia? Code::Blocks to darmowe IDE (Integrated Development Environment) czyli zintegrowane środowisko programistyczne przeznaczone dla programistów C/C++, zaprojektowane w taki sposób by łatwo było je wzbogacić o dodatkową funkcjonalność (tutaj mam na myśli rozbudowany system wtyczek - pluginów).

Code::Blocks to projekt open source, udostępniony na licencji GPL2, jego źródła są dostępne na stronie autorów - gotowe do samodzielnej kompilacji, także za pomocą wspomnianego środowiska. Archiwa z kodem źródłowym zawierają pliki projektu `*.gdb` (format plików w jakich dane zapisuje Code::Blocks).

Środowisko stworzone zostało za pomocą GNU C++, dzięki temu dostępne jest na dwóch platformach Linux oraz Windows. Wersje dla tych dwóch systemów nieznacznie się od siebie różnią, o czym napisze później. Rozszerzenie możliwości aplikacji zapewniają wtyczki, SDK potrzebny do pisania wtyczek jest również dostępny na stronie projektu. Ciekawi was pewnie, jakie kompilatory obsługuje Code::Blocks... Zadziwię was trochę, gdyż w tej chwili można podpiąć aż 5 różnych kompilatorów. Mnie zaskoczyło to bardzo mile, ponieważ jeszcze nie natknąłem się na środowisko które by na to pozwalało:


1. **GCC (MingW/Linux GCC)** - W tej chwili najlepiej obsługiwany kompilator przez Code::Blocks, dla niego środowisko posiada najwięcej opcji konfiguracyjnych. Jedyny mały problem występuje podczas debugowania projektu, jeżeli w ścieżce do plików, które maja być odpluskwione znajduje się spacja - debugger może nie zadziałać poprawnie. Ze strony projektu można ściągnąć dwie wersje środowiska z kompilatorem MingW lub bez, różnica jest znacząca - paczka z kompilatorem zajmuje bowiem aż 10 MB (dla posiadaczy słabszych łącz może być to przykre). Jednakże można ściągnąć same środowisko a kompilator doinstalować później. Code::Blocks wykrywa kompilatory znajdujące się w systemie i pozwala na ich wybór. Obecnie gdy wyszła wersja 1.0 RC1 środowisko wykrywa kompilator, który dostarczony wraz z DevCPP.
2. **MSVC++** czyli Microsoft's Visual C++ Free Toolkit 2003. Część projektów kompiluje się poprawnie, jednak nie ma możliwości debugowania.
3. **Digital Mars Compiler** - dawniej kompilator firmy Symantec, obecnie projekt freeware. Za jego pomocą możemy kompilować 16 i 32 bitowe programy dla DOS (tak! Digital Mars udostępnia specjalne rozszerzenia pozwalające kompilować 32 bitowe programy dla DOS). Digital Mars występuje w trzech wersjach: kompilującej C, C++ i D. Przyznam szczerze, że nie udało mi się uruchomić przykładowego programu, ale spowodowane było to faktem, że nie poświęciłem DMC zbyt dużo czasu. Cieszy mnie fakt, iż kompilator ten kompiluje D, bo oznacza to, że będzie z czym eksperymentować.
4. **Borland C++ 5.5** - Kompilatora firmy Borland chyba nie muszę przedstawiać. Niestety równierz tak jak w przypadku DMC nie udało mi się popracować z nim za pomocą Code::Blocks. Z tego co słyszałem były pewne problemy podczas kompilacji przykładowych projektów i nie jest możliwe także odpluskwianie.
5. **Open Watcom** - Z wersją BETA6, którą testuję, nie było możliwości uruchomienia tego kompilatora - jest to nowość, którą wprowadziła wersja RC1.

Programista ma do dyspozycji możliwość bezpośredniej kompilacji, bądź za pomocą makefile-ów. Osoby przyzwyczajone do DevCPP i jego predefiniowanych szablonów aplikacji zadowoli identyczna opcja w Code::Blocks. Mamy też możliwość tworzenia własnych szablonów do późniejszego wykorzystania, podobieństwo nie kończy się na szablonach, pomocne okażą się znajome skróty klawiaturowe (F9 - Kompiluj, F8 - Debuguj, Ctrl-F10 - Uruchom itd.). Kolejną ważną zaletą jest możliwość importowania projektów zarówno z DevCPP jak i MSVC++ (tu mała uwaga - na razie nie ma wsparcia dla kodu Asemblera i wewnątrz projektowych zależności w przypadku MSVC++). Podczas importowania projektu istnieje możliwość wyboru kompilatora, którego będziemy używać do kompilacji. Wersja BETA, na razie, podaje wszystkie kompilatory, które może obsługiwać a nie tylko te, które są rzeczywiście dostępne w systemie.

Code::Blocks został napisany z myślą o nieograniczonej możliwości rozszerzania funkcjonalności, za pomocą wtyczek, a samo środowisko, po standardowej instalacji, posiada już kilka przygotowanych wtyczek:


+ Podświetlanie składni - daje ogromną możliwość dostosowania i rozszerzania jej o kolejne słowa kluczowe. Obecnie, wspierane składnie to: C/C++, pliki zasobów Windows, HTML, XML, XSL, skrypty Lua i GameMonkey oraz Hitachi H8 ASM. Sam o mało się nie zgubiłem w mnogości ustawień kolorowania składni. Spodobała mi się opcja ustawienia formatowania programu, czyli jak mają wyglądać: if, while, switch-e. Ja na przykład lubię pisać tak:

```cpp
if (warunek){
 //kod;
} else {
 //kod;
}
```

+ Uzupełnianie tzw.: Code completion, środowisko podpowiada nam metody jakich możemy użyć;
+ Zakładki (tylko w wersji na Linux) lub Okna (w Windows i Linux) - to jest ta mała różnica o której wspomniałem na początku tekstu;
+ Podgląd klas, funkcji, struktur i dyrektyw preprocesora użytych w projekcie;
+ Inteligentne wcięcia - sam spotkałem się z tym dopiero w MSVisual Stduio.Net i uważam taką opcję za bardzo przydatną - pozwala ona "zamykać" kod należący do danej klasy i funkcji, aby nie zajmował miejsca na ekranie;
+ Lista otwartych plików - pozwala na zaawansowaną obsługę zewnętrznych narzędzi;
+ System zarządzania planowaniem rozwoju projektu (to-do list).

Pracuję w Code::Blocks już dwa miesiące i uważam, że był to dobry wybór jeżeli chodzi o środowisko pracy. Projekt stale się rozwija, wiec kolejne poprawki i wtyczki powstają bardzo szybko. Uważam, że program ten ma świetlaną przyszłość i stanie sie popularny wśród programistów. Zachęcam do spróbowania.
