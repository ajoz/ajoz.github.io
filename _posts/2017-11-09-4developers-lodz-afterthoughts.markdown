---
layout: post
date: 09-11-2017
author: Andrzej Jóźwiak
title: 4Developers Łódź afterthoughts
tags: 4developers conference
disqus: true
---

[Mobilization](https://mobilization.pl) is finally finished and wrapped up so I have time for other events. I've started my end of the year conference marathon with [4Developers Łódź](https://lodz.4developers.org.pl/). For anyone unfamiliar with this brand it's `backend` / `fronted` related conference with some `agile` sprinkled on top. Although it's not my daily cup of tea I went there to check what new and fun Java things Android will be missing for years ;-) Jokes aside, I like to listen to some web related stuff to learn, get inspired and stretch my brain a little.

The conference was held inside the campus of Bionanopark on the outskirts of Łódź. This is my city and still, I had some issues with finding the place. The building was nice the rooms also but you could feel it was not meant for larger conferences. Enough complaining!

### O annotacjach - czas powstrzymać demony - Jarosław Ratajski

It was the first talk I picked and I must say that I did it without any hesitation. I know Jarek from his talks that he held on [JUG Łódź](https://www.meetup.com/Java-User-Group-Lodz/) and they were imho one of the best we had in years. That said, I knew I was up for a treat.

Just from the title, you can easily guess it was a rant of a sort ;-) A rant indeed but a well-deserved one. Jarek was showing common issues with "enterprise" code related to annotations, to name a few:

* `@Autowired` annotation leading to a global state
* How dependent (pun intended) we became on the dependency injection frameworks. Jarek mentioned an article by Oliver Gierke ["Why field injection is evil?"](http://olivergierke.de/2013/11/why-field-injection-is-evil/) - definitely a valuable read!
* How beloved Java `new` keyword became an issue
* How much "magic" is there under the hood of EJB and why you cannot use threads there

It made me think about the current Android situation. If you will check any "app skeleton" repository on Github like [android-boilerplate](https://github.com/ribot/android-boilerplate) you will notice that we are reliant on Dagger 2 or ButterKnife. Will this lead to the same issues that our friends from the enterprise world are suffering? Android comparing to Enterprise Java is still relatively young so I guess we are on our way there.

Other issues in our code mentioned by Jarek:
* Any `postInit`, `postInitialize` method as an object-oriented "contract" breaker. I saw this often enough in mobile code
* All of those pesky `get/set` methods that expose internals of our objects which defeats the purpose of encapsulation

He finished his talk with a nice example of an app stripped from frameworks like Spring. It was a working example written with Ratpack library. It would be nice to achieve this level of freedom in Android.

Other reading/watching materials suggested by Jarek:
1. Uncle Bob - [Dependency Injection Inversion](https://sites.google.com/site/unclebobconsultingllc/blogs-by-robert-martin/dependency-injection-inversion)
2. Mario Fusco - [From GoF to Lambda](https://www.youtube.com/watch?v=FnWntVfEEQg)
3. Jarek Ratajski - [WEB Pong written in Plain Java](https://github.com/javaFunAgain/ratpong)

### So, you wanna upgrade to Java 9 - Tomasz Adamczewski

Although we had some really nice announcements this year: Kotlin supported by Google, Jack and Jill death and Java 8 support I was hungry for more. I wanted to know what lies ahead and what new and cool issues it will bring ;-)

Tomek was showing his experiences with migrating to Java 9. We first started with issues with modules and a `jdeps` tool which is responsible for showing class dependencies. I'm mentioning this tool because it was the first time I heard about it. The more interesting was example with code that was depending on internal JVM packages. `jdeps` can suggest you changes you should make to remove the internal packages like `sun.misc.Unsafe`.

It seems that a single underscore `_` is now restricted, all variable names with only a single underscore won't compile - don't worry double underscores `__` still work ;-)

Tomek was kind enough to explain the pipeline his company is using, and this started a lot of discussions with the audience. Questions were flying back and forth, really nice experience. Also good to know that my project is not the only one with flaky tests.

I learned about existence of:
* OWASP security testing
* Consumer-Driven Contract - Spring Cloud Contract

I need to especially check the last one as it seemed very interesting. Can it be used in Android development?

### Micro-monolith anti-pattern - Tomasz Fijałkowski

I'm hearing about how "microservices" are the saviour of enterprise software for quite some time. On most conferences, you can only learn about benefits.

So are there no disadvantages to using them? It seems there are and [Allegro](https://allegro.pl/praca) have found some ;-)

* microservices are not the cure for every disease your project might be suffering
* debugging anything is complicated if a request goes through multiple services - it's not as simple as reading a stacktrace
* refactoring is complicated if you want to move code between two separate services
* even moving a simple feature might be hard - services can differ in architecture, programming language, database used, overall infrastructure and especially API they are exposing to their clients
* monolith is not evil it has its uses
* monolith can have polyglot persistence
* each microservice has a test pyramid

Was very nice to hear how things are done in a large company. What impressed me the most was 100 deployments per day. Tomek also mentioned that a lot of the teams are working on the infrastructure instead of business logic.

### Modern Agile Retrospectives - Piotr Stawirej

After all those technical talks I wanted to chill a bit. The Agile track was my choice and it was a good choice I tell you. Later on, I learned this presentation was one of the three best lectures of the conference so I was really lucky to attend it.

Piotr was talking about common Scrum "implementations" in companies and how most often they transform into cargo cults. People doing daily standups, retrospectives without understanding what they are all about.

Do you want to check if your standup is working? Ask people to say what others are doing? If they cannot, it means that they were not paying attention. You have a daily standup that doesn't bring any value!

The talk was about retrospectives alone, Piotr had a lot of good advice for Scrum masters:
* try to do retrospective in a different place (pub, restaurant, park)
* people should sit in a circle to see each other
* make some agreements: no phones, no laptops, no blaming
* engage people with Questions

The retrospective should be used to gather data and generate insight.

I really liked this talk, a lot of new concepts for me like:
* there are different types of retrospective participants: **Explorer**, **Shopper**, **Vacationer**, **Prisoner** - scrum master should identify them and act accordingly
* Smart Goals after retrospective: **Specific**, **Measurable**, **Achievable**, **Realistic**, **Timely** - should define what the team wants to achieve, depending on the team size there shouldn't be more than 5 (someone in the audience said that not more than even 3)
* retrospective closing activities - if we have opening activities why not closing activities?
* use system of `plus/delta` to gather feedback from the part. ipants, `delta` instead of `minuses` to force people to explain what they don't like and what whould they chan.

I learned that the basic outline of a retrospective:
1. **Setting the stage** - it.  the time to welcome everyone, reiterate team goals, review all the working agreements.
2. **Gather data** - on a tim. ine of previous release / sprint a satisfaction line is drawn for each team member. The satisfaction line goes up or down due to "memorable, person. ly meaningful, significant events". It also worth to group people who work closely together to check how the satisfaction of those people compare.
3. **Generate insight** - this is the moment to discuss the satisfaction line.
4. **Decide What to Do** - team should define SMART Goals.
5. **Close** - `plus/delta` after retrospective.

### Sagi, strumienie, reaktywność i inne buzzwordy - Jarosław Pałka

This was my last talk of the day and I had a problem who should I choose. On one hand, I had this talk on the other *Getting Things Programmed* by Michał Bartyzel. I already read his book and I felt I have the strength for one more technical talk so I've chosen Jarek. I was not disappointed. It was not technical in the strict sense, it was inspirational. A very nice journey from the definition of a problem, through some nasty fails to a good ending. Everything with a lot of humour.

### Final thoughts

Although nothing remotely Android related I still had fun. I hope there will be another edition in Łódź. Also, a note to myself I need to attend more "Agile" related talks like the one given by Piotr Stawirej a perfect mix of entertainment and education. See you all on the next 4Developers Łódź!
