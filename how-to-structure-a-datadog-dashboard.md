# Building a dashboard people actually read

The structure we teach in the session, written out so you can use it at your desk against your own services.

You do not need to have attended. If you did, this is the long version of the dashboard block.

---

## The test

**A dashboard should tell you what it is telling you, at a high level, in under ten seconds.**

That is the bar everything below is measured against. Ten seconds is roughly how long someone looks at a dashboard before deciding whether it is useful, and if they cannot get an answer in that time they go and ask a person instead.

And the half of it people miss: **avoiding clutter is not removing information. It is displaying that information better.** A widget that packs a rate, a label, a time window, a trend and a colour into the same space as a bare number is *less* cluttered, not more, because you no longer have to work anything out.

---

## Build the skeleton before the widgets

Almost everyone builds a dashboard by adding a graph, then another, then trying to organise the mess afterwards. That is how you end up with forty widgets, no structure, and nobody willing to delete anything.

Do it the other way round.

**1. Name it, and write down the question it answers.**

Put the question in the dashboard description, in one sentence. *Is the checkout path healthy right now, and if not, which service is the problem?* That is a question. *Payments* is not.

If you cannot write the question, stop. You do not have a dashboard yet, you have a pile of graphs.

A naming convention helps more than it sounds like it will. A bracketed prefix tells you the scope before you have read anything else, and it makes dashboards findable six months later:

```
[Checkout] Service health
[Platform] Kubernetes capacity
[Exec] Monthly reliability
```

**2. Create the empty groups and title them.**

Two or three. Give each one a title that says what the section is about. That is the entire structure and it takes about a minute.

**3. Then fill them.**

Two minutes of skeleton keeps the thing from sprawling, and it forces you to decide what the dashboard is *for* before you have anything pretty to look at. This is the single most transferable habit in this document.

### The repeatable unit

Every section of every good dashboard is the same four things:

| | |
|---|---|
| **A group** | The container. A visual boundary and a collapsible section |
| **A title** | On the group. Says what this section is about |
| **Two widgets** | One that answers, one that explains |
| **A note** | One sentence of context, or the links out |

Two widgets is the discipline, not a hard limit. If a group genuinely needs six, it is probably two groups.

Learn the unit once and the dashboard builds itself. You are repeating the same pattern down the page, getting more detailed as you go.

---

## Above and below the fold

**Below the fold** is a newspaper term. Papers were folded in half on the stand, so the headlines went on the top half and you had to pick it up and unfold it to get the detail. Web pages inherited the idea. Dashboards work the same way.

**Above the fold:** the two or three numbers that answer *is this healthy right now*. Big, few, readable from the back of a room. Someone who looks for two seconds should be able to walk away correctly informed.

**Below the fold:** the breakdowns you need once you know something is wrong. Per service, per endpoint, per host. This is where the detail lives, and it is fine that people have to scroll for it, because by the time they are scrolling they have a reason to.

A rough shape:

```
┌──────────────────────────────────────────────┐
│  $service    ← template variable             │
├──────────────────────────────────────────────┤
│  ▼ Overview                    ABOVE FOLD    │
│    ┌────────────┐  ┌────────────┐            │
│    │ Error rate │  │ p95 latency│            │
│    │   0.42%    │  │   1.62s    │            │
│    └────────────┘  └────────────┘            │
│    Note: what this dashboard is for          │
├──────────────────────────────────────────────┤
│  ▼ Where the time goes         BELOW FOLD    │
│    ┌────────────┐  ┌────────────┐            │
│    │ p95 by     │  │ Slowest    │            │
│    │ service    │  │ services   │            │
│    └────────────┘  └────────────┘            │
│    Note: links out to traces and runbook     │
├──────────────────────────────────────────────┤
│  ▼ Throughput and errors                     │
│    ...                                        │
└──────────────────────────────────────────────┘
```

Three groups, two widgets each, a note where context helps. That is a complete dashboard and it will outperform most forty-widget boards you have seen.

One practical note: dashboards reflow depending on browser width and zoom. If you are putting one on a wall display, clone it and size the widgets for that screen rather than fighting the layout on both.

---

## Choosing the right widget

The question decides the widget, never the other way round.

| The question | The widget |
|---|---|
| What is it right now? | Query Value |
| Is it getting worse? | Timeseries |
| Which one is worst? | Top List |
| How do several things compare across dimensions? | Table |
| Is one request slow, or are they all slow? | Distribution or Heatmap |
| How does this week compare to last? | Change |
| Are we meeting the target we promised? | SLO |
| How do these services talk to each other? | Service Map |
| Where do I go next? | Notes and Links |

