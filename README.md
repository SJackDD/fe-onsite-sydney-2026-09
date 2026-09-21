# Foundation Enablement Onsite

Everything worth taking away from the session, in one place.

If you attended: this is what we covered, what you can still get to, and the two reference documents we said we would leave you with. If you did not: it is still readable, it will just make more sense if you have used Datadog once or twice.

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

Two reference documents, written to be used at your desk against your own services rather than read once and forgotten.

**[How to structure a Datadog dashboard](./dashboard-structure.md)**
The structure we teach in the session, written out properly. Skeleton first, the repeatable group unit, above and below the fold, and the reasoning behind each choice. Ends with a checklist you can run over a dashboard you already have.

**[Monitor notification template](./monitor-notification-template.md)**
The four-section notification structure, as copy-and-paste templates with the conditional syntax filled in. Start with one monitor, then apply it to the rest.

---

## The lab

The workshop we used is a Datadog Learning Center lab. It runs entirely in a browser, spins up a Kubernetes cluster with a demo application, and gives you your own Datadog training organisation.

**Workshop link:** `[WORKSHOP LINK]`

**Two things worth knowing about it:**

**Your Datadog organisation keeps everything you built.** The dashboard and the monitor are still there. What stops is the data flowing into them, because the cluster behind it is torn down when the lab session ends.

**The lab relaunches, but it starts clean every time.** Your access runs for a limited window after the session, and each relaunch builds a fresh cluster with nothing you did previously in it. So anything hands-on means instrumenting the application again first. Worth setting aside an hour rather than ten minutes.

**The link above is specific to our session.** It is not a permanent, publicly available environment, and it will not stay open indefinitely. Once it lapses, the Learning Center has self-paced equivalents that cover the same ground, listed below.

---

## Feedback

**[Session feedback survey](#)** — `[SURVEY LINK]`

Two minutes, and it genuinely shapes the next one. If you attended, please fill it in.

---

## Where to go next

**Learning Center** — [learn.datadoghq.com](https://learn.datadoghq.com)
Free, self-paced courses and labs. Each one gives you a demo account with sample data, so you can build and experiment without creating noise in your own organisation.

Closest to what we covered:

- [Introduction to Dashboards](https://learn.datadoghq.com/courses/intro-dashboards) — covers most of the dashboard block
- [Building Better Dashboards](https://learn.datadoghq.com/courses/building-better-dashboards)
- [Creating Graph Widgets](https://learn.datadoghq.com/courses/dashboard-graph-widgets) — timeseries, query value, top list, table, distribution
- [Table, List, SLO and Architecture Widgets](https://learn.datadoghq.com/courses/discovering-table-list-widgets)
- [Getting Started with Notebooks](https://learn.datadoghq.com/courses/getting-started-with-notebooks)
- [Customize Alert Monitor Notifications](https://learn.datadoghq.com/courses/alert-monitor-notifications) — the monitor block, in more depth

**[Certifications](https://learn.datadoghq.com/pages/certification)** and **[learning paths](https://learn.datadoghq.com/pages/learning-paths)** — proctored exams and curated course sequences, both with digital badges.

**Documentation** — [docs.datadoghq.com](https://docs.datadoghq.com)
The reference for everything we touched. The pages that come up most after this session:

- [Dashboard template variables](https://docs.datadoghq.com/dashboards/template_variables/)
- [Monitor notification variables](https://docs.datadoghq.com/monitors/notify/variables/)
- [Notebooks](https://docs.datadoghq.com/notebooks/)

**Blog** — [datadoghq.com/blog](https://www.datadoghq.com/blog)

**YouTube** — [youtube.com/@datadog](https://www.youtube.com/@datadog)

---

## A few notes

**The slides are not here.** They are visual aids for the room and they do not carry the content. The two documents above do, and they are better than the slides for the purpose.

**Ask your Datadog account team** about running this for more of your team, or about the sessions that go deeper on individual products.

**Shared with session attendees to use in their own work.** This is enablement material, not official documentation — [docs.datadoghq.com](https://docs.datadoghq.com) is authoritative where it differs.
