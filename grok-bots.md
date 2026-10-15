---
layout: page
title: "My Team of Twenty Bots"
permalink: /grok-bots/
---

*How I run a small team of AI assistants alongside my day job as an engineering lead, and what I've learned doing it.*

I'm Michael Neeley ([@redreceipt](https://github.com/redreceipt)). I lead engineering at [Differential](https://differential.com), where we build Apollos, a platform for church apps, plus the apps that run on it. Like most engineering leads, I spend my days switching between code review, project tracking, support escalations, a full inbox, meetings, and the constant feeling that the codebase needs some love nobody has time to give it.

A few months ago I started handing pieces of that work to a team of AI assistants, Grok Bots. I didn't set up one giant "do everything" agent. I set up a bunch of small ones, and each owns one job. There are twenty of them now. This post covers who they are, how they fit together, and the rules I've had to write along the way. If you're an engineering leader or developer wondering whether a team of assistants is worth it, I hope it saves you some trial and error.

One caveat: this post describes how the setup works. It isn't a results report, and there are no productivity numbers in it.

---

## Why a team instead of one assistant?

My first try was one assistant with lots of instructions. It got muddy fast, and when something went wrong I couldn't tell which instruction caused it. Splitting the work up fixed most of that:

- **Each bot has one job and a short brief.** It's easier to write, easier to debug, and easier to tighten when it does something I don't like.
- **Ownership is clear.** Only one bot touches my inbox. Only one bot reviews a given kind of PR. Two bots never fight over the same thing.
- **I can turn things off one at a time.** If the performance bot gets noisy, I pause it, and nothing else breaks.
- **Names help.** "Nanny says CI is red" is easier to take in than "the assistant says CI is red."

## How it fits together

There's one hub and a lot of spokes. **Switchboard** is my executive assistant. Every other bot either reports to it or works quietly in its own lane. Most of the hands-on coding gets handed off to cloud coding agents (I use Cursor's), which open small pull requests. Humans review every one of those PRs. Some get merged by hand. Others have auto-merge turned on, so they land by themselves once the required human reviews and CI pass.

```
                         ┌──────────────┐
                         │   Michael    │
                         └──────┬───────┘
                   one daily briefing │ reviews, approvals, decisions
                         ┌──────▼───────┐
                         │ Switchboard  │  ← executive assistant / hub
                         └──────┬───────┘
     ┌───────────────┬──────────┼───────────────┬────────────────┐
     │               │          │               │                │
 Coordination    Inbox & PRs   Code improvement   Production & support
 Foreman         Sieve         Keystone  Smoke     Triager
 Trawler         Nanny         Codemod   Hotpath   Pipeline
 Chisel          Stamp         Pruner    Intruder  Medic
 Bottleneck      Semver        Onramp              Compass
                                   │
                         Cursor cloud agents
                 open small PRs → human review + CI → merge
                 (by hand, or by auto-merge on some bots' PRs)
```

The tools they work through are ordinary: GitHub, Linear, Slack, Gmail, Google Calendar, Granola (meeting notes), Sentry, Airflow, and Expo.

## The daily rhythm

**Before the briefing**, the coordination bots do their passes. Foreman checks the Linear projects I lead. Trawler scans engineering projects more broadly. Chisel looks for scope creep. Nanny and Stamp roll up PR activity. Semver sweeps dependency PRs at 8:35. Each one sends a short digest to Switchboard.

**At 8:42 every weekday**, Switchboard sends me one briefing. It merges those digests, drops anything that doesn't need me, and adds prep for the day's meetings. For a recurring meeting, it pulls the Granola notes from the last one in the series so I walk in remembering what we said. On Fridays it adds talking points for my 1:1s with developers.

**During working hours**, the code-improvement and production bots each run about once an hour on weekdays. Their start minutes are staggered, and most runs end with nothing to do, which is the point. Sieve clears inbox noise every hour, around the clock. Nanny keeps an eye on my own PRs.

