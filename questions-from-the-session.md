# Questions from the session

The topics that came up in the room, with the answers and where to read more.

If you were at the session, this is the follow-up. If you weren't, it's still worth reading — these are the questions people reach for once they've seen APM working on a real application.

---

## What does instrumented code actually look like?

Usually, nothing. Almost everything in the session came from **automatic** instrumentation — the tracing library wraps your framework, your HTTP client and your database driver, and you never touch the code. There is genuinely no instrumented code to look at, which is the point of it.

When you do want to instrument something by hand, there are two reasons to bother. Either the library does not know about a piece of your own business logic, or you want a named span around something specific so it shows up in every trace.

- **[Create Custom Spans and Span Tags with APM](https://learn.datadoghq.com/courses/create-custom-spans-and-tags)** — start here. A free hands-on course, which is a better answer to "show me what it looks like" than any page of docs
- **[Instrument a custom method](https://docs.datadoghq.com/tracing/guide/instrument_custom_method/)** — the short version, with examples per language. Usually a decorator or a few lines around the block you care about
- **[Code-based custom instrumentation](https://docs.datadoghq.com/tracing/trace_collection/custom_instrumentation/)** — the full reference, in both the OpenTelemetry API and the Datadog API
- **[Code Origin for Spans](https://docs.datadoghq.com/tracing/code_origin/)** — attaches the file path, line number and function name to spans automatically, so you can jump from a span to the source
- **[Dynamic Instrumentation](https://docs.datadoghq.com/tracing/dynamic_instrumentation/)** — add instrumentation at a specific line from the Datadog UI, with no code change and no redeploy. Worth knowing about before you start editing source

---

## How do you break a monolith down below the service level?

A monolith reports as one service, which makes it hard to see which part of the codebase is actually responsible for anything. There are four ways to get finer granularity, in roughly increasing order of effort.

What you should **not** do is split the monolith into several fake service names. That fragments your service map and catalog, and the tracing libraries actively work against it.

**Resources, which you already have.** Each endpoint in a monolith is already a resource: `GET /checkout`, `ShoppingCartController#checkout`. The Service Page breaks latency, errors and throughput down per resource with no work at all. Start here, because it is often enough.

**The Continuous Profiler, filtered by endpoint.** No code change, and there is a guide written for exactly this problem: **[Isolate Outliers in Monolithic Services](https://docs.datadoghq.com/profiler/guide/isolate-outliers-in-monolithic-services/)**. It gets you method-level CPU attribution per endpoint — the worked example narrows a CPU spike down to a single constructor loading assets it did not need. It also offers a useful middle granularity: group a flame graph by **Trace operation** rather than by method, which the docs describe as a balance between the high granularity of individual methods and the low granularity of whole endpoints.

**Span tags for ownership.** Tag spans with the module or team that owns that part of the codebase — `module:billing`, `team:payments` — and you can then group and filter by area of code without inventing services.

**Custom spans.** As above, for the specific business logic you want named in every trace.

---

## How do you catch errors in the front end?

APM traces stop at the server. Anything that happens in the browser — a JavaScript error, a failed fetch, a slow render — needs **Real User Monitoring**, which is a separate browser SDK and not something APM picks up on its own.

- **[RUM Browser Monitoring](https://docs.datadoghq.com/real_user_monitoring/application_monitoring/browser/)** — setup
- **[Collecting browser errors](https://docs.datadoghq.com/real_user_monitoring/browser/collecting_browser_errors/)** — what gets captured automatically and how to send your own
- **[Browser Error Tracking](https://docs.datadoghq.com/real_user_monitoring/error_tracking/browser/)** — groups thousands of similar errors into single issues, with stack traces and session timelines
- **[Connect RUM and Traces](https://docs.datadoghq.com/tracing/other_telemetry/rum/)** — the one worth doing. Links a browser session to the backend trace it caused, so a user-reported problem goes straight to the slow span

---

## How would you spot a version tag that didn't get updated on a deploy?

The short answer is that the platform notices before you do.

The `version` tag is part of **[Unified Service Tagging](https://docs.datadoghq.com/getting_started/tagging/unified_service_tagging/)** — `env`, `service` and `version` together. When it is set consistently, **[Deployment Tracking](https://docs.datadoghq.com/tracing/services/deployment_tracking/)** appears on the Service Page and shows every active version side by side with its own error rate and latency. That is how you compare a new release against the one before it.

Miss the tag on one deployment and the correlation breaks visibly: the new code's traffic gets attributed to the old version, so the comparison stops making sense and Deployment Tracking shows a version that should have gone away still serving requests. The fix is to set the three tags from your deployment pipeline rather than by hand.

There is an equivalent for the front end too — **[RUM Deployment Tracking](https://docs.datadoghq.com/real_user_monitoring/guide/setup-rum-deployment-tracking/)**.

---

## How do you point a monitor at one specific service?

Covered properly in [the monitor notification guide](./how-to-write-a-datadog-monitor-notification.md), but the short version: the query has a scope, and the scope is where you filter. On an APM monitor you pick the service from a dropdown and add tags like `env:prod` in the filter field.

The part people miss is that **scope and grouping are two different decisions**. Scope decides what the monitor watches. Grouping decides whether you get one alert for everything or one alert per service — and it also determines which variables you can use in the notification. `{{service.name}}` only works if you grouped by service.

---

Something we didn't get to, or an answer that raised another question? Talk to your Datadog account team, or get in touch and I'll point you at the right place.
