# Suttas — canon text endpoint for SuttaPlayer

This repository is the public, static canon-text endpoint consumed by the
SuttaPlayer PWA (`sp3-nb`). It is generated from the
[`dt.o-suttas-builder`](https://github.com/dhamma-initiative/dt.o-suttas-builder)
release pipeline and published through GitHub Pages.

Do not hand-edit generated artifacts. Fix the source or the builder, publish a
new builder release, and hydrate this repository again.

## Artifact layout

- `text/` — stable per-sutta text paths and per-catalog manifests.
- `book/<Catalog>-book-with-file-hdrs.txt` — stable aggregated catalog books.
  Book filenames do not contain a version label.
- `phoneme/` — English and Pali phoneme dictionaries coupled to this text build.
- `vector-index/` — RAG chunk metadata and binary embeddings.
- `version-manifest.json` — the authoritative build version for all artifacts.
- `release-manifest.json` — provenance for the builder release that produced
  the current artifacts.
- `hydrate.json` — the mapping used by the builder's hydration command.

Consumers must resolve the build version from `version-manifest.json` and use it
for cache invalidation. Do not derive the version from an artifact filename.

## Source of truth

A release is identified by a six-digit `YYMMDD` build version, for example
`261020`. The corresponding builder release tag is `build-261020` and the
artifact is `dt.o-suttas-builder-261020.tgz`.

The builder release is immutable. Hydration verifies the artifact SHA-256 before
replacing any files. The mappings are declared in `hydrate.json`:

```json
{
  "mappings": [
    { "from": "build/book", "to": "book", "mode": "replace" },
    { "from": "build/phoneme", "to": "phoneme", "mode": "replace" },
    { "from": "build/text", "to": "text", "mode": "replace" },
    { "from": "build/vector-index", "to": "vector-index", "mode": "replace" },
    { "from": "build/version-manifest.json", "to": "version-manifest.json", "mode": "file" }
  ],
  "provenanceFile": "release-manifest.json",
  "pages": { "repo": "dhamma-initiative/suttas", "branch": "{version}", "path": "/" }
}
```

`mode: "replace"` removes stale files, so deleted or merged suttas do not remain
in the published endpoint.

## Publishing a release

### Recommended: publish from the builder

The builder script publishes the release asset and hydrates this repository:

```bash
cd /home/ash/dev/di/dt.o-suttas-builder

deno run -A tool/publish-suttaplayer.ts --version 261020 \
  --targets suttas \
  --suttas-branch 261020 \
  --commit --push --pages
```

To publish both SuttaPlayer data endpoints (`suttas` and `vits-piper`) in the
correct order:

```bash
deno run -A tool/publish-suttaplayer.ts --version 261020 \
  --suttas-branch 261020 \
  --vits-piper-branch ver2-261020-9429 \
  --commit --push --pages
```

For the first publish of a new build version, create the version branch from
the current target HEAD:

```bash
deno run -A tool/publish-suttaplayer.ts --version 261023 \
  --targets suttas --suttas-branch 261023 --create-branch \
  --commit --push --pages
```

The script:

1. Runs the builder integrity checks and publishes the release artifact.
2. Verifies the asset SHA-256.
3. Replaces `book/`, `phoneme/`, `text/`, and `vector-index/`.
4. Updates `version-manifest.json` and writes `release-manifest.json`.
5. Commits and pushes the target branch when `--commit --push` is supplied.
6. Repoints GitHub Pages to that branch when `--pages` is supplied.

### Direct hydration

Use this when the builder release already exists and you only need to refresh
this repository:

```bash
cd /home/ash/dev/di/dt.o-suttas-builder

deno run -A tool/0.pipeline.ts hydrate --version 261020 \
  --target ../suttas \
  --branch 261020 \
  --commit --push --pages
```

The target worktree must have no tracked changes. Untracked files are allowed.
Use `--create-branch` when the version branch does not exist yet, and `--force`
only when you intentionally want to override tracked changes.

### GitHub Pages

The Pages source is the build-version branch (`261020`) at `/`. The builder
updates it with the GitHub Pages REST API:

```bash
deno run -A tool/publish-suttaplayer.ts --version 261020 \
  --targets suttas --suttas-branch 261020 \
  --commit --push --pages
```

If Pages is not enabled, the builder creates it. The token needs Pages write
permission.

## Verification

Check the published version and artifact digests:

```bash
curl -fsSL https://dhamma-initiative.github.io/suttas/version-manifest.json
```

The public phoneme maps should match the local hydrated copies:

```bash
sha256sum \
  'phoneme/pho_en[gb]-to-espeak-v1.51-ipa.json' \
  'phoneme/pho_pi[si]-to-aksharamukha-2.1.0-ipa.json'
```

The same digests are recorded by `vits-piper` in
`phoneme/phonemizer-config.json`.

## Consumers

`sp3-nb` uses:

- `https://dhamma-initiative.github.io/suttas/` as `endpoints.suttasBase`
- `version-manifest.json` for canon/vector cache invalidation
- `book/` for book-first loading
- `text/` for per-sutta content
- `vector-index/` for local RAG retrieval
- `phoneme/` through the `vits-piper` runtime config

## License

The suttas on dhammatalks.org are primarily released under the Creative Commons
Attribution-NonCommercial 4.0 Unported License.

License Type: Creative Commons Attribution-NonCommercial 4.0 (CC BY-NC 4.0).

Usage: You are free to share and adapt the material for non-commercial
purposes, provided you give appropriate credit.
