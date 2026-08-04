# Changelog

All notable changes to the CLDK DevTools plugin are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.4.0] — 2026-08-04

### ✨ Added

- **`planning-cldk-work`** — a new mode upstream of design, for work that cannot be
  stated as a single contract decision: a theme that decomposes into several
  (e.g. "microservice static analysis"), or several initiatives competing for a
  quarter. Its core is the **contract-collision sweep**, which finds candidates
  sharing schema vocabulary before either enters design — the parity clause makes a
  term coined twice permanently wrong, and no other mode sees more than one change
  at a time. Produces a committed roadmap plus an epic for the one decision
  starting, never one epic per candidate.
- `references/roadmap-template.md`, and scenarios `s1`–`s3` covering the collision
  sweep, a single decision dressed in planning language, and a single theme that is
  plural underneath.

## [0.3.0] — 2026-08-03

### ⚠️ Changed — **BREAKING** (behavioural)

This release changes how agents decompose and track work. Sessions running under
0.2.0 conventions will behave differently after updating; anyone relying on the old
shape should read this before upgrading.

- **Issue decomposition is now proportional and user-decided.** 0.2.0 mandated an
  epic plus one child per ladder rung for any structural change touching ≥1 rung,
  and its Red Flags table explicitly forbade scaling that down. Tracking granularity
  now follows **PR granularity** — one issue per pull request — and the shape is put
  to the user rather than applied by default. A single-PR change is one issue.
- **`<HARD-GATE>` semantics changed.** It previously bound to "the spec AND the
  GitHub epic + child issues exist". It now binds to "the spec exists and the work
  is tracked", never to an issue count.
- **Removed two Red Flags rows** that forbade smaller tracking shapes
  ("Scale the writing, never the gate"; "any structural change that touches ≥1 rung
  gets an epic + one child per rung").
- **Epics moved repos.** They now live in `codellm-devkit/.github`, not on the repo
  owning the deliverable.
- **`Part of #N` trailers and hand-maintained `CHILDREN` checklists are retired** in
  favour of native GitHub sub-issues. Existing epics carrying either will not be
  updated automatically.
- **Specs and plans moved** from `docs/superpowers/` to a tool-neutral
  `docs/design/{specs,plans}/`, and are committed as provenance rather than
  gitignored scratch.
- **Every ladder transition now stops for the user.** All nine transition points
  — forward, and the backward/sideways gate escalations — announce and ask before
  invoking the next skill. An end-to-end run is materially more interactive than
  under 0.2.0.

### ✨ Added

- Issue bodies now come from org-level forms in `codellm-devkit/.github`
  (`epic.yml`, `work_item.yml`), with the convention in that repo's
  `CONTRIBUTING.md`.
- Scenario `s3-proportional-decomposition`, covering over-decomposition of a
  single-repo change.
