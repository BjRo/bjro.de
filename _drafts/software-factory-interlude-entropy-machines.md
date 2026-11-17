---
title: "Entropy Machines"
eyebrow: "The software factory · Interlude"
permalink: /software-factory-interlude-entropy-machines/
excerpt: "Before the next layer, a pause to talk about expectations. What coding agents can realistically do, why the most proficient people I know constrain them hard, and what that looked like on a 10,000-line file nobody dared to touch."
---

In [Part III](/software-factory-part-3-adaptive-goal/), I left off with adaptive-goal as a primitive for the factory and promised task recipes as the next layer. Before I get to them, I want to take a break from building things and talk about something that's been on my mind all year. Expectations.

This year I've talked to a lot of developers who have played with AI coding tools. I've also read a lot of loud opinions on LinkedIn and elsewhere. What people expect from coding agents is often far from how the heaviest users actually work with them. The gap is bigger than I thought it would be.

## Where I started

If you've read my earlier posts, you know where I stood at the beginning of the year. An agent needs guidance. Code quality matters a lot, because an agent is a pattern-matching machine at heart. It looks at your codebase to figure out how things are done and then does more of the same. I made a version of this argument in [On Boilerplate and the Death of Old Principles](/on-boilerplate-and-the-death-of-old-principles/). Less noise in the code means the agent spends less of its context wading through it.

The flip side is uncomfortable. If your codebase carries a lot of technical debt, the agent will copy it. It finds your worst patterns and writes more of them, and it does that fast. The better shape your code is in, the more an agent can help.

What I learned later in the year is that a good codebase isn't enough either. If agents are supposed to succeed in the medium to long term, you need to shepherd them with even harder guidance and even more deterministic tooling than I initially thought. I'll show what that means with an example from this week's client work.

## What if I'm wrong?

What stuns me on LinkedIn is how often very vocal people claim that agents are on an exponential capability curve. According to them, it's only a matter of time until none of this matters. Stop caring about code quality. Stop building guardrails. Leave the agents alone and let them do their job.

Some of them say, almost proudly, that they don't look at the code anymore. They don't know how it works, and they say they don't need to. Given where LLMs are today, that position makes me deeply uncomfortable. Coding agents almost certainly aren't there yet. Whether they ever get there remains to be seen. In the meantime, a lot of slop gets produced, and humans are still accountable when things go wrong. The people accountable for what an agent delivers should know whether what the agent produced is any good, and they should be able to judge it. You can't hide behind an agent. "The agent wrote it, I don't know" isn't an answer I'd accept from an engineer.

Still, somewhere this year I started to wonder. Maybe I had this wrong. Maybe I was being too harsh. Maybe they were right, and all the work I put into guidance, evals, and deterministic checks would soon look like building a better horse carriage.

So I paid closer attention. I went to conferences. I attended trainings with some extremely proficient people in this space and looked carefully at how their setups work.

Several months later, my answer hasn't changed. I don't think the need for shepherding and code quality is going away any time soon. Definitely not in the short term. Probably not in the medium term either.

Don't get me wrong. You can achieve astonishing things with coding agents. Purely magical stuff, in record-breaking time. I've seen it and I've done it. But this technology has limits, and the people who have spent hundreds of hours with these tools know them well.

Spoiler alert. None of them work by letting the agent do its magic and hoping the capabilities catch up.

## Tactical specialists

