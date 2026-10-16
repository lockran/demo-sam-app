---
description: Checks repository work against the organisation's ADRs; raises issues for inconsistencies and proposes new ADRs in the architecture repo.
on:
  schedule: weekly
  workflow_dispatch:
  pull_request:
    types: [opened, synchronize, reopened]
permissions:
  contents: read
  issues: read
  pull-requests: read
tools:
  github:
    mode: gh-proxy
    toolsets: [default]
network: defaults
safe-outputs:
  create-issue:
    title-prefix: "[adr-gap] "
    labels: [adr, architecture]
    max: 5
    close-older-key: adr-gap
  create-pull-request:
    target-repo: "lockran/architecture"
    title-prefix: "[proposed-adr] "
    labels: [adr, proposal]
    draft: true
    max: 1
    allowed-files:
      - "docs/adr/**"
      - "adr/**"
      - "docs/decisions/**"
---

# ADR Compliance Check

Ensure work in this repository (`${{ github.repository }}`) follows the Architecture Decision Records (ADRs) in the organisation's architecture repository, `lockran/architecture`.

## Steps

1. Read all accepted ADRs from `lockran/architecture` (look under `docs/adr`, `adr`, or `docs/decisions`; use `gh api` / `gh` read commands). Ignore superseded or rejected ADRs.
2. Examine this repository: on `pull_request`, focus on the files changed in the PR; on `schedule` / `workflow_dispatch`, review the whole repository (e.g. `template.yaml`, `Dockerfile`, pipelines, workflows, dependencies, code).
3. Classify each finding:
   - **Inconsistency**: the work contradicts an existing accepted ADR. Call `create-issue` describing the ADR (link and number), the violating file(s)/lines, and the suggested remediation. Check existing open issues with the `[adr-gap]` prefix first and do not duplicate.
   - **New decision needed**: the work makes a significant architectural choice (technology, pattern, platform, security, data, integration) that no ADR covers. Call `create-pull-request` against `lockran/architecture` adding a proposed ADR file following that repo's existing ADR template, numbering and format, with status `Proposed`, including context, options, decision drivers and a link back to this repository. Check for existing open proposals first.
4. If everything is consistent and no new decision is needed, call `noop` with a short reason. Also call `noop` if the ADRs cannot be found or read, stating why.

Only report high-confidence findings backed by specific ADR text and specific evidence in this repo. Issue bodies must be substantive and reference ADRs by link.
