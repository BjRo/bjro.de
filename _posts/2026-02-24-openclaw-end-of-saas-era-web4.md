---
title: "OpenClaw, the End of the SaaS Playbook, and the Arrival of Web 4.0"
seo_title: "OpenClaw and the Arrival of Web 4.0"
excerpt: "OpenAI's OpenClaw hire was framed as a talent acquisition. Zoom out and you see a platform stack being assembled in plain sight — model, agent, app store, distribution, device. The opening move of Web 4.0."
---

After twenty years in software engineering, I took a sabbatical. The first real break of my career. I told myself I'd rest. What actually happened is that I spent months going deep on agentic engineering — building with Claude Code, MCP servers, agent orchestration — and trying to make sense of where this industry is heading. Not for a client or an employer. For myself. I'd already been [writing about how AI changes our day-to-day work](/ai-and-existential-fears/), but the more I built, the more I saw a pattern forming that was bigger than tooling. I needed to understand what I was looking at before deciding what the next chapter of my career looks like.

I'm still figuring that out. But I have a thesis now, and it's not a small one.

Then OpenAI [brought in Peter Steinberger](https://techcrunch.com/2026/02/15/openclaw-creator-peter-steinberger-joins-openai/), the creator of OpenClaw — the open-source AI agent that gained [150,000+ GitHub stars in weeks](https://nathanowen.substack.com/p/openclaws-clawdbot150k-github-stars), the fastest-growing project in GitHub's history — and the pattern clicked.

Most coverage treated the acquisition as a talent hire. A prominent open-source developer joins a well-funded AI lab. Story over. But zoom out: OpenAI is now assembling a proprietary model (GPT), an open-source agent layer (OpenClaw), a skill marketplace (ClawHub), access to users through WhatsApp, Telegram, and Signal, and hardware ambitions through the [Jony Ive acquisition](https://openai.com/sam-and-jony/). That's not a talent acquisition. That's model + agent + app store + distribution + device. To me, it looks like a platform stack being assembled in plain sight. And the structural echo — open-source engine absorbed into a consumer platform with proprietary services on top — is familiar. It's the Google/Android pattern. Not a prediction that the outcome will be the same, but a recognition that the *type of move* is the same.

## The Web 4.0 Frame

Here's the pattern I see:

| Era | Model | Platform gatekeepers |
|-----|-------|---------------------|
| **Web 1.0** | Read | Yahoo, AOL, early Google |
| **Web 2.0** | Read / Write | Google, Facebook, Apple, Amazon |
| **Web 3.0** | Read / Write / Own | Failed — no durable consumer platform |
| **Web 4.0** | Read / Write / Delegate | LLM platforms — being assembled now |

The numbering is a convention for magnitude of shift, not a claim about logical progression. What matters is the consistent pattern: each prior shift created new distribution gatekeepers. Microsoft dominated the desktop. Google dominated the web. Apple and Google dominated mobile. Whoever controls the primary user interface captures the platform economics — and distribution is the moat.

Web 3.0 was supposed to be the next shift, but it was a solution looking for a problem. Decentralised ownership wasn't something consumers needed. It failed to produce a single durable consumer platform.

Web 4.0 — LLM-mediated delegation — starts from a problem everyone actually has: digital life is fragmented, adversarial, and labour-intensive. You manage thirty apps with thirty accounts, do your own research, navigate dark patterns, and perform the labour of synthesis and decision-making yourself. The delegation model inverts this.

Now, the individual pieces of this thesis are all over the news — OpenClaw's viral growth, MCP's release, AI coding tools, SaaS companies bolting on AI features — each covered as a standalone story. The synthesis hasn't been made. That either means it's too early to see, or it's not there to see. Judge the argument on its merits.

## We've Been Here Before — And It Failed

If this sounds familiar, it should.

The 2016-2017 bot era had the exact same vision: conversational interface as application platform. Facebook launched Messenger bots in April 2016. Slack integrations proliferated. WeChat mini-programmes launched in January 2017.

The Western versions failed because NLU (natural language understanding) was too primitive. The bots *worked* — you could order flowers through Messenger — but the experience was consistently worse than just using the app. When the conversational interface can't actually understand your intent reliably, it's friction, not convenience.

WeChat succeeded in China under specific conditions: a mobile-first population, near-universal adoption, and no Western competition for the attention graph. It proves the model is viable under the right conditions. It doesn't prove it's inevitable.

The question that matters: what's different now?

## MCP: HTTP for the Agent Era

What's different is that the protocol layer exists.

[MCP](https://modelcontextprotocol.io/) — the Model Context Protocol — was released by Anthropic in November 2024 as an open standard for LLM-to-service interaction. It was created by a commercial competitor, which gives rivals rational reasons to resist. And yet: ChatGPT adopted it. VS Code adopted it. Goose adopted it. OpenClaw consumes it through mcporter. When competitors adopt your competitor's protocol, the protocol is winning on merits, not on politics. Google is the notable holdout — and their reasons have more to do with the innovator's dilemma than protocol preferences, but that's a story for a later post.

The architectural parallel is striking:

| Web | LLM Platform |
|-----|-------------|
| HTTP | MCP (interaction protocol) |
| HTML / APIs | Tool schemas (interface contract) |
| Browser | LLM client app (rendering layer) |
| Websites | Service providers |

In January 2026, [MCP Apps](https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/) shipped — tools that return rich interactive UIs (dashboards, forms, workflows) rendered in sandboxed iframes inside Claude, ChatGPT, VS Code, and Goose. This isn't text completion. It's a browser inside the conversation. Build once, accessible everywhere. That's what made the web win over AOL and CompuServe.

What's changed since 2016:
- **The NLU gap is closed.** The core failure point in 2016 — the agent couldn't reliably understand you — is resolved.
- **The protocol layer exists.** In 2016, every bot platform had proprietary APIs. Now there's a standard.
- **Rich UI ships.** In 2016, you got text and basic button cards. Now you get full interactive interfaces.
- **Multi-client from day one.** In 2016, you were locked to Messenger or Slack. Now the same tool works everywhere.

**But here's what's still missing — and it matters.** There's no discovery layer. Right now, MCP is like HTTP without DNS and without search engines. The protocol works; finding the right service for a given intent doesn't. The missing piece is on-demand schema loading: user expresses intent, a discovery layer identifies relevant services, only those schemas get loaded. This is DNS for MCP. And critically, this discovery layer *is* the platform economics. Whoever controls which tools get loaded for a given intent controls the business model.

The HTTP analogy carries a warning, too. HTTP being open didn't prevent Google from monopolising discovery. An open protocol moves power concentration from the protocol layer to the discovery layer. It doesn't eliminate it. "Open protocol" sounds like it guarantees a competitive market. It guarantees a contestable *protocol*. The platform built on top can still be a monopoly.

## Delegation, Not Point-and-Click

The interaction model is what actually changes things for users — and for what we build.

Web 4.0 isn't point-and-click. The paradigm is delegation + review + confirmation: describe intent in natural language, the agent executes, a UI surfaces options for review, you confirm. I think this completes a historical full circle — and the trade-off that swings back with it is worth understanding:

- **Pre-web:** Human intermediaries did the work for you. Your travel agent booked the flights, showed you options, you committed. Conversational, personalised, expensive — but *conflicted*. They had commissions and preferred partnerships.
- **Web 2.0 / SaaS:** Self-service replaced the intermediary. Skyscanner with 14 filters, 40 results, 6 tabs open — you do the labour. Cheaper, always available — and *less conflicted*. You saw the raw options. Nobody was steering you. The labour shifted to you, but you trusted the results more.
- **Web 4.0:** AI intermediaries do the work again — conversational and personalised, at software cost. But the conflict-of-interest returns in a subtler form: promoted placements, filtered information. The convenience swings back. So does the trust problem — and the agent *feels* neutral in a way a commissioned travel agent never did.

Latency gets reframed in this model. Twenty seconds for an agent to research and curate three options is *expected*. You're delegating, not clicking a button. Total time-to-outcome drops even though individual operations are slower.

**For engineers, the implication is direct.** Building for Web 4.0 is closer to building an API than building an app. Expose well-typed tools and display templates. The LLM client handles the rendering. The barrier drops from "build a desktop application" (pre-web) to "build a mobile app" (App Store era) to "expose an API with a display template" (Web 4.0). If you're building MCP servers today, you're already building for this.

## The Internet Splits — And the SaaS Playbook Ends

I don't think Web 4.0 replaces the entire internet. If the thesis holds, it reshapes the transactional half.

**Web 4.0 territory:** Booking, purchasing, comparing, researching, scheduling, finances, insurance, government services. Delegation + review + confirmation is genuinely superior for these tasks. Cross-provider coordination is the killer advantage — trip planning isn't flights *or* hotels *or* restaurants. It's all of them together, and the agent handles the coordination.

**Content territory:** Instagram, TikTok, YouTube, Spotify, Netflix, gaming, social media. The consumption experience *is* the product. You don't delegate scrolling. This stays native.

Television didn't kill radio. Mobile didn't kill the web. New platforms capture the highest-value interactions and coexist with what came before.

Now, the title of this post makes a claim: the SaaS playbook ends. Let me be precise about what I mean. The *SaaS playbook* ends as the default model — raise money, build a UI, sell subscriptions, grow at all costs. Three independent forces are driving this, and they don't depend on whether a centralised Web 4.0 platform emerges:

1. **Post-ZIRP economics** compressing multiples and raising the bar for profitability.
2. **LLM commoditisation** — general-purpose models doing what SaaS products charged subscriptions for.
3. **Agentic coding** enabling solopreneurs to build in a weekend what required funded teams, exploding competition and eroding moats.

Here's how I think the impact shakes out:

- **Dies.** Thin wrappers, comparison tools, "nice UI on top of a database" where users endure the interface to reach an outcome. An agent bypasses these entirely.
- **Gets commoditised.** Products with real underlying capability but whose interface premium erodes. Salesforce still exists, still holds the data, still runs the workflows — but users increasingly interact through the agent layer. It becomes infrastructure. Revenue may compress.
- **Untouched.** Control-as-value workflows where the interface IS the product. Portfolio management, creative tools, dev environments — the things where the process of doing is the point.

**The sharpest trap** is for SaaS products bolting on shallow LLM features — "ask your data in natural language." This is building a better search bar in 2003 while Google indexes the entire web. An agent that asks *everyone's* data and synthesises across sources will always outperform a chatbot trapped inside one product. And the irony cuts deep: companies adding these features are training their users to prefer delegation — the exact behaviour that makes those features unnecessary.

## The Economics — Briefly

I'll go deeper on the economics in a later post, but the architecture supports multiple revenue streams: per-request micropayments (Twilio model), platform commissions (Apple's 30% model), promoted placements (Google Ads model), transaction processing (Visa model), and subscription bundling (Spotify model). Combined, that's more monetisation surface than any current platform captures.

Let me be honest about what this means. Promoted placements will happen. Commissions will happen. These patterns replicate across every platform era — they're expected, not a betrayal. The conversational format removes old manipulation vectors (hidden fees, buried buttons, urgency messaging) but introduces new ones: information filtering in a trusted context, promoted placements that feel like personal recommendations. The net effect is likely better than today's adversarial UIs. But the new vectors are subtler, and the agent *feeling* like your personal assistant makes them harder to detect.

The reliability question matters here too. Confirmation gates ("confirm this EUR 340 booking?") catch wrong actions — wrong date, wrong flight. They don't catch filtered information: the agent showing three options when ten exist. For the highest-value use cases — financial decisions, insurance, medical research — this invisible filtering is the more consequential failure mode. Building trust for these domains requires transparency or institutional guarantees that don't exist yet.

## What I Think We're Watching

The OpenClaw acquisition looks small. I think the signal is much larger than the event.

The early infrastructure of Web 4.0 is being assembled: an open protocol (MCP), rich interactive UI (MCP Apps), an agent layer (OpenClaw), a marketplace (ClawHub), multi-client adoption across competitors. I don't think the question is whether delegation-based interaction is better for complex tasks — OpenClaw's developer traction suggests it is, even if GitHub stars don't prove consumer demand. The questions I keep coming back to: who builds the trust, payments, and discovery layer on top? And whether one company becomes the new Google, or the open protocol enables something more distributed.

Three companies are making three different bets. Apple is betting the device controls the agent. OpenAI is betting on both a purpose-built device (the Jony Ive acquisition) and meeting users where they already are through messaging apps (OpenClaw). Anthropic is betting the protocol is the platform, and devices are interchangeable. Google's notable absence from MCP — embedding Gemini into existing products instead of building a cross-cutting platform — looks to me like the innovator's dilemma playing out in real time: the company with the most to lose betting against the platform thesis entirely.

I said at the top that I'm still figuring out where I want to be in this transition. That's true. But I've seen what happens when paradigm shifts catch people off guard — personally — and I'd rather think about this one clearly than be surprised by it. I hope these thoughts help others doing the same.

*This is the first in a series. More soon.*
