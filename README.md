# idp-config

GitOps config for Argo CD. Each values file is rendered with the shared chart `charts/service`
into an Application `{service}-{cluster}` in namespace `apps-{cluster}`.

## Layout

    charts/service/                      shared chart: Deployment + Service, opt-in extras below
    envs/{env}/{cluster}/{service}/values.yaml

## Onboarding a service

Open a PR adding `envs/{env}/{cluster}/{service}/values.yaml` (repository, port; initial tag/digest).
Set `probePath` (e.g. `/readyz`) to get a readiness probe; without it the pod has none and counts as ready
as soon as it starts.

## Opt-in extras (chart 0.2.0)

All off by default; with the defaults the chart renders exactly what 0.1.0 did. Set them in a service's values file:

| Values | Adds | Notes |
|---|---|---|
| `metrics.enabled` (`path`, `interval`) | ServiceMonitor, names the Service port `http` | Prometheus picks it up from any namespace |
| `pdb.enabled` (`minAvailable`) | PodDisruptionBudget | only with 2+ replicas, or it blocks node drains |
| `autoscaling.enabled` (`minReplicas`, `maxReplicas`, `targetCPUUtilizationPercentage`) | HorizontalPodAutoscaler on CPU | replaces `replicas` |
| `networkPolicy.enabled` (`allowFromNamespaces`) | Ingress NetworkPolicy | allows same namespace, listed namespaces, and `monitoring` with metrics |
| `replicas`, `resources` | | defaults 1 and `cpu: 10m, memory: 32Mi` requests |

A change to a prod values file is applied in the next prod sync window (Tue/Thu 22:00 America/New_York).

## Image fields

Gatekeeper's app merges PRs here that set `image.tag` and `image.digest`. Humans don't edit image fields
(one exception: seeding dev before the ruleset, see idp-infra's "Live integrations").
When `image.digest` is set, the chart deploys `repository@digest` and `image.tag` is informational only.

## Repo settings

Enable Settings > General > "Automatically delete head branches": Gatekeeper opens each PR from its own
`gatekeeper/<commit sha>` branch, and they pile up otherwise.
