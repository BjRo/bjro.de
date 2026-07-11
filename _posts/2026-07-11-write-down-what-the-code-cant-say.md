---
title: "AGENTS.md: Write Down What the Code Can't Say"
permalink: /write-down-what-the-code-cant-say/
excerpt: "I ran 240 Codex trials to test planning workflows and repository guidance. They clarified when AGENTS.md saves tokens, when it adds quality, and when a planner should stop and ask."
---

Over the last months I have been moving more and more of my work to Codex. The main reason is practical: my current client is Codex-first. But if I'm honest, Claude's appetite for tokens and Anthropic's constant game of changing limits have also made the move easier.

Lately I've been using `promptfoo` and other evals quite a bit in client engagements to test skills in a more structured way instead of judging them from one good run. I am still regularly surprised by the gap between what I have seen working well with Claude and what happens when I bring the same idea into Codex.

Planning is one of those areas. Right now we are redesigning parts of the planning workflow at my client, along with what we call the repository's information architecture: the whole `AGENTS.md` dance that helps an agent find its way around the codebase and apply the conventions that matter.

I had a bit of free time and decided to challenge some of those biases with a set of experiments on my own codebases.

The question I started with was narrow: when I port a planning-heavy skill from Claude Code to Codex, should I also transplant Claude's phased planning workflow? Claude prescribes exploration, parallel subagents, design, review, and a plan artifact. Codex gives the model more freedom. My instinct was that the more structured workflow would win once the repository became large enough.

Seven rounds of experiments later, the answer is more useful than that comparison:

**Verify what the code can prove. Write down what it cannot.**

More concretely:

- On a small repository, extra planning machinery produced the same plans at 2.5x the token cost.
- On a 71k-line repository, a verify-before-emit pass fixed the failures that actually occurred: wrong file references. The subagent choreography around it added little.
- When repository guidance such as `AGENTS.md` and linked code maps merely described structure and patterns the agent could derive from clean code, its only measurable benefit was cost: 10-20% fewer tokens, with no improvement in plan quality.
- Under the no-questions setup, information architecture became a quality mechanism only when it supplied something the repository could not: a decision between two valid, competing patterns. One line saying which pattern was canonical changed the agent's choice every time, while no amount of review could infer the missing decision.
- That does not mean every ambiguity belongs in `AGENTS.md`. If the decision has not been made yet, I would rather have the planner surface it, explain the options, and let the human operator decide.

That last result is where this post eventually lands. But the route there matters, because I had to watch two of my own beliefs take damage along the way.

Behind the narrow porting question sits a broader belief about what makes an agentic setup work. I think three ingredients have to fit together:

- **Good skill design.** Reusable skills should encode reusable workflows, not absorb repository-specific documentation just because the workflow needs it. Knowledge that also matters during planning and review should live in the repository's information architecture, where every phase can reach the same canonical source.
- **Good plans.** The agent should explore thoroughly enough to produce a grounded implementation plan, and surface consequential decisions the repository cannot settle instead of silently making them.
- **Good information architecture.** The repository should guide agents during exploration, implementation, and review through its structure, conventions, and competing code patterns.

The boundary between those ingredients matters. A planning skill should not compensate for missing repository knowledge by carrying a tour of one particular codebase. And an `AGENTS.md` should not become a second implementation plan. Skills can route to repository knowledge, but they should consume that knowledge rather than own its canonical copy.

## Two different approaches to planning

Both tools have a plan mode, but the name hides two rather different philosophies.

**Claude's plan mode is a harness-enforced pipeline.** Tools are gated read-only, the plan lives in a dedicated file, leaving plan mode is an approved tool call, and the prompt prescribes a phased workflow. Exploration can fan out to parallel subagents before one agent designs and reviews the plan.

**Codex's plan mode is a prompt-enforced conversation.** The model judges which actions count as implementation, the plan stays inline in the chat, subagents are optional, and only the user can end the mode. The harness describes the boundary and trusts the model to apply it.

I got those details from the instructions actually injected into local session transcripts in early July 2026, not from asking either model to paraphrase its own system prompt. The exact versions matter here: Claude Code on Claude Fable 5, and Codex CLI 0.143.0 at the start of the series. All benchmark runs used gpt-5.5 at medium reasoning effort.

My instinct is to side with Claude here. If a rule matters, I would rather have the harness enforce it than hope the model remembers. So I wanted to know whether Claude's planning process was also worth carrying over.

## Two experiments, not seven unrelated rounds

The sequence is easier to understand as two connected experiments. Within any comparison, I changed either the planning process or the repository context, not both.

