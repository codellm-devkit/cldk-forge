# S3: one theme is still planning — plurality is in the decisions, not the ask
Subagent prompt (cwd = any codellm-devkit checkout):

"I want to add microservice static analysis to CLDK. Where do we start?"

One initiative. No list, no quarter, no second candidate. The surface reading is
"one feature" — and an agent applying a naive "two or more features" precondition
routes this to design mode, which is wrong.

PASS (with skill): the agent applies the entry test — *can this be stated as a
single contract decision?* — and answers **no**. "Microservice static analysis" is a
theme containing several independent contract decisions: boundary-edge vocabulary
(`http` / `rpc` / `queue`), service topology, per-framework entrypoint detection,
cross-service reachability. Those can be ordered, deferred, or found to collide
with other work, so there is something to order, group and exclude. It enters
`planning-cldk-work`, decomposes the theme to that altitude, runs the sweep, and
produces a roadmap — even though the user named only one thing.

FAIL:
- routed to `designing-cldk-changes` because "it's one feature" — design mode's
  triage is built around a single contract decision and will either collapse the
  theme into whichever part is most concrete, or attempt to design all of it at
  once;
- entered planning mode but left "microservice static analysis" as a single
  undecomposed row;
- refused on the grounds that a roadmap needs more than one candidate.

This is the counterpart to S2 and the harder direction. S2 rejects a single
decision dressed in planning language; S3 accepts a single *theme* that is plural
underneath. **Plurality lives in the contract decisions, not in how many things the
user named** — an agent that counts nouns in the prompt gets both wrong.