**Mondays** bring the bigger-picture stuff: Bottleneck's weekly constraint report. Once a month, Keystone writes a tech-stack proposal.

The daily status comes to me as one message. Beyond that, bots ping me mostly when they've opened a PR or need a decision.

---

## The roster

### Coordination

**Switchboard: Executive Assistant**
The hub. It keeps the other bots on task, sends work to whichever teammate fits best, and builds the morning briefing. When I make a new standing rule ("stop @-tagging people in Slack unless they need to act"), I tell Switchboard and it relays the rule to every bot it applies to. *Never does:* the specialist work itself. It delegates.

**Foreman: Project Manager**
Watches the Linear projects I lead every day. It works to unblock them and posts project updates when something material changes. Once a week it sweeps for stale tickets and hands the follow-up to cloud agents. *Never does:* post updates for the sake of posting. Quiet weeks stay quiet.

**Trawler: Linear Projects**
A wider net than Foreman. It scans engineering projects across the team for status, blockers, and drift, then sends a short digest to Switchboard. Its job is to watch and report.

**Chisel: Engineering Manager (Scope Hammer)**
Every weekday morning, Chisel compares each in-flight project's stated scope against what people are actually working on: active issues, open PRs, board state. Then it tells me where scope is bloated or drifting and what to cut, defer, or merge to protect the critical path. *Never does:* overlap with Foreman. Foreman unblocks, and Chisel recommends cuts.

**Bottleneck: Constraints Advisor**
Every Monday, Bottleneck reads as widely as it can across Slack, Linear, and our project tools, then gives me *one* top constraint on the business, with evidence and concrete recommendations. Getting exactly one answer forces prioritization. *Never does:* hand me a list of ten problems.

### Inbox & PRs

**Sieve: Email & Linear Triage**
The bot that owns inbox triage. (Semver is the one exception; see below.) It runs hourly against my Gmail and Linear notifications and follows explicit, numbered rules for what counts as skippable. One example: a GitHub PR email thread gets archived if the PR has merge conflicts or changes requested, or if it's in a repo that doesn't require my approval. It archives whole threads, never individual messages, so nothing pops back up half-read. Real asks and blockers stay put. *Never does:* clear in bulk without an approved policy.

**Nanny: PR Monitor**
Babysits *my* pull requests. It tracks reviews, comments, CI status, and auto-merge status, and grabs proof screenshots when a PR needs them. About once an hour it also checks my draft PRs and asks whether each one should be marked ready, closed, or kept. *Never does:* merge, or update a branch just because it's behind main.

**Stamp: PR Reviews**
Handles the boring reviews: TypeScript migrations, codemods, and other mechanical refactors, whoever wrote them. When one of these requests my review, Stamp comments `@codex review this` from my account (the Codex reviewer only responds to me). If Codex comes back clean, Stamp approves. If it has findings, Stamp leaves a comment asking the author to address them, then re-requests the review after they push. In repos that don't require approvals, it just tells the author they're clear to merge. *Never does:* merge, enable auto-merge, push code, or touch anything that changes CI, workflows, or guardrails. Drafts, PRs with conflicts or changes requested, and anything not clearly mechanical get skipped.

**Semver: Dependency Updates**
Works through Dependabot PRs. A sweep runs at 8:35 every weekday, and it also wakes on PR events. It sorts each bump into safe or risky. It does the prep work itself (fixing CI, splitting a huge bump into smaller draft PRs) before asking any human for time, and it only requests as many reviewers as the repo actually requires. For a **safe** bump that's ready (CI green, no conflicts, no blocking reviews), Semver approves it from my account and turns on squash auto-merge. Where the repo allows, it merges the PR directly. **Major or risky** bumps get a risk/migration checklist, no auto-merge, and a ping to me so I can merge them deliberately. It also does a little inbox cleanup of its own in my work Gmail: it archives notification threads for merged Dependabot PRs and approval emails on PRs I didn't write. That's the one place where inbox work isn't only Sieve's. *Never does:* auto-merge or approve a major or risky bump.

