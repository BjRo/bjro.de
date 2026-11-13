---
published: false
title: "Editorial evidence — Adaptive Goal"
---

Research notes for `software-factory-part-3-adaptive-goal.md`. This is editorial material, excluded from publication.

Researched November 13, 2026. Darrow checkout: `de1809c8bcd2dc29ce8d664fbf4ba8b36a259e89`. No Darrow files changed. User confirmed use of the latest documented benchmark figures.

## Editorial approach

The story moves from the personal experiment, through a benchmark that challenged the architecture, to a smaller native-goal design. The ending retains the value of explicit delivery discipline without claiming an efficiency or reliability advantage the data does not establish.

The opening and phrasing are drafted in the author's voice using the supplied recollections. Career details, the early all-Opus setup, the evolution to fixed per-stage routes, teaching the approach, and the over-curated pet-project benchmark are author-supplied accounts, not independently documented benchmark results. No personal anecdote was invented to connect the experiments.

## Corrections to the rough outline

- **Conference:** Agentic Conf Hamburg was March 22, 2026, rather than February. The organiser's news page states the date; the blog's March 23 post also describes attending the preceding Sunday. Recording: https://www.youtube.com/watch?v=b8ds_VX8kYY . Organiser: https://agentic.hamburg/news/blog/2026/talk-video-bjrn-rochel-you-dont-need-a-bigger-model-you-need-a-better-ai-harness/ . The recording was located, not transcribed or watched in full; descriptions of the original pipeline rely on the author's account and retained reference implementation/benchmark.
- **Corpus:** The original main comparison uses four projects, not five. The manifest contains seven source snapshots, with three additional projects used in later work. Avoid saying all seven belong to the same experiment.
- **AI exposure:** Pre-Copilot source snapshots are supported by the corpus manifest. They do not establish absence from model training. Avoid “uncontaminated” or “AI-free benchmark” claims.
- **Efficiency:** The November 3 PR #293 workflow calibration reports 2.47× estimated cost and 1.94× elapsed time versus historical vanilla controls. Two four-task passes were 2.72× and 2.22× cost. This pilot and the 2.5× adoption target are separate evidence; quality preservation remains unproven.
- **Historical pipeline costs:** Original child usage cannot be fully reconciled. Do not turn lower bounds into full costs or repeat a precise 4–8× cost range as a measured result.
- **Time direction:** The October workflow takes roughly 3.7× as long; it is not “two times the speed.”
- **Placement:** A lower selected route does not always launch a delegate. Current policy retains direct execution when handoff would not repay itself.
- **Readiness/review:** These are selected capabilities, rather than mandatory product-specific stages in every goal. Routine risk can omit independent review; explicit user requirements can strengthen assurance.
- **Ownership:** Current native goal ownership remains in the main thread. Historical separate-owner and goal-loop-controller experiments must not be described as the current architecture.
- **Repair:** The current default is two implementation repair attempts total, shared across providers and findings. Follow-up stays within findings and repair-caused regressions; important scope changes can require broader reassessment.
- **Quality:** Passing original checks is not evidence of equivalent quality. Click and go-git supplemental probes expose different defects on different candidates. Both belong in an honest treatment.

## Source map

All paths below are relative to `/Users/bjro/Sources/darrow` unless specified. Public equivalents use `https://github.com/BjRo/darrow/blob/de1809c8bcd2dc29ce8d664fbf4ba8b36a259e89/` plus the path. Reader-facing benchmark citations are pinned to this revision. Product and experiment-directory links use `main` for current navigation.