**Do not default to a timeseries for everything.** It is genuinely good at trends over time and genuinely bad at ranking things or showing distribution. If you find yourself squinting at a legend to work out which line is which, you wanted a top list.

**If you want one number on a dashboard, the query value widget is the one to reach for.** It is the most useful widget in the product and the most underused.

**The distribution one is worth knowing about.** A timeseries of average latency hides the shape of the problem. A distribution shows you whether you have a uniformly slow service or a fast service with a bad tail, and that distinction is most of latency work.

**On pie charts.** With two or three slices they psychologically skew how the reader reads the proportions. With ten they become unreadable mush. A reasonable rule of thumb: more than five or six slices and it is the wrong visual; fewer than three and something simpler communicates better.

---

## Harvest, do not build

**The graphs worth keeping are the ones you were already looking at when you worked out what was wrong.**

Most people open an empty dashboard and a metric picker and start browsing for things to graph. That is slow, and it produces worse dashboards, because you end up graphing whatever is easy to find rather than whatever told you something.

Next time you finish an investigation — you have found the slow service, you know which span owns the time, you have the graph on screen that made it obvious — **export that graph to a dashboard before you close the tab.** Most graphs across the product have an export or add-to-dashboard action in their menu.

Do that three or four times over a couple of weeks and you have a dashboard built entirely out of things that have already proved useful, which is the only real test.

The Datadog Clipboard is worth knowing about here too: you can copy widgets between dashboards, including out of the out-of-the-box integration dashboards. If the Kubernetes or Postgres dashboard already has the graph you want, take it rather than rebuild it.

---

## Template variables

This is the mechanic that turns one team's dashboard into everyone's dashboard. Without it, every other team clones yours and edits the filters, and within a year there are twelve copies quietly drifting apart.

A template variable is a dropdown at the top of the dashboard, built on a tag key, that filters every widget you apply it to.

**Setting one up.** Add Variable in the dashboard header, pick the tag key it is built on, give it a name, set a default value, then choose which widgets it applies to. Select All is usually what you want.

**Using it in queries.** Once defined, the variable appears as an option in the widget editor's filter field. Append `.value` when you need the bare value inside a string. In note widgets, with a variable named `env` built on the tag `environment`, currently set to `prod`:

| You write | You get |
|---|---|
| `$env` | `environment:prod` |
| `$env.key` | `environment` |
| `$env.value` | `prod` |

**Three things worth knowing.**

*The URL carries the selection.* Change a variable and the dashboard URL picks up `&tpl_var_<name>=<value>`. That means you can send a colleague a link to your dashboard already filtered to their service, rather than "here's a dashboard, go find your bit". This is the single most appreciated trick in this document.

*Saved views remember combinations.* Variable selections do not save on their own. If there is a combination you keep coming back to, save it as a view and get back to it in one click.

*Values come from the data your widgets actually query, and they are time-scoped.* If a value you expect is missing from the dropdown, that is usually why. Metrics look back about 48 hours; other sources use the dashboard time frame.

