---
title: "On Boilerplate and the Death of Old Principles"
seo_title: "Boilerplate and the Death of Old Principles"
excerpt: "When a machine can rewrite a module in seconds, do YAGNI and DRY still earn their place? The principles survive — but the reasons we hold them shouldn't."
---

A few days ago, I published [a piece on AI and existential fears](/ai-and-existential-fears/) — my attempt to navigate the space between hype and dismissal in the current AI-assisted coding landscape. It struck a nerve, and the responses were thoughtful. One in particular made me stop and think.

Krisztina Hirth asked a deceptively simple question:

![Krisztina's question on LinkedIn](/assets/images/krisztina-question.png)

This is a better question than it might appear. It's not really about boilerplate. It's about whether an entire generation of hard-won engineering principles — minimalism, DRY, "every line should earn its place" — needs to be re-examined when the economics of writing and rewriting code fundamentally change.

I think the answer is nuanced. And I think it reveals something important about how our relationship with code is shifting.

## The Cheap Rework Argument

Let me start with the part that feels almost heretical to say out loud.

**Rework is becoming very cheap.**

When a machine can rewrite a module in seconds, the cost calculus of code changes dramatically. In the old world, boilerplate was expensive because humans had to read it, understand it, maintain it, and modify it. Every unnecessary line was a tax on future developers. We built entire philosophies around this — YAGNI, DRY, the relentless pursuit of minimal code — because the human cost of complexity was real and compounding.

But if an AI agent can regenerate a component from scratch in the time it takes you to read the first function? The maintenance cost argument weakens considerably.

Here's the key insight though: this only holds if you protect what matters. The outward behaviour of your system — your APIs, your data contracts, your persistence formats, the observable behaviours that other systems and users depend on — these are your guardrails. As long as those interfaces are stable and well-defined, the internals become genuinely more disposable than they've ever been.

This is contract-based thinking taken to its logical extreme. Programme to interfaces, and let the machine worry about the implementation behind them. From this perspective, boilerplate behind a stable interface really doesn't matter as much as it used to.

## But Here's Where It Gets Interesting

Now, you might expect me to stop there. "Boilerplate doesn't matter, embrace the machine, move on." But I think there's a second argument that actually pushes in the opposite direction — and it's one that most people haven't considered yet.

**Removing boilerplate is in the AI agent's best interest too.**

This is the part that surprised me when I started thinking about it carefully. Current AI coding agents operate within a context window — a finite amount of information they can hold and reason about at any given time. This context is precious. It's the agent's working memory. And like all working memory, it degrades as you fill it up.

When your codebase is full of noise — boilerplate, repetitive patterns, verbose scaffolding — the agent has to wade through all of it to orient itself. Every unnecessary line of code is a line that competes for attention in a limited context. Less noise means the agent can ground itself on your existing code faster and more accurately. It means you're getting more out of every token of that context window.

And here's the practical reality: context quality degrades as it fills up. The more you stuff into it, the less reliably the agent reasons about any of it. A lean, well-structured codebase isn't just aesthetically pleasing — it's *operationally superior* for AI-assisted development.

So the old principle survives, but for an entirely new reason. "No more code than strictly necessary" used to be about respecting the humans who'd maintain it. Now it's *also* about respecting the agent that navigates it. The principle didn't die. Its justification evolved.

## And What About Krisztina's Real Question?

Let me come back to the sharpest part of her question: *how much of the code can become boilerplate?*

The implication is provocative — maybe we should lean *into* boilerplate because it's predictable, standard, and easy for machines to generate and rewrite. If the machine handles it, why fight it?

I think the honest answer has two parts, and they pull in different directions.

**Some boilerplate is genuinely eliminable.** If a pattern is so standard and mechanical that we'd call it boilerplate, it's often a sign that a better abstraction exists — or should exist. Think about the route handler that needs request validation, error mapping, serialisation logic, and client-side types, all derivable from an API contract. That's not code you need to accept. That's code you can replace with a cleaner abstraction, a smarter framework choice, or a build step that generates it from the contract. *That* boilerplate should disappear. It's noise, it bloats the context window, and eliminating it is a genuine win for both you and the agent.

**Some boilerplate is unavoidable.** There's mechanical wiring code that simply has to exist somewhere for the system to work. No amount of abstraction elegance removes the fact that services need to be configured, dependencies need to be wired, and infrastructure needs scaffolding. In the old world, this was a constant source of friction and maintenance cost. In an AI-assisted world? This is where the cheap rework argument genuinely applies. If the agent can regenerate this code reliably behind stable interfaces, stop agonising over it. It's not worth the engineering energy to gold-plate code that a machine can rewrite in seconds.

The practical upshot: eliminate the boilerplate you can — it makes your codebase leaner and your agent more effective. And for the boilerplate you can't eliminate, stop treating it as precious. Protect the interfaces, let the machine handle the wiring, and save your engineering judgment for the code that actually carries decisions.

## Trust Is Good, Double Check Is Better

Now, all of this raises an obvious concern. If machines are writing and rewriting code with increasing autonomy, how do you prevent the whole thing from drowning in technical debt?

This is where I think the conversation needs to move from principles to practice. I still construct my delivery pipeline to enforce good engineering practices — but I enforce them at multiple checkpoints rather than relying on any single gate.

**In ideation**, before a single line of code is written, the first question is: *does this need to exist at all?* This is the cheapest place to prevent unnecessary complexity. An AI agent will happily build whatever you ask for. The engineering judgment about *whether* to build it is still yours.

**In planning**, every implementation plan gets challenged against good engineering practices. I use specialised review personas — think of them as Staff-level engineering perspectives tuned to specific domains like React, Go, or LLM integrations. The plan gets scrutinised before implementation begins. Does this architecture make sense? Are we introducing unnecessary coupling? Is there a simpler approach?

**In review**, after the implementation agent finishes its work, the code goes through a thorough pull-request review by specialised personas. Critical, high, and warning issues get kicked back to the implementation agent for fixing. Trust the machine to write the code, but verify the result before it ships.

**In scheduled maintenance**, I still do periodic reviews of the whole codebase. This catches the holistic problems that individual feature-driven plans and reviews miss — the creeping inconsistencies, the patterns that made sense in isolation but create confusion at scale, the technical debt that accumulates between the cracks.

This isn't paranoia. It's the same principle that makes aviation safe: multiple independent checks, because no single check catches everything.

## The Principle Evolves

So where does this leave Krisztina's question?

The old principle — "no more code than strictly necessary" — isn't dead. But its justification has fundamentally shifted. We used to minimise code because humans paid the maintenance tax. Now we minimise code because it makes AI agents more effective *and* because it keeps the codebase comprehensible for the humans who still need to operate, debug, and make judgment calls about the system.

The cost of boilerplate has changed. Rework is cheap. But clarity is still expensive, and context is still finite. A lean codebase serves both the human operator and the AI agent. The principle survives not out of nostalgia, but because it turns out to be correct for reasons its original authors never imagined.

And the guardrails? They've moved from "write less code" to "build better checkpoints." The engineering discipline isn't in every line anymore — it's in the pipeline that ensures the machine's output meets the bar.

Welcome to the evolved principle. Same destination, different map.
