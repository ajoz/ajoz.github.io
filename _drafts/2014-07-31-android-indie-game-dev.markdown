---
layout: post
date: 31-07-2014
author: Andrzej Jóźwiak
title: Android Indie Game Development
tags: android gamedev games
disqus: true
---

So you want to create games, games for Android in fact. The reason behind your idea is irrelevant, maybe you like existing games, you have an incredible idea you need to show to the world or you just want to try yourself. You need a pinch of creativity and a bit of zeal and luck. If working for a mega corporation is not for you and you want to have influence on the end product, then maybe Indie game dev is for you. Let's explore!

What hides the name "indie"? The term comes from the word "independent", indie games are mostly written by single people or small teams without any financial support from a publisher. Such independence allows more creativity, as a designer you can commit to greater risks and there is no board of directors who need to accept every change.

Due to limited resources such games often emphasize innovative game mechanics, unusual story or different forms of presentation. Even though the founding is limited it doesn't mean that indie games are not successful. Recent years gave us such hits like: Minecraft, World of Goo, Braid. They earned a lot thanks to services like: Steam or Google Play. I can safely say that the evolution of online game stores allowed indie game dev to rise.

I think that Minecraft is the best example of an indie game success, there are PC, XBox, Android versions, even for Raspberry Pi. When I last read about this game it had almost 10 milion copies sold.

I want to gather some tips how to create a game and not get insane in the process especially if we are working on it in our spare / free time after our day job or on the weekends. From my own experience I know it takes a lot more work then we originally planned / estimated but the results are really satisfying.

## Monetization - how to sell our game?

Most designers will tell you that only an interesting idea counts, but not all ideas will fit a mobile device. You have many obstacles: small screen, not physical keyboard, issues with reading accuracy of the user input also sometimes issues with permanent connection to the Internet. Not all created games will be a success on mobile, good example are First Person Shooters. They need complicated control systems: camera, movement, shooting, jumping, browsing equipment which can be at best wonky on a touch device. Its quite frustrating when you obscure the view you need with your own fingers when your trying to interact with the game. Such games often have on-screen controls but still they are not as responsive as a normal controller would be. You could always assume that players will use bluetooth controllers with your game, this however will limit the market your game can be sold at. We are facing now the first problem: how to design our game that it will sell? I want to note here that you do not have limit yourself with the ways of monetization but its good to know how they work and how they affect gameplay.