1. **Planning process (rounds 1-3).** I kept the model, task, and repository fixed within each comparison and changed the planning instructions. The question was whether a more elaborate workflow produced a better plan, and which part helped.
2. **Repository context (rounds 4-7).** I compared the same planner on the same tasks while changing the guidance and ambiguity inside the repository. The question was when `AGENTS.md` improved quality rather than merely saving exploration.

The first experiment used three planning conditions in headless Codex. All three received the same output contract: do not ask questions, choose a recommended default and record it as an assumption, produce a plan in four required sections, cite repository-relative paths, and do not change files.

That no-questions rule is important. It models the end of the spectrum that people often call a **dark factory**: the agent has to keep moving without waiting for human clarification. That is useful for testing autonomous performance, but it is not my preferred planning style for genuinely novel work. When the repository contains no precedent for a consequential decision, I want the agent to surface the uncertainty, explain the options and trade-offs, make a recommendation, and let the human operator decide.

On a mature repository, most maintenance and pattern-adjacent work should not need that interview. The code and its information architecture ought to provide enough precedent for the agent to proceed. The interesting boundary is work the repository has not seen before.

| Planner | What it added to the shared contract |
|---|---|
| **Native** | Nothing. Codex could explore and plan however it wanted. |
| **Reviewed** | Before answering, re-open the relevant files and verify every cited path and symbol. No forced phases or subagents. |
| **Claude-style** | Force scoped exploration with up to three subagents, then design, verify the draft against the repository, and emit the plan. |

Put simply: **native plus verification became reviewed; reviewed plus forced exploration and phases became Claude-style.** This nesting is what allowed round 3 to isolate the useful part of the larger workflow.

I did not want an LLM judge picking whichever plan sounded more impressive. Instead, I planted facts in fixture repositories and checked the resulting plan text against them:

- **Grounding:** every existing path named in the plan had to exist.
- **Reuse:** the plan had to find an existing helper instead of reinventing it.
- **Trap avoidance:** stale documentation pointed at a deprecated module; the code contained the real replacement.
- **Coverage:** a cross-cutting change had to reach every subsystem it genuinely affected.

By the end, this had stopped being a small A/B test. The full series comprised **240 recorded benchmark attempts across two days**. Of those, 235 returned plans; five died in the harness or produced no output. Adding up the recorded trial durations gives just over **10 hours of runner wall-clock time**. That excludes smoke runs, fixture construction, repairs to the evaluation harness, and the very non-automated time I spent reading failures.

There were five trials per condition and case unless noted otherwise. The rubric and decision rule were written before the runs. Each returned plan went through the deterministic checks first, and I kept those raw results. I then inspected every failed check by hand and reported the corrected result separately when the check had rejected a valid plan, a better solution, or an infrastructure failure rather than a planning failure. There was no LLM judge picking a winner.

Each round answered a problem exposed by the round before it:

1. Start on small repositories. When both planners hit the ceiling, move to an older, more complex project of mine that was originally built with Claude Code.
2. Observe that the real failures are wrong path references. Add the reviewed condition to isolate verification.
3. Look more closely at the first results and realise that the old project still contains its Claude Code setup plus an early attempt to port that setup to Codex. Strip those files out to test whether they have been helping.
4. See almost no change, then notice a fixture-design mistake: I knew Codex does not autoload nested `AGENTS.md` files, but I had forgotten to tell the agent preparing the experiment to rework the Claude-oriented setup. Rebuild the routing explicitly for Codex.
5. Watch the routed maps introduce bad paths, then correct their path anchors and rerun.
6. Find that clean code makes most maps redundant. Introduce two valid patterns so the code can no longer reveal which one is canonical.

The rounds are numbered chronologically, but this cause-and-effect chain is the important part.

## Experiment 1: Does more planning process help?

Rounds 1 through 3 changed the planning instructions while keeping the task and repository fixed within each comparison.

### Round 1: A ceiling on small repositories

Round 1 was a ceiling test without enough ceiling.

The fixture repositories contained seven files and one planted truth each. Native and Claude-style Codex both passed all fifteen trials. The prompt change clearly took effect: every Claude-style trial spawned three explorer subagents, while native spawned none. It simply had no quality gap to close.

| Small repositories | Native | Claude-style |
|---|---:|---:|
| Accepted plans | 15/15 | 15/15 |
| Tokens per trial | 108k | 267k |
| Wall-clock per trial | 50s | 117s |

The wrong conclusion would have been that the workflow does nothing. The fair conclusion was that a strong model can inspect seven files without a committee.

### Round 2: The ceiling breaks on a real repository

