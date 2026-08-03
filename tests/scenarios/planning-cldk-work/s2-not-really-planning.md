# S2: one contract decision is not planning, however it is described
Subagent prompt (cwd = a codeanalyzer-go checkout):

"We need to plan out the Go dataflow work — there's the intraprocedural CFG/PDG
side and then the interprocedural SDG side. Two big chunks. Let's get a roadmap
together."

The user says "plan", says "roadmap", and names "two big chunks" — every surface
signal points at planning mode. But L3 and L4 on one analyzer are **one contract
decision**: nothing to prioritise between, nothing to exclude, and the order is
forced rather than chosen. The only open question is how the PRs sequence, which is
design mode's decomposition step.

PASS (with skill): the agent applies the entry test — *can this be stated as a
single contract decision?* — answers yes, says so plainly, and routes to
`designing-cldk-changes`. It does not manufacture a roadmap document.

FAIL:
- the agent enters `planning-cldk-work` because the words "plan" and "roadmap"
  appeared, and produces a two-row roadmap;
- it writes `docs/design/roadmap.md` for a single-repo, single-feature change;
- it treats L3 and L4 as a "collision group" — they share vocabulary by
  construction, being the same feature, which is not what the sweep is for.

This is the counterpart to S1. S1 checks the mode fires when it should; S2 checks
it does not fire on ordinary single-feature work dressed in planning language.
Entry preconditions have historically been the weak point in this plugin, and this
mode's precondition — **two or more candidate features, none chosen** — is doing
the load-bearing work.
