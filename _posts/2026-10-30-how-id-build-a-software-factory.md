---
title: "How I'd Build a Software Factory Today: Part I — Foundations"
eyebrow: "The software factory · Part I"
permalink: /how-id-build-a-software-factory/
excerpt: "My current mental model for a modular software factory, starting with the foundations: repository guidance, evals, skill building, observability, decisions, and deterministic quality checks."
---

I think an engineer's work is moving towards the front of the delivery pipeline and towards the pipeline itself: defining what needs to be built and creating the conditions for agents to deliver it reliably. In [an earlier post](/why-software-factory/), I wrote about why that makes the idea of a software factory appealing. So, how would I actually build one?

Before getting into that, my usual disclaimer: this is a snapshot. The setup I built for myself at the beginning of this year is already different from what I would build today. The agents have become more capable. My understanding has evolved, too, and keeps evolving. I expect to disagree with parts of this post eventually. And that's fine. I'd be embarrassed if I didn't learn some new tricks this year.

That expectation influences the architecture quite a bit. I want a setup that is easy to change as I learn what works and what the models can do on their own.

## The mindset behind my factory

That shift means figuring out what to build, resolving ambiguity, and defining acceptance. It also means preparing the repository and the tools so that an agent can do the best possible job.

Once a ticket is well defined, I want the mechanical work of turning it into a deliverable to run 100% on an agent. Implementation, tests, the required checks, and preparing the result for review should happen without me having to walk it through every step. Engineers still own defining the work and assessing the result. If the agent keeps needing help with the same thing, that is usually a reason to improve the ticket, the environment, or the capability responsible for that step.

