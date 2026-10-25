---
title: "Keeping Myself Honest: Why I Built Sevro"
excerpt: "A sensible change made my agentic workflow more expensive. On measuring the skills and harnesses we build, and why I created Sevro to help."
---

Working in agentic engineering is a somewhat funny experience. There is so much noise everywhere and the pace is hard to keep up with, even for the people working full-time in this field. Every new model arrives with a lot of vendor fanfare, with vendors boasting about why it's revolutionary and ahead of the competition. And the time between model releases is getting shorter and shorter.

I've been working with agentic coding tools for multiple years now and have been relying on agents for almost a year. My last hand-written line of code dates back to December last year during my previous job. Some may call me AI-pilled.

There's a gap in this field that I've felt most strongly whenever a new OpenAI or Anthropic model was released: how does the new model actually impact my workflow? Does it improve it or introduce regressions? Have parts of my setup potentially even become obsolete?

With horror I look back at Opus 4.7 and 4.8, which definitely didn't feel like improvements on their predecessors in my workflows. Until GPT-5.5 arrived, I actually mostly worked with Opus 4.6, true story. I didn't have the tooling to compare them systematically, though. I had my own observations and reports from other people.

I feel like these are fairly obvious questions. After all, we don't run applications in production without significant safety nets in terms of tests and monitoring. So why are we mostly flying blind when it comes to agentic setups?

## A cautionary tale

Working with coding agents for a while doesn't mean you stop running into surprises. My intuition has certainly led me in the wrong direction before. Without evaluation tooling, I would have made costly changes.

An example: On my current project, there's an AFK-oriented skill that takes a ticket and implements it all the way to an open PR, with reviews, convergence loops, etc. all in the mix. It's built on top of [LangGraph](https://github.com/langchain-ai/langgraph).

> Sidenote: Don't use LangGraph for such things these days, but that's a story for another day.

Anyway, in this setup [Codex](https://github.com/openai/codex) interacts with the tooling and polls the LangGraph application for status updates.

Using eval tooling and looking at the hard data, it became obvious that the aggressive context management across LangGraph phases and the polling from Codex were adding considerable time and cost, with only modest quality improvements.

Before changing things radically, I tend to search for low-hanging fruit. And I thought to myself: "Hey, foreground tokens can be brought down easily. We just have to tell the agent to poll less frequently for status updates."

So I implemented that and collected the numbers across a couple of runs.

Foreground token usage consistently went up by roughly 20%.

Well fuck!

Codex largely ignored the new polling instructions. Even worse, the instructions seemed to confuse it because they conflicted with the way Codex expected to observe ongoing tool execution. That was my reading of the behaviour. The increase in token usage was what I could measure.

I had a plausible explanation for why the change should help. I had implemented something that looked sensible. And without measuring it, I could easily have walked away believing I had improved the setup. Insert narrator voice here: "It didn't."

> Sidenote: We did find a way to reduce foreground token usage by roughly 40% in this scenario. But it required changes to Codex itself, and the solution was so involved that we didn't end up merging it. Another story for another day.

## Agentic setups need their own safety nets

If we're super honest, a lot of skill building right now is running on the equivalent of "it worked once on my machine". That's nuts to me. We've put decades of effort into making unit tests, integration tests, and production observability the norm. And now we're back to square one?

I want a better basis for shipping a change than my impression that it worked well in a conversation yesterday.

## Meet Sevro

I knew roughly how evals worked in the context of agentic APIs. There is tooling for these, such as [Langfuse](https://langfuse.com/) or [Braintrust](https://www.braintrust.dev/). I'd used [promptfoo](https://github.com/promptfoo/promptfoo) before, and [Claude's built-in eval tooling](https://platform.claude.com/docs/en/test-and-evaluate/develop-tests). But I couldn't find a solution that quite matched what I wanted: evaluating individual skills as well as the integrated capabilities of a local agentic setup, ideally easy for Codex or Claude to pick up and reusing my subscription login.

I also wanted the coding agent to run these evaluations inside its own session, so it could make a change, measure the result, and iterate.

I'm constantly experimenting with my setups. Eventually, I built the tooling I wanted in one of my private projects, generalised it, and extracted it.

It is called [Sevro](https://github.com/BjRo/sevro).

![Sevro logo: a black S-shaped track with red segments above the sevro wordmark.](/assets/images/sevro-logo.png){: style="width: 360px; max-width: 100%; display: block; margin: 2rem auto;" }

If you've read *Red Rising* by [Pierce Brown](https://www.piercebrown.com/), you'll recognise the name. Sevro is Darrow's right-hand man for the ugly stuff. Given that my experimental agentic tooling already lives under the Darrow name, this felt appropriate.

Sevro runs repeatable evaluations of coding agents. You give it a case, a setup to run, and criteria for success. It retains the execution evidence and grading results so you can inspect what happened and compare outcomes.

I use it to evaluate individual skills and to benchmark broader agentic setups. My day-to-day work is mostly in Codex now, so that is where it has received the most attention. There is also best-effort [Claude Code](https://github.com/anthropics/claude-code) support, and it can be extended to other harnesses in the future.

## How it changed how I work

On my current engagement, I have a suite of historical tickets with known end states. When a new model comes out, I run our eval suite with that model and compare how the fully equipped harness performs against the vanilla coding agent without additional instructions.

It's eye-opening. You get a much better feel for what the new model really brings to the table. And where your elaborate setup potentially only adds cost.

I can also test changes to individual skills before releasing them, almost like A/B testing. That's what caught the polling change. Having a repeatable way to compare outcomes gives me a much better basis for deciding whether a change helps.

I usually go from one run (worked once) to three (worked 3/3) to five (worked 5/5). For complex tasks, reaching 5/5 can be tricky, so I have an internal "good enough" at 4/5. Five runs are still a small sample. I use them to spot failures and decide what to investigate next.

## Checking the results

Sevro combines deterministic checks with agentic grading. Some properties can be checked directly. Others need an agent to inspect the evidence and assess the result. The framework is extensible, so projects can bring their own cases, checks, and grading logic.

Isolation was important to me from the beginning. An agent evaluating one case should not be able to browse previous runs or look into git history to discover what a successful answer looked like. A grading agent should receive the evidence for the trial it is assessing, with access bounded accordingly.

Sevro uses native Codex or Claude sandboxing while running the trial, with support for shared library paths and caches. It uses macOS Seatbelt or Linux [bubblewrap](https://github.com/containers/bubblewrap) to isolate graders, which receive access to the relevant session's transcript.

Getting a result is only useful if I can trust how that result was obtained.

## An experiment of its own

Sevro is written in `TypeScript` and runs on [Bun](https://bun.sh/) and [Node.js](https://nodejs.org/en). The language choice was fairly practical. I've worked with TypeScript before, and I have a feeling for what good TypeScript code looks like.

I didn't write a single line of Sevro by hand. If you're curious what a larger codebase built entirely with agents can look like, here's one example.

One experiment I'd like to try in the future is an agentic migration to `Go` or `Rust`. How well can an agent translate a working system into another language while preserving its behaviour? I don't know Rust, which would make assessing the result a rather different exercise. But I'm super curious to try.

Sevro works for me, which doesn't mean it's perfect, bug-free or works for everyone out of the box, though I've spent quite a while on cleanup and documentation.

Sevro is available at [github.com/BjRo/sevro](https://github.com/BjRo/sevro). Feel free to check it out. If you like it, it would be awesome to know, too. If not, I would love to see your eval tooling. What's important to me is that this kind of tooling exists, not that it comes from me.

:wq