| Claim | Source | Interpretation |
| --- | --- | --- |
| Series architecture, voice, transition to adaptive-goal | Blog `_posts/2026-10-30-how-id-build-a-software-factory.md`, `_posts/2026-11-06-software-factory-part-2-capabilities.md` | Preserve the established foundations → capabilities → orchestration → recipes progression. |
| Client LangGraph experience | Blog `_posts/2026-10-25-sevro-agentic-evals.md` | Personal setup observation, not a claim that every graph runtime is slower. |
| Source pins and provenance | `evals/corpus/orchestration/manifest.yaml` | Click 8.0.0, Ajv 8.5.0, go-git 5.4.2, Express 4.17.1; later Requests 2.25.1, Commander 7.2.0, Cobra 1.1.3. |
| Task scope and checks | `evals/experiments/orchestration/cases/oss-click-streams.yaml` and case inventory | Bounded behaviour contracts; no assumption that exact upstream patches are the sole valid implementation. |
| Original four-way failure | `evals/experiments/orchestration/snapshots/2026-08-07-n1.md`, companion JSON | N=1 per task/configuration. Codex pipeline 3/4 vs vanilla 4/4, 1110.4s vs 216.5s = 5.13×. Claude pipeline 2/4 vs vanilla 4/4, 1404.0s vs 413.9s = 3.39×. Child costs incomplete. |
| Fixed preflight and subsequent routing experiments | `evals/experiments/orchestration/README.md`, `snapshots/2026-08-09-adaptive-goal-sol-baseline-n1.md` | Earlier routing benefits are narrower than whole-workflow value. August 9 retrospective: 61% lower reconstructed cost vs raw Sol/medium on three unchanged contracts; non-contemporaneous runs and changed tooling. Do not compare directly with October as a controlled trend. |
| Workflow/risk ablation | `snapshots/2026-08-08-workflow-risk-heldout-n1.md` | All pass; workflow +1.6% time vs raw, workflow+risk +33%. Small already-passing corpus cannot establish risk-gate benefit or futility. |
| Fidelity and failed hypotheses | `evals/experiments/orchestration/adaptation-fidelity.md` | Separate passive shipped behaviour from eval-only enforcement; successful task does not prove the requested route actually ran. |
| Current goal architecture | `plugins/orchestration/darrow-adaptive-goal/README.md`, `skills/adaptive-goal/SKILL.md`, `docs/specs/adaptive-goal.md` | Main-thread owner; separate difficulty and consequence; configurable routes; handoff economics; selected readiness/assurance; retained implementor for repairs; bounded assignments. |
| Name and historical ownership | `docs/research/adaptive-goal-rename-2026-10-05.md`, `docs/research/adaptive-delivery-main-thread-owner-2026-10-01.md` | Renamed from adaptive-delivery; recorded historical names are preserved in source evidence. |
| Main-thread continuation caveat | `docs/research/adaptive-delivery-main-thread-owner-2026-10-01.md` | Old headless CLI ended at first-turn completion. The draft does not claim native goals complete reliably in every CLI/adapter. |
| October full-delivery comparison | `snapshots/2026-10-30-review-medium-n1.md`, companion JSON; parent directory `evals/experiments/orchestration/` | Eight valid candidate cells, archived vanilla controls, N=1. Experimental reader route and observer repairs. |
| Earlier October first pass | `snapshots/2026-10-30-codex-n1.md` | Xhigh baseline includes interrupted Click delivery and partial usage. Do not mix its three assessed pairs with the follow-up's four-task means. |
| Real independent review contribution | `snapshots/2026-10-30-review-medium-n1-click-probe.json`, follow-up report | Binary `writelines` bypasses `write` override. Earlier xhigh repaired, medium implementation missed, medium delivery repaired; both vanilla candidates missed. Supplemental post hoc probe, not original acceptance. |
| Counterexample and test integration | `snapshots/2026-10-30-implementation-comparison.md`, companion JSON | Go-git xhigh delivery mishandles reordered/duplicated prefix edits; both readers had cleared it. Vanilla passes these probes. Ajv latest recipe test file falls outside upstream normal discovery pattern. Retained-source reconstruction limits apply. |
| Later assurance coordination | `docs/research/assurance-coordination-291.md` | Capable coordination runs inline, independent readers remain fresh. Functional evidence does not satisfy the separately required matched cost/quality study. Full Claude compositions have unresolved completion evidence in the record. |
| Isolated ticket workers | Current Adaptive Goal README; commit `e65722e9` | Present capability, no claimed measured savings. |
| Review axes and fix verification | `plugins/capability/darrow-review/README.md` | Applicable Standards and Spec axes, fresh independent readers, closed follow-up; reviews are not infallibility guarantees. |
| Attribution | Matt Pocock upstream `skills/productivity/grilling/SKILL.md` and `skills/engineering/code-review/SKILL.md` | Both public source pages checked. Credit attached to the relevant ideas in the post. |

