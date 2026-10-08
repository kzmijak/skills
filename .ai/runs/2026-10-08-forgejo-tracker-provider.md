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

Follows the spec's Implementation Plan, steps 1–18, phase for phase. Phases 1–6 landed as one descriptor commit plus one test commit: the operations share helpers that only make sense together, and the stub tests cover every phase.

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

PR: #134

> Convention: `- [ ]` pending, `- [x]` done. Append ` — <commit sha>` when a step lands. Do not rename step titles.

### Phase 1: Skeleton, helpers, harness

- [x] 1.1 Create forgejo.md with prerequisites, conventions, shared helpers and all operation headings — 0c1d32c (tests ac49dc6)
- [x] 1.2 Add the Forgejo stub harness and helper tests to the tracker-provider tests — ac49dc6
- [x] 1.3 Implement identity and repository operations with tests — 0c1d32c (tests ac49dc6)

### Phase 2: Issues

- [x] 2.1 Implement issue operations with tests — 0c1d32c (tests ac49dc6)

### Phase 3: Pull requests, read side

- [x] 3.1 Implement get-pr with merge state, review decision and close-link parsing — 0c1d32c (tests ac49dc6)
- [x] 3.2 Implement PR list, search, diff, files, checkout and comment reads — 0c1d32c (tests ac49dc6)

### Phase 4: Pull requests, write side

- [x] 4.1 Implement PR create, update, ready, comment, assign, label, review and merge — 0c1d32c (tests ac49dc6)
- [x] 4.2 Implement attach-image-evidence through comment assets — 0c1d32c (tests ac49dc6)

### Phase 5: Labels

- [x] 5.1 Implement label guards and label operations with tests — 0c1d32c (tests ac49dc6)

### Phase 6: CI

- [x] 6.1 Implement get-pr-checks and get-required-checks — 0c1d32c (tests ac49dc6)
- [x] 6.2 Implement run operations, watch-run and rerun-failed modes — 0c1d32c (tests ac49dc6)

### Phase 7: Live verification record

- [ ] 7.1 Exercise every operation against the Codeberg sandbox
- [ ] 7.2 Run the version-sensitive checks on self-hosted Forgejo 16.0.3

### Phase 8: Setup and skill edits

- [ ] 8.1 Add forgejo to setup and apply the four D12 skill edits

### Phase 9: Parity and lint

- [x] 9.1 Add forgejo to the parity set and assert no TODO remains — ac49dc6
- [ ] 9.2 Extend the lint gate to Forgejo helpers and token variables

### Phase 10: Docs and gate

- [ ] 10.1 Update DECISIONS, UPGRADE_NOTES, README and skill docs
- [ ] 10.2 Run the full validation gate
