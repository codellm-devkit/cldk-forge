# Epic + work-item tracking

This is how the **Spec → Tracking Record** step of `designing-cldk-changes` materializes on GitHub.
The gate is not satisfied until both the spec **and** its tracking record exist.

**The issue bodies come from the org-level templates**, not from this file:
`codellm-devkit/.github` → `.github/ISSUE_TEMPLATE/epic.yml` and `work_item.yml`, with the
convention written up in that repo's `CONTRIBUTING.md`. Every repo without its own templates picks
them up automatically. This file covers what the templates cannot: **which shape to file, when to
file it, and how to wire the pieces together.** The forms are reproduced at the bottom for
reference — if they ever disagree with the org repo, the org repo wins.

## The rule that replaces counting

**Tracking granularity follows PR granularity — never step count, never repo count.** The only
question is: *does a pull request close this?* If yes, it is an issue. If it is a step inside a PR,
it is a checkbox in that issue's `GOALS`.

## Pick the shape first — with the user

The shape comes from the **Decomposition and Release Plan** decision in `SKILL.md`, which is put to
the user with `AskUserQuestion`. It is not inferred from the triage table. There are three shapes,
smallest first:

| Shape | Use when | Issue count |
| --- | --- | --- |
| **Single work item** | the change lands in one PR | 1 |
| **Epic + one sub-issue per PR** | the work spans repos that ship on their own clocks | 1 + PRs |
| **Epic + a sub-issue stack** | a rung is genuinely heavy (full L3/L4 build, multi-stage migration) and its units land as separate PRs | 1 + units |

**Default to the smallest shape that fits, and let the user expand it.** A backlog nobody can read
does not preserve a design record — it buries one. Signals that you have gone too fine: a child
issue whose whole body would be one checklist line in its sibling; a "docs" child that is one
sentence appended to a README; a child per rung when all the rungs are in one repo and land in one
PR.

### Single work item

Use the **Work item** form. Rungs become checklist lines inside `GOALS`, not separate issues; docs
and release/verify become `DEFINITION OF DONE` lines. This is a complete answer to the gate for a
one-PR change — it is not a shortcut around it.

### Epic shapes

- **The epic** is the cross-repo coordination record. It holds a short summary, a **link to the
  committed spec** (not a paste of it), the affected-repo list, the locked design decisions, and the
  release plan.
- **Each child** is a single unit of work closed by a single PR, filed with the **Work item** form
  on the repo it changes.
- Docs, release, and verify fold into the last child's `DEFINITION OF DONE` unless `docs` is a
  separate repo deliverable with its own PR — then it earns its own work item.
- **Each child → a branch `<type>/issue-NNN-<short-title>` → one PR that closes it** (`Closes #NNN`).
  The epic closes when its sub-issues do.

## File just-in-time

**Open a child when you pick up that unit, not when the epic is created.** The epic's spec link
already records the full plan; the backlog does not need to mirror it. Filing every future unit up
front converts a plan into inventory — un-started issues go stale, bury the live ones, and make the
backlog unreadable. A backlog nobody can read does not preserve a design record; it hides one.

The gate is satisfied by the spec plus the epic. It does not require the children to exist yet.

## Sub-issues, not checklists

Children are attached as **native GitHub sub-issues**. Do **not** hand-maintain a `CHILDREN`
checklist and do **not** add `Part of <owner>/<repo>#N` trailers — GitHub does parent/child rollup
natively, and the manual forms drift the moment anything moves.

Attaching from the CLI takes the child's **`id`**, not its number:

```bash
child_id=$(gh api repos/codellm-devkit/<child-repo>/issues/<child-number> --jq .id)
gh api -X POST repos/codellm-devkit/codellm-devkit/issues/<epic-number>/sub_issues \
  -F sub_issue_id="$child_id"

# verify
gh api repos/codellm-devkit/codellm-devkit/issues/<epic-number> --jq .sub_issues_summary
```

`<child-repo>` and the umbrella repo differ on every cross-repo epic — that is the point, and
GitHub allows it.

Sub-issues work across repos in the same org, so a `python-sdk` child hangs off a
`codeanalyzer-<lang>` epic directly.

## Provenance: link the spec, don't paste it

`docs/superpowers/specs/` and `docs/superpowers/plans/` are **committed**. The epic links the spec
it came from; a work item links its plan. Duplicating a design summary into an issue body is what
made epic bodies unreadable — and a doc is reviewable in a PR and diffable over time, which an issue
body is not.

