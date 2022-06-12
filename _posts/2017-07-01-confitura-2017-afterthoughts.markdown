---
layout: post
date: 01-07-2017
author: Andrzej Jóźwiak
title: Confitura 2017 afterthoughts
tags: confitura conference
disqus: true
---

This years [Confitura](https://2017.confitura.pl/schedule) was a tough nut to crack, there was a lot of talks I wanted to attend and I really had to choose carefully. The worst thing is that the conference returned to it's old location which ment no air conditioning and going a lot down and forth the stairs other then that everything was fine. Now without further ado:

### Why TDD is slowing you down? - Adam Pierzchała, Wojciech Przechodzeń

First session and first dillema of the day. I was torn between this talk and a lecture from a well known speaker Jakub Nabrdalik. I thought that Jakub will show more things about architecture of enterprise apps so in the end I've chosen TDD. Sadly it was a wrong decision (I saw Jakub's talk a little bit later online and I regret my choice now).

Everything started with a usual reference to *Red-Green-Refactor*, then a bit about **FIRST** testing principle:
* *F - FAST*
* *I - ISOLATED*
* *R - REPEATABLE*
* *S - SELF-VERYFYING*
* *T - TIMELY*

I'm very interested in TDD as a technique, I'm trying to find answer to a question that is bothering me for some time: *Does Red-Green-Refactor always lead to a correct solution?* Correctness is not defined only by the fact that the tests are passing. I'm really interested if there was any research about it, like comparing solutions that were designed first and solutions that came to existance through TDD. I would love to read about the comparison of code complexity, amount of bugs, eventual maintainence etc. But let's get back to the topic.

Guys were explaining usual TDD calisthenics: readability, what can be treated as a single unit, size of tests. I can't say it wasn't well delivered or entertaining but it was hardly an "advanced" material. Also after reading the title I expected this talk to be more about issues with TDD, what things about us slow us down, what kind of habits can hinder good TDD.

Still there was an interesting thing mentioned about SQLite project. It has 745 times more test code than production code. WoW! Sounds impressive but is it so unbelivable?

### A Hitchhiker's Guide to the Functional Exception Handling in Java - Grzegorz Piwowarek

Some time ago Grzegorz gave a nice talk about functional programming for JUG Łódź, I've also seen few of his talks online (thank you youtube!) so the choice seemed simple. I was again a bit disapointed. Grzegorz was jumping through language and library examples `Java 8` -> `Scala` -> `Vavr.io` which was a bit confusing for an average listener. In the 45 minute talk we saw `Optional`, `Try` (both Scala and Vavr.io versions), `Either` and some `@tailrec` Scala functions. Nothing that I haven't seen or read about already. This lecture was definitely too tightly packed resulting with something to hard for a newbie and to simple for advanced programmers.

### Kariera developera - zostałem seniorem i co dalej - Michał Gruca

I didn't know what to expect from this talk. I often hesitate about going to such lectures, many times it's just hit or miss. This one was definitely a hit.

With a lot of humour Michał described career possibilities for every developer. This boils down to 3 options:
* point - when we focus our efforts on a specialization
* vertical - when we are "promoted" to a different role like Product Owner, Product Manager it's basically a career change
* horizontal - when we just jump between different department in the company
A grim vision?

Michał explained what is associated with the role of a software architect or a team leader. Anyone thinking about such position should understand that it means more work with people and less with technology. Architects often spend more time talking with stakeholder and business. Team leaders focus on peoples happiness, they manage time and priorities, keep an eye on requirements and goals. If you want to do some coding, this might not be the route for you.

Management role is a complete change of a career. Instead of working with technologies, you are working with KPI's (Key Performance Indicators). Instead of working with single people, you work with large groups or some processes. Manager strives to fullfil the demands of the higher management. It's in most cases thankless and stressful job.

Don't want to be caged in a corporate environment? Become a **Contractor**/**Freelancer** or create your very own **startup**.

It's important to ask: *what is important to me?* Create a career plan. Define what you want to achieve and when. Check who can help you with your goals!

### Wprowadzenie do JUnit 5 - Bartłomiej Kuczyński

What can I say probably the best "technical" lecture of the day. Good examples from start to finish. Time well spent!

### Ship every change to production! How it’s done at LinkedIn, in Mockito, and how you can do it with shipkit.org - Szczepan Faber, Marcin Stachniuk, Wojtek Wilk

The lecture was built from three parts:
- continous delivery in LinkedIn
- continous delivery in Mockito
- continous delivery using [shipkit](http://shipkit.org)

At LinkedIn they have a 3x3 rule:
* 3 hours is a maximum time it takes a change to get from a commit to production
* 3 releases each day

To achieve such speed pipeline needs to be distributed and jobs need to be done in parallel. Releasing 3 times a day means it's not possible to do a manual confirmation/validation that everything is ok. So a strict workflow is needed:

- Master branch is always green
- Development has steps:
  1) Introducing a code change
  2) A mandatory code review
  3) Pre/Post push validation
  4) "Depender" testing:
    - automated validation of all projects and modules that depend on that change
    - if any consumer (so called "depender") is broken that means no shipping
  5) New version is created - every change causes a version increase
  6) Staging - extra testing
  7) Canary - performance and memory testing
  8) Ramp up features - change is sent to whole userbase

Everything is explained in detail on the LinkedIn blog.

Unfortunately two other parts were not that interesting.

### Plantacje programistów - kolonializm XXI wieku - Wojciech Seliga

I love listening to Wojtek's lectures. I once told him in a joke that I only come from Łódź to see his talks. The funny thing is that this might be true. He tells a lot of truth about our profession, and our future. Even if you don't like what he is selling it's worth to listen.

This year was about how NOT to loose job to cheap programmers from third world countries.

It's not enough to know a programming language!
It's not enought to know software engineering!

To be relevant on the market we need to focus on doing product engineering.

What does it mean? We need to help our clients with their products from start to finish. Not just code mindlessly, but advise features, show shortcommings, help achive success.

Most importantly - leave the comfort zone and stop beeing afraid of loosing!

### Final thoughts

Very good edition of the conference. Few weak lectures, few strong, the same as usual. Still time well spent!
