# Cheat-Sheet — Prompting & Skills

*One-liners for fast scanning. For the full entry (when to use, pitfalls, example, source) see [glossary.md](./glossary.md).*

**Top wins:** be specific · give examples (few-shot) · separate instructions from data (delimiters) · ask for step-by-step (chain-of-thought) · set standing rules once (system prompt) · align before building (grilling).

---

## Core concepts
- **Prompt** — the instructions you give an AI; prompt engineering = structuring them so you reliably get what you want.
- **Token** — chunks of text (~4 chars) that usage, cost, and limits are measured in.
- **Context window** — all the text the AI can see at once; overflow is forgotten, and clutter hurts quality.
- **In-context learning** — the model "learns" from examples/instructions in the prompt, only for that session.
- **Temperature** — randomness knob; near 0 for code/facts, higher for creativity.
- **Hallucination** — confident output that's actually invented or wrong.
- **Grounding** — anchoring answers in source material you provide so they're verifiable.
- **RAG** — system auto-looks-up relevant docs and inserts them before answering.

## Prompt structure
- **System prompt** — standing rules for the whole conversation (the constitution).
- **User prompt** — the specific request this turn (today's task).
- **Role / persona** — "act as a senior reviewer" to set tone, depth, assumptions.
- **Delimiters** — separators marking instructions vs. data vs. examples.
- **XML tags** — Claude's preferred delimiters: `<instructions>`, `<data>`, `<example>`.
- **Output formatting** — state the exact shape: JSON, table, bullets, code-only.
- **Prompt template** — reusable skeleton with fill-in slots for repeated tasks.

## Prompting techniques
- **Zero-shot** — instructions only, no examples; for simple tasks.
- **Few-shot (multishot)** — include worked input→output examples; highest-impact for accuracy/style.
- **Chain-of-thought (CoT)** — "think step by step" before answering; for reasoning tasks.
- **Self-consistency** — answer several times, take the most common; for tricky reasoning.
- **ReAct** — reason → act (tool/search) → observe → repeat; the agent loop.
- **Prompt chaining** — break a task into a sequence where each output feeds the next.
- **Least-to-most** — decompose simplest→hardest, solving in order.
- **Meta-prompting** — use the AI to write/improve a prompt.
- **Negative prompting** — state what to avoid (use sparingly; prefer saying what you DO want).
- **Stop sequences** — text that halts generation at a boundary.
- **Prefilling** *(legacy)* — starting the answer for the model; unsupported on current Claude — ask for the format instead.
- **Guardrails** — rules that keep behavior in bounds and enable refusals.

## Alignment & collaboration *(Pocock)*
- **Misalignment** — the agent builds something different from what you wanted; the #1 failure mode.
- **Grilling / "Grill Me"** — the agent interrogates *you* to sharpen the plan before any code.
- **One question at a time** — ask, wait, then ask the next; include a recommended answer.
- **Design tree** — the hierarchy of interdependent decisions; resolve top-down.
- **Rubber-ducking, made argumentative** — the duck pushes back instead of nodding along.
- **Adding friction** — deliberately slow the agent where slowing down matters.
- **Vibe coding** *(anti-pattern)* — unconstrained "just build it"; fine for throwaways, risky for production.
- **Shared / ubiquitous language** — project vocabulary so one word replaces a paragraph.
- **Domain modeling** — actively building and sharpening that vocabulary during design.
- **CONTEXT.md** — a root glossary file holding only domain vocabulary.
- **Handoff** — compact doc letting a fresh agent resume without replaying context.
- **Agent brief** — the concise spec on a "ready-for-agent" issue.
- **Triage state machine** — moving issues through needs-info → ready-for-agent, etc.

## Planning & delivery
- **Spec-driven development** — write the spec first, then let the agent implement.
- **Constitution** — fixed project rules the agent must never violate.
- **Brainstorming** — Socratic pre-coding design refinement in chunks.
- **PRD** — "As a [actor], I want [feature], so that [benefit]" requirements doc.
- **Writing plans** — tiny tasks (~2–5 min) with exact paths, full code, verification.
- **Task right-sizing** — smallest unit that carries its own test cycle and review gate.
- **Vertical slice** — thin end-to-end cut through all layers, independently demoable.
- **Tracer bullet** — narrow-but-complete path through the system to prove it works (kept, not throwaway).
- **Horizontal slicing** *(anti-pattern)* — building one whole layer at a time.
- **Interfaces block** — exact inputs/outputs contract between sequential tasks.
- **Prefactoring** — "make the change easy, then make the easy change."
- **Executing plans** — batch execution with human checkpoints between steps.
- **Subagent-driven development** — subagents do tasks; two-stage review gates them.
- **Dispatching parallel agents** — concurrent agents on independent work.
- **Git worktrees** — isolated working dirs so parallel work doesn't collide.
- **Goal-driven execution** — define verifiable success criteria before starting.
- **Throwaway prototype / spike** — code to answer one design question, then deleted.

## Debugging & testing
- **TDD / red-green-refactor** — failing test → minimal pass → refactor; never refactor while red.
- **Behavior over implementation** — test through public interfaces, not internals.
- **Systematic debugging (4-phase)** — no fixes without root-cause investigation first.
- **Debugging discipline (6-phase)** — reproduce → minimize → hypothesize → instrument → fix → post-mortem.
- **Feedback loop** — automatic pass/fail signal; "build the right loop and the bug is 90% fixed."
- **Loop qualities** — red-capable, deterministic, fast, agent-runnable.
- **Falsifiable hypothesis** — "if X is the cause, changing Y will…"
- **Root-cause tracing** — trace back to the trigger instead of patching the symptom.
- **Tagged debug instrumentation** — uniquely-prefixed logs you can grep and remove.
- **Condition-based waiting** — poll a condition instead of `sleep()`.
- **Defense-in-depth** — validate at multiple layers (after finding root cause, not instead of).
- **Verification before completion** — confirm it actually works before declaring done.
- **Self-review** — run a checklist on your own diff before asking for review.
- **Property-based testing** — assert properties over many generated inputs.
- **Differential / variant analysis** — review a diff vs. history; hunt repeats of a found bug.

## Architecture & design vocabulary
- **Module** — any unit with an interface + implementation (scale-independent).
- **Interface** — everything a caller must know; "the interface is the test surface."
- **Depth** — behavior per unit of interface; deep = small interface, big logic (good).
- **Deepening opportunity** — a refactor turning a shallow module deep.
- **Seam** — where you can change behavior without editing that spot; aim for one.
- **Adapter (one/two rule)** — one adapter = potential seam, two = genuine one.
- **Deletion test** — does removing this concentrate or scatter complexity?
- **Leverage** — more capability per unit of interface learned.
- **Locality** — changes stay in one place.
- **Ball of mud** — tangled codebase; the failure state design fights.
- **ADR** — record only hard-to-reverse, non-obvious trade-off decisions.
- **Think before coding** — state assumptions/interpretations before writing code.
- **Simplicity first** — minimum code that solves it; no speculative abstractions.
- **Surgical changes** — touch only what's required; don't drive-by refactor.
- **YAGNI / DRY** — don't build for hypotheticals; do consolidate real repetition.

## Learning with an agent
- **Teaching workspace** — multi-session tutor grounded in a MISSION.md (knowledge/skills/wisdom).
- **Zone of proximal development** — pitch lessons just beyond current ability.
- **Storage strength over fluency** — design for retention (spacing, retrieval), not recognition.

## Writing skills — mechanics
- **Skill** — a reusable folder of instructions + metadata + resources, loaded on demand.
- **SKILL.md** — the required entry file: YAML frontmatter + instructions.
- **Frontmatter** — the `---` config block; only `name` + `description` are core.
- **`name`** — identifier/label; usually doesn't set the slash command.
- **`description`** — the trigger; the most important field for discovery, third-person.
- **Progressive disclosure** — load info in tiers as needed, not all upfront.
- **Three levels** — metadata (always) → SKILL.md body (on trigger) → resources (as needed).
- **Bundled resources** — files read only when referenced; zero cost until used.
- **Executable scripts** — code Claude runs; only output costs tokens.
- **Degrees of freedom** — match instruction specificity to task fragility.
- **Conciseness** — only add context Claude lacks; the window is a public good.
- **Composability** — multiple skills active at once, working together.
- **Model- vs. user-invoked** — auto-triggered (constant cost) vs. `/command`-only (`disable-model-invocation`).
- **`allowed-tools` / `disallowed-tools`** — pre-approve / remove tools (CLI only; not restrictions/permanent).
- **`context: fork` / `agent`** — run the skill in an isolated subagent of a chosen type.
- **Dynamic context injection** — `` !`command` `` runs shell as preprocessing; Claude sees only output.
- **Argument substitution** — `$ARGUMENTS`, `$1`, `$name` placeholders at invocation.
- **Skill scope** — Enterprise > Personal > Project precedence; doesn't sync across surfaces.
- **Token budget** — metadata ~100 tokens; body <5k; listing scales ~1% of context.
- **Security / trust** — treat installing a skill like installing software.

## Skill design — craft
- **Predictability from stochastic systems** — skills fix the *process*, not the output.
- **Leading words** — compact pretrained terms that invoke rich behavior cheaply (this glossary catalogs them).
- **Branches / triggers** — description lists distinct trigger phrases, one per branch.
- **Information hierarchy** — steps first, reference second, external pointers last.
- **Completion criteria** — checkable conditions that stop premature "done."
- **Granularity** — split a skill only by invocation or by sequence.
- **Skill failure modes** — premature completion, duplication, sediment, sprawl, no-op.
- **Pruning / single source of truth** — one canonical location per fact; delete failures.
- **Router skill** — one entry point listing your other skills.
- **Evaluation-driven development** — build evals before docs; test fresh-session with vs. without.
- **Claude A / Claude B** — author with one instance, test with fresh ones.
- **Plan-validate-execute** — emit a plan, validate it, then apply.