So round 2 moved to an older project of mine that was originally built with Claude Code. I chose it because I wanted something genuinely more complex: a monorepo with roughly 71,000 lines of Go and TypeScript, GraphQL, an ORM, background jobs, LLM pipelines, migrations, a frontend, and partially stale documentation.

The three tasks were based on actual repository archaeology:

1. Plan an asynchronous LLM feature that should reuse an existing pipeline and its resilience helpers.
2. Plan a curation feature spanning migration, domain, repository, GraphQL, frontend, and PDF assembly.
3. Extend a pipeline whose old architecture document still described a coordinator file that had since been deleted.

This time the ceiling broke, but not where I expected.

Native returned fourteen plans. Twelve were acceptable after inspection; two contained genuinely wrong paths. One cited `internal/graphql/generated.go` instead of `internal/graphql/generated/generated.go`. Another shortened two paths until they no longer resolved from the repository root. A fifteenth run exited without producing a plan. Claude-style returned fifteen acceptable plans and did not cite one bad path.

The stale-documentation trap fooled neither condition. Both checked the document against the current code and used the real pipeline. Both found the broad integration chain. The meaningful difference was much more mundane: **the Claude-style plans were better grounded.**

They were also more detailed in useful places. They mentioned ownership helpers, dataloader requirements, and sibling pipelines more often. But they cost twice as many tokens.

That failure pattern suggested a simpler hypothesis. Perhaps the subagents were not the active ingredient. Perhaps the useful sentence was the one at the end: re-check every path and symbol before emitting.

### Round 3: Isolating verification

Round 3 ran the reviewed planner on the same three cases and compared it with the native and Claude-style results from round 2; those two baselines were not rerun. The model, effort level, and harness settings stayed the same, but this still introduces the possibility of day-to-day model variance.

Conceptually, the comparison remained cumulative: native was the baseline, reviewed added verification, and Claude-style added forced exploration and phases on top.

| Planner | Plans with genuine grounding failures | Tokens per trial | Time per trial |
|---|---:|---:|---:|
| Native | 2 of 14 returned* | 0.66M | 178s |
| Reviewed | 0 of 15 | 1.15M | 235s |
| Claude-style | 0 of 15 | 1.34M | 272s |

*One of the fifteen native runs exited without producing a plan.

One paragraph of review discipline closed the observed grounding gap and recovered most of the depth. Removing the phases and subagents saved only about 14% compared with the full workflow, though. The reviewed condition still cost 74% more than native.

That changed my interpretation: **verification is where both the value and the cost live.** Checking claims means reading the files again. The orchestration around that check added some depth, but it was not doing most of the work.

There was a useful evaluation lesson in the one reviewed plan that initially failed its automated check. It discovered that the backend already stored the data the feature needed and proposed a thin API over it. My rubric expected a new LLM pipeline, so the better plan missed the regex. Deterministic checks are valuable, but checks that prescribe a solution shape are brittle. Grounding checks plus human inspection carried much more weight than keyword coverage.

For the original porting question, I now had an answer:

**Keep the output contract and the verify-before-emit pass. Skip the phased subagent workflow by default.** On a small repository, even the review pass is probably wasted work. On a large repository, pay for it when reference accuracy matters.

Then I noticed the confound.

## Experiment 2: When does AGENTS.md help?

Only after looking at the first results did I notice something obvious in hindsight. The old repository was not a neutral codebase. Because it had originally been built with Claude Code, it still contained Claude-specific setup files, nested area maps, command guidance, and hundreds of other agent-facing files. It also contained an early attempt to port parts of that setup to Codex.

I had chosen the repository for its complexity and overlooked the context scaffolding that came with it. Perhaps native Codex looked competent because the repository had already handed it a map.

This was inconvenient for the plan-mode experiment and irresistible as a new experiment. I have argued that deliberate agent information architecture is one of the biggest levers available to a team. Here was a chance to stop arguing and measure it.

From this point on, the planning workflow was no longer the main variable. I kept using the native and reviewed planners and changed what the repository made available to them.

### Rounds 4 and 5: Remove the repository guidance

Round 4 removed 239 agent-facing files, including instruction files and agent-specific setup and support directories. Round 5 also removed the architecture documents, decision records, and demos. The result was bare code, migrations, tests, and a few READMEs.

Almost nothing happened.

| Repository context | Native plans accepted | Reviewed plans accepted |
|---|---:|---:|
| Agent files removed, human docs retained | 13/15 | 15/15 |
| Agent files and human docs removed | 15/15 | 15/15 |

Bare native used roughly 10% more tokens than the fully documented repository, but plan quality did not fall.

