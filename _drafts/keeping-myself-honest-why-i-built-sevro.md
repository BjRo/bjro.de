---
title: "Keeping Myself Honest: Why I Built Sevro"
excerpt: "A sensible change made my agentic workflow more expensive. On measuring the skills and harnesses we build, and why I created Sevro to help."
---

Working in agentic engineering is a somewhat funny experience. There is so much noise everywhere and the pace is hard to keep up with, even for the people working 100% focussed on this. Every new model arrives with a lot vendor fanfare, with vendors boasting why its revolutionizing and ahead of the competition. And the time between model releases is getting shorter and shorter.

I've been working with agentic coding tools for multiple years now, have been on agentic coding agents for almost a year. My last hand-written line of code dates back to December last year during my previous job.

It always was pretty obvious to me that there's a gap in this field that I felt the strongest whenever a new Codex or Claude version was released, answering the question of how the new model actually impacts my workflow. Does it improve or regress it? Do parts of my setup have become obsolete? With horror I look back at Opus 4.7 and 4.8, which definitely weren't improvements on their predecessors. I didn't have the tooling to structurally prove it though, beyond my emperical observations (and those of other people).

I feel like these are fairly obvious questions. After all we don't run applications in production with significant safetynets in terms of tests and monitoring. So why are we mostly flying blind in regards to agentic setups?

## A tale of a caution

That you're working for a while with these tools, doesn't mean that you can't run into suprises and be wrong. I've certainly been wrong before and my intuition led me into wrong directions. Without that tooling I would have made costly changes.

An example: On my my current project, there's an AFK-oriented skill that takes a ticket and implements it all the way to an open PR with reviews, convergence loops, etc. all in the mix. It's built on top of LangGraph. 

> Sidenote: Don't use LangGraph for such things these days, but that's a story for another day. 

Anyway, in this setup Codex interacts with the tooling and polls the LangGraph application for status updates.

Using eval tooling and looking at the hard data, it became obvious that the agressive context management with different phases in LangGraph and the polling from Codex towards the LangGraph application was adding considerable walltime and cost, at overseeable quality improvements. However before you change things radically, at least I often search for low-hanging fruits. And I thought to myself "hey forground tokens can be brought down easily, we just have to tell the agent to poll less frequently for status updates". Guess what, I implemented that, collected the numbers across a couple of runs and foreground token usage went up consistently not down. Well fuck! Codex largely ignored the new polling instructions. Even worse, the new instructions seemed to confuse Codex because they conflicted with the way Codex expected to observe ongoing tool execution. My attempt to make the workflow cheaper added more activity and cost to the foreground (in the realm of 20% more)

I had a plausible explanation for why the change should help. I had implemented something that looked sensible. And without measuring it, I could easily have walked away believing I had improved the setup. Insert narrator voice here: "It didn't"

## Agentic Engineering setup needs its own safety nets

I keep coming back to this point: Why is there not a solution yet that allows both evaling individual skills as well as evalling or even benchmarking of the integrated capabilities of a harness?

If we're super honest a lot of the skill building right now is runnig on the equivalent of "it worked once on my machine". That's nuts to me. We've put decades of effort into getting to the point where safetynets like unit test and integration tests as well as observability into production are the norm. And now we're back to square one?

That's bad and something that direly needs to improve if we want to progress as an industry in this new age.

I want a better basis for shipping that change than my impression that it worked well in a conversation yesterday.

## Meet Sevro

I knew roughly how evals worked in the context of agentic APIs. After all there is tooling for these such as Langfuse. I used promptfoo before. Also the Claude builtin evals tooling. But I couldn't find a solution that quite matched what I wanted to have: being able to validate not only skills but also local agentic setups, ideally super easy to pick up for Codex or Claude and just re-using my subscription login.

We're in the age of agentic engineering now. So I'm constantly experimenting with my setup(s). Eventually, I built the tooling I wanted in one of my projects, generalized it and extracted it.

It is called [Sevro](https://github.com/BjRo/sevro).

TODO: Insert sevro logo here

If you've read *Red Rising* by [Pierce Brown](https://www.piercebrown.com/), you'll recognise the name. Sevro is Darrow's right-hand man for the ugly stuff. Given that my experimental agentic tooling already lives under the Darrow name, this felt appropriate.

Sevro runs repeatable evaluations of coding agents. You give it a case, a setup to run, and criteria for success. It retains the execution evidence and grading results so you can inspect what happened and compare outcomes.

I use it to evaluate individual skills and to benchmark broader agentic setups. My day-to-day work is mostly in Codex now, so that is where it has received the most attention. There is also best-effort Claude Code support. It's built in a way that can be extended to other harnesses perspectively. 

It combines deterministic checks with agentic grading. Some properties can be checked directly. Others need an agent to inspect the evidence and assess the result. The framework is extensible, so projects can bring their own cases, checks, and grading logic.

Isolation was important to me from the beginning. An agent evaluating one case should not be able to browse previous runs or look into git history to discover what a successful answer looked like. A grading agent should receive the evidence for the trial it is assessing, with access bounded accordingly.

Sevro uses native Codex/Claude sandboxing while running the trial with support for shared library paths and caches. It uses OSX seatbelt or Linux bubblewrap to isolate graders, but they still have acess to the sessions transcript for example.

Getting a result is only useful if I can trust how that result was obtained.

## It's not perfect and will continue to change

If this is interesting to you, feel free to play around with it. It works for me doesn't mean its bug free or works for everyone out of the box (though I've spent quite a while on cleanup and documentation).

One thing that I want to experiment with in the future with it are agentic migrations: Right now Sevro is written in TypeScript and runs on Bun and Node.js. The language choice was fairly practical. I've worked with TypeScript before. I have some feelings for what good TypeScript code looks like. However, at some point, I would like to try rewriting it in Go or Rust as an experiment. How well can an agent translate a working system into another language while preserving its behaviour?

That is an interesting question for another day. I don't know Rust, which would make assessing the result a rather different exercise. But I'm super curious to try.

## How it changed how I work

Building software with agents has become easy enough that creating my own evaluation framework was a realistic response to a problem I kept encountering. So I went for it. It allows me to prove that something sensibly works. It also allows me to see and experiment in a more structured way, f.E. to test improvements before releasing them, almost like A/B testing.

I usually go from n:1 (worked once) to n:3 (worked 3/3) to n:5 (worked 5/5). For complex stuff reaching 5/5 might be not easy so I have an internal "good enough" at 4/5.

But the great thing is this. On my current engagement I have a suite of historical tickets with known end states. When a new model comes out I run the suite with the new model and am able to compare how the fully equipped harness performs against the vanilla coding agent without instructions. It's eye opening. You get a so much better feel what the new model really brings to the table. And where your elaborate setup potentially only adds cost.  

Sevro is available at [github.com/BjRo/sevro](https://github.com/BjRo/sevro). Feel free to check it out. If you like it, would be awesome to know, too. If not, I would love to see your eval tooling. What's important to me is that this kind of tooling exists, not that it comes from me.

:wq
