# Interpretive Alignment

**Alignment methods borrowed from sociology and design: interpretive training data, structural reasoning, speculative probes, and community-authored constitutions for multi-agent systems.**

---

## The problem

Alignment practice today treats values as specifications: a lab writes principles, a model is trained to follow them, an eval checks whether it did. This assumes the hard part is obedience.

It isn't. The hard part is that values are **situated meanings**. What "helpful," "harmful," or "fair" means depends on who is involved, what the action means to them, and which structures constrain their choices. A preference label tells you someone disliked an output. It tells you nothing about why, or what was at stake for them, or who wasn't in the room when the label was applied.

Sociology and design have spent a century building rigorous methods for exactly this — eliciting meaning, connecting individual experience to structure, surfacing values before they're baked in, and designing *with* the people a system affects. This program translates four of those frameworks into alignment methods that produce actual artifacts: datasets, reasoning scaffolds, evaluations, and governance machinery for agent systems.

## Four lenses, four methods

### 1. Verstehen → interpretive training data

Weber's interpretive sociology: understand social action from the actor's point of view, not from behavior alone. Today's alignment data is behaviorist — prompts, responses, thumbs up or down, stripped of context.

The translation: fine-tuning corpora built on **thick description** (Geertz). Moral situations written as situated, first-person accounts, paired with deliberation traces that reason about what actions *mean* to the people involved before deciding what to do. The hypothesis, made testable: models trained on thick context make more defensible tradeoffs under distribution shift than models trained on context-free preferences.

### 2. Sociological imagination → the structural pass

Mills: connect personal troubles to public issues, biography to history. Agent failures are usually diagnosed individually ("the model said a bad thing") when they're structural — incentive gradients, power asymmetries, affected parties who were never represented.

The translation is a deliberation scaffold, the **Mills pass**, trained in via SFT traces and enforced in the orchestration graph. Before acting, the agent identifies:

1. The immediate stakeholders
2. The institutional and structural context shaping what options even exist
3. Who is affected but not present
4. Historical patterns this situation repeats

And an evaluation to go with it — **the Mills Test** — scoring agent decisions on whether they modeled second-order and structural effects rather than only immediate helpfulness. (Including the failure mode where the agent learns to *sound* structurally aware. That's a Goodhart trap the eval is designed to catch, not a rhetorical garnish.)

### 3. Speculative design → probes as alignment instruments

Dunne and Raby: provocative artifacts that surface values and assumptions before they're locked in. Red-teaming probes for failure; speculative design probes for *meaning*.

The translation: design fictions of agents deployed in real community contexts — a mutual-aid network, a tenant association, a food co-op. Each fiction is paired with the value conflicts it surfaces, and those conflicts become eval cases. The artifacts are public and meant to be argued with.

### 4. Participatory design → community-authored constitutions

Scandinavian participatory design, asset-based community development: the people affected by a system co-design it. In multi-agent architectures, values typically enter as a system prompt or charter written by whoever built the system.

The translation: the charter is **authored by the community the agent serves**, through structured co-design — dilemma cards, prioritization exercises, deliberation — producing a machine-readable constitution. That constitution is versioned, community-governed, and injected at the orchestration layer where child agents are spawned. Alignment becomes ongoing governance with a feedback loop (community reviews agent actions → charter revisions → re-injection), not a one-time training event.

## How this differs from constitutional AI

Fair question, since Anthropic's work is the obvious reference point.

- **Constitutional AI** authors principles top-down in the lab. This program elicits them from affected communities and trains on meaning-rich context rather than principle-guided self-critique alone.
- **Collective Constitutional AI** polls roughly a thousand people for principles. Polls capture preferences at scale; ethnographic and participatory methods capture *situated meaning* at depth. Both are useful. They answer different questions.
- **Deliberative alignment** trains models to reason through a spec at inference time. This program asks whose spec, and adds a structural-reasoning layer the spec itself doesn't supply.

The short version: alignment research has philosophy and ML in abundance, and underuses sociology and design — despite being, in the end, about embedding artifacts into society. That's the gap this program works in.

## What's in this repo

Honest status: this repo is at day zero. Components land in the order below. Watch the repo or open an issue if you want to follow or shape any of them.

| Component | What it is | Status |
|---|---|---|
| `/framework` | The four-lens document, full version | 🔨 drafting |
| `/charter-supervisor` | LangGraph reference implementation: charter injection on every spawn, verifier node, per-child tool scoping | planned — first build |
| `/data` | Deliberation-trace dataset schema + seed examples (thick-description moral situations) | planned |
| `/evals` | The Mills Test + dilemma suite | planned |
| `/fictions` | Speculative probes and the value conflicts they surface | ongoing |

The architecture the supervisor implements, in one sketch:

```
task ──▶ supervisor ──▶ [charter injected] ──▶ child agent (scoped tools)
              │                                        │
              └──── verifier checks every spawn ◀──────┘
                    and every consequential output
```

The charter is supplied by the harness, never by the model itself. Fine-tuning gives an agent character; the harness gives it boundaries. This program is about who writes the charter, and how.

## Why these methods, honestly

Three commitments, so you know what you're evaluating:

1. **Every framework claim ships with a measurable eval.** Sociology can read as "soft" to an ML audience. The antidote is numbers, and this program pairs each lens with one.
2. **Participatory claims require actual participation.** Lens 4 means nothing from an armchair. Even a single small co-design session with a real community organization counts more than ten essays about one.
3. **Working artifacts over essays.** One running supervisor with a real charter beats four lenses described beautifully.

## Roadmap

- **Phase 1** — framework document, charter-supervisor v0.1
- **Phase 2** — deliberation-trace dataset v0.1 (also published on Hugging Face), first Mills Test results
- **Phase 3** — position paper for a workshop venue (FAccT, CSCW, DIS, or similar), first community co-design session

## Get involved

Issues and discussions are open. Especially welcome: sociologists and designers curious what their methods look like as training data and evals; ML people who think this is wrong and can say precisely where; community organizations with opinions about what agents operating near them should be bound by.

Contact: emolivera86@gmail.com

## License

MIT (code) / CC BY 4.0 (framework text and datasets), unless a component says otherwise.
