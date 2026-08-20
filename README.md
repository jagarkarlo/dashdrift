# dashdrift

<p align="center">
  <img alt="Python" src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img alt="Grafana" src="https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white" />
  <img alt="Jsonnet" src="https://img.shields.io/badge/Jsonnet-0064A5?style=for-the-badge" />
  <img alt="License" src="https://img.shields.io/badge/License-Apache%202.0-3FB950?style=for-the-badge" />
</p>

**Find the Grafana dashboards that no longer match your Git repo.**

You manage dashboards as code. Then, at 2am during an incident, someone edits a
panel in the UI to see something quickly. It works. Nobody writes it back.

Six months later, Git and production have silently diverged, and neither is
trustworthy. Re-applying from Git would destroy real fixes. Nobody knows which
of the two is correct, so everyone leaves it alone and the rot compounds.

`dashdrift` tells you exactly what diverged, ignoring the noise.

## Why not just diff the JSON?

Because Grafana dashboard JSON is enormously noisy. A raw diff after a trivial UI
visit reports changes to `version`, `id`, `iteration`, panel `gridPos`,
`fieldConfig` defaults Grafana injected, key ordering, and datasource references
that got rewritten from name to UID.

None of that is drift. All of it drowns the two lines that are.

`dashdrift` normalises both sides and reports only **semantic** change:

| Reported as drift | Suppressed as noise |
|---|---|
| PromQL/LogQL query text | `version`, `id`, `uid` churn |
| Alert conditions and thresholds | `iteration`, `weekStart` |
| Datasource identity | `gridPos` when layout is unchanged |
| Panel type, added/removed panels | Grafana-injected `fieldConfig` defaults |
| Variable definitions and queries | Key ordering, whitespace |
| Units, legend format, thresholds | Cosmetic-only `options` deltas |

## What it looks like

```
$ dashdrift diff --grafana https://grafana.example.com --source ./dashboards

DRIFTED  platform/api-latency.json
  panel "p99 latency" (id 4)
    query   - histogram_quantile(0.99, rate(http_duration_bucket[5m]))
            + histogram_quantile(0.99, rate(http_duration_bucket[1m]))
  threshold "warn"
            - 250    + 400

IN SYNC  platform/error-budget.json
IN SYNC  platform/saturation.json

MISSING IN SOURCE  platform/adhoc-debug-jan.json
  exists in Grafana, not tracked in Git

1 drifted, 2 in sync, 1 untracked
```

## Three modes

| Command | Purpose |
|---|---|
| `dashdrift diff` | Show semantic drift, human-readable |
| `dashdrift check` | CI mode — exit non-zero on undeclared drift |
| `dashdrift adopt` | Write a UI change back into your source of truth |

`adopt` is the mode that makes this a workflow rather than a nag. The 2am fix was
probably correct. The problem is that it never made it back to Git.

## What this is not

Grafana Labs' **Foundation SDK** and **Grizzly** cover authoring and applying
dashboards-as-code, and they do it well. `dashdrift` does not compete with
authoring. It does the one thing they handle poorly: **low-noise, semantic,
bidirectional drift detection**.

Use Foundation SDK to write dashboards. Use `dashdrift` to find out when reality
stopped agreeing with them.

## Status

**Pre-alpha.** Nothing works yet. See [ROADMAP.md](ROADMAP.md).

## Contributing

The normalisation ruleset is the heart of this tool. If `dashdrift` reports
something as drift that you consider noise — or misses something you consider
real — open an issue with the before/after JSON. That feedback is worth more
than code right now.

## License

Apache-2.0
