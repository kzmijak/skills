# Forgejo tracker provider

Source doc: .ai/specs/2026-10-08-forgejo-tracker-provider.md (spec PR #133; issue #132)

## Overview

Add a shipped, stand-alone `forgejo` tracker descriptor. A repository hosted on Forgejo, self-hosted or Codeberg, can then run the whole pipeline (issues, pull requests, reviews, CI, and labels) through the Forgejo REST API with `curl` + `jq`, without a GitHub companion.

## Goal

`om-setup-agent-pipeline` installs a ready-to-use `.ai/trackers/forgejo.md` that implements every tracker operation `github.md` implements, with the same guard, claim, and serialization semantics. No skill learns Forgejo, apart from the four additive edits in spec D12.

## Scope

- New `skills/om-setup-agent-pipeline/references/trackers/forgejo.md`, following spec D1–D11, the Shared helpers, the Conventions, and the Operation map.
- Contract tests in `scripts/test-tracker-providers.mjs`. A stubbed `fj_http` with recorded fixtures checks parity, the helpers, the guards, serialization, search translation, and the rerun modes.
- Setup integration (body, interview questions, `TEMPLATE.md`), plus the D12 edits:
  - workflow detection for the `forgejo` tracker in setup and in `om-prepare-test-env`;
  - Forgejo links in `om-followup-issue-from-pr`;
  - the `RERUN_UNAVAILABLE` branch in `om-auto-fix-pr` CI stabilization.
- A lint rule that rejects Forgejo helper names and the token variable outside `references/trackers/`.
- Docs: README, `docs/skills/om-setup-agent-pipeline.md`, `DECISIONS.md`, `UPGRADE_NOTES.md`.

## Non-goals

- Gitea, and Woodpecker run operations.
- Split providers with a Forgejo code host, per-agent identities, MCP.
- Changing the tracker operation contract, operation names, config schema, or chaining-line shapes.
- Installing a Forgejo CLI or runner, or storing tokens.

## Implementation Plan

Follows the spec's Implementation Plan, steps 1–18, phase for phase.

## Risks

- Several server behaviors are inferred, not read from source:
  - the status context of a dispatched run;
  - the review `event` values;
  - the `refs/pull/N/head` ref of pull-request runs;
  - reading `GET /branches/{base}` without admin.

  They are verified live in Phase 7 (Codeberg, then self-hosted 16.0.3) and recorded below.
- Codeberg runs a 16.0.0 dev build. Version-sensitive results are repeated on 16.0.3 per spec D11.
- Hosted Codeberg runners have quotas. CI checks use tiny workflows.

## Live verification

(Filled in Phase 7.)

## Progress

> Convention: `- [ ]` pending, `- [x]` done. Append ` — <commit sha>` when a step lands. Do not rename step titles.

### Phase 1: Skeleton, helpers, harness

- [ ] 1.1 Create forgejo.md with prerequisites, conventions, shared helpers and all operation headings
- [ ] 1.2 Add the Forgejo stub harness and helper tests to the tracker-provider tests
- [ ] 1.3 Implement identity and repository operations with tests

### Phase 2: Issues

- [ ] 2.1 Implement issue operations with tests

### Phase 3: Pull requests, read side

- [ ] 3.1 Implement get-pr with merge state, review decision and close-link parsing
- [ ] 3.2 Implement PR list, search, diff, files, checkout and comment reads

### Phase 4: Pull requests, write side

- [ ] 4.1 Implement PR create, update, ready, comment, assign, label, review and merge
- [ ] 4.2 Implement attach-image-evidence through comment assets

### Phase 5: Labels

- [ ] 5.1 Implement label guards and label operations with tests

### Phase 6: CI

- [ ] 6.1 Implement get-pr-checks and get-required-checks
- [ ] 6.2 Implement run operations, watch-run and rerun-failed modes

### Phase 7: Live verification record

- [ ] 7.1 Exercise every operation against the Codeberg sandbox
- [ ] 7.2 Run the version-sensitive checks on self-hosted Forgejo 16.0.3

### Phase 8: Setup and skill edits

- [ ] 8.1 Add forgejo to setup and apply the four D12 skill edits

### Phase 9: Parity and lint

- [ ] 9.1 Add forgejo to the parity set and assert no TODO remains
- [ ] 9.2 Extend the lint gate to Forgejo helpers and token variables

### Phase 10: Docs and gate

- [ ] 10.1 Update DECISIONS, UPGRADE_NOTES, README and skill docs
- [ ] 10.2 Run the full validation gate
