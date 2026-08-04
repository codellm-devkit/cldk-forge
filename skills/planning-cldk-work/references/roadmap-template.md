# Roadmap template

The artifact `planning-cldk-work` produces. Lives at **`codellm-devkit/.github` →
`docs/design/roadmap.md`**, committed, next to the epics and cross-repo specs it coordinates.

One roadmap per planning pass, amended in place rather than duplicated per quarter — the git
history is the record of how thinking changed, which is exactly what an issue body cannot give you.

## Template

```markdown
# CLDK roadmap

**Pass:** <date>  ·  **Planned with:** <who>
**Status:** current  (supersede by editing, not by adding a second roadmap)

## Candidates

| # | Feature | Moves schema v2? | Collision group | Blocked by |
| - | ------- | ---------------- | --------------- | ---------- |
| 1 | <one line> | yes — <what vocabulary> / no | A | — |
| 2 | <one line> | yes — <same vocabulary> | A | 1 |
| 3 | <one line> | no — SDK surface only | — | 1 |

## Collision groups

Candidates that touch the same schema vocabulary. **Each group is ONE design session for the
shared part**, even where the features ship far apart — the parity clause makes a term coined
twice permanently wrong.

- **Group A — <vocabulary name>**: candidates 1, 2.
  <What must be decided once: the kinds/fields, and who emits them.>
  Design session: <which candidate carries the decision>.

## Dependency order

<A DAG, not a list. Say what unblocks what and why.>

    1 (vocabulary) ──▶ 2 (linking) ──▶ 3 (surface)

## Release trains

| Train | Carries | Notes |
| ----- | ------- | ----- |
| <analyzer x.y.0> | 1 | schema major — SDK pins only once cut |
| <sdk a.b.0> | 3 | after the analyzer release lands |

Candidates sharing a schema major ride **one** migration, not one each.

## Not now

**The list that makes the rest mean something.** What is excluded from this pass, and why.

- <candidate> — <reason: blocked, not worth it yet, superseded, no owner>

## Starting now

<The single feature entering design mode, and its epic link once filed.>
Everything else on this roadmap has no issue yet, by design.
```

Amend in place. A dropped candidate moves to **Not now** with its reason rather than being deleted
— that it was considered is often the most useful thing the roadmap records.
