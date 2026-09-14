# QA — the image mirror

What `push-images.yml` owns: every base image the org's builds pull, copied from Docker Hub or gcr.io to
`public.ecr.aws/b2i4h4k9` so CodeBuild and CI never pull from a rate-limited registry.

- **Scope** — the matrix in `.github/workflows/push-images.yml`, and what it publishes
- **Runs on** — GitHub Actions, on a push to `main` or a manual `workflow_dispatch`
- **Cases** — 1 (0 verified, 1 not verified)
- **Also see** — `cvhome/store-pod/landing-ui/qa/landing-ui-qa.md` (the image's consumer)

Each case is tagged **[verified]** (run end to end and passed) or **[not verified]** (never run by anyone —
where the bugs are).

## 00 — Before you start

A machine with Docker. No credentials: the gallery is public.

## 01 — Node runtimes

### 01.1 Distroless Node 24 is published and runs [not verified]

- Setup: the PR that adds `gcr.io/distroless` `nodejs24` `latest` to the matrix is merged, and its
  `push-images (…nodejs24…)` job is green.
- Steps: `docker run --rm --entrypoint /nodejs/bin/node public.ecr.aws/b2i4h4k9/nodejs24:latest -v`
- Expect: `v24.x`. The same command against `gcr.io/distroless/nodejs24:latest` gives the same version. Before
  the merge, the mirror image does not exist (the pull fails), and cvhome's landing-ui image cannot build on it.

## 99 — known gaps

- `latest` moves. A re-run of the workflow mirrors whatever distroless publishes that day, so the mirror is not a
  pin. That is how `nodejs20:latest` already works.
