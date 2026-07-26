---
excerpt: "Agility isn't a process. It's the technical decisions you made six months ago. The XING One codebase choices that kept us able to actually inspect and adapt."
---
I've participated in efforts to deliver software for more than 15 years now. The first five using the good old waterfall approach with infrequent, big releases; the last 10 years in teams that followed one of the flavors of the agile method. 

Some of the teams I was working on were using `Scrum`. Others used `Kanban`. Some used a mixture of both. The worst application of the agile method I encountered in the last 10 years (and sadly the most prominent one) is what I would generally describe as `Agile without a soul`, or as Martin Fowler calls it [faux-agile](https://martinfowler.com/articles/agile-aus-2018.html). You know, the version that heavily focuses on the prescriptive process portions. Which sometimes has teams whose members don't meet on eye level because the Product Owner actually turns out to be a manager in disguise, other times has developers that don't care at all about business outcomes but celebrate technical discussions about `KISS`, `DRY` and friends to the death, often in places where it doesn't really matter. 

> To be fair, I've been guilty of those discussions in the past as well :)

Maybe you know those kind of teams I'm talking about. Teams were removal of impediments and/or real continuous improvement is seen similarly often as unicorns. The only thing that does turn out to be continuous is the nagging sentiment in higher management levels that "This 'agile' thing is fraud / doesn't deliver what it promises / hurts us more than it actually helps". 

But guess what, I've seen those unicorns. And I've seen them more than once. Highly productive, self organized software engineering teams, that constantly inspect and adapt how they're delivering software. Teams that are conscious of the value that they provide to others, that collaborate closely with their stakeholders to deliver high quality results in a given time-frame. I still consider myself a believer in what slowly is somehow becoming similar to Voldemort (he who should not be named, for you non Harry Potter folks). 

If you now got the impression that this is going to be an epic post about how to do agile "the right way", I have to disappoint you. That's not going to be what I would like to talk about today. I doubt that there is "the right way", it all depends on context. And even if we accept that, I think it's still a big hairy audacious goal to describe how to be agile in all its intricacies. 

So, after bailing out on that, I would like to talk about something much simpler today. Something that is often overlooked when trying to deliver software in an agile way: Technical and semi-technical stuff that might help you being agile. And I don't want to approach this in an abstract way, but reflect a bit on what we actually did for our `XING One` project. Especially those "Yeah, that was a good idea", "saved our back more than once" or "would do that again" stuff. 

So let's have a look at those.

## 1. Coding with the right mindset
You can go into a new software adventure with the "I'm going to rock this" attitude. Especially if you're coming from other successful stints or projects and are highly confident in your capabilities. 

Or you can choose to be a bit more humble. 

You can accept that you're not perfect, that you're going to make mistakes and some sub-optimal decisions along the way, that requirements likely will change in an unforeseen way and that new opportunities may arise that you would like to follow-up on. If you accept that as a starting point, I believe that you look at your software and your setup differently. Consequently this was pretty much our stance with `XING One`. 

Just to give you some idea what we had to correct during our first year:

* We completely ripped out our first `REST` layer (the HTTP layer talking to the internal APIs, based on [akka-http](https://doc.akka.io/docs/akka-http/current/)) and replaced it with [Twitters Finagle](https://twitter.github.io/finagle/) (because our application was dying in overload scenarios and profiling pointed to `akka-http`)
* The HTTP layer itself was refactored and revised numerous times to adapt to new requirements that were introduced with new capabilities (primarily caused by new custom `SDL` directives)
* We proactively replaced the `JSON` library we were initially using ([json4s](http://json4s.org/)) after we had been live for several months, because we learned at some point in the project while profiling large `GraphQL` queries, that the `JSON` library we used implemented key lookup on top of a list (e.g. the larger the `JSON` document that was selected from, the longer the key lookup would take)
* The metadata layer was refactored numerous times (we convert a lot of the `SDL` directives into `Sangria` `FieldTags` which then control the execution in the execution layer) 
* We switched how you would extend `XING One` from using a `Scala DSL` to `SDL` after 10 months, once we were sure that this would work
* We changed the way how you'd call from `SDL` into native `Scala` function at least twice

and probably some more changes that I don't remember off the top of my head right now. 

What I want to say is this: Even though our outside `GraphQL` server interface didn't change much over the course of the first year, the internals were constantly revised, rewritten and improved, on a system that after 6 month had already real production traffic and real users on it.

## 2. Use an refactoring friendly language stack
I'm repeating myself here, but I want to emphasize the point. If you can't change your software fast and in a fairly cheap manner, you will have a hard time trying to work with agile and iterative product development. The ability to pull of refactoring with confidence, low ceremony and fairly low effort is key to a successful agile implementation. Move fast and break things sounds super sexy until you actually start breaking things and turn your users against your product. Your customers actually like when your application behaves predicatably. Your boss probably too .... and wait ... I guess you as well. So make sure that you can refactor with ease.

There are different opinions on what makes a codebase easy to refactor out there. Dynamic typing advocates usually mention that you have less code to work with and less abstractions that you need to deal with, that it's faster because the compiler is usually slow. People from the static typing camp usually bring up the better tool support and that the compiler does a great job for you (whose absence you have to compensate in dynamic languages for instance by writing more tests).

I've build production systems in `C#`, `Ruby`, `Elixir` and `Scala` now, with a bit patching and bug-fixing in `Objective-C`, `Perl` and `Erlang` sprinkled in. So compared to a lot of people out there who have strong opinions regarding this, I can at least say that I spend significant time in each technology to feel its benefits and downsides first hand. 

I always liked the feeling of speed that you have when you're working on a new `Ruby` or `Rails` codebase. But I also have to admit that working on large `Ruby` codebases, at least for me, was almost always outside the fun category. And refactoring `Ruby` applications even today ends up with a glorified 'search and replace' and 'fingers crossed that you found all references and/or everything is well covered by tests' (yes, I've also tried `Rubymine` and didn't see a lot of improvements there).

Once a codebase has reached a certain size I can definitely see a lot for value in static typing. Having `Resharper` in `C#` or `IntelliJ` in `Scala` at your disposal for me makes one hell of a difference. And I also think a good refactoring tool changes the way you work. You rename more often and you move stuff around more often. You can easier check where code is actually used in the codebase. And much much more.

`Scala` for me with `Intellij` fits well into this category, but it's not without it's caveats.

## 3. Throw out refactoring unfriendly language features
There is `"A better Java" - Scala` and there's `"I'd rather do Haskell but I'm stuck with this" - Scala`. The latter comes with one of the most refactoring unfriendly features I have actually seen in a static language, `implicts`. Not only do they slow down your IDE quite dramatically, they also cause puzzling looks on developers faces, when somebody removes an "import" by accident and the application doesn't compile anymore. 

You certainly can do cool things with them (Typeclasses anyone?), but I don't think `implicts` are worth the cost. Especially when you know that you want to rely on refactoring capabilities a lot. 

## 4. Remove unnecessary discussions about code style and conventions
Debates about code style are lovely time-sinks and I'm super grateful for the development of the recent years that brought more and more tools into the various tech stacks that do automated code formatting, based on some agreed upon time conventions. I'm not sure where it originated, but I think [gofmt](https://golang.org/cmd/gofmt/) started this. I definitely appreciate the idea. Consequently we relied on [scalafmt](https://scalameta.org/scalafmt/) from day one and since that day it's automatically ensuring that all our contributions are in a similar format. 

We also had fairly conservative [Scalastyle](http://www.scalastyle.org/) checks in place early on. `Scala` gives you many options on how to write your code (ranging from `obj.method()` to `obj function`). While I like the idea of the "scalable programming language", having multiple of those styles mixed together in one application, makes it more confusing to work with the code in a team and creates unnecessary distractions. Also here we favored the boring solution of `obj.method()` over the more functional syntax.

## 5. In-memory integration tests yield the most bang for the buck
One thing that I've seen many teams struggle with that embraced automatic testing, is finding the right testing approach that brings not only safety, but also speed and confidence. Similar to agile, I think you can see quite a bit of cargo culting in the automated testing area. Don't get me wrong, automated tests are an essential part of everything I've done in the last years, but somehow more often than not, when I look into different teams codebases, the setup falls into one of two extremes:

1. Heavy reliance on end-to-end integration tests, which are usually not very insightful, very slow, aren't run often and even worse sometimes even flaky (especially when you're dealing with distributed applications)
2. Heavy reliance on unit level tests and mocks, which are usually very fast, but very closely coupled to the implementation (resulting in parallel rewrites of the tests whenever the application changes)

I think there's a sweet spot in between, when you can figure out a big enough functional unit that can be decoupled from outside dependencies (`HTTP`, `DBs`, `AMQP`, etc.) and at the same time be effectively used to describe the behavior of your application. The decoupling from outside dependencies brings the necessary speed to run them often. The 'big unit' usually brings the ability to describe your applications behavior without describing the internal implementation, which often means little rework when the implementation changes. 

The result of this in my experience is that tests tend to become more central to the development workflow of a team and that testcode is also more considered as being 'part of the package' and not an 'appendix to the actual thing'. There's value in treating test code like production code. Remember it's not about the amount of code written, it's about the right amount. An imbalance between effort for test automation and and writing the actual feature can have a severe impact of flow in your team.

> Out of curiosity I took a look into the `XING One` repository while writing this.
> 
> At the time of writing the test codebase consists of **1229 tests** that take **~55 seconds** to run on my machine (a 2018 Macbook Pro). The testcode is of similar size compared to the actual application code (**22628** vs. **21018** lines of code)

The 'big unit' in our case is the `GraphQlEngine`, the piece of code that immediately takes over in our application once the outside HTTP interface received a request. It can be configured in different ways (authenticator, schema, middlewares and query providers). A spec for a particular feature usually showcases the feature or capability with the minimum necessary configuration. 

Tests typically follow an [AAA format](http://wiki.c2.com/?ArrangeActAssert):

1. Given a `GraphQLEngine` with the following middleware, schema and query providers configured
2. Stubbing the following HTTP requests (Optional)
3. When I run the following query 
4. It should have performed the following requests
5. It should produce the following result

To give you some better idea how that might look in code, here's an actual (simplified) example from the codebase:

```scala
class ConstResolverSpec extends EngineSpecification {

  val schema = """
    |extend type Query {
    |
    |  # Builtin scalars
    |
    |  aString: String! @const(value: "MUCH STRING")
    |  anInt: Int! @const(value: 1234)
    |
    |  # ... shortened
    |}
  """.stripMargin

  val engine = createSchemaEngine(schema)

  "successful conversions" >> {
    "String!" >> {
      val query    = "query ConstTest { aString }"
      val response = runQuery(engine, query)

      response.statusCode must beEqualTo(StatusCodes.OK)
      response.result must beJson("""
          | {
          |   "data" : {
          |     "aString" : "MUCH STRING"
          |   }
          | }
        """)
    }

    "Int!" >> {
      val query    = "query ConstTest { anInt }"
      val response = runQuery(engine, query)

      response.statusCode must beEqualTo(StatusCodes.OK)
      response.result must beJson("""
          | {
          |   "data" : {
          |     "anInt" : 1234
          |   }
          | }
        """)
    }

    // Shortened
  }
```
## 6. Get rid of cross repository coordination
At my current company we favor very small repositories. Libraries have their own repository. Individual services have their own repository. It also often happens that integration tests are located in a different repository. That definitely has some advantages. You isolate the work from different people better, including having a clear, focused git commit history. You also don't spend a lot time cloning on that particular repository. You can pick only the few repositories that you need and then you're good (compared to cloning the world and then using just a small portion of it).

But it also has a very clear disadvantage: Coordination. Say for instance your documentation is in one repository, your `GraphQL` server in another, integration tests are in a third repository and then maybe the client libraries are in a fourth one. Now things become slightly more complicated, because you can't change everything in a single pull request. You can't review all the changes that belong to each other as a whole. QA also becomes interesting since you now have to figure out how to temporary bring things together for validation (usually involving lots of branches). And last but not least, you now probably have an integration order. 

We made the conscious decision to put everything related to `XING One` into a single repository. The code, benchmarks, load tests, documentation, integration tests, utilities, you name it. This allowed us to get rid of a lot coordination ceremony. Especially the documentation benefited quite a lot from it, because we required it to be written or updated together with the actual code. We also changed the substructure of the source code quite a bit during the first year (when we pulled apart the `XING One` project into multiple `sbt` projects located in the same repository).

I found it tremendously helpful to look at all aspects of a change as a whole (code, unit tests, integration level tests, documentation, deployment, ...) and I certainly can see why companies like `Google` take the idea to the max, by having everything inside a single repository. 

In our company it wasn't always a free lunch though. The small repository approach and assumptions about the root repository structure were hardcoded in different custom `Docker` and `Kubernetes` tools we're using at the company, that we needed to patch in order to get everything we wanted to have properly working. We were willing to do that and pull through with it, but there definitely needed some extra work here and there to make our way work. 

As I outlined earlier, I would do it again. Overall the benefits of the mono repository outweigh the costs for me.

## Conclusion

Today I ranted a bit about agile and then talked in more depth about some more technical decisions and tweaks we did that allowed us to iterate fast and with confidence. 

To recap:

1. It helps to accept that change is inevitable and part of the game and prepare for that
2. Choosing a refactoring friendly language stack and staying away from language features that make refactorings more difficult is one of those possible preparations that at least for us payed off big time
3. Using automated tests and decoupling them good enough from the actual implementation so that they're fast, stable and a reliable safety-net is an often overlooked part of project setup. Don't cargo cult. Find the level of tests that actually drive your project. In our case these are the in-memory, integration-like tests of the `GraphQLEngine`.
4. Don't waste time on code format or style discussions, nowadays there are tools that automate these things away. Make good use of them
5. If you can, favor fewer bigger repositories over many smaller repositories: to spare yourself coordination cost, as well as to be able to change and review all touchpoints in one go

After ~1.5 years in production and a lot refactorings of different scope, we haven't yet managed to introduce regressions through new features or refactorings. For me that's one core element that allows you to be actually agile as a development team.

The next time, I'm going to take an interlude and will share our `GraphQL` learnings and observations.

See you next time around!!!

:wq