### Code improvement

These bots share one pattern, and it's the most useful thing in this post: **the one-open-PR gate.** Every bot has its own GitHub label (`architecture`, `slop`, `e2e`, `performance`, and so on). Before it starts work, it checks every repo in scope for an open PR with its label. If one exists, the bot does nothing and says nothing. So each bot has at most one PR in front of humans at a time. The pace of the work matches our review capacity, and the bots can't bury the team in PRs.

The bots in this group don't all handle PRs the same way, though. Under their current briefs, **Codemod, Pruner, Smoke, and Onramp** open ready-for-review PRs, turn on auto-merge where the repo supports it, and request at least two reviewers from the code owners or recent authors of the touched paths. Onramp waits for green CI before it requests them. Their PRs land automatically once those required human reviews and CI pass. **Keystone, Hotpath, and Intruder** (and Triager and Pipeline, below) open *draft* PRs and don't turn on auto-merge. A human marks those ready and merges them. Honestly, this is mid-transition: the older hourly prompts for the auto-merge bots still say "draft PR," and the current briefs win.

**Keystone: Software Architect**
Looks for places where the contracts between our services and apps are implicit, and makes them explicit. Think clearer boundaries between services and fewer handoff gaps. It opens small draft PRs. Once a month, before my briefing, it surveys the tech stack and writes one technical proposal in Linear, with evidence, tradeoffs, and a migration plan. *Never does:* pitch speculative rewrites. Concrete flow improvements beat grand redesigns.

**Codemod: TypeScript Migration**
Moves JavaScript to TypeScript in small, mechanical, type-safe steps. It goes after duplicated JS/TS modules first. *Never does:* change behavior in a migration PR.

**Pruner: Code Cleanup**
Hunts for slop: useless tests, pointless wrappers, extra complexity, and places where we drifted from a dependency's recommended patterns. It leans toward deleting and simplifying instead of adding structure. *Never does:* add abstraction in the name of cleanup.

**Smoke: E2E Testing**
Finds critical user flows with no end-to-end coverage and adds tests for them. *Never does:* write brittle or speculative tests just to bump a coverage number, or invent reviewers.

**Hotpath: Performance**
Looks for real, low-risk performance wins: wasted work, N+1 queries, unnecessary re-renders, heavy synchronous work on hot paths. *Never does:* chase speculative micro-optimizations.

**Intruder: Security**
Does ethical, defensive security work on systems we own. It looks for concrete, fixable issues and opens a draft PR that fixes the root cause. *Never does:* look at anything outside our own organization, or open PRs for noisy scanner output it can't show is a real problem. (For obvious reasons, I won't share what it has found.)

**Onramp: Agent DX**
Makes our repos easier for *coding agents* to work in: READMEs, `AGENTS.md` files, setup scripts, debug access, end-to-end QA flows. Every improvement helps every other bot's cloud agents. *Never does:* cosmetic docs polish for its own sake.

### Production & support

**Triager: Error Triage**
Every hour on weekdays, it picks the unresolved Sentry error with the biggest impact, digs into the stack traces, and opens a small draft PR that fixes the root cause. *Never does:* silence or swallow an error to make the graph look better.

**Pipeline: Data Pipelines**
Finds a failing active Airflow DAG, diagnoses it from the run logs, and opens a small draft PR with a fix and tests. *Never does:* pause or disable a DAG and call it fixed.

**Medic: Support Triage**
Takes one support issue at a time from our support queue in Linear. It posts what it found, and when the fix is small it opens a PR with proof from an Expo simulator.

**Compass: Product Guide**
Answers product questions in Slack for non-technical teammates: "Does the app support X?" "Why would a user see Y?" It looks at how the platform, code, and features actually work, so those questions don't need an engineer to drop what they're doing.

---

## Guardrails and lessons learned

Most of what I know now came from a bot doing something I didn't want and me writing a rule about it. These are the rules that held up.