At [code.talks](https://www.codetalks.de/) this year, I listened to Neal Ford, formerly of ThoughtWorks, talk about how to constrain an agent so it does the right thing. He wrote the architecture down, in decision records or in a language for describing architectural constraints. Then he enforced it with tools like [ArchUnit](https://www.archunit.org/). His reasoning was simple. Agents don't care much about architecture. They are very, very willing to get the job done.

Matt Pocock made the same point in his [software factory cohort](https://www.aihero.dev/cohorts/software-factories-for-real-engineers-xdy1m), which I took part in recently. Agents are tactical specialists, not strategic ones.

That matches what I see. An agent will solve the problem in front of it in whatever way works for that one change. It won't stop and ask whether the solution breaks a module boundary your team spent two years establishing.

Neal also compared today's frontier models with the [Dreyfus model of skill acquisition](https://en.wikipedia.org/wiki/Dreyfus_model_of_skill_acquisition). If you haven't come across it, the model describes five stages people go through on the way from novice to expert:

1. **Novice.** Follows rules rigidly, without regard for context. Can't tell when a rule doesn't apply.
2. **Advanced beginner.** Recognises situations they've seen before, but treats every aspect of a situation as equally important. Doesn't see the big picture yet.
3. **Competent.** Plans deliberately, sets priorities, and chooses what to focus on. Feels responsible for the outcome.
4. **Proficient.** Sees the situation as a whole. Notices what matters most and when something is off.
5. **Expert.** Doesn't need the rules anymore. Knows what to do from deep experience, and can still reason it through step by step when something new comes up.

According to Neal, agents are currently stuck at **advanced beginner**. I find that fascinating, and it lines up completely with what I'm seeing. They can copy work and recombine existing work. They can't zoom out and reason about that work.

Yes, with a lot of agents and a lot of money you can brute-force a problem. Many agents together can find new solutions by trying and recombining. But they're still bound by how the technology underneath works. LLMs have limited attention. They lose focus when there are too many things to pay attention to. They lose focus when their instructions conflict. All of that shows up directly in the result.

So no, I don't feel like we're on an exponential curve. That doesn't make the technology any less amazing when you use it properly. I'm amazed every day by what I get done with it and by the systems you can build on top of agents.

The most proficient people I know all describe agentic systems without constraints the same way. They call them **entropy machines**. Left alone, they create disorder. The agents aren't malicious or stupid. They're eager to finish the task, and nothing tells them when they've gone wrong.

## Finding certainty in a loud world

Even so, I find it hard to feel certain about any of this with so many opinions and expectations flying around. I guess it's hard for other engineers, too. One group tells them not to bother because it'll fix itself in a couple of months. Others, like Mario Zechner, who created the Pi coding agent, tell them to [slow the fuck down](https://mariozechner.at/posts/2026-03-25-thoughts-on-slowing-the-fuck-down/). I've stolen that phrase more than once. Mario's view of what agents can and can't do is very close to mine.

## "It should have figured this out"

Then there's the everyday version of the problem. About every third developer I meet expects magic. The complaint usually goes something like this:

> It should have figured this out. This solution is poor. I would have done a better job.

Sometimes that's fair. Often, though, they don't realise they're part of the problem. The requirements they gave the agent were vague. They're sitting on lots of tacit knowledge they never wrote down: why that service is built the way it is, which approach the team already tried and dropped, what "done" means here. The agent had none of that. Of course it didn't figure it out. That's why I keep telling people to [write down what the code can't say](/write-down-what-the-code-cant-say/).

Matt Pocock has a line about this that I like a lot:

> The bottleneck was "the work needs to be done". The bottleneck is now "the work needs to be properly planned".

What I like about it is that it points at a part of working with these tools that people underestimate badly. Every task has open questions. If nobody answers them before the agent starts, the agent answers them itself, and it won't tell you it guessed. It wings it. Getting the uncertainty out of a task up front, and getting the agent to stop and ask instead of improvising, matters as much as anything the agent does afterwards. That's also why my own setup moved more of its effort into an interview while shaping the ticket, as I described in [Part III](/software-factory-part-3-adaptive-goal/).

I see the same thing with skills. Someone generates a skill and expects the AI to work out the rest. When something unexpected happens, they're confused, and they blame the technology.

I've worked with these tools for several years now, and full-time on this for the past year. I'm still surprised by how much nuance it takes to get a setup working as intended over the long term, let alone a software factory. It takes a lot of caretaking and system design. Part III was partly a story about that. My own expectations were off there too, just in the other direction, and it took benchmarks to show me.

## A dark factory on a brownfield

The same thing happens at a larger scale. A lot of people want a **dark factory** these days. That's the top level in [Dan Shapiro's five levels of AI-assisted development](https://simonwillison.net/2026/Jan/28/the-five-levels/). Specifications go in, software comes out, and no human writes or reviews the code.

So they take an existing application, usually more brownfield than greenfield, and put a software factory on top of it. Then they're surprised when the code quality gets worse month after month. They're also surprised that the system doesn't fix itself that well.

Ask a few more questions and you usually find that the code underneath wasn't great to begin with. There was never really time. That's a recipe for disaster. Whoever has to clean up that application and get it back into shape is in for a very painful time.

## On the bright side

Here's the example I promised, from a client engagement this week.

The team had a component written in Python. An agent had built it, with tests and a written spec that was kept in sync with the code. So it wasn't vibe-coded or YOLOed. It was working software. Still, it was far from being in good shape. Its internal structure was a perfect example of entropy. One file in particular scared a couple of people on the team. It was more than 10,000 lines long, and nobody dared to touch it. I even heard people say they'd rather delete the code and start from scratch.

The cool thing about having agents at your disposal is that you can fix situations like this far more easily than before. You just need a few tricks.

The first is the one I mentioned earlier. Use a linter to define which layers and packages inside the application may depend on each other. It doesn't have to be perfect from the start. You'll probably begin with a long list of exclusions. Then you ratchet. You tell the agent it isn't allowed to add new exclusions anymore, and you work with it step by step to remove the existing violations. The list can only get shorter. Matt Pocock shows this kind of ratchet in his latest course, using [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) to enforce package boundaries in TypeScript.

This week I used [Tach](https://github.com/tach-org/tach), which does something very similar for Python. I also used [mypy](https://mypy-lang.org/) to turn untyped Python into typed Python. That got a ratchet of its own, an exclusion list of modules that weren't fully typed yet. Then I used Matt's [codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/SKILL.md) and [improve-codebase-architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md) skills to work on the structure of the code itself.

And guess what? At the time of writing, that 10,000-line file is down to fewer than 3,000 lines. We didn't introduce any major bugs. We tested the component afterwards, and it behaves exactly as before. The difference is that its structure is now much better, and the tools enforce it.

That's amazing. Think about it. Not long ago this was close to impossible, or it would have taken very proficient people a long time. Now you can do it in a couple of days, if you know the tools and the approaches.

It turns out this is a critical part of a software factory, too. When advanced folks talk about deterministic tooling, they don't stop at unit tests and simple linters like ESLint or RuboCop. They also mean newer tools that protect the design of the code and help you shepherd it, or garden it, for lack of a better metaphor. They go way further than that. Exhibit A is the architecture linter, but the sky is the limit here. Property-based testing and mutation testing used to be too much effort for most teams. In this new age, they're becoming much more reachable, and much more useful. Matt showed examples of both in his cohort.

The important point is that agents need constraints to produce the right software over time. They don't come up with those constraints themselves. That's your job. If you're trying to set up a software factory without them, good luck, my friends. It's going to be tough.

And if you do have those constraints? Look at what Lauren Tan, known as poteto, shows in her talk [How I Shipped 2000 PRs Last Month](https://www.youtube.com/watch?v=NjoZoUm85x0) about her work on Grok Bot at SpaceXSI. By her own account, she ships around 2,000 PRs a month. And that number keeps growing. In her more recent AI Engineer talk, [All Code Is Slop (Except Mine)](https://ai.engineer/talks/O_zcc6Xl12g-all-code-is-slop-except-mine), she was at over 3,000 a month. She's clear that verification makes that volume possible, not the number of agents. It's incredible.

## Two ends of a spectrum

So we end up with a spectrum. At one end are the people who want to be completely hands-off. Trust the agent, trust the curve, get out of the way. At the other end are the people who put an enormous amount of work into the environment the agent runs in, so they get the best possible runs and the best possible results.

Everything I've seen this year, and in the years before, indicates that the latter group is more spot on.

Agents aren't magical creatures. They're tools with limits and trade-offs, and you have to use them properly. I don't think that's pessimistic. It's the reason I'm building a software factory in the first place. Repository guidance, capabilities, orchestration, and deterministic checks are how I give agents the constraints they won't set for themselves.

Next up, back to building: task recipes.

:wq
