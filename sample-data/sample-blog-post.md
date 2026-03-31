# Why Async-First Teams Outperform Sync-Heavy Ones

**Author:** Jordan Ellis  
**Published:** March 15, 2026  
**Reading time:** 8 minutes

---

Over the past three years, our engineering team at Meridian grew from 12 to 78 people across five time zones. We tried the obvious solutions first: more standups, longer standup, weekly all-hands, async standups that were really just synchronous standups with extra steps. None of it worked.

Then we made a decision that felt radical at the time: we declared ourselves async-first.

Twelve months later, our team ships features 40% faster, our engineers report 28% higher job satisfaction in quarterly surveys, and we've reduced meeting hours by an average of 11 hours per person per week. These are not theoretical numbers — they came from our internal tooling that logs calendar time alongside Git commit frequency.

## What "Async-First" Actually Means

Async-first doesn't mean "no meetings ever." It means that synchronous communication is treated as a last resort rather than a default. Before scheduling a meeting, the question isn't "should we have a meeting?" — it's "why can't this be a document?"

The key principle: **default to written, move to synchronous only when asynchronous has failed or is clearly insufficient.**

At Meridian, this translated into three concrete practices:

1. **Written decisions as the default.** Every architectural decision, sprint planning output, and post-mortem lives in a document first. We use a lightweight Decision Log — a simple table in Notion where each row is a decision, its alternatives, the rationale, and the owner. Anyone can comment asynchronously for 48 hours before the decision is finalized.

2. **Meetings require a written pre-read.** We don't allow meetings without a written agenda and pre-read document sent at least 24 hours in advance. If you can't write down what the meeting is about, you don't know what the meeting is about.

3. **Video for emotion, writing for logic.** Complex emotional conversations (feedback, conflicts, career discussions) happen synchronously. Complex logical conversations (technical design, project scoping) happen in writing. This separation alone eliminated roughly 60% of our recurring meetings.

## The Hidden Cost of Synchronous Communication

Most teams underestimate the cost of meetings. It's not just the time in the room — it's the context-switching cost before and after, the preparation time, the recovery time for deep work.

Cal Newport's research on deep work suggests that knowledge workers need at least 90 uninterrupted minutes to reach a flow state, and that a single interruption can cost 20-30 minutes of recovery time. When your team has four 30-minute meetings scattered through the day, you've effectively eliminated the possibility of deep work entirely.

We measured this directly. Before going async-first, our engineers averaged 2.3 "focus blocks" (90+ minute uninterrupted stretches) per week. After six months of async-first practices, that number rose to 8.1 per week. Feature complexity and velocity tracked with it.

## What We Got Wrong the First Time

Our first attempt at async-first failed spectacularly. We eliminated meetings without building written communication muscles. The result was an information vacuum: people didn't know what was decided, why things were built the way they were, or what the team's priorities were.

The lesson: **async-first requires a writing culture, not just a no-meetings culture.**

We invested in three things to build that writing culture:

- **Writing templates.** We created templates for RFCs, decision logs, sprint updates, and incident post-mortems. Templates lower the activation energy for writing by giving people a structure to fill in rather than a blank page.

- **Writing feedback loops.** We started reviewing documents the same way we review code: constructive comments, explicit approvals, and clear ownership. A document without a reviewer doesn't get merged to the team's knowledge base.

- **Writing as career signal.** We made written communication an explicit part of engineering levels. A senior engineer is expected to write design docs that junior engineers can learn from. A staff engineer is expected to write position papers that influence company direction.

## The Unexpected Benefits

Some of the most valuable outcomes weren't the ones we planned for.

**Onboarding speed doubled.** New engineers can read the Decision Log and understand 18 months of architectural context in their first week. Previously, that knowledge lived in the heads of senior engineers and was transferred through ad-hoc conversations — slowly and incompletely.

**Better decisions.** Written decisions force clarity. You can't be vague in writing the same way you can be vague in a meeting. When we ask "why did we choose Postgres over MongoDB?" we have a document that answers that question with actual reasoning, not "I think someone made that call in 2022."

**Inclusive by default.** Introverted engineers participate more in writing than in meetings. Non-native English speakers have time to formulate thoughts carefully. Engineers in different time zones aren't excluded from decisions made during the 10am EST standup they can't attend.

## How to Start

If you're thinking about moving your team async-first, don't try to change everything at once. Start with one practice:

Pick the most expensive recurring meeting on your calendar. Cancel it for one month. Replace it with a weekly written update sent to the same attendees. At the end of the month, review: was any decision made worse by switching to async? Was any relationship damaged? Was there information that simply couldn't flow without the meeting?

In our experience, the answer is almost always no. And once your team experiences the productivity of a week without that meeting, it's very hard to go back.

The goal isn't fewer meetings for their own sake. The goal is more time for the work that actually matters.

---

*Jordan Ellis is VP of Engineering at Meridian. Previously at Stripe and HashiCorp.*