Monetization is a term that is spreading with a speed of a lightning. It describes ways of earning profit on a given product (it doesn't necessary need to be a game). On the Android platform, there are available:

1. *sell* - for example on Google Play, Amazon Appstore or GetJar. Depending on the contract with the shop you will get percentage from the sold game. They are giving you the platform, this is why you give them some compensation.
2. *ads* - in different forms: as banners inside the app, as notification bar adds, as popup windows. There are many solutions for example Google AdMob or MobClix. They will pay you either for each displayed advert or for each clicked.
3. *iap* - in app purchase - its a good way to sell all available additional content you have for your game: maps, skins, models. On Android we have different solutions: Google In-App-Purchase. Recently Amazon released their own API for integrated purchase.
4. *virtual currency* - its a modified IAP solution, instead of selling particular map or skin, developer is selling in game currency, which can be used to get additional content inside the game. Most often players have possibility to acquire the currency just by playing the game, but it takes more time.
5. *mixed* - its a combination of all the above solutions. For example Tapjoy is a solution that is using a virtual currency, players can get it by clicking displayed ads or by installing other apps in the Tapjoy network. Another very interesting solution is AddOverlay from LeadBolt which combines unlocking content with clicking ads. Player needs to click an advert to unlock content.

There are many places you can sell your game in (there are around 30 different Android app markets):

1. *Google Play* - https://play.google.com/ - Its the default app market for the majority of devices. Many people are used to browse the contents of this market in search of something new and exciting. If you do not have any previous experience with selling apps on Android, it is advised to start with this one first.
2. *Amazon Appstore* - http://www.amazon.com/ - Well known alternative for Google Play, by default install on Amazon Kindle Fire. Unfortunately its availability is limited to USA. Each day one paid app is free of charge for one day.
3. *GetJar* - http://getjar.com/ - Its marketed as a second largest app market in the world. It might be true due to the fact its available also on platforms other then Android. Most commonly installed on devices which do not have access to official Google Play market. Angry Birds from Rovio was first on GetJar before it came to Google Play.
4. *SlideME* - http://slideme.org/ - Its marketed as a "better" alternative to Google Play, it takes a lower share for selling the app. It allows to get the app through a web browser or an app on the device.

We now know how and where to sell, but which model should be picked? If we already have finished our game and we can count on a good advertisement then maybe we should try with a free demo and paid full model. Free version doesn't have to be limited in any way, it can just use worse resources (often HD versions of the game are paid). Demo version is sometimes the only way to go for our game, banners with ads might often not fit into our game UI or we do not have enough game items to offer for IAP. Some developers often release their game with price higher then the one they want just to have option to create deals where they cut the price. Sneaky!!

Adds force us inherently to make room for them in our UI. Either we will squeeze some space or we will allow ad banners to overlap our interface. User might click an ad by mistake in such case -- more income for us? or maybe not if users will get angry and comment. If we are doing this on purpose we might end will lower download rate.

Also don't overdo with the amount of banners, game can have a fragile artistic vision which can be easily broken with colorful ads not matching our style. How would you like to look at bright colorful images on a banner in a dark noir style game?

So, once you know where to place an ad then how can you earn on them? There are some acronyms that you will often read: CPC and CPM.

1. CPC - Cost Per Click - in most cases we will only get paid if someone clicks the ad / banner
2. CPM - Cost Per Mille (Cost per thousand views) - the letter `M` is from roman numer meaning a thousand

Google AdMob is based on the CPC although determining the amount of money for one click may be difficult, it depends on app type, target audience etc. To better know what kind of money would such solution generate, you can simply integrate it into your game and just check after few weeks.

If we do not want to pollute our artistic vision with ads we have other means of monetization, for example push notifications can be used for advertisement. They seem perfect as we do not have to mess our UI to use them, unfortunately they have a really bad opinion amongst gamers. They were shown too often and in random moments. I think that the first company which introduced this type of monetization was `AirPush`. They say they have 800k active users but this kind of ads are a bit confusing. Users often don't know from where the ad is coming and treat is as a sign of malware (its marked as a second biggest problem for user right after malware). A word of warning, app called `APNDriod` was removed from Google Play after a lot of user reports, the main reason was excessive amount of notifications. True or not, you need to remember that with Android 4.2 users can now block notifications from a given app!

Whichever type of ads you pick remember to have it from day one, you don't want to surprise your users later on in your app life cycle. Adding monetization after some time might cause a loss of players. If not ads than what?

Maybe you can give your game for free and earn money through selling maps, in game items, in game currency etc. You can find many articles about this form of monetization: microtransactions, free-to-play, freemium. Of course there are some issues with selling in game items. A new term was created among gamers which is "pay-to-win". This term describes all games that have microtransactions allowing achieving easier victory, its especially frustrating if you can buy something in multiplayer competitive games. For example World of Tanks allows buying golden bullets that deal more damage to players and still this game is very successful.

Selling cosmetic items and convenience is the most accepted form of monetization. What is convenience you ask? In most cases its time after which a player gains access to a certain thing in the game. Let's say we created a multiplayer game with multiple different weapons, you have two ways of gaining access to said weapons: buy them in in-game store, unlock them through playing the game (this takes time). Your not restricting your players access to these weapons you just give them choice, either they will pay a small one time fee to get it or they will just wait until the weapon naturally unlocks for them. Clear and simple. You need to balance the cost of the weapon both money wise and time wise, people don't like to feel obligated to do something.

What do I need non paying players for? Its simple, the more players there are, the more popular (alive) your game will be. Healthy and lively community will reassure your players and they will be more willing to spend money on your game.

For microtransactions your game needs to be designed from the ground up: ui, shop, what will be sold, gameplay in context of buying items etc. You also need to be ready for complaints about payment, credit card transaction withdrawal and so on. Its better to be prepared beforehand.

## Maintaining interest

So how to keep your userbase stable / growing? Fundamentally the main way is to update the content, however, nothing keeps interest like player competition or increasing game difficulty. Competitive doesn't necessarily means mulitplayer -- scoreboards or achievement boards allow users to compete with one another. You have broad design space for this: most kills, fasts level finish, lowest amount of deaths in a level, as I said possibilities are endless.

Recently "star rating" became very popular, it allows to inform the player about "how good" he finished a game level with, the more stars you get the better you finished. Sometimes the star system is combined with content unlocking.




System osiągnięć jest znacznie bardziej skomplikowanym mechanizmem niż to, co znamy pod postacią prostych gwiazdek. Doskonale znane z gier Masive Multiplayer Online (typ gry rozgrywanej przez wielu graczy jednocześnie) osiągnięcia to skomplikowana forma wyzwań dla gracza. Zaczynają się od prostych działań, takich jak zdobycie określonego poziomu w grze czy pokonanie pewnej liczby przeciwników. Kolejnym etapem są  akcje trudne i pracochłonne, jak zdobycie określonego przedmiotu, - często przy pomocy innych graczy. Naturalnym efektem wprowadzenia osiągnięć będzie rywalizacja graczy między sobą. Umożliwić to może wiele istniejących rozwiązań takich jak Swarm czy HeyZap. Łatwo się domyślić, że powracający zmotywowani gracze będą częściej oglądali wyświetlane przez nas banery lub kupowali w naszym wbudowanym sklepie, co może zwiększyć założony przychód.

Swarm
http://swarmconnect.com/
Rankingi wyników
Tabele osiągnięć
In-App-Purchase
Social Gaming
Przechowywanie danych w chmurze
Analiza danych
Wszystkie usługi są całkowicie darmowe, nie ma miesięcznych opłat ani żadnej innej formy subskrypcji. W przypadku wykorzystania wbudowanego IAP, Swarm pobiera prowizje od każdego zakupu.
Scoreloop
http://www.scoreloop.com/
Rankingi wyników
Tabele osiągnięć
In-App-Purchase
Social Gaming
Wyzwania
Wyniki w chmurze
Notyfikacje Push
Wirtualna waluta
Cross promotion
Analiza danych
Wszystkie usługi są całkowicie darmowe, nie ma miesięcznych opłat ani żadnej innej formy subskrypcji. W przypadku wykorzystania IAP, kupowania wirtualnej waluty, Scoreloop pobiera prowizje od każdego zakupu.
Scoreninja
http://scoreninja.appspot.com/
Rankingi wyników
Brak opłat, brak gwarancji stabilności i działania usługi.
Scoreoid
http://www.scoreoid.net/
Rankingi wyników
Tabele osiągnięć
Zarządzanie aktywnymi graczami
System notyfikacji
Geolokacja
Wyświetlanie treści w zależności od platformy
Analiza danych
Aktualnie usługa jest w fazie beta i jest darmowa. Na usługę nałożone są pewne ograniczenia jak np.: quota na składowane dane, po przekroczeniu której należy skontaktować się ze wsparciem technicznym Scoreoid. W przyszłości przewidzianych jest kilka planów płatności.
HeyZap
http://developers.heyzap.com/
Rankingi wyników
Wyzwania
Tabele osiągnięć
Integracja z portalami społecznościowymi
Reklamy
SDK oraz wszystkie usługi są darmowe. HeyZap zarabia na reklamach i wypłaca prowizje za wyświetlone reklamy.
Geosophic
http://www.geosophic.com
Rankingi wyników powiązane z geolokacją.
Darmowa do 20 tysięcy zapytań dziennie. W przypadku przekroczenia limitu niezbędny jest indywidualny kontakt z Geosophic.
Skiller
http://www.skiller-games.com/
Wsparcie dla multiplayer
System turniejowy
Wyzwania
Wyniki w chmurze
Rankingi wyników
Tabele osiągnięć (osobne dla każdej z gier oraz dla współzawodnictwa z przyjaciółmi)
Listy znajomych
In-App-Purchase
Wirtualna waluta
Reklamy
SDK oraz wszystkie usługi są darmowe. Bardzo trudno odnaleźć informacje o ewentualnych prowizjach w przypadku reklam, IAP oraz wirtualnej waluty.
Tabela: Jeżeli chcemy wzbogacić naszą grę w tabele wyników, rankingi lub system osiągnięć, nie musimy pisać ich od zera.

Jak zbierać dane, by ulepszyć grę?

Same statystyki pobrań nie stanowią wystarczającej informacji o tym, czy nasza gra podoba się użytkownikom. Informacje o tym, jak długo przeciętny gracz bawi się naszym dziełem, jak daleko udaje mu się przejść, jakie ekrany odwiedza najczęściej, których elementów interfejsu używa, to tylko przykład danych, dzięki którym możemy analizować zachowania użytkowników. Ma to kolosalne znaczenie, jeżeli korzystamy z jednego z przedstawionych wcześniej sposobów monetyzacji. Analiza korzystania z naszej aplikacji pozwala odpowiednio rozlokować banery reklamowe i umiejscowić odniesienia do wbudowanego sklepu. Możliwe, że ekran, który wyświetla reklamy jest w ogóle przez graczy nieoglądany! Obecnie na rynku jest wiele rozwiązań pozwalających na zbieranie anonimowych danych o użytkowaniu. Najbardziej popularny, Google Analytics, znany przede wszystkim z możliwości analizowania ruchu na stronach www, zagościł też na platformie mobilnej Android. Konkurencję dla niego stanowią moduły odpowiedzialne za zbieranie danych wbudowane we frameworki stosowane do implementacji rankingów. Przykładem może być Swarm lub Scoreloop. Bardzo popularny ostatnio stał się Flurry, stosowany w takich grach jak Angry Birds. Duże firmy jak EA, SEGA czy Zynga także stosują to rozwiązanie.


Rys. W grze Splendor of Giza wykorzystanie Flurry pozwala stwierdzić, jak użytkownicy nawigują w aplikacji i które ekrany otwierają najczęściej.


Rys. Flurry pozwala na rozbudowaną kontrolę wykorzystania naszej aplikacji. Informuje o częstotliwości wykorzystania, długości sesji oraz ilości nowych i powracających użytkowników. Wszystko prezentowane jest za pomocą interfejsu webowego.

Inspiracja i jej prawne aspekty

Co robić, kiedy brakuje pomysłów na tematykę gry lub jej mechanikę? Z pomocą przychodzi Internet, gdzie można znaleźć strony skupiające twórców wymieniających się pomysłami. Wiele rzeczy, które nam wydawały się genialne, może faktycznie spotkać się ze sporą krytyką. Istnieją też strony zawierające całe listy wymyślonych już mechanik i prototypów gier, które możemy wykorzystać jak np. Three Hundred Mechanics.

http://www.gamedesignideas.com/
Ciekawie prowadzony blog. Autorzy omawiają w nim różne aspekty projektowania gier. Omawiane są pomysły na scenariusze, poziomy oraz mechaniki. Artykuły zawierają opisy i oceny istniejących już gier i zawartych w nich pomysłów.
http://www.squidi.net/three/index.php
Próba udokumentowania 300 pomysłów na różne mechaniki dla gier komputerowych. Według pierwotnego planu, na stronie miał być codziennie przedstawiany nowy pomysł przez 300 dni. Obecnie prace kontynuowane są w znacznie wolniejszym tempie. Zbiór jest bardzo ciekawy, albowiem do niektórych pomysłów stworzone zostały proste prototypy za pomocą HTML oraz javascript.
http://gmc.yoyogames.com/
Forum dyskusyjne społeczności skupionej wokół narzędzia Game Maker: Studio firmy yoyogames. Na forum dyskutuje wielu pasjonatów, którzy chętnie wymieniają się pomysłami i opiniami. Można uzyskać porady dotyczace naszych własnych pomysłów, jak i przyjrzeć się temu, co planują inni twórcy.
http://www.streamingcolour.com/blog/game-idea-generator/
Prosty generator pomysłów, propozycje tworzone są z trzech zbiorów kategorii. Niektóre dość niedorzeczne np. historical, sad, dark, FPS combined with puzzle game, set in a factory.
http://orteil.dashnet.org/gamegen
Generator pomysłów pozwalający na tworzenie wielu propozycji i przechowywanie ich w schowku. Posiada dodatkową opcję eliminującą kompletnie niedorzeczne sugestie.


Tabela: W przypadku braku pomysłów, można skorzystać z pomocy w Internecie. Fora dyskusyjne, grupy, tematyczne blogi pomogą nam przełamać blokadę twórczą.

Propozycje tworzone przez generatory pomysłów mogą wydać się niedorzeczne, często okazują się jednak być dobrym początkiem. Dla przykładu, generator wylosował dla mnie: szczęśliwa krwawa gra dla dzieci dziejąca się na innej planecie. Na pierwszy rzut oka nic nie da się z tym zrobić, ale propozycje można podrasować, np.: Baba Jaga wysyła dzieci na inną planetę, gdzie chce je zjeść, dzieci muszą uciekać z jej piernikowej twierdzy i bronić się przed potworkami ze słodyczy. Jak widać potrzebna jest odrobina kreatywności.

Czy są jakieś prawne konsekwencje wzorowania się na innych grach? Na gruncie prawa polskiego reguł gier nie można zastrzec, opatentować ani objąć żadną inna formą ochrony prawnej. Ustawa o prawie autorskim i prawach pokrewnych z 1994 r. zawiera następującą definicję utworu: każdy przejaw działalności twórczej o indywidualnym charakterze, ustalony w jakiejkolwiek postaci, niezależnie od wartości, przeznaczenia i sposobu wyrażenia. Ochronie podlega więc jedynie konkretna realizacja danego pomysłu, będąca utworem: specyficzne elementy interfejsu, konkretne grafiki. Mechanika czy zwyczajnie mówiąc zasady, nie mogą być objęte ochroną. Czy to oznacza, że można tworzyć wierne kopie dowolnych gier? I tak, i nie, wszystko jest bardzo rozmyte. Wiele elementów gier, które możemy próbować kopiować, może być zarejestrowane jako znak handlowy (znakiem handlowym może być np. schemat kolorów). Próba kopiowania może zakończyć się w sądzie. Dopóty, dopóki nie będziemy tworzyć wiernych kopii oryginalnych gier, a jedynie czerpać z rozwiązań stosowanych w ich mechanikach, nie musimy się obawiać, że ktoś wytoczy nam sprawę. Warto jednak trzymać rękę na pulsie i co jakiś czas sprawdzać, czy nic nie zmieniło się w tej materii.

Czy można stworzyć i wydać grę za darmo?

Mimo, że większość technologii jest darmowa (jak usługi typu Swarm czy Flurry) stworzenie gry bez zaangażowania środków nie jest niestety możliwe, nawet na tak otwartej platformie jaką jest Android. Do dyspozycji mamy darmowe silniki przeznaczone tylko na platformę Android jak AndEngine, lub wieloplatformowe rozwiązania jak Unity. Jeżeli planujemy zdobyć inne rynki mobilne, na przykład iOS lub Windows Phone, musimy się przygotować na to, że rozwiązania wieloplatformowe są zazwyczaj płatne. Przykładem może być Corona SDK pozwalająca na tworzenie gier w języku Lua i łatwe przenoszenie ich na inne środowiska niż Android.

AndEngine
http://www.andengine.org/
Java
Darmowy
Wraz z silnikiem dostarczany jest rozbudowany zestaw przykładów. Niestety nie posiadają one komentarzy z wyjaśnieniem dlaczego coś jest zrobione tak a nie inaczej. Mimo tej wady, budowa silnika jest względnie prosta, co pozwala na dość szybkie budowanie prototypów. Zaletą jest też duże forum dyskusyjne skupiające społeczność twórców korzystających z AndEngine. Wadą jest istnienie dwóch wersji silnika: GLES1 oraz GLES2 i jednocześnie brak lub szczątkowa ilość dokumentacji na temat różnic między wersjami. Duża część przykładów na różnych blogach i forach traktuje o wersji GLES1. Silnik może być dość wolny w przypadku niektórych zastosowań. Głównie przeznaczony do tworzenia gier dwu wymiarowych.
Unity
http://unity3d.com/unity/
C#
Unity Script
Boo
Płatny
Rozbudowany silnik 3D, pozwalający tworzyć gry na dowolną platformę: zarówno mobilną jak i pc lub konsole. Dla twórców gier dostępny jest sklep z zasobami: grafika, dźwięk, modele 3D oraz narzędzia służące do edycji scen oraz animacji. Wysoką barierą wejścia jest konieczność poświęcenia pewnego czasu na nauczenie się sposobu działania silnika. Sam koszt narzędzia też nie jest niski.
Game Maker: Studio
http://www.yoyogames.com/make
GML
Płatny
Twórca dostaje do dyspozycji rozbudowane narzędzie z graficznym interfejsem użytkownika pozwalające budować gry nawet bez znajomości jakiegokolwiek języka programowania. Bardzo szybkie prototypowanie prostych gier pozwala przekonać się, czy tworzona przez nas mechanika jest grywalna. Zasadniczą wadą jest trudność w implementacji złożonej logiki, głównie z powodu ograniczeń języka GML stworzonego na potrzeby Game Maker: Studio.
Corona SDK
http://www.coronalabs.com
Lua
Płatny
Bardzo popularny framework pozwalający na tworzenie gier za pomocą języka Lua. Dzięki zastosowaniu języka skryptowego SDK jest łatwy w opanowaniu oraz znacznie przyspiesza tworzenie aplikacji. Prototypowanie i testowanie różnych mechanik jest dzięki temu szybkie i prawie bezbolesne. Jednakże nauka nowego języka programowania może być uważana za wadę. Mimo, że istnieje wersja darmowa, nie można publikować aplikacji przy jej użyciu. Bardzo bogate API pozwala na obsługę wielu funkcji urządzenia jak akcelerometr lub gps, jednakże Corona SDK to produkt o zamkniętych źródłach i jeżeli coś nie działa lub nie jest obsługiwane, nie przeskoczymy łatwo tego problemu.
Gideros
http://www.giderosmobile.com/
Lua
Darmowy
Kolejne rozwiązanie bazujące na języku Lua. SDK posiada własne IDE ułatwiające pracę z frameworkiem, testowanie, podpowiadanie składni i łatwy podgląd API pozwalające na przyspieszenie pracy. W przeciwieństwie do Corona SDK, Gideros ma darmową opcję w swojej ofercie. Jedynym warunkiem korzystania z darmowej wersji jest wyświetlenie ekranu z logo firmy przed załadowaniem gry. Jeżeli sprzedaż gry przyniesie zysk większy niż 100 tysięcy dolarów, należy wykupić licencję Pro.
Moai
http://getmoai.com/
Lua
Darmowy
Projekt open source. Korzystanie z niego jest w pełni darmowe, jednakże pełen komfort pracy uzyskamy dopiero po kupieniu dedykowanego IDE, które kosztuje około 40 dolarów. Framework posiada bogate API, niestety w wielu miejscach bardzo słabo udokumentowane lub z nieaktualną dokumentacją. Wiele aspektów ukrytych w innych frameworkach w wypadku Moai zależy od programisty, co oznacza dłuższy czas nauki szczegółów dotyczących silnika i budowy gry.
libGDX
http://libgdx.badlogicgames.com/
Java
Darmowy
Jeden z najszybszych frameworków do pisania gier w Javie. Jest całkowicie darmowy, jednakże wsparcie można uzyskać tylko poprzez oficjalne forum oraz wiki. Głównie służy do realizowania gier 2D, których lista jest już całkiem pokaźna. Wspierane jest kilka platform w tym HTML5. Ze względu na budowę frameworka, możliwe jest tworzenie gry na Androida i testowanie jej bez urządzenia lub emulatora, gra uruchamiana jest wtedy jako aplikacja desktopowa Java.
Tabela: Na rynku istnieje obecnie kilkanaście frameworków przeznaczonych do tworzenia gier, zarówno 2D, jak i 3D. W zależności od kosztów, jakie jesteśmy gotowi ponieść, dostępnych jest dla nas wiele rozwiązań.

Kolejnym aspektem jest tworzenie grafiki oraz dźwięku. Dla wielu płatnych narzędzi, jak znany wszystkim Photoshop, istnieją darmowe odpowiedniki. Jednakże jeżeli mamy zatrudnić profesjonalnego grafika wiąże się to z dodatkowymi kosztami. Pozostają też aspekty testowania tworzonych gier i wejścia na market. Teoretycznie grę możemy stworzyć od początku do końca bez jakiegokolwiek urządzenia, jednakże szybko okazuje się, że w praktyce jest to niemożliwe albo bardzo trudne. Emulator dostarczany z system Android może nie pozwolić na uruchomienie gier wykorzystujących OpenGL, albo praca z nim stanie się zbyt uciążliwa ze względu na powolne działanie. Jako początkujący twórcy musimy więc mieć do dyspozycji przynajmniej jedno urządzenie, które pozwoli nam na wygodną pracę i testowanie, co wiąże się z kosztem co najmniej kilkuset złotych. W przypadku Google Play musimy uiścić także jednorazową opłatę w wysokości 25 dolarów w czasie zakładania konta za pomocą którego wystawiać będziemy nasze gry. Większość twórców czeka z tym do momentu ukończenia swojego pierwszego dzieła. Google tłumaczy opłatę jako sposób na zwiększenie jakości udostępnianych aplikacji. GetJar stanowiący bezpośrednią konkurencję dla Google Play nie posiada opłat wejściowych co międzyinnymi stanowi o jego sukcesie. Najważniejsze, o czym nie wspomniałem dotąd, to czas. Zanim niezależny twórca osiągnie taki sukces jak autor Minecraft’a, musi zazwyczaj pracować, aby zarobić na utrzymanie. Oznacza to, że większej części swojego czasu nie poświęca na tworzenie, a pisanie gry po godzinach wymaga od niego wytrwałości i cierpliwości, by doprowadzić projekt do końca.

Co dalej?

Tworzenie gier, nawet Indie, nie jest proste i szybkie. Zanim nasza gra pojawi się na markecie może minąć znacznie więcej czasu niż przewidywaliśmy, szczególnie, że wiele wykorzystywanych przez nas technologii wymaga nauki. Na sam koniec zachęcam do poczytania na temat samego projektowania gier. Dobrą początkową lekturą może być książka “Rules of Play - Game Design Fundamentals” autorstwa Katie Salen i Erric Zimmermana.