**1. Be deliberate about which bots can merge or act as you.** I didn't stick to "bots never merge." Several bots turn on auto-merge for their own small PRs, and Semver can approve and merge safe dependency bumps. What I kept is that nothing reaches main without the repo's required human reviews and green CI (Semver's own approval counts toward that on safe bumps), and major bumps wait for me. Stamp, Nanny, and the draft-PR bots never merge. On GitHub, Stamp and Semver act from my account within tight scopes. Anything that would go out under my name in Slack or email starts as a draft I approve. Each of those powers was a separate decision, and that's how it should be.

**2. Drafts, not sends, for anything that speaks for me.** Proposals go to Linear Triage, not the backlog. Code goes into PRs, never straight to main. Messages are drafted, not sent.

**3. One bot owns each job, and you name the exceptions.** Sieve owns inbox triage, and other bots' briefs (Stamp's, for example) explicitly say to stay out of it. The exception is Semver, which archives a narrow set of dependency-related emails as part of its own sweep. It's a known, written-down exception. Overlapping ownership you haven't spelled out is how jobs get done twice or not at all.

**4. Keep PRs small, simple, and focused, especially when responding to feedback.** A reviewer asks for one change, and an eager agent "also cleans up" three files nearby. Now the rule is to fix exactly what was asked.

**5. I'm moving away from hard line caps.** Early briefs said "under 200 lines added," and I found that a number pushed bots to split PRs in odd places. Most current briefs now say "as small and simple as possible, no hard line cap." The move isn't finished. Pruner's brief still has the 200-line cap, and the hourly scheduled prompts still mention it.

**6. One open PR per bot.** The label gate described above. Without it, ten bots that each run hourly could swamp the team in a day. With it, bot output can't outrun what humans can review.

**7. Silence is a feature.** Every scheduled bot is told to stay quiet when there's nothing useful to do. Most runs end with no message, and I'm glad they do. A bot that reports "nothing to report" every hour teaches you to ignore it.

**8. Don't @-tag people who don't need to act right now.** Bots love to mention everyone relevant. People hate it. Now a bot tags someone only when that person has an immediate action.

**9. Request reviewers from the actual owners, and don't over-request.** The code bots pick reviewers from code owners and recent authors of the touched paths, never invented ones. Semver requests only the approvals the repo still needs.

**10. Funnel status through one briefing.** The digests all go to Switchboard, which compiles one morning message. The code bots still ping me directly when they open a PR, but status and digests come through one place.

**11. Ban churn work.** "Update branch because it's behind" is the classic example: it looks like work and only burns CI minutes and reviewer attention.

**12. Write rules down, and send them to every bot they apply to.** When I correct one bot, Switchboard passes the rule on to the others it applies to.

## How to start your own

If you want to try this, here's the order I'd go in:

1. **Start with your inbox or your PR queue, not your codebase.** It pays off right away and carries little risk.
2. **Write a one-paragraph brief per bot.** Include what it does, what it never does, and when to stay quiet. If you can't fit the job in a paragraph, split it into two bots.
3. **Add a hub early.** Once you have three bots, you'll want one briefing instead of three message streams.
4. **Gate code-writing bots on review capacity.** Use the one-open-PR label trick, or something like it, from day one.
5. **Keep a human on every irreversible action.** Sending, deleting, and spending money all need a person. For merging, the minimum is required human review plus CI, and save auto-merge for small, low-risk PRs.
6. **Invest in agent DX.** Good `AGENTS.md` files, reliable setup scripts, and real end-to-end test flows make every agent better. If I could only keep one code bot, it might be Onramp.
7. **Expect to keep editing.** My briefs change every week, and as you saw above, they don't always agree with each other yet.

The surprise for me is that the hard part isn't the AI. It's the stuff I've always found hard as a lead: clear ownership, small work, protecting people's attention, and saying "no" to busywork.

If you're building something similar, I'd love to compare notes. Find me on GitHub at [@redreceipt](https://github.com/redreceipt).