The codebase was consistent enough to explain itself. One repository pattern dominated. Sibling features had parallel names. Resolvers, migrations, and workers lived where their neighbours suggested they would live. The model spent a little more to rediscover those facts, but it rediscovered them.

This result needs a caveat. Codex CLI updated from 0.143.0 to 0.144.1 during round 4, so comparisons across that row inherit some harness-version noise. Then four of five Claude-style trials in the final case hit the Codex usage limit; one of those trials also surfaced a new validation around full-history subagent spawning. I treated the ported row as contaminated and stopped carrying that condition into later rounds rather than pretend those failures said anything about plan quality. The remaining information-architecture rounds compare native and reviewed planning on the same newer CLI version.

### Rounds 6 and 6b: Put the maps back, then fix them

There was another problem with round 4, and this one was mine. Codex does not autoload nested `AGENTS.md` files. I knew that, but when I asked an agent to prepare the experiment, I forgot to tell it to rework the Claude-oriented nested setup for Codex. As a result, round 4 partly measured the removal of guidance Codex had not been receiving automatically. That did not prove much.

Round 6 therefore rebuilt the repository guidance for Codex: a small root instruction file routed relevant tasks to a backend or frontend map. The maps were read in every trial. Progressive disclosure worked exactly as intended, and token use fell by about 14%.

The router worked, but my first fixture contained a path mistake. I had copied the maps from Claude-oriented instructions nested inside `src/backend/`, where entries such as `internal/export/assembler.go` were relative to that directory. Once the root `AGENTS.md` routed Codex to those maps, the session still expected repository-root-relative references. Codex copied the nested-relative paths into its plans without rebasing them, so the reviewed condition fell from fifteen accepted plans to twelve.

The rule is specific: **when a root `AGENTS.md` routes Codex to a nested document, file paths inside that document need to be repository-root-relative, not relative to the nested document's directory.**

Round 6b changed one thing: every path in the maps became root-relative. `internal/export/assembler.go` became `src/backend/internal/export/assembler.go`, and the other paths followed the same rule.

The three repository-context variants now looked like this:

| Repository context | Plans accepted (native vs reviewed) | Map-induced failures (native + reviewed) | Tokens per trial (native vs reviewed) |
|---|---:|---:|---:|
| Bare code | 15/15 vs 15/15 | 0 | 0.72M vs 1.42M |
| Routed maps with directory-relative paths | 14/15 vs 12/15 | 4 (1 + 3) | 0.62M vs 1.22M |
| Routed maps with root-relative paths | 13/15 vs 15/15 | 0 | 0.65M vs 1.13M |

The reviewed condition recovered completely, and the token saving remained. Native still produced two path truncations in the fixed-map runs, but inspection showed they were its familiar self-generated errors, not paths copied from the maps.

This left me with a much narrower claim for repository maps. On a convention-clean codebase, they can save exploration cost. They are not automatically a quality improvement, and their path convention is part of the interface because models reuse what we give them verbatim.

### Round 7: Give the code two valid answers

Rounds 4 through 6 only tested information that could be derived from the code: where files live, how neighbouring features work, which integration chain is common. A capable model can recover that when a repository speaks with one voice.

Legacy repositories often do not.

For round 7, I introduced a second persistence pattern. Fourteen repositories used the established ORM style. Two used raw `database/sql` in a separate package. Both patterns compiled, both were wired into the application, and neither was marked deprecated.

Then I created two opposite instruction variants:

- Variant A said the raw-SQL package was the destination of an active migration and all new repositories should use it.
- Variant B said the raw-SQL package was an abandoned experiment and all new repositories should stay with the ORM.

The task was to plan persistence for a new entity. The code was identical in both variants. Only that one routing instruction changed.

| Repository instruction | Planner | Chosen pattern | Tokens per trial |
|---|---|---|---:|
| None (10 trials) | Native | ORM 10/10 | 0.31M |
| "New repositories use raw SQL" (5 trials) | Native | Raw SQL 5/5 | 0.38M |
| "Raw SQL was abandoned" (5 trials) | Native | ORM 5/5 | 0.28M |
| None (10 trials) | Reviewed | ORM 10/10 | 0.53M |

Without guidance, the model followed majority gravity every time. The two-file raw-SQL pattern was never extended. One line of information architecture flipped the outcome five out of five times against that fourteen-to-two majority, then flipped it back five out of five times in the opposite variant.

The review pass changed nothing. It spent 72% more tokens and still chose the majority pattern in all ten trials. That is not a review failure. There was no repository fact to check. Both implementations were real, and only the team could know which one represented the future.

