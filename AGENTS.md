# AGENTS.md

Registry data for TextRefs. This directory is the [`textrefs/registry`](https://github.com/textrefs/registry) submodule, consumed by [`textrefs/textrefs.org`](https://github.com/textrefs/textrefs.org). Schema, validation, and the compiler all live in the parent repo.

## Commands (run from the parent repo)

- `npm run validate:data` — schema + UUIDv5 determinism + JSON-LD context completeness.
- `npm run compile:data` — expands `references_range`, emits JSONL resources plus datapackage.
- `npm run build:data` — both of the above.

## Authoring rules

- Verify a Wikidata QID before mapping: fetch `https://www.wikidata.org/wiki/Special:EntityData/Q{n}.json` and confirm `labels.en.value` is the work — not an edition, translation, place, animal, or disambiguation page.
- Choose the mapping relation by what the target is, not by confidence: `alternateOf` when the target is another entity denoting the same Work (e.g. a Wikidata item); `isReferencedBy` when the target is a document or page about the Work (e.g. a Wikipedia article).
- Do not casually edit UUIDv5 seed fields: mapping (`subject`, `relation`, `target.identifier`); reference (`work_key`, `citation_system_key`, `locator`). Changing any of these mints a new ID.
- `references_range:` when the canonical set is regular and complete; `references:` when it is sparse, hierarchical, or source-defined.
- The top-level `citation_system:` is the work's **preferred** system: it is what mints the short `/cite/{work_key}/{locator}` alias and what the compiler publishes as `preferred_citation_system_key`. Changing it retargets that alias to a different reference — aliases are presentational and may be retargeted, but say so in the PR (ADR-0005).
- A work cited under more than one system lists the others under `additional_systems:`, each with its own `citation_system:`, `resolvers:`, and locators. Their references get only the qualified `/cite/{work_key}/{citation_system_key}/{locator}` alias.
- `reference_status:` sets the status of one block's references. The top-level block defaults to the work's status; an `additional_systems` block defaults to `draft` and never inherits `active` — promote a fallback system deliberately, not by adding it.
- Locators MUST NOT contain `/`: the alias grammar tells `{work}/{locator}` from `{work}/{system}/{locator}` by segment count alone, and the compiler rejects the file otherwise.
- Resolver template variables: `{chapter}` etc. come from named capture groups in the system's `locator_regex`; compiler also derives `{var02..04}`, `{varRoman}`, and `{verseGlobal}` (the last requires `chapter_sizes:` on the system). Confirm the variables you use exist.
- For URLs that don't fit a template, use `url_by.{var}` maps; for one-off URLs, `extra_resolvers` on the reference.
- Record `last_checked` on resolver URLs when adding or updating source-derived resolvers, and note licence/access where known.
- Data boundaries: citation identity, mappings, aliases, resolver targets only. No full text, critical apparatus, protected translations, or application code.

## Naming and identity

Get `work.key`, `work.preferred_label`, and `work.creators` right on the first author — renaming a key after publication is a tombstone event (see `scripts/compile.ts` tombstone invariants).

- **`key`** — shape is `{author-slug}.{work-slug}` for attributed works; bare `{work-slug}` for anonymous, collective, or canonical corpora.
  - `author-slug`: lowercased family name (or single mononym for antiquity); ASCII-folded; `-` for spaces; no initials. E.g. `homer`, `plato`, `aristotle`, `wittgenstein`, `confucius`, `laozi`, `murasaki-shikibu`.
  - `work-slug`: the short form readers actually use — `iliad`, `republic`, `tractatus`, `analects`, `daodejing`. Avoid cryptic initialisms (`eth-nic`) and avoid full Latin titles unless that _is_ the short form.
  - Bare slug for unattributed corpora: `tanakh`, `dhammapada`, `new-testament`, `quran`.
  - Multiple works per author with the same short title: disambiguate inside the work-slug, not by promoting the author. E.g. `aristotle.nicomachean-ethics`, `aristotle.eudemian-ethics`.

- **`preferred_label`** — the display title. No parenthetical disambiguator: author goes in `creators`, edition (SBLGNT, OCT, …) goes on the resolver target, alt-names belong in a future `alt_labels` field.
  - Attributed: just the title — `Iliad`, `Republic`, `Tractatus Logico-Philosophicus`.
  - Anonymous/collective: the conventional English name — `Tanakh`, `Dhammapada`, `New Testament`.

- **`creators`** — CSL-style entries matching the `Creator` schema. Follow CSL-JSON conventions so citeproc-js / Zotero render correctly.
  - Standard names with family + given: `kind: person`, e.g. `{ family: Wittgenstein, given: Ludwig }`.
  - Mononyms (Homer, Plato, Confucius, Laozi, Murasaki Shikibu, …): `kind: person` with `family` only and no `given`. This is the CSL convention for single-name authors and matches Chicago's "Homer, _Iliad_ 1.1." output.
  - Anonymous / collective: **omit `creators` entirely**. Do not write a literal "Anonymous"; absence is the correct CSL signal and the Chicago formatter already handles missing authors.
  - Reserve `kind: literal` for names that genuinely should not decompose: corporate/institutional authors ("World Health Organization"), or pseudonymous attribution strings ("[Pseudo-]Aristotle").
  - Attributed-but-disputed (e.g. Laozi for _Daodejing_): record the traditional attribution as `kind: person, family: Laozi`; do not encode the dispute in the name string. TextRefs has no relation for attribution uncertainty in this version.

## Pointers

- Full YAML format and worked example — [authoring guide](https://textrefs.org/get-started/authoring/).
- Schema source of truth — `standard/schema/*.ts` in the parent repo.
- Project layout, site conventions, submodule workflow — parent [`AGENTS.md`](../AGENTS.md) and [`CONTRIBUTING.md`](../CONTRIBUTING.md).
