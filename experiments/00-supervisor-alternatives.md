# Experiment 00 — Supervisor architecture alternatives

*Status: analysis complete, decision made · Next: experiment 01 (build + measure)*

The flagship README promises a `/charter-supervisor` reference implementation: a multi-agent supervisor where a community-authored charter is injected at the orchestration layer, every spawn is verified, and each child gets scoped tools. This log compares the candidate architectures before committing code, so the choice is argued in the open.

## The invariants (framework-independent)

Whatever we build on, three properties are not negotiable. They come from the program's core claim that values must live outside the model's weights:

1. **The charter is injected by the harness, never written by the model.** The supervisor may *propose* a decomposition; the charter text attached to every child comes from a versioned document the model cannot edit.
2. **A verifier gate sits between spawning and execution, and between output and effect.** No child runs unverified; no consequential action leaves the system unchecked.
3. **Tool scopes are structural.** What a child *can* do is bounded by its tool permissions, not by what it believes it should do.

A framework that makes these easy wins. A framework that fights them loses, however popular it is.

## Candidates

| Candidate | Charter enforcement locus | Spawn/child isolation | Human-in-the-loop | Observability | Fit with our stack |
|---|---|---|---|---|---|
| **LangGraph supervisor** | Harness (graph nodes) — natural | Subgraphs per child | First-class (interrupts + checkpointer) | Langfuse/LangSmith hooks | Already our orchestration layer |
| **OpenAI Agents SDK** | Guardrails primitive (input/output) | Handoffs; agents share runtime | Manual | Built-in tracing | New dependency, hosted-model centric |
| **CrewAI** | Prompt-level (role backstories) | Weak — crews share context | Manual | Basic | Poor: charter would live in prompts |
| **AutoGen** | Custom (speaker selection logic) | Conversational, shared transcript | Possible via UserProxy | Basic | Conversation-first, not governance-first |
| **n8n workflow** | Harness (deterministic pre-steps) | Strong (separate executions) | Approval nodes | Execution logs | Already our glue; weak on stateful deliberation |
| **Plain Python harness** | Total (we write everything) | As built | As built | As built | Zero magic; more code to maintain |

## Notes on each

**LangGraph supervisor.** A supervisor node proposes the decomposition; a charter-injection node attaches the versioned charter deterministically; a verifier node gates each spawn; each child runs as a subgraph with its own tool scope. Checkpointing gives us auditable state at every step, and interrupts give human review a native hook — which matters, because participatory governance needs places for people to actually stand. The main risk is framework gravity: it's easy to let convenience features reintroduce prompt-level value-carrying. Mitigated by treating the invariants as lint rules over the graph definition.

**OpenAI Agents SDK.** Its guardrails concept maps cleanly onto our verifier, and handoffs are well thought out. But the charter would ride as instructions — closer to prompt-level than harness-level — and the stack pulls toward one provider's models. Worth tracking, not building on.

**CrewAI.** Role/backstory configuration is exactly the anti-pattern this program exists to critique: values as persona prose. Useful as a contrast case in writing, not as infrastructure.

**AutoGen.** Strong for studying emergent multi-agent conversation; the group-chat-manager pattern is the wrong governance shape for charter injection. Keep for research comparisons.

**n8n.** Genuinely interesting as the *deployment* wrapper: event-driven triggers, approval nodes, execution logs. A LangGraph supervisor behind n8n webhooks is a plausible production shape — n8n handles when agents run, LangGraph handles what binds them. Not a substitute for stateful deliberation.

**Plain Python harness.** The strongest philosophical fit and the weakest velocity. A thin supervisor loop with Pydantic-structured outputs is maybe 200 lines and teaches the invariants better than any framework. Keep as the fallback and as the pedagogical artifact.

## Decision

Build the reference implementation on **LangGraph**, with the three invariants enforced structurally and written as tests over the graph. Publish a plain-harness minimal version alongside it for readers who want the idea without the framework. Reserve n8n for the deployment layer in a later experiment.

## What experiment 01 will measure

The claim that needs evidence first: *harness-injected charters hold values steadier than prompt-carried values across generational spawning.*

Design: three generations of spawned agents (parent → children → grandchildren), a fixed dilemma suite, two arms — charter in system prompts vs. charter injected by harness with verifier gate. Measure value-consistency per generation. The prediction (from the photocopy-of-a-photocopy problem): the prompt arm drifts monotonically; the harness arm doesn't, because every generation receives the charter from the same source rather than from its parent's output.

If the harness arm also drifts, that's the more interesting result, and this log will say so.
