# TextRefs Registry

Hand-authored seed registry data for [TextRefs](https://textrefs.org). This repository is included as a git submodule at `data/` in [`textrefs/textrefs.org`](https://github.com/textrefs/textrefs.org).

Registry data is released under [CC0-1.0](./LICENSE).

## Layout

```
works/{key}.yaml      # One Work per file — embeds its `mappings:` and `references:`
systems/{key}.yaml    # One CitationSystem per file
decisions/            # ADRs scoped to the registry
```

Only hand-authored YAML lives here. The compiled registry — expanded `CanonicalReference` and `MappingAssertion` records, the URL `aliases.json`, JSONL resources, and the Frictionless `datapackage.json` — is derived at build time by the compiler in `textrefs/textrefs.org` (see `.gitignore`). Published dumps are long-term archived in the [TextRefs Zenodo community](https://zenodo.org/communities/textrefs/) and receive citable DOIs.

See the [authoring guide](https://textrefs.org/get-started/authoring/) for the YAML format and the [versioning policy](https://textrefs.org/standard/versioning/) for the release process.

## Versioning

- Tagged releases use calendar-style SemVer: `vYYYY.MM.N` (e.g. `v2026.06.0`, `.1` for re-cuts).
- `main` is the working branch.
- The data-package `version` inside `datapackage.json` follows SemVer-without-`v`.

## Data boundaries

Registry records describe citation identity, mappings, aliases, and resolver targets. They do not contain full text, critical apparatus, protected translations, or application code.