I also want safety nets around the agentic setup itself. Tests for the application tell me whether the application behaves correctly. Evals help me find out whether the skills and workflows producing that application behave as intended. I believe in this strongly enough that I built [Sevro](https://github.com/BjRo/sevro), which I wrote about in [the previous post](/sevro-agentic-evals/).

The factory should be modular, with optional parts that are easy to replace. There are two reasons for that.

- **Model evolution.** Something that needs an elaborate workflow today might become a native capability tomorrow. I want to be able to rip that workflow out without rebuilding everything around it. Evals give me a way to check whether removing it actually helps.
- **Adoption.** If there is one thing engineers have a hard time agreeing on, it is one way of doing things. Different teams will have different needs and preferences. A factory has a much better chance of being adopted if people can use the parts that help them without signing up for somebody else's entire worldview.

And the setup has to be a versioned artifact. Repository instructions, skills, hooks, and plugin versions need to be inspectable and reproducible. Project-scoped skills are fine. A custom plugin marketplace is fine. User-local skills and personal agent memory have no place in the factory's required setup. If a workflow only works because my agent remembers something from last Tuesday, I haven't made it reproducible yet.

## Five layers I keep coming back to

With those beliefs in mind, I tend to think in five broad layers, starting at the bottom:

<figure class="factory-diagram" aria-label="Five layers of an agentic stack, from automations down to foundations">
  <div class="factory-diagram-label">The agentic stack <span>05 / 01</span></div>
  <ol class="factory-stack" reversed>
    <li><div><strong>Automations</strong><span>Start ready work from a scheduler, respecting capacity.</span><small>Example: pick up a ticket and invoke its recipe.</small></div></li>
    <li><div><strong>Task recipes</strong><span>Turn a simple input into an opinionated delivery request.</span><small>Example: turn a ticket ID into a pull request.</small></div></li>
    <li><div><strong>Orchestration</strong><span>Carry a bounded outcome through delivery, with cost and risk controls.</span><small>Example: coordinate implementation and verification.</small></div></li>
    <li><div><strong>Capabilities</strong><span>Teach repeatable procedures selected when the task calls for them.</span><small>Example: read a ticket, create a commit, or review code.</small></div></li>
    <li class="factory-stack-current"><div><strong>Foundations <em>Part I</em></strong><span>Give agents a reliable, maintainable environment.</span><small>Example: guidance, evals, decisions, and quality checks.</small></div></li>
  </ol>
  <figcaption>Each layer can provide value independently; the upper layers build on the foundations.</figcaption>
</figure>

Level five is where this starts to look like the software factory I described earlier: automations pick up ready work and run the delivery workflows around it. The layers below make that possible.

But a good agentic stack should also be useful before you reach that level. A team might only want better repository guidance and evals, or individual capabilities that engineers invoke in their daily work. A review capability should be useful when I ask for a review directly. An orchestration workflow should be usable without a scheduler. People can adopt the layers that help them without committing to an automated factory.

These layers describe responsibilities. They don't require five tightly coupled products. Composition happens through explicit inputs, outputs, and responsibilities, so each part can earn its place independently.

So let's talk more about layer one:

## Layer one: Foundations

These are the foundations I would start with for agentic engineering in a single repository, long before adding autonomous delivery. They are my baseline, not a requirement to adopt every part at once; teams can choose and replace the pieces that fit their needs.

How much of this you need depends on who is using it. When I'm working alone, I can often rely on direct experience and local transcripts. Once a setup is shared across a team, changes affect more people and failures are harder to see. That's when repeatable evals and shared observability start to pay for themselves.

### 1. Repository information architecture

I usually call the first part the repository's *information architecture*: how an agent discovers the codebase, its conventions, and the decisions that matter while working in it.

**Keep the entry point small.**

The entry point is `AGENTS.md`, or the equivalent instruction file for your coding tool. Treat that entry point as something a fresh session, and potentially every delegated agent, has to pay for. Keep it small. Put the minimum there that needs to be present by default.

I want each piece of guidance to have one authoritative source. Duplicating a convention in `AGENTS.md`, a contributing guide, and three skills creates four places that can drift apart. I also avoid documenting things the agent can discover cheaply from the repository itself. A list of package scripts copied from `package.json` is another thing to maintain, and the agent can read `package.json` perfectly well.

What does belong in the guidance is the stuff the code cannot tell you. An unwritten convention. A surprising constraint. Which of two valid patterns is the one we want new work to follow.

**Load guidance when the task needs it.**

For everything that only matters in a particular situation, I use progressive disclosure through intent matching. Matt Pocock calls these [context pointers](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md): a short reference that tells the agent both where to look and when to look there.

For example, `AGENTS.md` might contain:

```markdown
When creating or amending a Git commit, read docs/git-commits.md
for this repository's commit message conventions.
```

The referenced file can then hold the actual rule:

```markdown
# Commit messages

Use <type>(<scope>): <summary> for the subject.
Choose a scope from the affected package name; omit it for changes
that span the repository. Use the body to explain the reason for
the change when it is not obvious from the subject.

Example: fix(checkout): preserve the basket after payment failure
```

An agent investigating a bug doesn't need the commit message convention yet. An agent creating a commit does. The pointer makes that distinction explicit and keeps the convention in one place.

**Maintain the guidance behind the pointers.**

The documents behind those pointers need the same care as the entry point. Broken links, outdated instructions, and descriptions that disagree with the code all create confusion. Moving a mess out of `AGENTS.md` doesn't fix the mess.

And where multiple competing patterns exist, state which one is the reference for new work. If half the code uses one approach and half uses another, the agent cannot infer your migration strategy from the file count. A sentence such as "New endpoints use the handlers in `src/http`; `src/legacy-api` is maintained only for existing endpoints" can settle an otherwise expensive investigation.

This is roughly the job people hope `/init` will do for them. I want the result to be deliberately curated and maintained. A fresh agent is effectively stateless: it has to rediscover the world on every run. We should help it do that efficiently.

In [my own measured runs](/write-down-what-the-code-cant-say/), repository guidance reduced token usage by roughly 10–20% in the planning scenarios I tested, without improving plan quality when the code already made the patterns clear. The more consequential result came when the guidance supplied a decision between competing patterns that the code couldn't settle. That changed which approach the agent chose.

### 2. Evals for the agentic setup

The next foundation is a way to test individual skills and, eventually, an entire delivery workflow. If I'm working solo, trying a change in practice and observing the results may be enough. I don't need an eval suite for every part of my personal setup.

On a team, a change to a shared skill or workflow affects more than the person making it. That's where I think evals are especially valuable: they provide a safety net and evidence for a change that everyone will depend on.

I want to be able to change a skill, run representative cases, and inspect whether its behaviour improved. The same applies when a new model arrives. Does the existing setup still help? Does it introduce regressions? Can I remove some of it?

That is what I use [Sevro](https://github.com/BjRo/sevro) for. It runs repeatable evaluations of coding agents and retains execution evidence and grading results so I can compare outcomes. A different tool is equally fine. The important part is having a way to test the agentic machinery you depend on.

For a commit skill, that could mean checking whether it follows the repository's convention and commits only the intended changes. For an end-to-end delivery workflow, it could mean replaying historical tickets with known acceptance criteria. I want evidence of what happened, including the failures, so I can make a better decision than "it felt good when I tried it yesterday".

### 3. A skill builder with a feedback loop

Once skills become part of the setup, I want a repeatable way to build and change them.

That can itself be a skill: ask the agent to create or improve another skill, and the builder guides it through authoring and evaluation. It carries the standards I want every skill to follow:

- How it declares when it applies.
- How it finds repository-specific guidance.
- How it defines completion.
- How it gets evaluated.

Shared authoring standards belong here, where they can shape new skills consistently.

The builder should help create representative cases, run the skill, inspect the failures, and iterate. Producing a plausible `SKILL.md` is the beginning of that process. I want to see how the instructions affect an agent doing real work.

Matt Pocock's [writing-for-agents](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md) is a useful blueprint for the writing side. It covers the same discipline that matters in repository guidance: clear triggers, deliberate information hierarchy, and pruning instructions that don't change behaviour. Pair that with evals and you have a much better basis for maintaining a skill over time.

### 4. Observability through hooks

Evals show me what happens in the cases I thought to test. I also want to see what happens during everyday use.

If I'm working solo, I can often get away with analysing local transcripts when something goes wrong. On a team using locally running agents, those transcripts are spread across people's machines. I can't easily see patterns across runs or understand where the team keeps getting stuck. Cloud-based agents are a different situation: their host may already provide shared visibility.

Ideally, observability is a plugin that relies on lifecycle hooks and exports traces to something like Langfuse. The [Langfuse observability plugin for Claude Code](https://github.com/langfuse/claude-observability-plugin) is one example: it captures session telemetry, including tool calls and token usage, without making the agent remember to report each action.

That is what I mean by non-invasive. Observability should sit around the agent's work, with collection handled by the harness. It should not become another procedure the model has to perform while trying to solve the ticket.

Those traces give me, and later an investigating agent with access to them, a way to analyse problems across runs. Where does the agent repeatedly get stuck? Which instruction is being missed? Where do retries consume time without getting anywhere? A recurring failure can then become an eval case, and I can check whether the fix actually addresses it.

### 5. Decisions that agents can find

I want a straightforward way to capture significant decisions and retrieve them when they matter.

These days I usually create ADRs (architecture decision records) through a skill. Underneath, they are still Markdown files with an index. The skill makes capturing a decision less work and gives the agent a direct route to look up previous decisions when a task calls for it.

That route matters. An ADR somewhere in the repository is easy to miss. An intent trigger that says to consult the decision index when investigating an architectural choice gives the agent a reason to reach it.

The records also need to make their status clear. A proposal, an accepted decision, and a superseded decision carry different weight. When an agent finds two conflicting records, it should be able to work out which one governs the current codebase.

The reason behind a choice often survives much longer than the conversation in which we made it. I wrote about [recording what the code cannot say](/write-down-what-the-code-cant-say/) before. Decision records are one of the most useful places to put that knowledge.

### 6. Deterministic enforcement of code quality

Finally, think about how the repository enforces code quality.

Formatting, lint rules, type checks, dependency boundaries, and similar properties belong in specialised, deterministic tools wherever possible. I want the agent to run those tools and respond to their findings. Spending model attention on checking indentation is an expensive way to get an inconsistent answer.

Enforce the relevant checks through commit hooks or CI/CD. Hooks provide early feedback; required CI checks can gate integration. An instruction saying "remember to run the linter" is a much weaker guarantee than a pipeline that rejects a change when linting fails.

Agents still have plenty of work to do evaluating things those tools cannot settle. Give the tools the checks they can settle reliably.

## The baseline for one repository

That's my level one setup for a single-repository application: clear repository guidance, evals, a way to build skills consistently, observability, accessible decisions, and enforced quality checks.

Repository guidance and quality checks are useful places to start. The investment in evals and observability can grow with the team and the impact of the setup.

For example, suppose an agent keeps choosing a legacy endpoint pattern. A trace can show where it went looking; the repository guidance can point it to the accepted decision and the preferred pattern. I can turn that task into an eval, update the guidance or relevant skill, and rerun the case to see whether the change helps. That feedback loop is how the factory gets better as I use it.

<figure class="factory-feedback">
  <figcaption>The improvement loop</figcaption>
  <ol><li>Observe a failure</li><li>Capture an eval</li><li>Improve guidance or a skill</li><li>Rerun the case</li></ol>
</figure>

My personal setup is distributed through a custom marketplace of plugins. That makes it easier for me to reuse and version parts across projects. Project-scoped skills and instructions committed directly to the repository are equally fine. The packaging can follow the needs of the team, as long as the setup is reproducible and its parts can be replaced.

Multi-repository applications need more at this layer, particularly around where guidance and decisions live and how an agent discovers them across repository boundaries. I'll talk about this in a future post.

In Part II, I'll move up to layer two: intent-triggered capabilities, and how I want agents to pick up the right engineering procedure at the right time.

:wq