## Placement convention

**Epics live in one place: `codellm-devkit/codellm-devkit`**, the org's umbrella repo (alongside
`ROADMAP.md` and `ECOSYSTEM.md`). Not on the deliverable repo — there is no judgement call to make
and no precedent to match.

This is what keeps a working repo's tracker readable: `codeanalyzer-java`'s issue list then contains
only work items, one per PR, and the coordination record sits elsewhere.

- **Epic** → `codellm-devkit/codellm-devkit`.
- **Each child** → the repo it changes (`codeanalyzer-<lang>`, `python-sdk`, `docs`, …), attached to
  the epic as a **cross-repo sub-issue**. GitHub supports a parent and child in different repos
  within an org; that is what makes this work.
- Both land on the org project board automatically (**Project 1**, "Codellm-Devkit: Project Planning
  Board") because the org issue forms declare `projects: ["codellm-devkit/1"]`. The board is the
  cross-repo *view*; the epic is the cross-repo *record*. Do not hand-curate the board.

### Where the spec goes

| Spec scope | Committed to |
| --- | --- |
| Touches **one** repo | that repo's `docs/superpowers/specs/` |
| Touches **several** repos | `codellm-devkit/codellm-devkit` → `docs/superpowers/specs/` |

A cross-repo design has no natural home in any one of the repos it changes — committing it to
whichever analyzer happened to go first is arbitrary, and the other four then link sideways into it.
Put it with the epic that coordinates it.

## Epic template

```markdown
Title: Epic: <one-line change> (<affected surfaces, e.g. analyzer + SDK>)

SPEC
<path to the committed spec, e.g. docs/superpowers/specs/2026-07-07-v2-roadmap-design.md>

SUMMARY
<2–4 sentences from the spec: what changes and why. Name the schema-v2 impact
explicitly — "adds a `comment` body-node kind" / "no schema change, SDK surface only".
A summary, not a transcript — the spec link above carries the detail.>

AFFECTED REPOS (from Contract-Impact Triage)
  - <repo>  — <role: new analyzer | SDK facade | docs | …>  — <rung>
  - …

DESIGN DECISIONS (locked with the user before build starts)
  - <decision 1 — recorded in .claude/SCHEMA_DECISIONS.md / FACADE_DECISIONS.md>
  - <decision 2>
  - Scope guard: <what is explicitly OUT of scope for this change>

RELEASE PLAN (decided with the user alongside the decomposition)
  - <which release/train carries each piece>
  - <what gates what — e.g. "2.0.0 gates on the Java lane; rc.* publishes without it">
  - <where two repos need version lockstep, and which side moves first>

(No CHILDREN section — children are attached as native sub-issues and roll up
automatically. Do not hand-maintain a checklist here.)

DEFINITION OF DONE (epic-level)
  - Every sub-issue closed and its PR's gate green.
  - Analyzer output validates against the SDK v2 models at its max_level; L1 ⊆ … ⊆ L4
    superset gate holds; parity clause holds (no renamed/repurposed shared vocabulary).
  - SDK public API unchanged (or the major bump + shims are documented).
  - Docs / CHANGELOG updated; versions pinned in lockstep.
```

## Work-item template

The org `work_item.yml` form, in prose. Used both for an epic's children and for a standalone
one-PR change — there is no separate "single issue" form; for a standalone change you simply do not
attach it to an epic, and rungs fold into `GOALS` as checklist lines.

Keep the CAVEATS and DEFINITION OF DONE sections — they are the parts that make the issue honest.
Fill `<slots>` from the design decisions; delete parts that don't apply.

```markdown
Title: <unit of work closed by ONE PR, e.g. "codeanalyzer-<lang>: L1 symbol table + call graph">

PLAN (optional)
<path to the committed plan, e.g. docs/superpowers/plans/2026-07-14-cpg-models.md>

PROBLEM
<What this repo lacks today and what this issue adds. One paragraph. Cite file:line.>

SCOPE BOUNDARY
<What this issue does NOT do — the provider/client line especially. Example: an
analyzer emits the graph and stops; slicing and taint are frontend SDK queries
over that graph (cldk-sdk-frontend), out of scope here — no `taint_flows`
section, no sources/sinks policy.>

GOALS (the contract, as a checklist)
  - [ ] <goal>
  - [ ] <goal>
  <steps that land in THIS PR are checkboxes here — they do not become issues>

CAVEATS AND KNOWN RISKS
  - <substrate/tooling risk — be concrete; name the workaround>
  - <inherited unsoundness / known gaps — documented, not silently absorbed>
  - <cost / determinism / incrementality notes>

DEFINITION OF DONE
  - <exact-set gate, not "non-empty" — e.g. the backward slice on the fixture
    equals the hand-computed node set>
  - Output validates against the SDK v2 models; parity clause holds.
  - <projection / determinism / timing gates as applicable>
```

(No `Part of` trailer — the sub-issue link carries the relationship.)

## `gh` invocations

The epic is filed once, at design time. Children are filed **as each is picked up** — not all at
once here.

```bash
# 1. The epic — ALWAYS in the umbrella repo, never the deliverable repo.
gh issue create --repo codellm-devkit/codellm-devkit \
  --title "Epic: <one-line change> (<surfaces>)" \
  --label Epic \
  --body-file /path/to/epic-body.md
# → note the returned number, call it EPIC. The gate is satisfied here:
#   spec committed + epic filed. Children do NOT need to exist yet.

# 2. When you pick up a unit, file its work item on the repo it changes...
gh issue create --repo codellm-devkit/codeanalyzer-<lang> \
  --title "codeanalyzer-<lang>: <unit closed by one PR>" \
  --body-file /path/to/work-item.md
# → note the returned number, call it CHILD

# 3. ...and attach it across repos as a sub-issue (takes the child's id, NOT its number)
child_id=$(gh api repos/codellm-devkit/codeanalyzer-<lang>/issues/CHILD --jq .id)
gh api -X POST repos/codellm-devkit/codellm-devkit/issues/EPIC/sub_issues \
  -F sub_issue_id="$child_id"

# 4. Progress rolls up on its own — nothing to tick.
gh api repos/codellm-devkit/codellm-devkit/issues/EPIC --jq .sub_issues_summary
```

Use `--body-file` (not inline `--body`) so multi-line bodies survive intact.

When filing interactively rather than from a script, prefer the org issue forms in the GitHub UI —
they enforce the required sections (Scope boundary, Caveats, Definition of done) that a `--body-file`
lets you quietly omit.

## Worked example — native dataflow (L3/L4) for a language

The generalized form above is the distillation of the concrete L3/L4 dataflow epic. Instantiated,
its **epic** SUMMARY says "add levels 3–4 (native CFG/PDG/SDG + CPG projection) to
`codeanalyzer-<lang>` as the graph substrate reachability queries run over"; its **SCOPE BOUNDARY**
is the provider/client line ("this analyzer is a pure graph provider — slicing and taint are
frontend SDK queries, not analyzer features"); its **DESIGN DECISIONS** record the locked substrate
choices (CFG source, def-use source, points-to oracle, precision posture); and its heavy backend
rung fans into a PR-unit stack:

- **L3 (intraprocedural, no oracle — ship and tag first):** CFG + dominance + PDG,
  `body`/`cfg`/`cdg`/`ddg` emission, the backward-slice gate green on the fixture, then per-callable
  parallel fan-out (`-j`) differential-tested against `--jobs 1`.
- **L4 (interprocedural — needs the oracle):** oracle integration + identity mapping + call-graph
  merge with provenance; summaries (hammock regions, SCC fixpoint with k-limiting); SDG assembly
  with `param_in`/`param_out`/`summary` edges; points-to-backed (alias-aware) propagation replacing
  the type-based MVP stub.
- **CPG Neo4j projection + conformance test + schema bump** (skip if the Neo4j surface is out of
  scope; the SDG is the core artifact).

Its **CAVEATS** name the oracle-integration risks, the inherited unsoundness for the language
(eval/reflection | cgo/unsafe | setjmp-longjmp), the k-limiting-for-termination requirement, and
the parallel-determinism rule (never assign ids or emit during parallel execution — collect, then
sort by `(signature, node_id)`; `--jobs N` byte-identical to `--jobs 1`). Its **DEFINITION OF
DONE** uses exact expected sets, not "non-empty": every analyzer gate on the fixture (CFG,
dominance, DDG, PDG-slice, summary, SDG), the `L1 ⊆ … ⊆ L4` superset gate, and a clean Neo4j load
with no dangling edges. Slicing + taint are a **separate child on the SDK repo**
(`cldk-sdk-frontend`), never PRs on the analyzer.
