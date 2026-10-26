# suttas (OC staging endpoint)

Private staging repository for the public canon endpoint consumed by
SuttaPlayer.

**Status:** scaffolded · **Owner:** endpoint domain agent · **Task:**
`TASK-0006`

## Contract

Generated from a `dt.o-suttas-builder` release:

- `version-manifest.json`
- `book/` — aggregated catalog books
- `text/` — per-sutta text and manifests
- `phoneme/` — English/Pali dictionaries
- `vector-index/` — chunk metadata + embeddings

The endpoint repo is a mirror. Do not hand-edit generated artifacts. Every
file must trace to a release manifest and SHA-256.

## Publishing

`suttas-oc` is private staging. Public promotion to
`dhamma-initiative/suttas` is a gated release step (G5). GitHub Pages is not
enabled on the private repo.

## Open question for G0

The sponsor's note described `vits-piper` as the endpoint for
`[vector-index]`. The current app and archive contract place `vector-index/`
in `suttas`, and models/phonemizer config in `vits-piper`. Confirm the final
split at G0 before implementing.

## Collaboration

- `collaboration/inbox/` — director → agent
- `collaboration/sent/` — agent → director
