# Roadmap

## v0.1 — Normalise and diff

The core. Everything else is delivery.

- [ ] Fetch dashboards from a Grafana instance via HTTP API (token auth)
- [ ] Load dashboards from a local directory of JSON files
- [ ] Normalisation pass: strip `id`, `version`, `iteration`, sort keys, canonicalise datasource refs to UID
- [ ] Semantic diff over the normalised tree, keyed by panel id (not array index)
- [ ] Human-readable output grouped by dashboard, then panel, then field
- [ ] `--json` output

**Done when:** editing one PromQL query in the UI produces a one-line diff and
nothing else.

## v0.2 — CI mode

- [ ] `dashdrift check` with non-zero exit on drift
- [ ] `.dashdrift.yml` config: instance URL, source dir, folder filters
- [ ] Ignore rules — per-dashboard and per-panel allowlists for intentional drift
- [ ] GitHub Action wrapper
- [ ] Annotated PR comment output format

**Done when:** a scheduled CI job can open an issue listing drifted dashboards.

## v0.3 — Adopt-back

The mode that turns detection into a workflow.

- [ ] `dashdrift adopt <dashboard>` — pull the live version into the source tree
- [ ] Preserve source formatting and key order where possible
- [ ] `--interactive` — accept or reject each drifted field individually
- [ ] Refuse to adopt when the source is generated (Jsonnet/Terraform), and say why

## v0.4 — Non-JSON source adapters

- [ ] Jsonnet sources — render via `jsonnet`, diff the rendered output
- [ ] Terraform sources — parse `grafana_dashboard` resources
- [ ] Grizzly-managed sources
- [ ] Adapter interface documented so others can add their own

**Done when:** teams using Jsonnet get the same low-noise diff as JSON users.

## v0.5 — Beyond dashboards

Same drift problem, adjacent Grafana objects.

- [ ] Alert rules (Grafana-managed)
- [ ] Contact points and notification policies
- [ ] Library panels
- [ ] Datasource definitions

## v0.6 — Scale

- [ ] Parallel fetch for instances with 500+ dashboards
- [ ] Folder-scoped runs
- [ ] Drift history / trend reporting

## Explicitly out of scope

- Dashboard authoring (use Foundation SDK)
- Applying/deploying dashboards (use Grizzly or Terraform)
- Hosting or SaaS
- Editing dashboards in the UI on your behalf

## Compatibility

- Grafana 10.x and 11.x
- Python 3.10+
