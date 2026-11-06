---
title: "How I'd Build a Software Factory Today: Part II — Capabilities"
eyebrow: "The software factory · Part II"
permalink: /software-factory-part-2-capabilities/
excerpt: "How intent-triggered capabilities let me shape the way agents work while keeping the parts of my software factory replaceable."
---

In [Part I](/how-id-build-a-software-factory/), I started laying out how I think about the different layers of a software factory. We began with the foundations: repository guidance, evals, observability, decisions, and deterministic quality checks.

Today I want to move up one layer: **capabilities**.

<figure class="factory-diagram" aria-label="Five layers of an agentic stack, from automations down to foundations, with capabilities highlighted">
  <div class="factory-diagram-label">The agentic stack <span>05 / 02</span></div>
  <ol class="factory-stack" reversed>
    <li><div><strong>Automations</strong><span>Start ready work from a scheduler, respecting capacity.</span><small>Example: pick up a ticket and invoke its recipe.</small></div></li>
    <li><div><strong>Task recipes</strong><span>Turn a simple input into an opinionated delivery request.</span><small>Example: turn a ticket ID into a pull request.</small></div></li>
    <li><div><strong>Orchestration</strong><span>Carry a bounded outcome through delivery, with cost and risk controls.</span><small>Example: coordinate implementation and verification.</small></div></li>
    <li class="factory-stack-current"><div><strong>Capabilities <em>Part II</em></strong><span>Teach repeatable procedures selected when the task calls for them.</span><small>Example: read a ticket, create a commit, or review code.</small></div></li>
    <li><div><strong>Foundations</strong><span>Give agents a reliable, maintainable environment.</span><small>Example: guidance, evals, decisions, and quality checks.</small></div></li>
  </ol>
  <figcaption>Each layer can provide value independently; the upper layers build on the foundations.</figcaption>
</figure>

This is where I teach the agent how I want certain engineering work to be done. Reading a ticket, creating a branch, reviewing code, opening a pull request: each is something I might want the agent to handle in a particular way.

But what interests me most is not any individual capability. It's how I can connect them without hard-wiring them together. That gives me room to swap out parts of the factory as my preferences change and as agents become more capable.

## Progressive disclosure and intent

At some point this year, I had a realisation about progressive disclosure. It turns out I'm not alone: I later found a similar idea in Matt Pocock's training material.

Progressive disclosure is a fancy way of saying: give the agent information when it needs it.

In Part I, I showed a simple version of this in `AGENTS.md`:

```markdown
When creating a Git commit, read docs/git-commits.md
for this repository's commit message conventions.
```

The entry point contains a small pointer. When the agent is about to create a commit, the instruction tells it where to find the relevant convention. An agent investigating a bug doesn't need to load that information yet.

Now look at the description of a skill:

```yaml
name: create-commit
description: Create a Git commit for the user's intended change.
  Use when creating a commit, including as part of a workflow,
  or retrying a failed commit.
```

That should feel familiar. Both descriptions connect an intent—creating a commit—to information the agent should load when that intent comes up.

There are differences in mechanics, packaging, and how the harness makes skills available. A skill can also bundle supporting files and scripts, and has a name that can be explicitly invoked. But from the perspective of the underlying model, the principle is much the same: **separate the when to use it from the what.**

Back in the days when we old cranky gals and dudes were still wrangling code by hand, we had another name for a similar idea: **lazy loading**.

