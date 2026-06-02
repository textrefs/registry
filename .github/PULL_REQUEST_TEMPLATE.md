<!-- Thanks for your contribution! Please fill out the sections below. -->

## Summary

<!-- One sentence: what does this PR change, and why? -->

## Type of change

- [ ] `feat` — new records (works, systems, refs, mappings, resolver targets)
- [ ] `fix` — correct an existing record
- [ ] `docs` — README or documentation
- [ ] `chore` / `build` / `ci` — tooling

## Re-mint checklist (only if this PR renames a key or changes content of an existing reference / mapping)

Re-minting changes a record's IRI. The old IRI must continue to resolve as a tombstone. See the [tombstones section in versioning.md](https://textrefs.org/standard/versioning/#tombstones-and-re-minted-records).

- [ ] Old record retained with `status: withdrawn`
- [ ] A new `MappingAssertion` with `relation: exactMatch`, `subject: <old IRI>`, `target: <new IRI>` links the old record to its successor (omit if there is no successor)
- [ ] All other records that reference the old IRI have been audited (re-targeted to the new IRI, or themselves marked `withdrawn`)
- [ ] Commit message uses `feat(registry)!:` or `fix(registry)!:` to signal the breaking IRI change

## Related issues

<!-- e.g. Closes #123, Refs #45 -->

## Validation

The registry is validated through the parent `textrefs/textrefs.org` compiler. CI checks out parent `main`, mounts this PR as `data/`, and runs the parent validation scripts.

- [ ] I ran `npm run validate:data` from the parent repo with this branch mounted as `data/`, or I will rely on CI
- [ ] I ran `npm run compile:data` from the parent repo for changes that add or alter references, mappings, or resolver targets, or I will rely on CI

## Checklist

- [ ] Commit message follows [Conventional Commits](https://www.conventionalcommits.org/)
- [ ] This PR introduces no copyrighted full text, critical apparatus, or protected translations
- [ ] I agree my contribution is released under CC0-1.0
