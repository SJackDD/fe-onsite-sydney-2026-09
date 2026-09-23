# Foundation Enablement Onsite

Everything worth taking away from the session, in one place.

If you attended: this is what we covered, the reference material we said we would leave you with, and the answers to what came up in the room. If you did not: it is still readable, it will just make more sense if you have used Datadog once or twice.

---

## What this session is

A free, half-day, hands-on session run by Datadog's Technical Enablement team. No slides to sit through, no product pitch. You work in a training environment we provide and you build things as we go.

The arc of the afternoon, in one line: at the start your application is running and completely invisible. By the end it is instrumented, tagged, traced, on a dashboard, with a monitor watching it.

**Who it is for.** Anyone new to Datadog, or new to their team's Datadog setup. Engineers, SREs, DevOps and platform teams who use it day to day. New starters who inherited an environment someone else built. Teams who have had Datadog for a while but never had a proper walkthrough. No prior Datadog experience needed.

**What you walk out able to do.**

- Get data into Datadog and understand where the configuration comes from
- Read your infrastructure and find a single service among many
- Follow one request end to end and say where the time actually goes
- Build a dashboard someone else can read in ten seconds
- Create a monitor that fires on something real, with a notification worth waking up for

---

## Agenda

| | |
|---|---|
| 01 | Getting data into Datadog |
| 02 | Introduction to observability and APM |
| 03 | Reviewing what arrived |
| 04 | Where the time goes |
| 05 | Break |
| 06 | Building dashboards |
| 07 | Creating monitors |
| 08 | Wrap and resources |
| 09 | Q and A |

---

## Take these with you

Written to be used at your desk against your own services rather than read once and forgotten.

**[How to structure a Datadog dashboard](./dashboard-structure.md)**
The structure we teach in the session, written out properly. Skeleton first, the repeatable group unit, above and below the fold, and the reasoning behind each choice. Ends with a checklist you can run over a dashboard you already have.

**[How to write a Datadog monitor notification](./monitor-notification-template.md)**
The four-section notification structure, as copy-and-paste templates with the conditional syntax filled in. Covers routing, formatting, choosing a threshold, and the rule that stops a channel getting muted. Ends with a checklist to run before you publish a monitor.

**[Questions from the session](./questions-from-the-session.md)**
The topics that came up in the room, answered properly — what instrumented code looks like, breaking a monolith down below the service level, catching front-end errors, spotting a version tag that didn't get updated, and pointing a monitor at one service.

**[Where to go next](./where-to-go-next.md)**
Courses, learning paths, certifications and the documentation worth actually reading. Free, self-paced, and each course comes with a demo account so you can experiment without making a mess of your own.

---

## What happened to the lab

The workshop ran in a Datadog Learning Center lab — a browser-based environment that spun up a Kubernetes cluster with a demo application and gave each of you your own Datadog training organisation.

**Your Datadog organisation keeps everything you built.** The dashboard and the monitor are still there. What stops is the data flowing into them, because the cluster behind it is torn down when the lab session ends.

**The lab relaunches, but it starts clean every time.** Your access runs for a limited window after the session, and each relaunch builds a fresh cluster with nothing you did previously in it. So anything hands-on means instrumenting the application again first — worth setting aside an hour rather than ten minutes.

**The link we used on the day was specific to our session** and is deliberately not repeated here, because it will lapse. The Learning Center has self-paced equivalents covering the same ground — see [where to go next](./where-to-go-next.md).

---

## Feedback

**[Session feedback survey](https://forms.gle/RHXfRk7PS5sh9oeG6)** — https://forms.gle/RHXfRk7PS5sh9oeG6

Two minutes, and it genuinely shapes the next one. If you attended, please fill it in.

---

## A few notes

**The slides are not here.** They are visual aids for the room and they do not carry the content. The documents above do, and they are better than the slides for the purpose.

**Ask your Datadog account team** about running this for more of your team, or about the sessions that go deeper on individual products.

**Shared with session attendees to use in their own work.** This is enablement material, not official documentation — [docs.datadoghq.com](https://docs.datadoghq.com) is authoritative where it differs.
