---
title: "Per Aspera Ad Astra"
excerpt: "Series intro: how four people set out to introduce GraphQL at XING, and the playbook for landing platform-shift technology inside a 1.3k-person company."
---
Since 2013 I've just written a single blog post on my [original blog](http://www.bjro.de). It's
crazy how time flies, especially when you're past the 30 line. When I wrote the last post I just recently had become a dad and was still unaware on how all of this would change my life (for the better). Time was short, focus was somewhere else and honestly I also didn't think I had a good story or topic to talk about.

All of this kinda changed when I became responsible for a little, then still unnamed project at the end of 2016 that attempted to introduce a new piece of technology into the system landscape at [XING](https://www.xing.com): [GraphQL](https://graphql.org). 

At the time `GraphQL` was still fairly new and certainly not such a hot topic as it is today. 
It was just me in the beginning. Not a single line of code was yet written. No clue what 
programming language we would use. All I knew back then was what I wanted to achieve and that `GraphQL` could be an important piece to the puzzle to get there. And one suspicion: That the organizational impact of `GraphQL` might be on a different scale, compared to other technologies I had used before.

Today more than 45 engineers have contributed to `XING One`, our `GraphQL` to `REST` proxy. It's not yet powering all of our products but some of the more important ones. It's also on the verge of becoming "the way to build things" at XING. 

One thing I'm especially proud of is that the core team that leads the effort is still just 4 t-shaped (maybe even tree shaped) people. In times where some companies hire specialists in every imaginable form, where (super)large teams are the norm and the industry successfully managed to completely derail the term "agile", we did everything we needed to make our effort a success: 

* aligning stakeholders
* analyzing risks and developing counter measures
* doing extensive project communication and marketing (internal blogs & screencasts)
* building extensive documentation 
* creating an on-boarding program for new colleagues and running workshops
* developing a new scheme for rolling out this technology within our company 
* and last but not least, writing every line of code to turn the idea into reality.

Did we get everything right? Of course not. Did we make a lot of mistakes along the way? You bet we did, but we set ourselves up so that we could recover and improve along the way. Did everything play out as we planned? Certainly not. Are we done? Also nope, we're still at it ~1,5 years later.

In this series I'm trying to recapture the (what I think) most important aspects of our effort. By nature some parts
are going to be more organizational and process oriented, while other parts are going to be deeply technical. I'm trying to touch all the important learnings. This post will also contain the table of contents, which will be updated whenever new content will be available.

I hope you folks enjoy the ride as much as I did!

Best,
Björn

## Table of Contents
1. [What made us interested in GraphQL](/what-made-us-interested-in-graphql/)
2. [Kickstarting the project](/kickstarting-the-project/)
3. [Choosing the right technology](/choosing-the-right-technology/)
4. [The initial game plan](/the-game-plan)
5. [On agile codebases](/on-agile-codebases/)
6. [Interlude: Our GraphQL learnings and observations](/graphql-retro/)
7. [The game has changed](/the-game-has-changed/)
8. GraphQL meet lighthouse project, lighthouse project meet GraphQL
9. Wait, are you saying I can't use the status code anymore for checking for errors?
10. Set it on fire
11. GraphQL in a traditional application performance monitoring tool
12. Context aware scalars
13. Don't ever, ever, ever use the JSON scalar
14. Who the f**k put 150k metrics in the last minutes into my Graphite?
15. Resilience in a distributed environment
16. Help, we need to upload some images
17. Documentation first, instead of an afterthought
18. Getting crazy with SDL
19. The first GraphQL workshops
20. Federated team structures aka building a community
21. First field test for pure SDL schemas
22. Managing WIP and variability
23. Doing an internal private beta
24. On managing risk, part 2
25. Supercharging SDL
26. Fixing the SDL development workflow
27. Cashing with Varnish
28. Team, this is Apollo!
29. Our mutation semantics suck
30. Delegating epic level priority to the organisation
31. Metrics and alerts, part 2
32. Limitations of federated structures
33. Improving image uploads
34. Beyond the private beta

... and probably some other topics