Matt Pocock demonstrates how skill descriptions and agent instructions look to the model by putting a [proxy](https://gist.github.com/mattpocock/5b3d76ea21f5f698aefded47a9cea3b1) between the coding agent and the APIs it talks to. Try it and see for yourself. The mechanics differ, but both provide instructions to the model about when to load more information.

That gives us an interesting way to compose a factory.

## Connect capabilities by intent

Let's look at a more practical example. For now, let's leave aside how useful the skill itself is and focus on the mechanics and tradeoffs. Imagine I have a skill that creates commits. It describes my preferred commit message style, checks what belongs in the commit, and handles the mechanics.

Another skill is responsible for preparing a pull request. At some point, it needs the work committed. I could tell it:

```markdown
Invoke the create-commit skill.
```

Depending on the harness, I could also invoke it directly myself, for example with `/create-commit` in Claude Code or `$create-commit` in Codex. Naming the skill is explicit, and there are situations where that dependency makes sense. But it also means the pull request skill now knows the name of my particular commit skill.

For my setups, I often prefer something more like:

```markdown
Commit the intended changes before opening the pull request.
```

The pull request capability expresses **what needs to happen**. A separately available capability can recognise the intent to commit and provide the procedure. A successful selection might look like this:

```text
“Commit the intended changes”
              │
              ▼
      Commit intent recognised
              │
              ▼
   Load the applicable commit skill
              │
              ▼
   Create the commit using its rules
```

The pull request skill doesn't need to know the implementation of the commit procedure or even its name. If someone prefers a different way to commit, they can use that instead. If a repository has its own convention, the applicable capability can pick that up.

I find this useful because engineers tend to have opinions. Shocking, I know.

In a larger organisation, one team might use conventional commits while another has different requirements. The same goes for reviews, branch naming, and how pull requests are prepared. I don't want every higher-level workflow to contain all those variations. Different people should be able to express their preferences. What this boils down to is that I want teams to be able to replace the capability that handles the work if they want to.

This depends on the agent recognising the intent and selecting the right instructions. It's not the same guarantee as a hard-coded function call. I'll come back to that.

Back in the day, we called this **loose coupling**, and generally considered it a good property to have in a software system.

## Optionality works in both directions

Loose coupling gives me another property I care about: I can remove a capability without changing all the workflows that use it.

The models are improving quickly. Something that takes a carefully written skill today might eventually be handled well enough by the agent with a short instruction in `AGENTS.md`. I haven't spent enough time with Matt Pocock's idea of **leading words**, but maybe a create-commit skill can be replaced by “use conventional commits” in `AGENTS.md`. If that's the case, I'd be the first one to kill it and move on.

If every workflow names my commit skill directly, removing it means finding and changing those references. If the workflows express the intent to commit, I can try removing or replacing the skill without rewriting all of them. 

Either way, it's good to have evals to compare the before and after, because regressions happen in an agentic setup too. Might as well set yourself up to catch them.

## The capabilities I use

Here are a few capabilities I find useful in my own setup:

- **Discovery.** An interview capability helps me work through unknowns and ambiguity before implementation starts. It's similar in spirit to Matt Pocock's [grilling skill](https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md).
- **Git and GitHub.** Capabilities help create branches and commits, and open pull requests using the repository's template and conventions.
- **Readiness.** When I ask whether a ticket is ready for implementation, a readiness gate checks for unresolved questions and missing information.
- **Review.** When I ask for a code review, a review capability guides how the agent approaches it.

The list continues to grow. I can use each of these directly. They can also become part of a larger workflow because the instructions can express the relevant intent.

I've also seen people take this further and use skills to encode principles that shape *how* an agent approaches work. I haven't done much of that myself yet, but I find the idea fascinating and want to experiment with it in the future.

## What do I expect to gain?

Do I expect every skill to produce dramatically better results? No. Sometimes it might; sometimes the agent would have done a good job without it. Evals are worth having to assess that.

My main motivation is to make my preferred way of working available to the agent. I want the factory to reflect how I like to build software, while keeping its parts easy to change. I also accept that this might cost more tokens than a vanilla “just let the agent do it” approach.

The result is less like a static script and more like a collection of procedures that can be brought in when the work calls for them. That fits how I want to work with the current generation of coding agents.

There is a practical limitation, though: this relies on the model noticing the relevant intent and following through.

In my own evaluations, I've found that smaller models—such as Luna in Codex or Haiku in Claude—can need considerably more help for certain capabilities. Session hooks can improve things, but getting a reliable experience takes more effort. It's similar to running a coordinating task on Luna: you can do it, but in my experience it hasn't been as reliable as using a more capable model. With the more capable models I've used, this approach has worked very well for me.

That's my experience with my setup, not a universal ranking, of course. The model, harness, instructions, and task all matter. And by the time this post is out, there's probably a new Claude and Codex version, plus two new model versions as well. Yay, change! Yet another reason to continuously evaluate the setup you actually intend to use.

## A lot of behaviour in one sentence

Once all these capabilities are available, a short instruction can express a surprising amount of work:

```text
Read ticket <TICKET_URL>, switch to a branch, implement it using
TDD, open a PR when you're ready, and hand it over with green CI.
```

Behind that sentence, several capabilities can be activated:

```text
Read ticket        ──►  Ticket-reading skill
Switch branch      ──►  Branch skill
Implement with TDD ──►  TDD skill
Open a PR          ──►  Pull request skill
```

The sentence describes the outcome and the broad steps. It doesn't have to spell out how to read the ticket, name the branch, practise TDD, or fill out the pull request template every single time. Those details live with the capabilities responsible for them.

That separation should sound familiar to anyone who has spent time designing software.

You can write that prompt manually. You can also package it up in different forms. For instance you can wrap it in a `/goal` and off you go. Either way, the combination already gets you a long way.

The exact capabilities matter less than the underlying idea: express what needs to happen, and let the setup bring in the relevant procedure. Then keep measuring whether that procedure is still useful. When the agent handles it better without the extra instructions, well, fuck it: kill the skill and move on.

Next time, I'll move up another layer and talk about a core primitive I've found useful for carrying work through delivery: `adaptive-goal`.

See you then.

:wq
