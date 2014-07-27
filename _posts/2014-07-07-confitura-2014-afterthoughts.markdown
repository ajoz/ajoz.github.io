---
layout: post
date: 07-07-2014
author: Andrzej Jóźwiak
title: Confitura 2014 afterthoughts
tags: confitura conference java scala
disqus: true
---

Some of you might not know but there are few free software conferences in Poland. By `free` I mean you don't need to pay for a ticket. One of them is organized in Warsaw: [Confitura](http://confitura.pl). Its a `java`/`jvm` related conference that's getting bigger and bigger with every year. And as you might imagine I went there as usual to check what's new in Java world. I also went there with hopes of seeing some presentations about Android / Mobile but unfortunately there were none. I guess enterprise java is the most dominant.

As usual the conference was held inside the campus of Warsaw University. Its 3rd time Confitura is there, big rooms that can accommodate quite a large number of participants + air conditioning which is very crucial in the summer.

### Type Driven Development - Maciek Próchniak

I've started learning Scala few months ago, so I thought its a good idea to go on a Scala related talk. To be really honest with you I was lost around halfway thourgh but I came to a conclusion that there is a hell lot of Scala for me to learn ;-)

The talk was about `Type Driven Development`, this term alone made me wanna go a watch the presentation. So what have I understood from it all?

