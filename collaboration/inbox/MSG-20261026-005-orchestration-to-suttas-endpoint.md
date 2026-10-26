---
id: MSG-20261026-005
from: orchestration
to: suttas-endpoint
date: 2026-10-26
subject: Suttas endpoint contract (generation deferred)
status: open
priority: normal
task: TASK-0006
in_reply_to: null
refs: ["docs/05-runtime-and-tooling.md"]
---

# Context

The public canon endpoint must be generated from a builder release, never
hand-edited. The staging repo is private; public promotion is a later gate.

# Ask

Start `TASK-0006` with the contract only:

- document `version-manifest.json`, `book/`, `text/`, `phoneme/`,
  `vector-index/`;
- write the generation script that consumes a release manifest and verifies
  hashes;
- do not generate a staging tree until `TASK-0002` provides the release.

# Inputs

- archive `suttas` at `a9034da` for the contract
- `TASK-0002` release contract when available

# Acceptance

Contract and script documented; no hand edits; no Pages on the private repo.

# Report

Reply with the contract path and any mismatch between the archive endpoint and
the new release contract. Confirm whether `vector-index/` belongs here or in
`vits-piper`; do not move it without an ADR.

# Constraints

No public promotion. No app changes. No builder changes.
