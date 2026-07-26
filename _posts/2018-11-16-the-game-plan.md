---
title: The initial game plan
excerpt: "Long-term plans in big-company IT go stale fast. Two principles — reduce risk early, make corrections cheap — that shaped how we rolled XING One into production."
---

[Last time](/choosing-the-right-technology/) I talked about how we selected the target technology stack for our `GraphQL` project with an extensive risk analysis and prototype phase. I spent roughly 3m on the project preparation. In March 2017 my colleague Alexis and myself began actually working on the real thing, the infrastructure that's now called `XING One`. Todays post will be mostly about how we approached the implementation part of the project.

Everybody who's done larger IT projects has probably experienced first hand how volatile and short-lived plans often are. It's almost impossible to predict the future (though we often behave as this isn't the case). In reality even highly experienced experts usually suck at predicting the future.

> I can again only recommend [Decisive](https://heathbrothers.com/books/decisive/) by Chip and Dan Heath. It contains a section about the experts and predictions. Quite enlightening in this regard

Complicating the picture further is that some companies (ours included) have a noticeable general tendency to run more initiatives in parallel than I think is healthy and as a consequence regularly expedite unplanned work between teams. So when implementation plans exceed the timeframe of one or two months, more often then not, they're already so far away from reality that they might look like satire.

As a consequence we had a plan, but a larger part of the plan consisted of only two core motives: 

1. Reduce project risks as early as possible
2. Set up the project so that learning, mistakes and course-corrections are fairly cheap

So what did that mean for the general rollout plan? In a nutshell it was this:

1. Bring the infrastructure as fast as possible into the production environment (but don't expose it to the user, in fact only whitelist the users that can connect to the system)
2. Shave off real product traffic to put the infrastructure under load from the start (but make it possible to control the amount of traffic that is relayed to the system) 
3. Instrument the application with metrics (performance and also `JVM` related metrics, like the Garbage Collection timings and occurrences) so that you have baseline data for learning and to judge changes in performance characteristics and operational behavior.
4. Find someone external who knows that `Scala` stuff inside out. Someone that brings `JVM` and `Scala` tuning experience into the team (so that we know how to profile the application performance and memory allocations when it becomes necessary)
5. Have a temporary schema that can be used to grok-out the infrastructure features and that you can actually run queries and mutations against it (even if the schema is not representative of the final product one yet)
6. Setup a load testing suite to explore the capacity boundaries for a single node (`RAM` + `CPU` cores) and to learn how the application behaves when it's under fire
7. Setup a resilience testing suite to explore how the proxy would behave when network timeouts, dropped connections, etc are happening
8. Iterate, inspect and adapt (and repeat)
9. Having an external security and penetration test

You probably notice that there's nothing in those steps, that mentions our "sister' product development team. We did start working with them in March 2017, but we came very fast to the conclusion that it would be best for the project if we would separate the two topics into parallel streams, at least in the early phase. 

Track one would develop the infrastructure and track two would develop the first prototype for the `GraphQL` version of the product using their default tech stack `Ruby and Rails` and `graphql-ruby`. Track one and two would join forces on the `GraphQL` schema design and identify common conventions and design rules that we wanted to document for the subsequent teams that would later join the platform.

The initial plan was to remove `graphql-ruby` out of the equation in July 2017 and to connect the `GraphQL` infrastructure to the product backend via `HTTP` / `JSON` APIs.

## The MVP and later versions
As I already pointed out in one of the earlier posts, we wanted to deliver value as early as possible and at the same time had a larger idea around how the developer experience around the `GraphQL` infrastructure should be. Our lofty goal for the infrastructure was that it should not require any knowledge of `Scala` in order to work with it, but instead use [GraphQL SDL](https://www.prisma.io/blog/graphql-sdl-schema-definition-language-6755bcb9ce51/) together with some custom directives to define the schema. 

That's obviously a lot to figure out and to bring to a production ready state in that timeframe (especially if you didn't yet know `Scala` that well). Because of that we decided to first focus on an intermediate version that didn't use `SDL`, but was using `Scala` to define the schema. That was the version we were shooting for until July 2017. Somewhere in autumn we would then try to figure out how we could marry the `Scala` schema with the `SDL` schema. 
 
>The idea being the "marrying" was that it would give us a lot flexibility if we could always drop back to `Scala` when we ran into limits with `SDL` or when there was simply functionality that we wanted to have in the schema but that was to complex to be generically expressed via `SDL`. An idea that turned out to be tremendously useful in the end.

## Boundaries for the infrastructure
One thing we also tried to make as early as possible transparent to the rest of the organization was how we would structure the integration with the underlying APIs and what the implications of our approach would be for our product teams (both technically and organizationally). 
 
> With "early" I mean this: When I gave the first presentation __0 lines of code__ were written. For the second presentation probably didn't have more than __15%__ of the MVP ready. Nevertheless I think managing assumptions and expectations around a project is crucial for its success. So the remainder of this post is about those other early pillars.

## 1. Authentication, but no authorization
Our infrastructure would provide the authentication for the web and mobile channels to the schema (one comprising of OAuth tokens, one cookie based for our web single sign on). 
 
Authorization logic tied to a particular API in terms of "user can only do this if he has membership X,Y or Z" or "quota for doing this has exceeded daily limit for user" was supposed to be implemented by the underlying API. 
 
In fact, even until today we have a very strict `No business logic in the GraphQL server` rule. 

## 2. No (or minimal) remapping
Our old API gateway is still to this day full of mapping code that tries to bridge some of our internal APIs to concepts that are still exposed through the API, but have been deprecated or internally abandoned years ago. We're often jokingly describe working on those as "keeping them alive". Sometimes there are valid non-technical reasons from the company side not to deprecate and shutdown obsolete APIs, but I can clearly say that I'm not a big fan of such approaches. Especially, if the company repeatedly had a hard time to figure out who owns the mapping code and who's responsible for ensuring that the mapping code is still working in a distributed, multi-team environment.

Our vision for our `GraphQL` gateway also was always something that exposes coordinates and connects APIs together in a mostly-declarative way. Imperative mapping code doesn't really fit well there in there and also complicates the picture. 

Long story short: Out of this came three simple agreements:

- The schema itself is owned by the respective product team
- All infrastructure would be owned, maintained and operated by our team
- APIs would be exposed as-is through the graph (as `GraphQL` fields)

## 3. Forwarding of selection sets
One of the benefits of `GraphQL` is that you exactly know which fields a client is requesting and because can also optimize bandwidth to only send over the wire what he actually needs. 

It turned out that we already had a matching piece of convention in our internal API. These typically accept a `fields` query parameter, which contains a comma separated list of fields that should be returned.

Because of our "No remapping" rule, we were able to derive the `fields` parameter automatically from the incoming selection set. For instance a `GraphQL` query with

```graphql
query MyProfile {
  viewer {
      profile {
          id
          firstName
      }
  }
}
```
where profile is actually an API underneath, would result in a request with `fields=id,first_name`.

> You probably notice the automatic camel case to snake case conversion in there. Since many of our APIs are written in `Rails`, this is the predominant casing format in our internal APIs. We preferred though to expose the fields in the client friendlier camel format and convert in the gateway. That conversion can be disabled though, when needed for a particular field.

## 4. Minimal conventions to enable batching
In order to prevent n+1 request cascades we were pretty early trying to figure out how to bundle multiple backend requests together or how to marry what [Sangria offers](https://sangria-graphql.org/learn/#deferred-value-resolution) with our underlying APIs. Luckily also for this case a convention was already existing that we could leverage. 

In our backends you can usually request a single `JSON` object representation under an url like `/profiles/profiles/1`. If you want to request multiple profiles, you would request `/profiles/profiles` and specify the ids as comma separated lists via the `ids` query parameter. 

You'd get back a `JSON` object which has at minimum a `collection` field that contains an array of the requested items.

```json
{
    "collection": [{
        "id": 1,
        "first_name": "John"
    }]
}
```

That's actually good enough to handle the typical scenario of "load list x from here and for each item load something else from there" for a lot of the cases.

## Some last words
Today I talked about how we approached building out our `GraphQL` to `REST` gateway from a very high level. I also talked about some of the rules and conventions about how the infrastructure would integrate with the internal APIs. 

Next time around, I'll dive in one specific aspect in particular: What you can do in a software project of this size to maintain adaptiveness, e.g. the ability to make mistakes and being able to course correct with fairly low overhead and cost. 

That being said, I hope you found this somehow useful or interesting. See you next time around!!!

:wq
