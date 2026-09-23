# Where to go next

The session is three hours, which is enough to get you started and nowhere near enough to get you finished. This is what to do with the momentum.

---

## Start here

**[Core Skills Learning Path](https://learn.datadoghq.com/bundles/core-skills-learning-path)**

If you only do one thing, do this. It covers navigation, tagging, metrics, monitors and dashboards as a single sequence, which is almost exactly the ground we covered plus the bits we had to skip. It carries a digital badge at the end.

---

## Learning Center courses

[learn.datadoghq.com](https://learn.datadoghq.com) — free and self-paced. Each course gives you a demo account with sample data, so you can build things and break things without creating noise in your own organisation. That last part matters more than it sounds: it means you can experiment properly rather than cautiously.

### Dashboards and notebooks

- **[Introduction to Dashboards](https://learn.datadoghq.com/courses/intro-dashboards)** — covers most of the dashboard block, at a slower pace
- **[Building Better Dashboards](https://learn.datadoghq.com/courses/building-better-dashboards)** — the design side, if [our dashboard guide](./dashboard-structure.md) left you wanting more
- **[Creating Graph Widgets](https://learn.datadoghq.com/courses/dashboard-graph-widgets)** — timeseries, query value, top list, table and distribution, and when each one is the right answer
- **[Table, List, SLO and Architecture Widgets](https://learn.datadoghq.com/courses/discovering-table-list-widgets)** — the widgets we didn't get to
- **[Getting Started with Notebooks](https://learn.datadoghq.com/courses/getting-started-with-notebooks)** — we mentioned notebooks in passing. This is the proper version

### Monitors and alerting

- **[Getting Started with Monitors](https://learn.datadoghq.com/courses/getting-started-monitors)** — start here if monitors are new to you. Creating and configuring them, the monitor types, and the status page
- **[Customize Alert Monitor Notifications](https://learn.datadoghq.com/courses/alert-monitor-notifications)** — the closest course to [our notification guide](./monitor-notification-template.md), with a lab where you draft and test a real notification
- **[APM Monitors and Alerting](https://learn.datadoghq.com/courses/apm-monitors-and-alerting)** — monitors on traces specifically, including tuning them so they don't cry wolf
- **[All monitors and alerting courses](https://learn.datadoghq.com/collections/monitors-alerting)**

### APM and tracing

- **[APM and distributed tracing courses](https://learn.datadoghq.com/collections/apm)** — the collection the workshop lab belongs to. If the flame graph work was the part that clicked, this is where to go
- **[Create Custom Spans and Span Tags with APM](https://learn.datadoghq.com/courses/create-custom-spans-and-tags)** — instrumenting your own business logic by hand

### Paths and certifications

- **[All learning paths](https://learn.datadoghq.com/pages/learning-paths)** — curated sequences by role and product area, each with a badge
- **[Certifications](https://learn.datadoghq.com/pages/certification)** — independently proctored exams, if you want something that travels on a CV

---

## Documentation

[docs.datadoghq.com](https://docs.datadoghq.com) is the reference for everything we touched, and it is genuinely good — worth reading rather than just searching.

The pages that come up most after this session:

- **[Unified Service Tagging](https://docs.datadoghq.com/getting_started/tagging/unified_service_tagging/)** — `env`, `service`, `version`. Get these three right and most of the platform starts working properly
- **[Dashboard template variables](https://docs.datadoghq.com/dashboards/template_variables/)** — the thing that turns one team's dashboard into everyone's
- **[Monitor notification variables](https://docs.datadoghq.com/monitors/notify/variables/)** — the full reference behind the four-section template
- **[Monitor Quality](https://docs.datadoghq.com/monitors/quality/)** — finds the monitors in your org that are quietly broken
- **[Notebooks](https://docs.datadoghq.com/notebooks/)**
- **[Continuous Profiler](https://docs.datadoghq.com/profiler/)** — was switched on in your lab and we never looked at it

---

## Elsewhere

**[Blog](https://www.datadoghq.com/blog)** — how the platform works under the hood, and how other teams are using it. Better than most vendor blogs.

**[YouTube](https://www.youtube.com/@datadog)** — short walkthroughs and recorded sessions.

---

## And the obvious one

**Talk to your Datadog account team.** They can arrange a session like this one for more of your team, or the deeper sessions that take a single product and work through it against your own environment rather than a demo app. That last part is the difference — this session used our application, and the version built around yours is a different and more useful conversation.
