# idp-config

GitOps config for Argo CD. Each values file is rendered with the shared chart `charts/service`
into an Application `{service}-{cluster}` in namespace `apps-{cluster}`.

## Layout

    charts/service/                      shared Deployment + Service chart
    envs/{env}/{cluster}/{service}/values.yaml

## Onboarding a service

Open a PR adding `envs/{env}/{cluster}/{service}/values.yaml` (repository, port; initial tag/digest).

## Image fields

Gatekeeper's app merges PRs here that set `image.tag` and `image.digest`. Humans don't edit image fields.