Full reference: [Template Variables](https://docs.datadoghq.com/dashboards/template_variables/).

---

## Aggregate for clarity

The single highest-return edit you can make to an existing dashboard.

**Before.** A query value widget showing `172.85`. That is a count of errors. Is it 172 errors in an hour, in four hours, in three weeks? Is that normal for this service? You cannot tell, so you go and look somewhere else, which means the widget has failed at its only job.

**After.** The same widget, the same space on the screen:

- **A rate instead of a count.** Errors as a percentage of requests. Now it is comparable to itself over time and to other services.
- **A label that says what it is.** "Error rate, last 4 hours", not "Errors".
- **A unit set explicitly.** Otherwise the platform does not know whether your formula produced a percentage, a duration or a count, and it will not format it for you.
- **Precision set sensibly.** One or two decimal places. Nobody needs six.
- **A trend behind it.** The big number tells you the state. The sparkline behind it tells you whether it is getting worse. This costs nothing and is badly underused.
- **A conditional colour.** Light green when healthy, bold red when not. Light green deliberately does not draw the eye, which is exactly what you want for good news. Bold red does, which is exactly what you want for bad.

Same real estate, far more information, and no arithmetic for the reader.

**If it then feels cluttered, that is progress, not a problem.** Take the trend off, or the change indicator, and keep the clean number. The point is that you now have the tools to decide how much context the widget needs, rather than accepting whatever the defaults gave you.

---

## Notes and context

The Notes and Links widget is how you put context on a dashboard without turning it into a document.

It has several display modes worth knowing: the default panel, **caption** (renders as though it belongs to the widget above it), **header**, and a post-it style with a pointer at whatever it is annotating. Caption and header are the two you will use most.

It supports markdown, so you can bold things, use inline code, and add links out — to a runbook, to a related dashboard, to the service page.

**Two rules that keep it useful.**

*One sentence.* You are giving the reader a foothold, not writing documentation. If it needs three paragraphs, it belongs in a notebook and the note should link to it.

*One context note per group.* More than that and you have rebuilt the clutter you were trying to avoid.

**The links out matter more than the prose.** A dashboard that tells you something is wrong and gives you nowhere to go is a dead end, and dead ends are why people stop opening dashboards. Every group that could surface a problem should offer a way to start investigating it.

---

## Reducing cognitive load

Probably the biggest single lever on whether a dashboard gets used.

**Ten to fifteen widgets per screen, maximum.** Past that, people stop reading and start scanning for the one thing they came for, which defeats the point of having built the rest.

**No more than about six lines on a timeseries.** Beyond that it is spaghetti, and a legend does not rescue it. If you need per-service detail across thirty services, that is a top list or a table, not a line graph.

**Make widgets visually distinct from one another.** If everything is the same size, the same colour and the same shape, the reader has to work to tell sections apart. Different colours, different widget sizes, a thicker stroke on the line that matters — all of it does work for free.

**Pre-compute your ratios.** Use formulas so the widget shows the error rate, not the error count next to the request count with the reader doing division in their head. Anything the dashboard can work out, it should.

**Be consistent, and try to make it bigger than you.** Same colour for the same kind of data, same units, same default time window. This is the hardest one on the list, because consistency across a team or an organisation needs agreement rather than effort, and people are attached to their own layouts. Worth having the conversation anyway.

---

## Dashboard or notebook

Both hold the same widgets. They are for different jobs, and picking wrong is a common reason a dashboard ends up cluttered.

**A dashboard is present tense.** Read repeatedly, by different people, in no particular order. It answers one question: is this healthy right now.

**A notebook is past tense.** Read once, top to bottom, in the order it was written. It answers what happened that time, and how we worked it out.

A notebook is built like a word processor document, a dashboard is built like a presentation. If you are writing narrative — an incident review, a runbook, an investigation you want someone else to be able to follow — that is a notebook, with live graphs embedded in the prose. Notebooks also support template variables, so the mechanic above transfers.

**The practical tell:** if the reason you are building it is *so we can understand later what happened*, it is a notebook. If it is *so we can tell at a glance whether things are okay*, it is a dashboard.

Notebooks also come with templates for postmortems, runbooks and investigations, and they preserve graph snapshots, which matters when you come back to an incident review a year later and the underlying data has aged out.

Full reference: [Notebooks](https://docs.datadoghq.com/notebooks/).

---

## A pass to run before you share one

- Can you say, in one sentence, the question this dashboard answers? Is that sentence in the description?
- Does the top of the screen answer *is this healthy* without scrolling?
- Is every widget the right type for its question, or is something a timeseries out of habit?
- Are units and precision set on every number?
- Is there a template variable, so another team can use this without cloning it?
- Does every group that could show a problem offer a link to start investigating?
- Under fifteen widgets? Under six lines per graph?
- Does it have a name and an owning team, so somebody can find it in six months?

**Then the real test: give it to someone outside your team.** Tell them the question it is meant to answer, hand it over, and ask them what they see. If they say "that's cluttered", or take thirty seconds to find the answer, that is the most useful feedback you will get, and it is free.

---

## Going further

- [Introduction to Dashboards](https://learn.datadoghq.com/courses/intro-dashboards)
- [Building Better Dashboards](https://learn.datadoghq.com/courses/building-better-dashboards)
- [Creating Graph Widgets](https://learn.datadoghq.com/courses/dashboard-graph-widgets)
- [Table, List, SLO and Architecture Widgets](https://learn.datadoghq.com/courses/discovering-table-list-widgets)
- [Getting Started with Notebooks](https://learn.datadoghq.com/courses/getting-started-with-notebooks)
- [Widgets reference](https://docs.datadoghq.com/dashboards/widgets/) in the documentation

Learning Center courses are free and each one gives you a demo account with sample data, so you can build and experiment without creating noise in your own organisation.