## November 3 calibration and numbers used in the illustration

The later [PR #293 benchmark](https://github.com/BjRo/darrow/pull/293) and local `/Users/bjro/Benchmarks/darrow/2026-11-03-pr293-comparison-69637dfe-system-skills-v2/pr-benchmark-section.md` report updated Adaptive Goal at 2.47× estimated cost, 1.94× elapsed time, and 3.94× total tokens against historical November 2 vanilla controls. Eight updated workflows completed and passed, two per category: Click binary-writelines repair, Express links, label-helper refactor, readiness-skill prose edit. Means are category-weighted. Runs used differing runtime profiles; controls were not contemporaneous. The frozen-defect quality study was interrupted by model capacity. No new quality equivalence or full ticket-to-PR estimate follows. Candidate `69637dfe08fcd14e68872ffcd04ecaec5fff2ef7` is a PR head; the PR was squash merged, so it is not an ancestor of current main.

The November chart uses $0.08828055 → $0.21801775, 84.4983s → 163.5698s, and 191,672 → 755,135 total tokens. Ratios are 2.47×, 1.94× and 3.94×. The two four-task cost passes are 2.72× and 2.22× versus the historical vanilla mean.

The following October comparison is retained for context; it is no longer the chart's source.

Latest medium-review follow-up, means over four tasks:

| Track | Vanilla seconds | Darrow seconds | Time ratio | Vanilla estimated USD | Darrow estimated USD | Cost ratio |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Implementation | 132.2 | 485.9 | 3.68× | 0.187 | 0.829 | 4.43× |
| Delivery | 176.0 | 649.3 | 3.69× | 0.236 | 1.205 | 5.11× |

Time labels are rounded to one decimal minute. Cost ratios use the displayed means, consistent with the experiment README. Counts are 4/4 original acceptance passes per condition, not defect-free rates. Optional review was omitted in the Ajv implementation candidate. Full delivery adds work and a different assurance process; it is not an equal-assurance ablation. Costs include participant parents/delegates using dated API-equivalent cached-input pricing; preparation, grading, subscriptions and real GitHub queues are not represented.

## Illustrations

- `assets/images/adaptive-goal-context-tax.svg`: conceptual comparison; not a measured breakdown of token cost or a complete historical stage list.
- `assets/images/adaptive-goal-accordion.svg`: current architecture; optional expansions and repair bound. Each parallel assignment is bounded; the owner retains acceptance and completion.
- `assets/images/adaptive-goal-benchmark.svg`: November 3 calibration ratios; all bars share a vanilla=1 scale. Labels retain the historical controls, estimated costs, and different-assurance limitations.
- `assets/images/adaptive-goal-initial-benchmark.svg`: August 7 initial comparisons within each harness; four tasks and one trial per configuration. Bars show mean elapsed time per task; labels show task-contract outcomes. Pipeline cost was incomplete.
- `assets/images/adaptive-goal-before-after.svg`: conceptual summary of the design change; it does not compare measurements across different benchmark corpora.

Each illustration has a `-mobile.svg` variant selected through a responsive `picture` element. Diagrams are native SVG; benchmark charts were generated with Matplotlib. All assets contain accessible titles and descriptions, with additional HTML alt text and captions.

## Publication notes

The draft remains in `_drafts`; no publication, commit or push performed. It is approximately 3,200 words including front matter and captions. The story could be shortened by moving some benchmark caveats to a footnote, but retain the sample size, different-assurance distinction, later-version limitation, and go-git counterexample.