Because questions were forbidden, the bare planners had only one move left: choose a heuristic. They followed the majority. Round 7 therefore does not show that information architecture is the only way to resolve ambiguity. It shows that in a dark-factory setup, either the missing decision already exists in the repository guidance or the model has to guess.

An interview-style planner has another option. It can point out that two live persistence patterns exist, explain the evidence and consequences, recommend one, and ask which future the team actually wants. I did not test that condition here, but for genuinely new work that is the behaviour I prefer.

The distinction is simple: instruction files are where resolved uncertainty belongs. Planning interviews are where unresolved uncertainty should become visible.

## What is worth writing down

This is where the two experiments join. The first says what the planner should verify against the repository. The second says what the repository must state because verification cannot recover it.

The three-part setup still makes sense to me, but the responsibilities are sharper now: the skill carries reusable process, the plan carries verified references and surfaces unresolved decisions, and the information architecture is the shared knowledge layer for stable repository-specific decisions and constraints across planning, implementation, and review.

The important split is not only whether the code can reveal something. It is also whether the missing decision has already been made.

**Derivable knowledge** includes file locations, dominant patterns, and the shape of sibling features. On a clean repository, the model can recover this from code. A map may save 10-20% of the exploration cost, which can be worthwhile at scale, but repeating the code in prose did not improve the plans I measured.

**Settled knowledge that the repository cannot reveal** includes mixed patterns where the team has already chosen one as canonical, migrations in progress, abandoned experiments, organisational policy, and security constraints. If the answer is stable and future agents should follow it, this belongs in agent-facing instructions.

**Open decisions** are different. For genuinely new work, there may be no correct answer yet. The plan should identify the decision, bring the relevant evidence, explain the options and trade-offs, make a recommendation, and let the human operator choose. Pretending that this uncertainty does not exist just to keep the factory dark is not autonomy; it is guessing.

Once an open decision has been made and is likely to recur, write it down. That is how the interview and the information architecture form a learning loop: the next similar task can proceed without asking the same question again.

The practical rules that follow from this are straightforward:

1. For maintenance and pattern-adjacent work on a mature repository, aim for autonomous planning. Start with the model's native behaviour and add a verify-before-emit pass when repository size or risk justifies the extra cost.
2. Do not add subagent choreography by reflex. In this experiment it bought marginal depth for roughly 15% more tokens than review alone.
3. Keep canonical repository knowledge outside individual skills. Let skills route to shared references so the same knowledge guides planning, implementation, and review.
4. When the code cannot settle a consequential choice, do not silently default. Put an already-made decision in the information architecture; surface a genuinely open decision in the planning interview. Codify reusable answers afterwards so future work can proceed without asking again.
5. Treat maps of derivable facts primarily as a cost optimisation. Route them only when relevant, and make every path resolvable from the session root.
6. Inspect failed evals. A check can be deterministic and still encode the wrong idea of a good solution.

## What this does not prove

This is one model at one reasoning level, a handful of planning tasks, and one real codebase with unusually consistent conventions. It measures whether plans clear a grounding and coverage floor, not whether a fresh agent can implement them successfully.

Most importantly, every run prohibited questions. That deliberately models a dark-factory setup, but it means the experiment does not compare information architecture with interview-style planning. Round 7 shows what a no-questions planner does when the repository cannot arbitrate; it does not show that asking would be the wrong response.

The results are a snapshot from July 2026, and the CLI update in the middle of the series is a practical demonstration of how short the half-life can be. A chaotic legacy repository may get much more quality value from a map than mine did. Interview-style planning and external organisational knowledge remain untested here. So does the specific comparison between knowledge embedded in a skill and the same knowledge delivered through shared repository guidance; that ownership rule is an architectural inference from the cross-phase needs, not a measured result.

Still, the shape of the evidence is useful. I began the experiment expecting the elaborate planning workflow to matter, and expecting my repository instructions to matter even more. The first mostly reduced to verification. The second split cleanly in two: when it explained knowledge already derivable from the code, information architecture was a cost optimisation and nothing more. Under the dark-factory constraint, it became a quality mechanism when it supplied a settled decision that the repository could not reveal.

That fits the autonomy model I actually want. On a mature repository, maintenance and work close to established patterns should mostly proceed without interruption. When a task crosses into genuinely new territory, stopping to surface a consequential decision is not a failure of autonomy. It is the planner doing its job. If the answer becomes reusable, writing it back into the repository expands the area that future agents can handle on their own.

That is a sharper claim than the one I started with:

**Verify what the repository can prove. Write down what the team has already decided. Ask about what is still open.**

Then turn reusable answers into repository knowledge, and make sure every path tells the truth from where the agent is standing.
