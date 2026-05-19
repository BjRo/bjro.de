---
title: "What's the Appeal of a Software Factory?"
excerpt: "Individual agentic coding is real leverage. The hard question is how to turn it into organisational capability without blowing up cost, quality, or customer trust."
---

I have spent a good chunk of the last months giving trainings and workshops on agentic engineering and software factories. The audience is usually technical. Often very technical. And increasingly, they are not coming in as blank slates.

Many of them have already built something useful with Claude Code, Codex, Cursor, or whatever tool happened to be the flavour of the month when they had a weekend to spare. They have felt the speed. They know the moment where the agent does in ten minutes what would have taken them half a day. They are not sceptical because they have never tried the tools.

They are sceptical because they *have* tried them.

And from their perspective, the obvious question is fair: if I am already productive with my own setup, what is the appeal of putting a "software factory" around it?

I think this is exactly the right place for the conversation to start. Because the value of a software factory is not obvious from the point of view of a strong individual contributor working alone. It becomes obvious when you step into the shoes of a larger organisation with existing customers, existing systems, regulatory exposure, production incidents, security requirements, review bottlenecks, onboarding problems, cost constraints, and a product that should not be accidentally damaged by a very confident machine.

The short version:

**Individual agentic coding is not the same thing as organisational agentic capability.**

The gap between the two is where the software factory lives.

![A diagram showing solo agentic coding, the organisational scaling cliff, the software factory as a shared harness, and organisational capability.](/assets/images/software-factory-main-arc.svg){: style="width: min(1180px, calc(100vw - 2rem)); max-width: none; display: block; margin: 2rem 0 0.75rem; position: relative; left: 50%; transform: translateX(-50%);" }
*Individual agentic coding creates leverage. A software factory turns that leverage into organisational capability.*

## The solo setup is real

Let me say this clearly, because otherwise the rest sounds like process people trying to take the fun away.

The solo setup works.

A good engineer with Claude Code or Codex can move absurdly fast today. Planning, implementation, refactoring, test writing, codebase exploration, documentation updates, throwaway scripts, boring glue code — all of it gets cheaper. I have experienced this myself. My own working style changed dramatically over the last year.

And for greenfield projects, prototypes, internal tools, or narrowly scoped tasks, the experience can feel almost unfair. You have a thought, you shape it a little, the agent runs, and suddenly there is a working thing.

That experience is not an illusion. It is real leverage.

But it is also heavily dependent on the person operating the system. Their taste. Their ability to notice when the agent is drifting. Their local scripts. Their prompt habits. Their willingness to read diffs carefully. Their mental model of the codebase. Their sense for when a task is too large, too vague, too risky, or too underspecified.

In other words: a large part of the system is still inside the person.

That is fine for a solo expert. It is not enough for an organisation.

## The organisational cliff

The moment you move from "one capable person with agents" to "many teams building a real product with agents", the problem changes.

Now the question is no longer only:

> Can an agent produce useful code?

The question becomes:

> Can the organisation repeatedly produce useful, safe, reviewable, maintainable code with agents, across teams, over time?

That is a very different question.

Large organisations have constraints that are easy to ignore when you are working alone. Security. Compliance. Architecture consistency. Release management. Cost control. Auditability. Customer safety. Production observability. Incident response. Domain ownership. Onboarding. IP and data boundaries.

None of these concerns disappear because the coding step got faster. If anything, they become more important, because the system can now produce more change per unit of time. More code, more diffs, more decisions, more surface area.

This is the part I think many AI coding conversations still underplay:

**AI does not only accelerate delivery. It accelerates variation.**

If every engineer invents their own harness, their own prompts, their own review process, their own permission model, their own definition of done, the organisation does not get one coherent productivity multiplier. It gets a lot of local optimisations that may or may not compose.

That can work for a while. It can even look impressive from the outside. But eventually the hidden costs show up downstream: larger PRs, inconsistent quality, missing evidence, brittle reviews, security exceptions, duplicated workflows, knowledge trapped in chat histories, and managers who cannot tell whether the new delivery machine is actually getting safer or just faster.

