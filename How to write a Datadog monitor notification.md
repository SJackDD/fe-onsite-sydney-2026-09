# How to write a Datadog monitor notification

The four-section structure we teach in the session, with templates you can paste into a monitor and adapt.

Syntax here is checked against the [notification variables documentation](https://docs.datadoghq.com/monitors/notify/variables/). Where the two differ, the documentation is correct.

---

## The idea

**The query decides whether you get told. The notification decides whether anyone can act.**

Most people build the first half carefully and never think about the second. The result is an alert that says a number went above another number, delivered to someone at 3am who now has to go and work out what that means, where to look, and whether it matters.

The framing worth carrying: **information at your eyeballs, not at your fingertips.** At 3am you do not have the capacity to go hunting. Twenty seconds reading a good alert beats five minutes logging in, finding the right monitor, and tracking down the runbook yourself.

The work is front-loaded. You write the notification once, calmly, and it pays back every time the monitor fires.

---

## The four sections

Every monitor notification should carry these four things, in this order.

| | | |
|---|---|---|
| **01** | **What is happening** | One line, plain words, with the numbers that triggered it |
| **02** | **Impact** | Who or what is affected and how badly. This is what decides whether it gets picked up now or at 9am |
| **03** | **Runbook** | Initial troubleshooting steps, known cause and resolution, related links |
| **04** | **Who should be notified** | The team that owns this, routed by tag rather than a person who happens to know about it |

The second one is the one most often missing and the most valuable. "Error rate is 4%" tells a responder nothing about urgency. "Checkout is failing for roughly one in twenty customers" tells them everything.

**The title is part of the message too.** It is the first thing that appears in Slack, in an email subject, on a phone notification, and often the only thing read before someone decides whether to open it. A pattern that works:

```
[CHECKOUT] Payment API p95 above {{threshold}}ms over 5 minutes — pages on-call
```

A bracketed scope, the condition with the threshold interpolated, and what happens as a result.

---

## The template to start with

Adapt the service, the thresholds and the handle. Anything in double braces is filled in by Datadog when the monitor fires.

```
{{#is_alert}}
**What's happening** — `{{service.name}}` p95 latency is {{eval "int(value)"}}ms, above the {{threshold}}ms threshold.

**Impact** — <what a customer or the business actually experiences when this is slow>

**Runbook**
1. <first thing to check>
2. <second thing to check>
3. <where to go if the time is in a dependency>
<link to the full runbook>
{{span.link}}

**Who should be notified** — <team> on call.
@your-handle
{{/is_alert}}

{{#is_recovery}}
**Recovered** — `{{service.name}}` back to {{eval "int(value)"}}ms after {{triggered_duration_sec}}s.
@your-handle
{{/is_recovery}}
```

Two notes on that.

`{{service.name}}` only works if the monitor is **grouped by** `service`. Tag variables come from the group-by dimension, so a monitor that groups by nothing has no tag variables available to it. This catches people out constantly — if a variable renders empty, that is almost always why.

`{{span.link}}` is a pre-built link into the Trace Explorer scoped to the matching events. There are equivalents for other monitor types: `{{log.link}}`, `{{rum.link}}`, `{{issue.link}}`. Use them rather than hand-building URLs.

---

## The rule that prevents alert fatigue

Straight from the documentation, and it is the single most important mechanical thing here:

> Any text or notification handle placed **outside** the configured conditional variables is invoked with every monitor state transition. Any text or notification handle placed **inside** of configured conditional variables is only invoked if the monitor state transition matches its condition.

An `@` handle left outside the conditional blocks pings someone on alert, on warning, on recovery, on no-data, and on every renotification. That is how a channel gets muted, and a muted monitor may as well not exist.

**Always pair an alert with a recovery.** If you put a handle inside `{{#is_alert}}`, put the same handle inside `{{#is_recovery}}`. Datadog's documentation recommends this explicitly, and the reason is human rather than technical: page someone about a problem and never tell them it is over, and they stop trusting the signal in both directions.

---

## The other two states

Worth adding once the first template is working.

```
{{#is_warning}}
**Warning** — `{{service.name}}` p95 at {{eval "int(value)"}}ms, above the {{warn_threshold}}ms warning threshold. Not paging yet.
@your-handle
{{/is_warning}}

{{#is_no_data}}
**No data** — no events from `{{service.name}}` for the evaluation window.
The service is either down or has stopped reporting. **Check that before you check the latency.**
@your-handle
{{/is_no_data}}
```

No-data is the block almost nobody writes and then loses an afternoon to. On a latency monitor it usually means the service stopped sending data entirely, which is a bigger problem than the latency you were watching for.

One caveat: do not enable no-data on something that legitimately drops to zero overnight, or you will page yourself every night at 2am.

**State conditionals cannot be combined or nested inside each other.** A monitor is in exactly one state at a time, so each needs its own block. This is a documented constraint, not a style preference.

---

## Variables worth knowing

### Built in

| Variable | What it gives you |
|---|---|
| `{{value}}` | The value that breached |
| `{{threshold}}` / `{{warn_threshold}}` | The thresholds you set. Usable in the title too |
| `{{comparator}}` | The relational operator from the alert condition |
| `{{triggered_duration_sec}}` | Seconds spent in a triggered state |
| `{{first_triggered_at}}` / `{{last_triggered_at}}` | UTC timestamps, with `_epoch` variants in milliseconds |
| `{{alert_recovery_threshold}}` | The value that recovered it |

### From the group-by

Only available if the monitor groups by that dimension: `{{service.name}}`, `{{host.name}}`, `{{host.ip}}`, `{{env.name}}`, `{{pod_name.name}}`, `{{kube_namespace.name}}`, and any tag on the group as `{{<key>.name}}`.

If a tag key contains a period, wrap it in brackets: `{{[dot.key.test].name}}`.

### Two that surprise people

If your monitor groups by `service` and that service has an entry in the Catalog, you get its metadata for free:

```
Owner: {{service.team}}
Runbook: {{service.links[Runbook]}}
```

That satisfies the Runbook requirement above without anyone pasting a URL, and it stays correct when the runbook moves. It is the practical payoff for filling in service metadata.

---

## Keep the long runbook in a notebook

Three numbered steps is about as much as a notification can carry before it stops being readable at 3am. Anything longer belongs somewhere you can link to, and a **Datadog notebook** is a good place for it.

A notebook is rich text with live graphs embedded in the prose, so a runbook written in one can say "if this widget is red, check this next" with the actual widget sitting in the page. That is difficult to do in a wiki and impossible in a notification body.

Practical reasons to keep it in Datadog rather than elsewhere:

- **The graphs are live.** A runbook that shows current state as you read it beats one describing what a graph might look like.
- **Notebooks have a runbook template** and a type tag, so they are findable as a set rather than scattered.
- **Version history**, so you can see what changed after the last incident and roll back a bad edit.
- **It is in the same place as the thing you are debugging.** No context switch between the alert, the platform, and a wiki in a third tab.
- **Datadog's AI features can read it.** In-platform context is available to the assistant in a way that a page in an external wiki is not.

Then the notification just links to it:

```
**Runbook**
1. <the first thing to check, in one line>
Full runbook: <notebook link>
```

If your runbooks already live in Confluence or SharePoint and that is working, link to those instead. The point is that the notification carries a pointer plus the first step, not the whole document.

---

## Monitor tags

Worth understanding properly, because monitor tags are not the tags you think they are.

**Monitor tags are independent of the tags sent by the Agent or your integrations.** They are metadata you attach to the monitor object itself, in the Configure notifications and automations section. You get up to 80 per monitor, and they are added to the alert event the monitor generates.

This is a separate thing from the tags in your query scope. Scoping a query to `env:prod service:checkout` filters what the monitor watches. Tagging the *monitor* with `env:prod service:checkout` is what makes the monitor findable and routable. Datadog pre-populates the tag field from your query scope, which is helpful and also why people assume they are the same thing.

### What the tags actually buy you

**Filtering.** The Monitor List, the Triggered Monitors page and the Downtimes page all filter on monitor tags. With a few hundred monitors this is the difference between finding the six that matter and scrolling.

**Surfacing elsewhere in the platform.** A monitor tagged `service:checkout` shows up against that service in the Catalog and on its service page, so someone looking at a service sees what is watching it. Tag a monitor to a team and it appears on that team's page, which over time turns into a genuine home page for the team: their dashboards, their monitors, their services, in one place.

**Routing.** Notification rules route centrally based on monitor tags rather than handles written into each message body. If you ever want to change where a whole class of alerts goes, tags are what make that a single edit.

**Downtime targeting.** Downtimes can target by tag, so `team:platform` silences a release window without anyone naming forty monitors individually. The same works through the API, which is how you wire a downtime into a deployment pipeline.

### A convention worth adopting

Pick a small set and apply it to everything:

```
service:checkout
team:payments
env:prod
severity:high
```

Four tags, consistently applied, and almost every question you will want to ask about your monitors becomes answerable. The specific keys matter less than the consistency.

One more that is worth considering, and it feeds directly into the next section:

```
audit_review_date:2026-09
```

A tag recording when the monitor was last reviewed. It is the simplest possible way to find the monitors nobody has looked at in a year, and it costs one field.

---

## Review cycles

A monitor is not finished when you publish it. Thresholds drift as traffic patterns change, services get renamed, people leave, and a monitor that was useful in March is noise by September. The teams with alerting people actually trust are the ones that review it.

**A cadence that works:** monthly for anything that pages, quarterly for the rest. Fifteen minutes, one person, working through a list rather than the whole estate.

**What to ask of each one:**

- Did it fire in this period? If not, is it watching something that no longer happens, or is it genuinely a quiet safety net?
- If it fired, did anyone do anything? An alert that gets acknowledged and closed every time without action is noise wearing a uniform.
- Was the threshold right? Too aggressive means false alarms. Too lean means you found out from a customer.
- Is the notification still accurate? Runbook links rot, teams rename their channels, people change roles.
- Does the ownership still exist? A monitor routed to someone who left is a monitor routed nowhere.

### The Monitor Quality page

You do not have to build the review list yourself. Datadog generates it.

From the [Manage Monitors](https://app.datadoghq.com/monitors/manage) page, open the **Monitor Quality** tab. It is enabled automatically once you have created a monitor, and it can be filtered by team, creator, service or environment, so you can look at just your own.

It surfaces seven specific problems, and each one is worth knowing because each has a different fix:

| What it finds | Why it matters |
|---|---|
| **High volume of alerts** | The alert fatigue list. These are the monitors training your team to ignore alerts |
| **Muted for over 60 days** | Somebody silenced it during an incident or a release and never turned it back on. You are not being monitored and you think you are |
| **Missing recipients** | No `@` handle at all, so it fires into the void. Common on monitors created quickly during an incident |
| **Missing a delay** | Monitors on cloud integration metrics need an evaluation delay, because that data arrives late via a crawler. Without one you get false positives on data that simply had not turned up yet |
| **Misconfigured notification channels** | The handle exists but does not resolve. The monitor thinks it notified someone and nobody got anything |
| **Composite monitors missing constituents** | A sub-monitor was deleted, so the composite no longer evaluates or notifies at all |
| **Stuck in an alert state** | Permanently red. The threshold is wrong, the detection type is wrong, or nobody cares. Related to the seven-day rule above |

**Two of those are silent failures** — missing recipients and misconfigured channels. A monitor in that state looks healthy in every list and notifies nobody. They are the first thing to fix on your first pass, and they are almost impossible to find any other way.

**How to actually run it.** Filter to your team, work down the categories, and fix or delete. Deleting is a legitimate outcome and usually the right one for anything stuck in alert that nobody has cared about for a month. If you use the review-date tag above, update it as you go, and bulk tag editing from the Monitor List makes that quick.

---

## Formatting numbers and times

**`{{value}}` on a latency monitor renders something like `1847.3921`.** Fix it:

```
{{eval "int(value)"}}             → 1847
{{eval "round(value, 1)"}}        → 1847.4
{{eval "round(value/1000, 2)"}}   → 1.85
{{eval "humanize_bytes(value)"}}  → for memory and disk
```

Arithmetic operators are `+ - * / ^ %`, and the expression goes inside quotes.

**Timestamps are UTC by default**, which is a small daily annoyance if your team is not. `local_time` fixes it:

```
{{local_time 'last_triggered_at' 'Australia/Sydney'}}
```

Renders as `2026-09-22 14:43:27+10:00`. Any [tz database](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) name works, and you can include several if the team is spread across regions.

**Two small things that save pain.** Variable output is HTML-encoded by default, which breaks URLs containing `&`. Use triple braces for raw output: `{{{variable}}}`. And `{{!-- like this --}}` leaves a comment in the message body that does not render, which is a good place to explain to the next person why the threshold is what it is.

---

## Routing

**`@` is the whole mechanism.** Type `@` in the message body and you get the list of available destinations: users, teams, Slack, Microsoft Teams, PagerDuty, Datadog On-Call, webhooks, and anything else you have integrated.

**Different destinations for different severities.** Put the handles inside the relevant blocks:

```
{{#is_alert}}
@slack-platform-priority @oncall-platform
{{/is_alert}}

{{#is_warning}}
@slack-platform-alerts
{{/is_warning}}
```

**Escalate on renotification** without repeating the whole runbook:

```
{{^is_renotify}}
[ the full four-section message goes here ]
{{/is_renotify}}

{{#is_renotify}}
**Still breached** after {{triggered_duration_sec}}s.
@oncall-platform
{{/is_renotify}}
```

`is_renotify` is a context conditional rather than a state one, so unlike the state blocks it *can* nest. Escalating differently per environment:

```
{{#is_renotify}}
{{#is_match "env" "production"}}
Still broken in production. Escalating. @oncall-platform
{{/is_match}}
{{/is_renotify}}
```

**Dynamic handles, and the fallback you need.** On a multi-alert monitor grouped by service you can route each service's alert to its own channel:

```
@slack-{{service.name}}
```

If that variable ever renders empty you get an invalid handle and **the notification silently goes nowhere**. Always add a fallback:

```
{{#is_exact_match "service.name" ""}}
@slack-platform-fallback
{{/is_exact_match}}
```

Related: the documentation advises against routing on attribute variables that may not be present, for the same reason. Route on tags you control.

**Notification rules** are worth knowing about once you have more than a handful of monitors. They let you route centrally based on tags rather than editing handles into every monitor individually, which stops the routing logic being scattered across hundreds of message bodies.

---

## Choosing a threshold

The part most guides skip.

**Derive the number, do not pick it.** Look at what the service actually does over a couple of weeks, then set the threshold just outside normal. A round number chosen because it is round gives you a monitor that is either permanently red or never fires, and both are useless.

**Set a recovery threshold.** Without one, a metric sitting near the line flaps in and out of alert and notifies on every crossing. A recovery threshold below the alert threshold means it has to genuinely improve before the monitor clears.

**Alert on the symptom, use resources as context.** A monitor on user-visible error rate or latency is a signal. A monitor on CPU at 80% is usually noise, because 80% CPU might be completely fine. Page on what a customer would notice, and keep the resource metrics for the investigation afterwards.

There are honest exceptions. Disk filling up is worth alerting on directly, because by the time a customer notices it is far too late to act. The principle is about which metrics deserve to wake someone, not a ban on infrastructure monitors.

**Static or dynamic.** Static thresholds for things with a real limit: disk full, error rate above what you will tolerate, latency above what you promised. Dynamic detection — anomaly, outlier, forecast — for things that move with traffic and have no fixed correct value. Anomaly detection needs weeks of history to be any good, so it is not the place to start on a service you instrumented yesterday.

**The seven-day rule.** If a monitor has been in alert for more than seven days, one of three things is true: the threshold is wrong, the detection type is wrong, or nobody actually cares about that monitor. All three are worth fixing, and the third is worth deleting.

---

## A pass to run before you publish one

- Does the title alone tell someone what is wrong and what will happen?
- Are all four sections present, including impact?
- Is there a runbook link, or steps, or both?
- Is every `@` handle **inside** a conditional block?
- Does every block containing a handle have a matching recovery block with the same handle?
- Is there a recovery threshold, not just an alert threshold?
- Did the threshold come from observed behaviour rather than a round number?
- Are the numbers formatted, or is it printing six decimal places?
- If you used tag variables, is the monitor actually grouped by that dimension?
- If you used a dynamic handle, is there a fallback?
- Is the monitor tagged with its service, team and environment, so it can be found and routed?

Then use the **Preview** tab before publishing. It renders the message as it will arrive, which catches unformatted numbers and empty variables in about five seconds.

---

## Going further

- [Notification variables](https://docs.datadoghq.com/monitors/notify/variables/) — the full reference for everything above
- [Template variable evaluation](https://docs.datadoghq.com/monitors/guide/template-variable-evaluation/) — arithmetic and formatting functions
- [Monitor notifications](https://docs.datadoghq.com/monitors/notify/) — the wider notification setup
- [Monitor Quality](https://docs.datadoghq.com/monitors/quality/) — the seven issues it finds, in detail
- [Monitor List](https://docs.datadoghq.com/monitors/manage/) — searching, bulk tag editing, bulk recipient editing
- [Notebooks](https://docs.datadoghq.com/notebooks/) — for the long-form runbooks
- [How to audit and clean up monitors effectively](https://www.datadoghq.com/blog/how-to-audit-and-clean-up-monitors/) — a walkthrough of the review process
- [Best practices for tagging your monitors](https://www.datadoghq.com/blog/tagging-best-practices-monitors/)
- [Customize Alert Monitor Notifications](https://learn.datadoghq.com/courses/alert-monitor-notifications) — a free Learning Center course covering this ground with hands-on exercises

Learning Center courses give you a demo account with sample data, so you can build and break things without creating noise in your own organisation.
