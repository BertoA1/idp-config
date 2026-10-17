# idp-config

GitOps config for Argo CD. Each values file is rendered with the shared chart `charts/service`
into an Application `{service}-{cluster}` in namespace `apps-{cluster}`.

## Layout

    charts/service/                      shared Deployment + Service chart
    envs/{env}/{cluster}/{service}/values.yaml

## Onboarding a service

Open a PR adding `envs/{env}/{cluster}/{service}/values.yaml` (repository, port; initial tag/digest).
Set `probePath` (e.g. `/readyz`) to get a readiness probe; without it the pod has none and counts as ready
as soon as it starts.

## Image fields

Gatekeeper's app merges PRs here that set `image.tag` and `image.digest`. Humans don't edit image fields
(one exception: seeding dev before the ruleset, see idp-infra's "Live integrations").
When `image.digest` is set, the chart deploys `repository@digest` and `image.tag` is informational only.

## Repo settings

Enable Settings > General > "Automatically delete head branches": Gatekeeper opens each PR from its own
`gatekeeper/<commit sha>` branch, and they pile up otherwise.