* static types are great because they help find bugs in the code before execution
* we should create our own types to make the code clearer and help find issues faster
* there is a [Scalaz](https://github.com/scalaz/scalaz) project to add more functional elements to Scala - and when you learn it you can never go back ;-)
* you can use tagged types in Scalaz, this allows to exactly show what type of data we want. In example below we want data that is a double value but represents meters:
```scala
var length : Double @@ Meter
// ....
sealed trait Meter
```
* Scalaz introduces IO monad to help with performing IO operations without side-effects. There was presentation on Scala conference in Paris 2013 that was explaining the concept. You can see slides [here](http://blog.higher-order.com/assets/scalaio.pdf)
* Option is very cool but we can still do something like this:
```scala
Some(null)
```
* there are lists that are heterogeneous - they know types of elements they contain
* if you want to achieve `type driven development` you should use [shapeless](https://github.com/milessabin/shapeless) library for Scala
* most of the libraries are experimental and we use them on our own risk
* most of IDEs lack support

Regarding the speaker, it looked like he knew what he was talking about. Also you could see his passion for Scala / functional solutions. Although I didn't get exactly everything it was still a good choice to hear his talk.

### Android do usług, czyli hybrydowe aplikacje mobilne - Grzegorz Kubiak, Jacek Zadrąg

I think this presentation was the biggest disapointment for me. I hoped for a nice Android developer related talk and I got a more or less an advertisment of a not yet finished product.

* each app has an embeded server
* you write the code in java
* its possible for the user to use the app remotely through a "cloud" interface
* their SDK is available at [myweb.io](http://myweb.io)

I can't say anything bad about the speakers, they were really prepared. They made a live demo of the product and showed a sample project. But still it was an advert.

I almost forgot, they are looking for a seed capital.

### Efektywna Refaktoryzacja - Włodzimierz Krakowski

Refactoring is something really important in our daily work, this is why I always love to hear something new about it. Even if I already know some of discussed aspects I still stay as its good to consolidate your knowledge.

I was really disappointed with this talk. I had an impression that it was speakers first time at such a large audience.

Topic introduction was too long and too boring for my taste. Later speaker just used the first chapter of a great book by Martin Fowler called "Refactoring: Improving the Design of Existing Code" and used its code samples for a live refactoring session. As you can imagine I was bored to death by that moment.

Last part of the presentation took me a little by surprise. We were shown the "seven habits of highly effective people" taken from the book with the same title by Stephen R. Covey. Long story short book is teaching about self-mastery, to be proactive, begin working with end in mind etc. I guess code refactoring may be compared to a self improvement. Still I was hoping for something else from this speach.

### Jak przerobić monolityczną aplikację na architekturę mikroserwisów - Tomasz Lelek

I rarely go on a "enterprise" related talks, but I thought the topic sounds nice and maybe I could use the knowledge there in Android environment.

Speaker promised us a real life example, how he and his company had to change their current architecture to a micro service oriented one. And the example was there at least sort of.

So what is the example you might ask? The example was data processing done at night. Tomasz told us that one of their services was doing one thing through the day and another completely different at night. This was the first sign that a monolitic architecture could and should be changed to a microservice oriented.

So what else did I learn? Did I hear how should a microservice be constructed? Not exactly but I learned a lot of enterprise technology / library names:

* Scalatra - scala's `sinatra` implementation from Ruby
* jetty - embeded container
* sbt - simple build tool for scala projects
* sigma.js - a nice library for graph visualization
* hystrix - latency and fault tolerance library created by Netflix
* etcd - key value store for shared configuration and service discovery
* elasticsearch + logstash for all the logging needs
* kibana - interface for elasticsearch and logstash
* swagger + swagger-ui for method annotation and easier testing

So what is a microservice? Microservice is a service composed of three elements: client, model and service logic.

* Client is a facade, other microservices if they want use each other, they need to do it through the client interface.
* Model defines means of comunication between client and service logic

And this is it. I would love to hear more about the architecture that is behind the idea of microservices in the future.

### Sztuka uczenia się na błędach innych - Jakub Kubryński

This was a really really really -- did I remember to say really? -- nice talk. Jakub showed all the problems that occur in projects:

* **silver bullet** - one technology for everything, this reminded me about one company that used stored procedures in the database for everything and by everything i really mean everything
* **proof of concept** - there is a major flaw in this technique as we automatically look for confirmations of our ideas and not look for their problems
* there are **two types of complexity**: essential and accidental. Essential comes with the problem we try to find solution for, accidental comes with the technologies we use, mistakes we make. People often want to discuss the accidental complexity rather then essential
* **programming motherfucker** - just write code, don't mind any methodology
* **reinventing the wheel** - we love to do it in every project
* **death march** and its new version **agile death sprint**
* we deliver **functionality that is easy to implement** and not functionality that is desired by our client
* **copy paste** errors
* **GOD object** - we like to call it `Lord of the rings pattern` - one class to rule them, one class to find them and in the project bind them ;-)
* **soft code** - business logic is somewhere else then in the code
* **lava flow** - when bad code spews forth and becomes permanent basically code is like a congealed lava you can't do anything about it
* **spaghetti code** - this really surprised me, it seems that kind of code has two kinds: "spaghetti with meatballs" - code were object oriented and procedural programming is mixed; "ravioli" - where the single responsibility is taken to extreme and we have hundreds of classes, this makes navigating through the code hard
* **big ball of mud** - we like to call it a bit different: big ball of sh*t and programmer is the beetle who is constantly struggling to roll his ball and keep it intact
* **shotgun surgery** - we have to do this when we have a small thing to add but a large amount to change

So what is the main cause of this? Probably projects are estimated to optimistically.

Overall a very nice talk, each example with a joke. It was  time well spent.

### Nie koduj, pisz prozę - lingwistyczne techniki wychodzące daleko poza - Sławomir Sobótka

Last presentation of the day, I would say that they left the best for last. Sławek gave a very good speech as usual. It was a mix of programming techniques, applied psychology and good design. Ofc if you heard one of Sławek's talks about `Domain Driven Design` this talk wouldn't be revealing to you, but still it was a very good idea to go and listen how we should design our code.

### Final thoughts

We left home around 6 in the morning to get to Warsaw and it was well past 22 when I got home yet it was time well spent.

Some good talks, some bad. I had a chance to talk with people I don't know about their view on the industry.

It's good to sometimes leave your dark room full of screens and talk with real people ;-)

Ohh and I'm waiting now for next years Confitura.