This is the scaling cliff.

## The competitive pressure is not going away

There is a second reason this matters. Large companies are not only trying to improve themselves. They are being compared to a new category of small, agent-amplified competitors.

A team of two or three strong people with modern coding agents can now build things that previously required a much larger team. They have low coordination cost, short decision loops, little legacy, and almost no internal politics. That does not mean they automatically win. But it does change the pressure on larger organisations.

The old enterprise answer — "we are slower because we are more responsible" — becomes harder to sustain when smaller teams can be fast *and* increasingly competent.

So the challenge for larger organisations is not to become reckless. It is to preserve the advantages that come with scale — trust, domain knowledge, customer relationships, operational maturity — while removing as much unnecessary coordination drag as possible.

That is the actual promise of a software factory:

**Keep the speed. Add the system.**

## The uncertainty is real

Now, before this sounds too neat, I want to name the uncertainty.

Agentic coding is still early. The tools are changing constantly. The economics are not settled. The cost question in particular is far from solved.

Agentic workflows are expensive compared to classic autocomplete. They involve long-running sessions, tool calls, test runs, review loops, context management, sometimes multiple models, sometimes multiple agents. Someone has to pay for all of that. And as the two dominant vendors in this space — Anthropic and OpenAI — move toward public-market scrutiny, whether through actual IPO timelines or the broader [IPO reckoning around the AI arms race](https://www.axios.com/2026/02/05/openai-ipo-anthropic-xai-elon-musk), I would expect pricing, packaging, quotas, and usage restrictions to keep changing. We have already seen the edges of this in the way [third-party agent usage became part of the subscription economics conversation](https://www.axios.com/2026/04/06/anthropic-openclaw-subscription-openai).

This is not a moral judgement. The infrastructure is expensive. The companies building it need the economics to work. But for engineering organisations, it means the stable state is not here yet.

My current bet is this:

If prices for the dominant agentic coding tools keep going up, or if usage becomes more constrained, local and open models become more attractive. Not for every task. Not immediately. But more often than today. The more the premium tools feel like a metered dependency you cannot fully control, the more teams will explore hybrid setups: expensive frontier models for planning and hard reasoning, cheaper or local models for routine execution, review, classification, and mechanical work.

The race for the tool of choice is also not over. Developers are switching fast. Loyalty is shallow when the productivity delta is visible. Tools like [Pi](https://pi.dev/) — a minimal, extensible terminal coding harness — and platforms like [Ona](https://ona.com/) — running background software-engineering agents in cloud environments — are getting attention because the market is still searching for the right shape. The defaults today are not necessarily the defaults in twelve months.

So no, I do not think the future is settled.

But I do think the direction is strong enough that waiting for perfect certainty is the wrong move.

## What I mean by software factory

When I say software factory, I do not mean a bureaucracy that turns engineers into ticket processors.

I mean the system around the models.

The harness. The environment. The workflow. The review structure. The feedback loops. The safety mechanisms. The conventions that make agentic work repeatable instead of heroic.

Concretely, that can include things like:

- Development environments that constrain the blast radius
- Project instructions that are slim, maintained, and actually used
- Rules, ADRs, and documentation that agents can load on demand
- Work sliced into small, independently deliverable increments
- Planning and review loops before implementation begins
- Deterministic scripts for anything the agent should not reason about every time
- Hooks that block unsafe actions instead of politely suggesting better behaviour
- Focused review personas for security, architecture, accessibility, performance, or AI-specific risks
- Browser-driven QA evidence for user-facing changes
- A codification step where lessons become rules, hooks, ADRs, or evals
- Metrics that tell you whether the pipeline is improving or just producing more activity

None of this requires a grand platform on day one. In many cases it starts with boring files and shell scripts.

But the principle matters:

**Do not rely on the agent remembering the right behaviour. Encode as much of the desired behaviour as possible into the system.**

Every time the model has to reason about a rule, it might get it wrong. Every time a tool enforces a rule, the probability surface shrinks.

That is not anti-AI. It is how you make AI useful in production organisations.

## The careful version of going all in

I am generally an advocate for these tools. I would not be spending my time in this field if I thought this was a toy.

But I am not in the "just let it rip" camp.

The more powerful the tools get, the more I care about active risk management. Not because I want to slow everything down. Because without it, the speed is fragile.

My default posture is: give the agent autonomy, but put it inside a system that can absorb obvious failure modes.

That means I prefer small deliverables over giant PRs. A giant AI-generated PR is not a productivity win if the human reviewer has to spend two hours reconstructing the reasoning from scratch. Small, independently useful slices are easier to review, easier to roll back, easier to test, and easier for the agent to reason about.

It also means I want evidence, not vibes.

"The agent says it tested it" is not evidence. A failing test that now passes is evidence. A browser-driven QA run with screenshots is evidence. A focused security review with concrete findings is evidence. A PR that includes the demo artefact is evidence. A task state machine that shows what happened is evidence.

Trust is good. Double check is better.

And the checking should not be a single heroic human gate at the end. That does not scale. The checking has to be distributed through the workflow: planning checks, deterministic checks, automated tests, focused reviews, acceptance evidence, and then human judgement where it matters most.

This is where the factory metaphor is useful. Not because software is literally manufacturing. It is not. But because factories are designed systems. They make work visible. They reduce variance where variance is harmful. They make quality inspectable. They improve through feedback loops.

That is what we need around agents.

## The learning loop is the point

The most interesting part of a software factory is not the first delivery. It is what happens after the tenth, fiftieth, hundredth delivery.

Every time the agent makes a mistake, the organisation gets a choice.

You can fix the output and move on.

Or you can ask: what should change in the system so this class of mistake becomes less likely next time?

Maybe the answer is a rule. Maybe it is a hook. Maybe it is an ADR. Maybe it is a review checklist. Maybe it is an eval. Maybe it is a better task template. Maybe it is a deterministic script that removes a branch of reasoning entirely.

This is where organisational learning becomes concrete. Not "we should remember to do better next time", but "the next agent run will see this constraint automatically."

That is the compounding effect.

The model may improve because Anthropic, OpenAI, Google, Mistral, or someone else ships a better one. You do not control that. Your harness can improve because your organisation learns. You do control that.

I think this distinction matters enormously.

## So what is the appeal?

Because raw agentic coding gives you individual leverage.

A software factory tries to turn that leverage into an organisational capability.

It gives you a way to answer questions that matter once real customers and real systems are involved:

- How do we let agents move fast without touching things they should not touch?
- How do we make work reviewable before it reaches a human?
- How do we keep cost visible?
- How do we avoid every team inventing a different workflow?
- How do we preserve product and architecture knowledge across agent runs?
- How do we make quality improve over time instead of depending on individual vigilance?
- How do we onboard people into the way we work with agents?
- How do we stay competitive with small teams without becoming reckless?

The answer is not one tool. It is not "use Claude Code" or "use Codex" or "use Ona" or "use Pi." The tool layer will keep changing.

The durable thing is the operating model.

The organisations that learn how to design the system around the model will be able to switch tools more easily, manage cost more deliberately, and adopt new capabilities without rebuilding their entire way of working every time the market shifts.

That is the bet.

## Where I am spending my time

This is also where I am spending my professional time now.

I am helping companies make this transition: from scattered individual AI tool usage to deliberate agentic engineering workflows. From "some people are very fast with Claude" to "we understand how agentic delivery works in our organisation." From impressive demos to production-aware systems.

The work is partly technical. Devcontainers, hooks, skills, agents, review loops, QA evidence, pipeline design.

But it is just as much organisational. What should be standardised? What should stay local? Where is the real risk? Which teams should start first? What does good look like? How do we keep engineers in the driver's seat while giving agents more room to operate?

I do not think there is one universal answer yet. Different companies will land in different places depending on their risk profile, architecture, regulation, product maturity, and engineering culture.

But I am increasingly convinced of one thing:

The serious conversation is no longer whether AI can write code.

It can.

The serious conversation is how we design the system in which AI can contribute safely, repeatedly, and with increasing value over time.

That, to me, is the appeal of a software factory.
