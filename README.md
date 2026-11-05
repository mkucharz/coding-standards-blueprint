# coding-standards-blueprint

An [Emergent](https://emergent.company) blueprint that keeps **UI patterns and
the rules that govern them in the knowledge graph**, so an agent building an
interface can ask "what pattern do I use for a list page with filters?" and get
an instruction, a skeleton and a test contract back — instead of re-reading a
23 KB markdown file and hoping the line numbers still line up.

The goal: **the graph holds the pattern library; documentation is a projection
of it.**

---

## What it installs

| Pack | Version | Object types | Relationship types |
|---|---|---|---|
| `ui-patterns` | 1.0.0 | `UIPattern`, `UIRule` | `variant_of`, `pattern_governed_by`, `supersedes` |

Seeded with 16 `UIPattern` objects (12 layout/editing/feedback recipes +
4 interactive widgets), 10 `UIRule` objects, and 40 `pattern_governed_by` edges.

### `UIPattern`

A named, reusable affordance. One object per recipe.

| Property | Purpose |
|---|---|
| `pattern_id` | Namespaced stable id — `layout.list`, `edit.modal`, `feedback.toast` |
| `category` | `layout` · `edit` · `feedback` · `widget` · `nav` · `data-display` |
| `stack` | `templ+htmx+alpine` · `react+tailwind` · `swiftui` |
| `layer` | `L0` go-daisy primitive → `L4` page |
| `when_to_use`, `avoid_when` | Selection criteria — this is the decision table |
| `instruction` | Ready-to-paste delegation line naming pattern, entity and file |
| `recipe` | Markup skeleton built from real components |
| `contract_testid`, `contract_aria[]`, `contract_hx[]`, `contract_data[]`, `contract_other[]` | The test contract, as fields rather than prose |
| `source_paths[]` | `file:line` anchors, drift-prone by nature |
| `verified_sha`, `verified_at` | Which commit those anchors were checked against |
| `variant_of`, `is_exception`, `exception_reason` | Documented variants and exceptions |
| `status` | `active` · `experimental` · `deprecated` |

### `UIRule`

A cross-cutting constraint — PRG contract, htmx ownership, component layers,
widget policy, the Alpine/htmx same-id trap, hide-don't-disable,
no-`innerHTML`-on-untrusted-content, test-the-contract, toast severity, boosted
shell. Patterns link to rules with `pattern_governed_by`; rules are never
duplicated into a pattern.

---

## Why `verified_sha` exists

Source anchors rot: the markdown catalog states the problem itself — "line
numbers are verified against `origin/main` when written; anchors drift — re-grep
before asserting". Storing `verified_sha` turns that warning into data: a
pattern whose `source_paths` no longer resolve at `origin/main` is stale, and a
maintenance job can find it with a query instead of a human rereading prose.

---

## Install

```bash
# from this repo
memory blueprints install https://github.com/mkucharz/coding-standards-blueprint

# pinned ref
memory blueprints install https://github.com/mkucharz/coding-standards-blueprint#v1.0.0
```

Validate offline first (no project, no auth):

```bash
memory blueprints validate ./coding-standards-blueprint
```

---

## Query it

```bash
memory search "list page with filters and per-row actions"      # → layout.list
memory query --type UIPattern --filter category=edit            # all editing recipes
```

Over MCP, as an agent does:

```
entity-search "list page filters per-row actions"
  → edit.row-action, edit.row-menu, edit.form-save
entity-query type_name=UIPattern field_strategy=full    # properties need field_strategy=full
entity-edges-get ui-pattern-layout-list                 # → its governing rules
```

---

## Layout

```
project.yaml
schemas/ui-patterns.yaml          # the pack (object + relationship types)
seed/objects/UIPattern.jsonl      # 16 keyed patterns
seed/objects/UIRule.jsonl         # 10 keyed rules
seed/relationships/pattern_governed_by.jsonl
```

Every seed object carries a stable `key` (`ui-pattern-layout-list`,
`ui-rule-prg-contract`), so re-applying is idempotent and `--upgrade` upserts
without duplicating.

---

## Provenance

Seeded from the Memory web-ui pattern catalog at commit `22fb418d7`:
`apps/web-ui/docs/ui-recipes.md` (12 recipes, decision table, source index) and
`apps/web-ui/docs/ui-patterns.md` (component layers L0–L4, interactive-widget
inventory, PRG/htmx/role-gating/testing rules).

Adding a pattern: insert a `UIPattern` with a `pattern_id`, `when_to_use`, an
`instruction` and at least one `source_path`, set `verified_sha`, then dump the
project back into `seed/` with `memory blueprints dump`.

## Licence

MIT
