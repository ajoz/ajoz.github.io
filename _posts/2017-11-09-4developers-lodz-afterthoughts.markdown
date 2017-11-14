---
layout: post
date: 09-11-2017
author: Andrzej Jóźwiak
title: 4Developers Łódź afterthoughts
tags: 4developers conference java jvm agile
disqus: true
---

After [Mobilization](https://mobilization.pl) is finally finished and wrapped up I have time for other events. I've started my end of the year conference marathon with [4Developers Łódź](https://lodz.4developers.org.pl/). For anyone unfamiliar with this brand it's `backend` / `fronted` related conference with some `agile` sprinkled on top. Although it's not my daily cup of tea I went there to check what new and fun Java things Android will be missing for years ;-) Jokes aside, I like to listen to some web related stuff to learn, get inspired and stretch my brain a little.

Conference was held inside the campus of Bionanopark on the outskirts of Łódź. This is my city and still I had some issues with finding the place. Building was nice the rooms also but you could feel it was not ment for larger conferences. Enough complaining!

### O annotacjach - czas powstrzymać demony - Jarosław Ratajski

It was the first talk of the conference I picked and I must say that I did it without any hesitation. I know Jarek from his talks that he held on [JUG Łódź](https://www.meetup.com/Java-User-Group-Lodz/) and they were imho one of the best we had in years. That said, I knew I was up for a treat.

Just from the title you can easily guess it was a rant of a sort ;-) A rant indeed but a well deserved one. Jarek was showing common issues with "enterprise" code related to annotations, to name a few:

* `@Autowired` annotation leading to a globale state
* How dependant (pun intended) we became on the dependancy injection frameworks
* How belowed Java `new` keyword became an issue
* How much "magic" is there under the hood of EJB and why you cannot use threads there

It made me think about current Android situation. If you will check any "app skeleton" repository on Github like [android-boilerplate](https://github.com/ribot/android-boilerplate) you will notice that we are reliant on Dagger 2 or ButterKnife. Will this lead to the same issues that our friends from the enterprise world are suffering? Android comparing to Enterprise Java is still relatively young so I guess we are on our way there.

Other issues in our code mentioned by Jarek:
* Any `postInit`, `postInitialize` method as an object oriented "contract" breaker. I seen this often enough in mobile code
* All of those pesky `get` / `set` methods that expose internals of our objects which defeats the purpose of encapsulation

He finished his talk with a nice example of an app stripped from frameworks like Spring. It was a working example written with Ratpack library. It would be nice to achieve this level of freedom in Android.

### So, you wanna upgrade to Java 9 - Tomasz Adamczewski

Altough we had some really nice announcements this year: Kotlin supported by Google, Jack and Jill death and Java 8 support I was hungry for more. I wanted to know what lies ahead and what new and cool issues it will bring ;-)

Tomek was showing his experiences with migrating to Java 9. We first started with issues with modules and a `jdeps` tool which is responsible for showing class dependancies. I'm mentioning this tool because it was the first time I heared about it. The more interesting was example with code that was depending on internal jvm packages. `jdeps` can suggest you changes you should make to remove the internal packages like: `sun.misc.Unsafe`.

It seems that a single underscore `_` is now restricted, all variable names with only a single underscore won't compile - don't worry double underscores `__` still work ;-)

Tomek was kind enough to explain the pipeline his company is using, and this started a lot of discussions with the audience. Questions were flying back and forth, really nice experience. Also good to know my project is not the only one with flaky tests.

I learned about existence of:
* OWASP security testing
* Consumer Driven Contract - Spring Cloud Contract

I need to epsecially check the last one as it seemed interesting.

### Micro-monolith anti-pattern - Tomasz Fijałkowski

I'm hearing about how "microservices" are the savior of enterprise software for quite some time. On most conferences you can only learn about benefits. So are there no disadvantages to using them? It seems there are and [Allegro](https://allegro.pl/praca) have found some ;-)

* microservices are not the cure for every disease your project might be suffering
* debugging anything is complicated if a request goes through multiple services - it's not as simple as reading a stacktrace
* refactoring is complicated if you want to move code between two separate services
* even moving a simple feature might be hard - services can differ in architecture, programming language, database used, overall infrastructure and especially API they are exposing to their clients
* monolith is not evil it has its uses
* monolith can have polyglot persistance

Was very nice to hear how things are done in a large company. What impressed me the most was 100 deployments per day.

### Modern Agile Retrospectives - Piotr Stawirej

After all those technical talks I wanted to chill a bit. Agile track was my choice and it was a good choice I tell you.

Piotr was talking about common Scrum "implementations" in companies and how most often they transform into cargo cults. People doing daily standups, retrospectives without understanding what they are all about.

You want to check if your standup is working? Ask people to say what others are doing? If they cannot, it means that they were not paying attention. You have a daily standup that doesn't bring any value!

Talk was about retrospectives alone, Piotr had a lot of good advices for Scrum masters:
* try to do retrospective in a different place (pub, restaurant, park)
* people should sit in a circle to see each other
* make some agreements: no phones, no laptops, no blaming
* engage people with Questions

Retrospective should be used to gather data and generate insight.

I really liked this talk, a lot of new concepts for me like:
* types of retrospecitve participants: Explorer, Shopper, Vacationer, Prisoner
* Smart Goals after retrospecive: Specific, Measurable, Achievable, Realistic, Timely

### Sagi, strumienie, reaktywność i inne buzzwordy - Jarosław Pałka

This was my last talk for the day and I had a problem who should I choose. On one hand I had this talk on the other *Getting Things Programmed* by Michał Bartyzel. I already read his book and I felt I have strenght for one more technical talk so I've chosen Jarek. I was not disappointed. It was not technical in the strict sense, it was inspirational. A very nice journey from the definition of a problem, through some nasty fails to a good ending. Everything with a lot of humor.

### Final thoughts

Although nothing remotely Android related I still had fun. I hope there will be another edition in Łódź. Also a note to myself I need to attend more Agile related talks like the one given by Piotr Stawirej a perfect mix of entertainment and education.
