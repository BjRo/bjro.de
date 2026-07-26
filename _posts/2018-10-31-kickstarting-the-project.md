---
title: Kickstarting the XING One project
excerpt: "Most projects don't fail for technical reasons. The naming, mission, and lighthouse-project work that set up XING One before a single line was written."
---
The last time around I shed some light on why `GraphQL` was appealing to me for sorting out our API game. It would have been easy after the project was green lit just to directly dive into the technical challenges. 

But if there's one thing that several years of work in larger companies have taught me, then it's that most of the time projects don't fail for technical reasons. There are so many non-technical influences that can derail a project. 

Just to name a few: 

* Overcommitting
* having a slightly different understanding about the project goal on different levels
* being crushed in internal politics or power struggles
* in-transparency because of half-baked project communication
* addressing risk too late in a project
* trying to stick to fixed plan that doesn't work in reality 

and probably many many more. 

I always viewed this project as a golden opportunity to do something truly great on our platform. To right some past wrongs. To enable the platform to take a productivity leap where previously tiny steps where the norm. That's why I didn't want to go naive into the project and tried to go some extra miles, that hopefully would accumulate and contribute to the success of the project.

## Setting the initial tone
My boss at the time had already made up his mind about the project name. He wanted to call the project `Everest`, because another project to establish a PAAS via Kubernetes named `Olympus` had been started earlier. I hated that name from the moment I heard it. Especially given that people have died trying to ascend that mountain. And also, to be frank, because I already had a name for this in mind: `XING One`. Luckily I managed to convince him that this one would be a better choice.

Most of the people at my current company know the name, but only very few know where the name originated. Some colleagues think it comes from "Lord of the Rings" (ala the one API to rule them all), but this isn't the case. It's a nod to the book [American Icon](https://www.goodreads.com/book/show/13132620-american-icon) which recounts the story of how Allan Mulally managed save and turn around the Ford Motor Company during the early 2000s. It's a remarkable story of what an aligned team with a shared goal and a clear mission can achieve. Mulallys misson statement for their turnaround strategy was "One team, One plan, One Goal".

I loved that. It's simple, memorable and uniting. With that it sets the initial beat for collaboration and culture. It's not surprising that our version is a derivation of that mission statement. 

> This is what's still inside the main project documentation page:
>
>* __One API__ - Removing the distinction between APIs for web and native mobile apps / XWS and vendor APIs
>
>* __One Model__ - Build one consistent and re-usable API data model on top of XING
>
>* __One Integration Engine__ - Consolidate how backend applications expose APIs toward clients and free our client applications from the major API integration challenges
>
>* __One Community__ - Evolve and scale API work via a Community of Practice  

## Aligning other major stakeholders
But this wasn't it. There were still some things that I wanted to get done, before diving into the technical details. One thing on the list was aligning with the major non-technical stakeholders in the company. In this case it was mostly the higher level management of the mobile product organization.

> You might wonder why I consider our product colleagues largely as non-technical. It's a completely valid question, but the answer to that is a whole story for itself. For the moment think of engineering and product management at XING as two different groups, with the usual communication and collaboration challenges that arise from that.

So how do you get those people on-board and set their expectations straight? My simple idea here was to look at how the product organization aligns itself and then use the same approaches / tools to communicate back to them. For me personally it also gave a nice opportunity learn something completely new and to extend my methodical toolset. 

In the end I did a full [Assignment Clarification Exercise](https://auftragsklaerung.com/), in short ACE (or in German Auftragsklärung) for our project and used that as the basis for discussions with every major stakeholder. If you've never heard of ACE before, welcome to the club. I had never heard of it either before working at XING. ACE is derived from the "mission command" concept outlined in Stephen Bungays [Art of Action](https://www.goodreads.com/book/show/9973202-the-art-of-action). It's a nice systematic tool to explore the current situation, it's challenges, the higher intent, what the effort is supposed to deliver, what the milestones along the way are and what's necessary in terms of resources. Even more important, it also forces you to lay out and document what is out of scope for your assignment. 

[Arne Kittler](https://www.xing.com/profile/Arne_Kittler/cv) helped me a lot coming to grips with this. He's one of the super engaged, public faces of XING in the product management space, with [ACE being one of the topics he frequently talks about](https://www.youtube.com/watch?v=47tcCYcrbnQ). Luckily for me, he's also the director responsible for the product management on our mobile platforms and a really nice chap. Especially in the early phase Arnes constant feedback was invaluable for streamlining our project intent before talking to a larger group of people.

In the meantime, my boss time did manage to convince one product engineering team that was planning a larger product refactoring to be our lighthouse project. Don't ask me how, I have absolutely no clue how he did it and also wasn't involved in any of that. But I'm grateful for the outcome. We always considered it important to validate the new infrastructure in the context of an actual product (as opposed as building it in isolation and then searching for people who might want to adopt our solution). To learn how people use it, where they struggle and use this as a feedback channel while building out the infrastructure. In January 2017 we decided that we were building and field testing `XING One` in the context of the profile section which was simultaneously rebuild for all platforms (web, iOs and Android). Special thank you for trusting in us goes out to [Björn Linder](https://www.xing.com/profile/Bjoern_Linder/cv) who was responsible for them at the time. It takes courage to bet such a large initiative on infrastructure that yet needed to be written. 

>In retrospect this is also where I made my first bigger mistake: I was too mobile focussed. 
>
>I was still thinking about `GraphQL` pretty much as a drop-in replacement for `REST`. Driven with the mobile challenges and the efficiency gains of using one platform as an added benefit. And with that I didn't align a lot with our web stack team and also wasn't putting much emphasis in the early project phase on client side tooling and integration on the web stack side. Worse, implicitly our frontend colleagues expected us to sort out the full web story for `GraphQL` with the lighthouse product, something like the `React`+`Relay`+`GraphQL` integrations `Facebook` was demoing at the time and were surprised and not amused when we didn't. That for itself left an interesting "gap" that our lighthouse team needed to figure out on their own, which was in retrospect less than ideal. And it also started collaboration with the web stack team on a low note, something which took us quite a while to recover from. 
>
>This rift is going to be something I'll separately write about in a future post. __So I leave it at that here with the hindsight that I'd probably approach that portion differently if I had to do it again with all I know today.__

## How do we actually start this?
Great, so the alignment part was kinda covered. I knew what I wanted to do. But I didn't yet know in what technology stack we later would implement `XING One` in. To be honest at the time I also had only vague ideas in how we would approach selecting the target solution. The team wasn't formed yet, it was still just me (Alexis the 2nd member of the team started roughly 3 weeks later). But I had a wild idea: Turning the assumptions I had about `GraphQL` into a checklist, implementing prototype versions of `XING One` in all the candidate technology stacks and then doing a shootout between them. 

How exactly I approached this is a topic for the next entry in the series. Hope you enjoyed this. See you next time around!!!
