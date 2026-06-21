---
title: "Maybe the Pull Request Is the Problem"
excerpt: "A recent paper argues human code review is redundant. I have mixed feelings — and I think the bigger question isn't whether to keep reviews. It's whether the pull request itself has run out of road."
---

A [recent paper](https://arxiv.org/pdf/2606.13175) by Martin Monperrus opens with a fairly bold claim:

> "Coding agents have reached a capability threshold at which human code review is redundant and should be replaced by agent-driven verification."

Honestly, I have mixed feelings about that verdict. Let me try to unpack why.

## Where I think the paper is right

A few of the underlying observations match what I see in the field.

**PR reviews really are becoming the next bottleneck.** Every additional team member increases output, and at some point the review queue is where all of that output piles up. I have experienced this myself, and I have heard enough other people describe the same dynamic that I would treat it as a stable pattern, not a one-off.

**Agentic review and rework can plausibly be part of the delivery pipeline.** Multi-level reviews with adversarial passes, focused review lenses, and automated rework loops are not science fiction. I have built versions of this myself, and the experience is good enough that I expect this to become standard.

**Developers increasingly become specifiers and orchestrators.** This is close to reality too. The "product engineer" archetype keeps showing up more often when agents are in the mix. And in parallel, we are quietly seeing the revenge of agile principles in new clothes. Shifting security requirements or QA acceptance criteria left was good practice long before anyone was talking about agents. Sadly, only a handful of companies ever really went all in on it. Now that agentic capabilities have become a major productivity lever everyone wants to benefit from, the pressure to actually do shift-left is finally there.

So far, so reasonable.

## What I think the paper underplays

Where I start to push back is on what is missing from the argument.

**Before declaring reviews dead, what can we do to make them more time efficient?** Part of the answer is using agents to do verification. Fine. But the other part is evidence. There is a big opportunity to capture and attach evidence as part of the contribution itself — protocols, screenshots, videos, demo artifacts, traces. A review where the contribution comes with a credible evidence package is a fundamentally different exercise than a review where the reviewer has to reconstruct what happened from a diff.

**We are nowhere near being able to shift accountability fully to agents.** Agents will not get woken up at night during an on-call. They will not get yelled at when there is a data privacy issue or a significant regression with monetary impact. Real people's heads will be on the line if that happens. There is no "well, the agent messed up" path where things just move back to normal. So *human contribution stewards* — one of my new favourite terms — have to stay in the loop. They need to know what is going live.

**Verification does not end when the code review is passed.** That is only half of the picture. What really matters is when the code hits production.

Which leads me to the question that has been sitting with me since I read the paper.

## Maybe the "pull request" part is the problem

I get a weird sense of déjà vu in a lot of agentic engineering conversations. So much of it feels like either a consequence of flow-oriented practices, or something we could just take from what advanced teams have been doing for quite a while.

My current suspicion is that the pull-request-based concept has reached the end of its usefulness in the mid term. Not now. But potentially soon.

What we should focus on instead is the agentic version of trunk-based development: feature toggles, agentic gradual rollout, and agentic release management — at least in the places where we have the luxury of doing continuous integration.

What I mean by that is doing all the verification we already talked about, but then also:

- rolling out the code in steps
- monitoring the rollout through observability tools
- having clear decision thresholds in place for when something needs to be rolled back
- being able to act on those thresholds automatically, e.g. by deactivating a feature toggle when something goes wrong

In that world, the review is no longer a single human gate at the end of a branch. It is one signal among many in a continuous, observable, reversible flow.

But that is probably a post for another time.
