# Glossary — Prompting & Skills

*A browsable vocabulary for writing better prompts and skills for AI coding/assistant agents.*
*Compiled 2026-06-22 from the most popular public skill repositories and the prompt-engineering canon (sources at the bottom).*

---

## How to use this

When you're stuck phrasing a request, or you read a term in someone's skill and don't
know it, find it here. Entries are grouped by purpose and each one follows the same shape:

- **Definition** — plain English, no jargon assumed.
- **When to use** — the situation where reaching for it pays off.
- **Pitfalls** — how it goes wrong or gets misused.
- **Example** — a phrase you could actually type, or a concrete instance.
- **Source** — where the term comes from, so you can read deeper.

**The fastest wins for a non-technical user writing coding prompts:** be specific,
give *examples* (few-shot), separate your instructions from pasted data (delimiters),
ask for step-by-step reasoning (chain-of-thought), set standing rules once (system prompt),
and align *before* building (grilling). Everything else is refinement on top of these.

### Contents
1. [Core concepts](#1-core-concepts) — the basics every prompt rests on
2. [Prompt structure](#2-prompt-structure) — how to lay a prompt out
3. [Prompting techniques](#3-prompting-techniques) — named moves that improve answers
4. [Alignment & collaboration](#4-alignment--collaboration) — getting on the same page (Pocock's core)
5. [Planning & delivery](#5-planning--delivery) — turning intent into shippable work
6. [Debugging & testing](#6-debugging--testing) — disciplined ways to find and fix
7. [Architecture & design vocabulary](#7-architecture--design-vocabulary) — words for talking about code shape
8. [Learning with an agent](#8-learning-with-an-agent) — using AI as a tutor
9. [Writing skills — the mechanics](#9-writing-skills--the-mechanics) — how SKILL.md actually works
10. [Skill design — the craft](#10-skill-design--the-craft) — what makes a skill good
11. [Sources](#sources)

---

## 1. Core concepts

### Prompt
- **Definition:** The text instructions you give an AI to get a result. "Prompt engineering" is the practice of structuring that text so you reliably get what you want.
- **When to use:** Always — every interaction is a prompt.
- **Pitfalls:** Treating it as one-shot magic. Good prompting is iterative: draft, test, refine.
- **Example:** "Refactor this function to be more readable, keeping the same behavior."
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

### Token
- **Definition:** The chunks of text an AI reads and writes — roughly a word or word-piece (~4 characters in English). Usage, cost, speed, and limits are all measured in tokens, not words.
- **When to use:** Understanding length caps and why long prompts/answers cost more. "Max tokens" sets how long a reply can be.
- **Pitfalls:** Assuming one token = one word. Both your prompt and the answer consume the budget.
- **Example:** "Limit your answer to about 200 tokens (roughly 150 words)."
- **Source:** [Google Cloud — RAG](https://cloud.google.com/use-cases/retrieval-augmented-generation)

### Context window
- **Definition:** The total text the AI can consider at once — your prompt + the conversation so far + the answer. Anything beyond it falls out of view.
- **When to use:** When pasting large files or running long sessions; the relevant material has to fit.
- **Pitfalls:** A big window doesn't mean you should fill it. Too much irrelevant text *degrades* quality — it's a shared resource, not free storage.
- **Example:** "Here is the only file you need for this task; ignore the earlier discussion."
- **Source:** [Redis — RAG vs. large context window](https://redis.io/blog/rag-vs-large-context-window-ai-apps/)

### In-context learning
- **Definition:** The model "learns" your task from what's in the prompt (instructions + examples) without being retrained. Zero-shot and few-shot are both forms of it.
- **When to use:** Any time you steer behavior with examples or instructions rather than expecting fine-tuning.
- **Pitfalls:** It's temporary — it lasts only for that prompt/session, not permanently.
- **Example:** "Here are two examples of the format I want; follow the same pattern."
- **Source:** [DAIR — Prompting Guide techniques](https://www.promptingguide.ai/techniques)

### Temperature
- **Definition:** A setting (often 0–1) controlling randomness. Low = focused and predictable; high = varied and creative.
- **When to use:** Near 0 for code, classification, and facts; higher for brainstorming.
- **Pitfalls:** High temperature on precision tasks gives inconsistent or wrong output. Not every chat UI exposes the knob.
- **Example:** "Set temperature to 0 for this — I need deterministic, repeatable output."
- **Source:** [Google — Prompt Engineering whitepaper](https://archive.org/stream/whitepaper-prompt-engineering-v-4/whitepaper_Prompt%20Engineering_v4_djvu.txt)

### Hallucination
- **Definition:** When an AI produces confident-sounding output that is actually false or invented — a fake citation, a non-existent function, a wrong fact.
- **When to use:** A risk to guard against, especially for facts, APIs, and library names.
- **Pitfalls:** Trusting fluent answers without checking. Coding agents love to invent plausible-but-fake function names.
- **Example:** "If you're not certain a library or function exists, say so instead of guessing."
- **Source:** [K2view — grounding & hallucinations](https://www.k2view.com/blog/what-is-grounding-and-hallucinations-in-ai/)

### Grounding
- **Definition:** Anchoring the answer in specific source material you provide (or it retrieves), so responses tie to real, verifiable information rather than the model's memory.
- **When to use:** When accuracy matters and you have the docs, code, or data.
- **Pitfalls:** Reduces hallucination but doesn't eliminate it.
- **Example:** "Answer only using the documentation I pasted below. If it's not there, say 'not in the docs.'"
- **Source:** [K2view — grounding & hallucinations](https://www.k2view.com/blog/what-is-grounding-and-hallucinations-in-ai/)

### Retrieval-Augmented Generation (RAG)
- **Definition:** A setup where the system automatically looks up relevant information from an external knowledge base and inserts it into the prompt before the AI answers.
- **When to use:** When answers must reflect a large or changing body of documents (a codebase, a wiki) that won't all fit in one prompt.
- **Pitfalls:** Garbage retrieval → garbage answers. If the wrong snippets are pulled in, the model is grounded in irrelevant material.
- **Example:** (often automatic) "Use the retrieved code snippets above as the source of truth."
- **Source:** [Google Cloud — RAG](https://cloud.google.com/use-cases/retrieval-augmented-generation)

---

## 2. Prompt structure

### System prompt
- **Definition:** A top-level instruction that sets the AI's role, rules, and behavior for the *whole* conversation, separate from your individual questions. The "constitution" of the session.
- **When to use:** To establish persistent ground rules — tone, constraints, what "done" looks like, what to never do.
- **Pitfalls:** Cramming task-specific details into it that belong in the message; or leaving it empty and re-explaining rules every turn.
- **Example:** "You are a senior Python engineer. Always include type hints and never use deprecated APIs."
- **Source:** [DAIR — Prompting Guide](https://www.promptingguide.ai/techniques)

### User prompt
- **Definition:** The specific request you type each turn, as opposed to the system prompt's standing rules. The system prompt is the constitution; the user prompt is today's task.
- **When to use:** For the actual question or task; keep persistent rules in the system prompt.
- **Pitfalls:** Mixing persistent rules and one-off requests so the AI can't tell which is which.
- **Example:** "Now add input validation to the `parse_date` function."
- **Source:** [OpenAI — prompt engineering best practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)

### Role / persona prompting
- **Definition:** Telling the AI to act as a specific kind of expert or character, which shapes its tone, depth, and assumptions.
- **When to use:** To set expertise level and style — "senior code reviewer," "patient teacher for beginners."
- **Pitfalls:** A persona alone doesn't guarantee accuracy; "act as an expert" won't supply missing facts. Overly theatrical roles waste words.
- **Example:** "Act as a security-focused code reviewer and flag any unsafe input handling."
- **Source:** [Google — Prompt Engineering whitepaper](https://archive.org/stream/whitepaper-prompt-engineering-v-4/whitepaper_Prompt%20Engineering_v4_djvu.txt)

### Delimiters
- **Definition:** Visual separators — triple quotes, tags, markdown headings — that mark where one part of your prompt ends and another begins (instructions vs. data vs. examples).
- **When to use:** Whenever a prompt has multiple parts, especially when you paste code or data alongside instructions.
- **Pitfalls:** Without them, the AI confuses your data for commands.
- **Example:** Wrap pasted code in tags, then "Refactor the code inside the `<code>` tags."
- **Source:** [OpenAI — best practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)

### XML tags
- **Definition:** Anthropic's recommended delimiter style for Claude — labeled tags like `<instructions>`, `<data>`, `<example>` that keep prompt sections cleanly separated.
- **When to use:** With Claude especially, when a prompt has context, instructions, examples, and data all at once.
- **Pitfalls:** Inconsistent or unclosed tags; over-nesting until it's unreadable.
- **Example:** "`<instructions>` Summarize the report. `</instructions>` `<report>` … `</report>`"
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

### Output formatting
- **Definition:** Telling the AI exactly what shape the answer should take — bullet list, JSON, table, code block only, a set number of items.
- **When to use:** Whenever you'll reuse the output or need it scannable/machine-readable.
- **Pitfalls:** Vague format requests ("make it nice"); not showing an example when the format is precise.
- **Example:** "Return only a JSON object with keys `summary` and `risks`. No prose."
- **Source:** [OpenAI — best practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)

### Prompt template
- **Definition:** A reusable prompt skeleton with blank slots (variables) you fill in each time, so you don't rewrite structure for repeated tasks.
- **When to use:** For tasks you do often — code reviews, commit messages, bug triage.
- **Pitfalls:** Templates drift out of date; leaving placeholders unfilled.
- **Example:** "Review the following {language} code for {focus_area}: {code}"
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

---

## 3. Prompting techniques

### Zero-shot prompting
- **Definition:** Asking the AI to do a task with only instructions and no examples.
- **When to use:** For simple, common tasks the model already handles well.
- **Pitfalls:** Fails on nuanced or format-sensitive tasks where it guesses wrong — add examples instead.
- **Example:** "Classify this commit message as feature, fix, or chore: 'patch null check in parser'."
- **Source:** [Google — Prompt Engineering whitepaper](https://archive.org/stream/whitepaper-prompt-engineering-v-4/whitepaper_Prompt%20Engineering_v4_djvu.txt)

### Few-shot prompting (multishot)
- **Definition:** Including a few worked input→output examples in the prompt so the AI mimics the pattern. One of the highest-impact techniques for accuracy and consistency.
- **When to use:** When zero-shot gives the wrong format or misses nuance; great for enforcing a house style.
- **Pitfalls:** Examples that are inconsistent, biased toward one answer, or unrepresentative — the model copies their flaws.
- **Example:** "Example 1: input → output. Example 2: input → output. Now do: [your input]."
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

### Chain-of-thought (CoT)
- **Definition:** Asking the AI to reason step by step before giving its final answer, which improves accuracy on math, logic, and multi-step problems.
- **When to use:** Anything needing reasoning — debugging logic, planning a multi-step change, weighing trade-offs.
- **Pitfalls:** Wasteful on trivial tasks; some newer "reasoning" models do it internally and you needn't ask.
- **Example:** "Think through this step by step before writing the fix."
- **Source:** [DAIR — Chain-of-Thought](https://www.promptingguide.ai/techniques/cot)

### Self-consistency
- **Definition:** Generating several independent step-by-step answers to the same question, then picking the one that comes up most often (a kind of voting).
- **When to use:** Tricky reasoning where a single pass is unreliable and accuracy beats speed/cost.
- **Pitfalls:** Slower and more expensive (multiple runs); overkill for easy tasks.
- **Example:** "Solve this three separate ways, then tell me which answer is most consistent across them."
- **Source:** [DAIR — Prompting Guide](https://www.promptingguide.ai/techniques)

### ReAct (Reason + Act)
- **Definition:** A loop where the AI alternates between reasoning and taking actions (using a tool, searching, reading a file), observing the result, then reasoning again. The backbone of most coding agents.
- **When to use:** Tasks needing tools or external steps — running code, reading files, looking things up.
- **Pitfalls:** Loops where the agent keeps acting without progress; needs clear stopping criteria.
- **Example:** "Investigate the bug: read the relevant file, form a hypothesis, test it, then report what you found."
- **Source:** [DAIR — ReAct](https://www.promptingguide.ai/techniques/react)

### Prompt chaining
- **Definition:** Breaking a big task into a sequence of smaller prompts, where each step's output feeds the next.
- **When to use:** Complex work — outline, then draft, then review — so each step stays focused and checkable.
- **Pitfalls:** Errors compound down the chain if you don't verify intermediate steps.
- **Example:** "Step 1: list the files that need changing. (I'll review, then ask you to edit them.)"
- **Source:** [DAIR — Prompt Chaining](https://www.promptingguide.ai/techniques/prompt_chaining)

### Least-to-most prompting
- **Definition:** Explicitly decomposing a problem into sub-problems from simplest to hardest, then solving them in order and carrying results forward.
- **When to use:** Problems that build on themselves, where solving easy parts first unlocks the hard part.
- **Pitfalls:** Over-engineering simple tasks; getting the decomposition order wrong.
- **Example:** "First handle the simplest case, then extend the solution to the harder cases one at a time."
- **Source:** [DAIR — Prompting Guide](https://www.promptingguide.ai/techniques)

### Meta-prompting
- **Definition:** Using the AI to *write or improve prompts* (or to set up a task's structure) rather than to answer directly.
- **When to use:** When you're unsure how to phrase something — ask the AI to draft a better prompt.
- **Pitfalls:** Accepting a generated prompt without checking it fits your goal; adds unnecessary abstraction.
- **Example:** "Write me an effective system prompt for a tool that reviews pull requests."
- **Source:** [DAIR — Meta-Prompting](https://www.promptingguide.ai/techniques/meta-prompting)

### Negative prompting
- **Definition:** Explicitly telling the AI what to *avoid* — topics, formats, tones, or approaches you don't want.
- **When to use:** To rule out known failure modes; pairs with positive instructions.
- **Pitfalls:** Long "don't" lists are often weaker than stating clearly what you DO want; pure negatives can confuse the model.
- **Example:** "Do not add comments, do not change the public API, do not introduce new dependencies."
- **Source:** [FlowHunt — negative prompt](https://www.flowhunt.io/glossary/negative-prompt/)

### Stop sequences
- **Definition:** Specific text that, when produced, tells the model to stop generating immediately — used to cap output at a natural boundary.
- **When to use:** Mostly in API/tool settings, to prevent rambling or stop after a delimiter.
- **Pitfalls:** Choosing a sequence that appears in normal output cuts answers off early. Often not exposed in chat UIs.
- **Example:** Set a stop sequence of `###` so generation halts at that marker.
- **Source:** [Daniel Corin — prefill & stop sequences](https://www.danielcorin.com/til/prompting/prefill-and-stop-sequences/)

### Prefilling *(legacy — see note)*
- **Definition:** Starting the AI's answer for it (putting the first words in its mouth) to force a format or skip preambles.
- **When to use:** On older models/APIs that support it, to enforce structure (e.g., begin with `{` for JSON).
- **Pitfalls:** **Not supported on current Claude models** (Opus 4.8, Sonnet 4.6, etc.). On today's models, ask for the format explicitly or use structured outputs instead.
- **Example:** (legacy) Begin the assistant reply with ```` ```json ```` to force a JSON block.
- **Source:** [Anthropic — prefill Claude's response](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/prefill-claudes-response)

### Guardrails
- **Definition:** Rules and safeguards — in the system prompt or a separate screening step — that keep the AI's behavior within safe, acceptable bounds and help it refuse bad requests.
- **When to use:** When the assistant faces untrusted input or must stay strictly on-task and in-character.
- **Pitfalls:** Vague guardrails the model talks around; relying on prompt rules alone for real security.
- **Example:** "Only answer questions about this codebase. For anything else, reply: 'Out of scope.'"
- **Source:** [Anthropic — keep Claude in character](https://docs.anthropic.com/en/docs/test-and-evaluate/strengthen-guardrails/keep-claude-in-character)

---

## 4. Alignment & collaboration

*The heart of Matt Pocock's approach: most failures come from the agent and you wanting different things. Fix that before any code is written.*

### Misalignment
- **Definition:** The communication gap between you and the agent — it builds something subtly (or wildly) different from what you wanted. Pocock calls it "the most common failure mode in software development."
- **When to use:** Recognize it whenever you're about to hand over a vague, one-line request. The cure is to align *before* building, not debug after.
- **Pitfalls:** Easy to ignore because the agent confidently produces *something*; the cost shows up later as rework. Fluent output isn't aligned output.
- **Example:** "Before writing any code, grill me on this plan until we have shared understanding."
- **Source:** [mattpocock/skills — README](https://github.com/mattpocock/skills/blob/main/README.md)

### Grilling / "Grill Me"
- **Definition:** A relentless interview where the agent refuses to write code and instead interrogates *you* to sharpen a plan until you reach shared understanding. Pocock's most popular skill; sessions can run ~45 minutes and 50+ questions.
- **When to use:** At the start of any non-trivial change, when the spec is fuzzy, or when you sense you haven't thought it through yourself.
- **Pitfalls:** Skipping it ("just build it") reintroduces misalignment. Grilling *trivial* tasks wastes time — reserve it for decisions with real branching.
- **Example:** "Before we build the stock screener's options scanner, grill me on it. Ask me one question at a time about how I actually want it to work, and don't write any code until we've thought it all the way through together."
- **Source:** [mattpocock/skills — grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md)

### One question at a time
- **Definition:** A grilling rule: ask one question, wait for the answer, then ask the next. "Asking multiple questions at once is bewildering."
- **When to use:** Any clarifying interview; it keeps your working memory free and lets each answer inform the next question.
- **Pitfalls:** Batching questions overwhelms you and yields shallow answers. The agent should also offer its own *recommended* answer with each question.
- **Example:** "As we plan the home server, ask me one question at a time and wait for my answer before the next one. Each time, tell me which option you'd recommend so I'm not guessing."
- **Source:** [mattpocock/skills — grilling](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md)

### Design tree / decision tree
- **Definition:** The hierarchy of interdependent decisions in a plan. Grilling traverses it by resolving dependencies between decisions one by one, branch by branch.
- **When to use:** When a design has nested choices where later decisions depend on earlier ones; walk the tree rather than jumping around.
- **Pitfalls:** Resolving a leaf decision before its parent wastes effort if the parent changes the branch entirely.
- **Example:** "Walk the decision tree top-down; don't ask about field names before we've settled the data model."
- **Source:** [mattpocock/skills — grilling](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md)

### Rubber-ducking, made argumentative
- **Definition:** Grilling reframed: it automates classic "rubber-duck debugging" (explaining a problem aloud to find the answer) but makes the duck *push back* — challenging assumptions instead of passively listening.
- **When to use:** When you want to pressure-test your own thinking, not just narrate it.
- **Pitfalls:** If the agent only validates ("great idea!"), it isn't grilling — adversarial refinement is the point.
- **Example:** "Don't agree with me — argue the weak points in this approach."
- **Source:** [aihero.dev — my Grill Me skill has gone viral](https://www.aihero.dev/my-grill-me-skill-has-gone-viral)

### Adding friction
- **Definition:** The philosophy behind Pocock's repo: skills deliberately insert steps the agent would otherwise skip. "They do not make the agent smarter; they make it slower in exactly the places where slowing down matters."
- **When to use:** When fast, unconstrained generation is producing low-quality or misaligned results.
- **Pitfalls:** Friction *everywhere* is just slowness — apply it at high-leverage points (alignment, design, architecture), not every keystroke.
- **Example:** "Before implementing, run the alignment and architecture skills first."
- **Source:** [Medium — the AI coding repo that went viral because it adds friction](https://medium.com/@marc.bara.iniesta/the-ai-coding-repo-that-went-viral-because-it-adds-friction-e2b5c4d2fcc2)

### Vibe coding *(anti-pattern)*
- **Definition:** Casual, unconstrained "just let the AI build it" workflow. Pocock's skills point *away* from this, putting AI inside mature engineering constraints.
- **When to use:** Name it to recognize when you've slid into it; fine for throwaway prototypes, dangerous for production.
- **Pitfalls:** Produces a "ball of mud" over time — speed now, entropy later.
- **Example:** "This is production code, not a vibe-coding session — apply TDD and domain modeling."
- **Source:** [mattpocock/skills — README](https://github.com/mattpocock/skills/blob/main/README.md)

### Shared language / ubiquitous language
- **Definition:** Consistent, project-specific vocabulary shared between you and the agent, so a single word stands in for a verbose explanation. Cuts tokens and improves consistency.
- **When to use:** Establish early (via domain modeling) so later prompts can be terse and unambiguous.
- **Pitfalls:** Letting the agent invent synonyms for an existing term; vague language that drifts.
- **Example:** "Use the term 'materialization cascade' as defined in CONTEXT.md — don't paraphrase it." (A coined phrase replacing a long explanation is the whole payoff.)
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### Domain modeling
- **Definition:** The *active* discipline of building and sharpening a project's shared vocabulary during design — challenging vague terms, proposing canonical ones, testing relationships with edge cases — not just reading existing docs.
- **When to use:** During grilling and design, whenever new concepts or ambiguous terms surface.
- **Pitfalls:** Treating it as passive reference; not calling out when your terminology conflicts with the glossary.
- **Example:** "That conflicts with how we defined 'session' — propose a precise canonical term."
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### CONTEXT.md
- **Definition:** A root-level glossary file holding *only* domain vocabulary, stripped of implementation detail or specs. Multi-domain repos use a CONTEXT-MAP.md pointing to per-domain files. Created lazily — only when something needs recording.
- **When to use:** As the single source of truth for shared language; reference it in prompts to keep the agent aligned.
- **Pitfalls:** Polluting it with specs or code; creating it eagerly before there's anything to record.
- **Example:** "Add 'tracer bullet' to CONTEXT.md with a one-line definition."
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### Handoff
- **Definition:** A compact transition document that lets a *fresh* agent resume work without replaying full context — it references existing artifacts (PRDs, ADRs, issues, diffs) rather than duplicating them, redacts secrets, and lists suggested skills.
- **When to use:** When a conversation grows long or you're passing work to another agent/session.
- **Pitfalls:** Copying artifacts wholesale (breaks single-source-of-truth); leaking secrets.
- **Example:** "/handoff focused on the billing migration — reference the PRD and open issues."
- **Source:** [mattpocock/skills — handoff](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md)

### Agent brief
- **Definition:** The concise spec posted on a "ready-for-agent" issue that tells an agent exactly what to build — a delegable handoff at the issue level.
- **When to use:** When promoting a triaged issue to agent-executable status.
- **Pitfalls:** A brief that's actually still ambiguous; grill it to sharpness first.
- **Example:** "Post an agent brief on this issue covering scope and acceptance criteria."
- **Source:** [mattpocock/skills — triage](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

### Triage state machine
- **Definition:** Moving issues through states (needs-triage → needs-info → ready-for-agent / ready-for-human / wontfix), each carrying one category label (bug/enhancement) and one state label.
- **When to use:** Managing an issue tracker that feeds agent work; "ready-for-agent" means fully specified and delegable.
- **Pitfalls:** Marking under-specified issues ready-for-agent; not disclosing AI involvement in comments.
- **Example:** "Triage this issue — verify the repro, then label it ready-for-agent or needs-info."
- **Source:** [mattpocock/skills — to-issues](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

---

## 5. Planning & delivery

### Spec-driven development (SDD)
- **Definition:** A methodology that puts a written specification at the center of AI coding: describe what to build, refine through structured phases, then let the agent implement — rather than prompting straight to code.
- **When to use:** Non-trivial features where inferring intent from a vague prompt is unreliable.
- **Pitfalls:** Heavy ceremony for tiny tasks; the spec drifts from reality if it isn't kept the single source of truth.
- **Example:** Spec Kit's chain `/specify → spec.md`, `/plan → plan.md`, `/tasks → tasks.md`, `/implement`.
- **Source:** [github/spec-kit](https://github.com/github/spec-kit)

### Constitution / non-negotiable constraints
- **Definition:** A file of fixed rules (allowed tech, testing requirements, style, forbidden libraries) the agent checks against continuously and is flagged for violating.
- **When to use:** Enforcing standards across a whole session so the agent can't silently reach for a banned dependency.
- **Pitfalls:** Stale constraints block legitimate work; too many rules and the agent loses focus.
- **Example:** "No new runtime deps; all new code needs unit tests; use the existing logging util."
- **Source:** [github/spec-kit](https://github.com/github/spec-kit)

### Brainstorming (Socratic design refinement)
- **Definition:** A pre-coding phase where the agent refines a rough idea by asking questions and exploring alternatives, presenting the design in chunks for your sign-off before any code.
- **When to use:** When requirements are fuzzy and you want design alignment before a plan.
- **Pitfalls:** Can stall in endless questioning; skipping it builds the wrong thing fast.
- **Example:** "/brainstorm — walk through the trade-offs section by section and wait for my sign-off."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### PRD (As-a / I-want / so-that)
- **Definition:** A Product Requirements Document synthesized from your conversation (no new interview): problem, solution, user stories in "As a [actor], I want [feature], so that [benefit]" form, implementation/testing decisions, out-of-scope.
- **When to use:** To crystallize a discussed feature into a publishable spec before slicing into issues.
- **Pitfalls:** Stuffing file paths/code into it; conducting fresh interviews instead of synthesizing what was said.
- **Example:** "/to-prd — synthesize our conversation into a PRD and publish it as ready-for-agent."
- **Source:** [mattpocock/skills — to-prd](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-prd/SKILL.md)

### Writing plans (bite-sized tasks)
- **Definition:** Turning an approved design into a plan of tiny tasks (~2–5 minutes each), every task carrying exact file paths, complete code, and verification steps.
- **When to use:** Before execution, to make work reviewable and stop the agent wandering.
- **Pitfalls:** "No placeholders" — vague directives like "add appropriate error handling" without the actual code defeat the purpose.
- **Example:** Task = "Write the failing test (path X, full code) → run it to confirm it fails → implement → run to confirm pass → commit."
- **Source:** [obra/superpowers — writing-plans](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md)

### Task right-sizing
- **Definition:** Sizing each task as "the smallest unit that carries its own test cycle and is worth a fresh reviewer's gate" — folding setup together but splitting where independent review is meaningful.
- **When to use:** Structuring a plan so each step is independently verifiable.
- **Pitfalls:** Too-large tasks can't be reviewed cleanly; too-small tasks fragment context and add overhead.
- **Example:** Combine "create file + its imports" in one task, but split "add endpoint" from "add the test harness it depends on."
- **Source:** [obra/superpowers — writing-plans](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md)

### Vertical slice
- **Definition:** A thin, end-to-end cut through *all* layers (schema, API, UI, tests) that is independently demoable or verifiable — the unit work should be broken into.
- **When to use:** When decomposing a spec into issues or tasks; favor complete narrow paths over whole layers.
- **Pitfalls:** Slicing *horizontally* instead (see below) leaves nothing demoable until the very end.
- **Example:** "Break this PRD into vertical slices, each demoable on its own."
- **Source:** [mattpocock/skills — to-issues](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

### Tracer bullet
- **Definition:** A narrow-but-complete implementation that goes the whole distance through the system to prove the path works — the mechanism behind vertical slices and TDD ("one test → one implementation, repeat").
- **When to use:** When you want early end-to-end validation and to learn before building breadth.
- **Pitfalls:** Don't confuse it with a prototype — a tracer bullet is real, kept code; a prototype is throwaway.
- **Example:** "Write a tracer-bullet slice that hits DB through to UI for one happy path first."
- **Source:** [mattpocock/skills — tdd](https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md)

### Horizontal slicing *(anti-pattern)*
- **Definition:** Building or testing one whole layer at a time (all schema, then all API…), or in TDD writing all tests up front then all implementation. Produces tests that reflect *imagined* rather than actual behavior.
- **When to use:** Name it to avoid it; recognize when work isn't independently verifiable.
- **Pitfalls:** Tests validate data shape rather than user-facing behavior and go blind to real regressions.
- **Example:** "Don't write all the tests first — go one tracer-bullet cycle at a time."
- **Source:** [mattpocock/skills — tdd](https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md)

### Interfaces block / contracts
- **Definition:** A section in a plan task showing consumed inputs and produced outputs with exact signatures, so sequential tasks have clear contracts between them.
- **When to use:** Multi-task plans where later steps depend on earlier outputs.
- **Pitfalls:** Undefined references to types/methods across tasks break sequential execution.
- **Example:** "Produces: `parseConfig(path: string): Config`; Consumes: `Config` from task 2."
- **Source:** [obra/superpowers — writing-plans](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md)

### Prefactoring
- **Definition:** Refactoring *before* implementing a slice so the actual change becomes trivial — "make the change easy, then make the easy change."
- **When to use:** When the current structure makes the intended change awkward; reshape first.
- **Pitfalls:** Bundling prefactor + feature into one messy commit; or forcing a hard change into hostile code.
- **Example:** "Identify prefactors that make this change easy before drafting the slices."
- **Source:** [mattpocock/skills — to-issues](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

### Executing plans (with human checkpoints)
- **Definition:** Batch execution of a plan's tasks where the agent stops at defined checkpoints for your approval rather than running unattended end to end.
- **When to use:** When you want autonomy *within* a step but a gate *between* steps.
- **Pitfalls:** Too many checkpoints kill throughput; too few lose the safety they exist for.
- **Example:** "/execute-plan — run a batch, pause, report, then continue on 'go'."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Subagent-driven development
- **Definition:** A workflow where subagents work through each task and their output is inspected in a two-stage review (spec compliance, then code quality) before moving on.
- **When to use:** Parallelizing or isolating task execution while keeping a review gate.
- **Pitfalls:** Subagents lack the parent's full context and can drift; review must catch spec deviations.
- **Example:** "Dispatch a subagent per plan task, review its diff for spec match then code quality, then proceed."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Dispatching parallel agents
- **Definition:** Running multiple subagents concurrently on independent units of work.
- **When to use:** Independent tasks (e.g. separate modules) with no shared mutable state.
- **Pitfalls:** Conflicting edits / merge collisions when "independent" tasks actually overlap.
- **Example:** "Have one helper check my home server's security while a separate helper tests the music tool's code at the same time. They're separate jobs that don't touch the same files, so run them together to save time."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Git worktrees (isolated workspaces)
- **Definition:** Using `git worktree` to give parallel branches isolated working directories so concurrent development doesn't collide.
- **When to use:** Running parallel agents or multiple features without stashing/branch-switching churn.
- **Pitfalls:** Forgotten worktrees pile up; shared build artifacts can still conflict.
- **Example:** "Spin one worktree per dispatched agent."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Goal-driven execution (success criteria)
- **Definition:** Define verifiable success criteria *before* starting — turning vague imperatives into testable outcomes — then loop independently toward that target with explicit verification checkpoints.
- **When to use:** Multi-step work where the agent should self-verify instead of asking constantly.
- **Pitfalls:** No measurable target means the agent can't tell when it's done (or claims done too early).
- **Example:** "Done = `npm test` green and the endpoint returns 200 with the expected JSON shape."
- **Source:** [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md)

### Throwaway prototype / spike
- **Definition:** Code written to answer one specific design question, then deleted. A "logic" spike tests a state model in a terminal app; a "UI" spike generates several radically different UI variants. Mark it throwaway from the start; expose state visibly; clean up after.
- **When to use:** When you're unsure whether a state model works or what something should look like — validate before committing to production code.
- **Pitfalls:** Letting prototype code stagnate into production; over-polishing it.
- **Example:** "Prototype this state machine as a throwaway terminal app that prints full state after each action."
- **Source:** [mattpocock/skills — prototype](https://github.com/mattpocock/skills/blob/main/skills/engineering/prototype/SKILL.md)

---

## 6. Debugging & testing

### Test-Driven Development (TDD) / red-green-refactor
- **Definition:** Write a failing test (RED), implement the minimum to pass (GREEN), then refactor with tests green. Pocock's rule: **never refactor while RED** — you lose your safety signal.
- **When to use:** Any change whose behavior can be expressed as a test; gives the agent a verifiable target.
- **Pitfalls:** Agents fake it by writing always-passing tests or implementing before the test fails. Refactoring during red is the classic mistake.
- **Example:** "Get to green first, then refactor — don't restructure while red."
- **Source:** [mattpocock/skills — tdd](https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md), [obra/superpowers](https://github.com/obra/superpowers/)

### Behavior over implementation (testing)
- **Definition:** Tests should verify *behavior* through public interfaces, acting as integration-style specs of real code paths — not couple to internal structure.
- **When to use:** Whenever specifying tests; ask "does this survive a refactor that keeps behavior?"
- **Pitfalls:** Implementation-coupled tests break on harmless refactors and validate data shapes instead of capabilities.
- **Example:** "Test the observable behavior, not the internal call sequence."
- **Source:** [mattpocock/skills — tdd](https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md)

### Systematic debugging (4-phase root cause)
- **Definition:** A four-phase process — Root Cause Investigation → Pattern Analysis → Hypothesis & Testing → Implementation — with the rule "NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST."
- **When to use:** Any bug, especially ones that "seem simple" (the rule guards against skipping investigation).
- **Pitfalls:** Named anti-patterns: "just try changing X and see," proposing fixes before understanding, changing many things at once, "one more fix attempt" after two failures.
- **Example:** "My stock screener just crashed. Before you change anything, figure out *why* it broke first — track down the actual cause and explain it to me. Don't just try random fixes and hope one of them works."
- **Source:** [obra/superpowers — systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md)

### Debugging discipline (reproduce → minimize → hypothesize → instrument → fix → post-mortem)
- **Definition:** Pocock's six-phase loop: confirm the failure, strip the scenario to only load-bearing parts, form 3–5 ranked falsifiable hypotheses, instrument one variable at a time, write the regression test before the fix, then clean up and document.
- **When to use:** Any non-obvious bug; impose structure instead of flailing. (A more granular cousin of the 4-phase process above.)
- **Pitfalls:** Skipping minimization (debugging a bloated scenario), testing multiple variables at once, leaving debug logging in.
- **Example:** "Give me 3 ranked falsifiable hypotheses before you change anything."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Feedback loop
- **Definition:** A rapid, automatic signal (types, tests, browser access, CLI checks) that tells the agent whether code works, preventing "blind" development. Pocock: "Build the right feedback loop, and the bug is 90% fixed."
- **When to use:** Establish one *before* iterating; the tighter and more deterministic, the better.
- **Pitfalls:** Letting the agent code without any pass/fail signal; slow or flaky loops it can't run itself.
- **Example:** "Set up a failing test as the feedback loop before you touch the bug."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Loop qualities: red-capable / deterministic / fast / agent-runnable
- **Definition:** The four properties of a good feedback loop: it catches the specific symptom (red-capable), gives the same result every run (deterministic), runs in seconds (fast), and the agent can run it without you (agent-runnable).
- **When to use:** As a checklist when building any repro harness or test signal.
- **Pitfalls:** A loop that's green even when the bug is present (not red-capable) is worse than none.
- **Example:** "Make the repro deterministic and agent-runnable before hypothesizing."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Falsifiable hypothesis
- **Definition:** A debugging guess phrased as a testable prediction: "If X is the cause, then changing Y will…" Hypotheses are ranked and shared with you for domain input.
- **When to use:** Before instrumenting any bug; forces the agent to commit to predictions it can disprove.
- **Pitfalls:** Vague guesses ("maybe it's a race condition") that can't be tested or ruled out.
- **Example:** "State each hypothesis as 'if X, then changing Y will fix it.'"
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Root-cause tracing
- **Definition:** Tracing a bug backward through the call stack to its original trigger rather than patching where the symptom appears.
- **When to use:** When the error surfaces far from its cause.
- **Pitfalls:** Patching the symptom layer masks the real defect and spawns more bugs.
- **Example:** "A null at render time traced back to an unvalidated API response three layers up."
- **Source:** [obra/superpowers — systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md)

### Tagged debug instrumentation
- **Definition:** Adding targeted logs with unique prefixes (e.g. `[DEBUG-a4f2]`) mapped to specific hypotheses, then removing them all in cleanup.
- **When to use:** When you need observability for a hypothesis test and want to find/strip the logs reliably afterward.
- **Pitfalls:** Untagged logs get lost in noise or shipped to production.
- **Example:** "Prefix all debug logs with [DEBUG-xyz] so we can grep and remove them."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Condition-based waiting
- **Definition:** Replacing arbitrary timeouts/sleeps with polling on an actual condition.
- **When to use:** Flaky tests or async code that "fixed" itself with a `sleep`.
- **Pitfalls:** Fixed sleeps are non-deterministic — too short flakes, too long wastes time.
- **Example:** "Use `waitFor(() => element.isVisible())` instead of `sleep(2000)`."
- **Source:** [obra/superpowers — systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md)

### Defense-in-depth
- **Definition:** Adding validation at multiple layers *after* the root cause is found (and, in security, layering independent controls).
- **When to use:** Hardening a fix or a security boundary so one failure doesn't cascade.
- **Pitfalls:** Adding layers as a *substitute* for finding the root cause; redundant checks that hide where the real problem lives.
- **Example:** "Don't protect my server's storage with just one safeguard. Lock it down in layers — a firewall, a login key, and a limited-access account — so that if one of them ever fails, the others still keep my files safe."
- **Source:** [obra/superpowers — systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md)

### Verification before completion
- **Definition:** A gate where the agent must confirm a fix actually works (run it, observe behavior) before declaring the task done.
- **When to use:** End of any change, to stop "I think this works" hand-offs.
- **Pitfalls:** Verifying with the wrong signal (compiles ≠ works); skipping because the change "looks right."
- **Example:** "After you make the change to my server, don't just tell me it's done — actually run it and show me proof it works the way I asked before you call it finished."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Self-review (requesting / receiving code review)
- **Definition:** A pre-review checklist the agent runs against its own diff before asking for review, plus a structured way to respond to feedback.
- **When to use:** Before opening a PR, to catch obvious issues without burning a human reviewer.
- **Pitfalls:** Self-review becomes rubber-stamping; defensiveness when receiving feedback.
- **Example:** "Check the diff for spec compliance then code quality before submitting."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Property-based testing
- **Definition:** Validating that *properties* hold across many generated inputs rather than asserting fixed example cases.
- **When to use:** Logic with broad input spaces (parsers, encoders, math) where examples miss edge cases.
- **Pitfalls:** Poorly chosen properties or generators give false confidence; slow/flaky if unbounded.
- **Example:** "For all lists, `reverse(reverse(x)) == x`, checked over hundreds of random lists."
- **Source:** [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills)

### Differential / variant analysis (security)
- **Definition:** "Differential review" analyzes a diff against git history; "variant analysis" hunts for other instances of a found bug pattern across the codebase.
- **When to use:** Auditing changes for introduced vulnerabilities, and sweeping for repeats of a known flaw.
- **Pitfalls:** Differential review misses pre-existing latent bugs; variant analysis floods false positives without a precise pattern.
- **Example:** "You found one place where my server was accidentally left open to the network. Now go look for any other settings that were left open in that same way, and list every one you find."
- **Source:** [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills)

---

## 7. Architecture & design vocabulary

*Mostly from Pocock's design skills, which lean on John Ousterhout's "A Philosophy of Software Design." Useful words for telling an agent what *shape* you want the code to take.*

### Module
- **Definition:** Any code unit with an interface and an implementation (function, class, package). The term deliberately ignores scale.
- **When to use:** As the base vocabulary for any architecture discussion with the agent.
- **Pitfalls:** Equating "module" with only a file or package; the concept is scale-independent.
- **Example:** "Treat this validator as a module and evaluate its depth."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Interface (as test surface)
- **Definition:** Everything a caller must know to use a module — signatures, invariants, constraints, errors, config, performance — far broader than just types. "The interface is the test surface."
- **When to use:** When defining what to test (test through the interface, not internals) and when judging module depth.
- **Pitfalls:** Treating the interface as just the function signature; testing implementation details.
- **Example:** "Test through the public interface only, not internal helpers."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Depth (deep vs. shallow module)
- **Definition:** Depth = "the amount of behaviour a caller can exercise per unit of interface they have to learn." A *deep* module has a small interface hiding substantial logic (good); a *shallow* one has a large interface for thin/pass-through logic (avoid).
- **When to use:** When designing or auditing modules; aim to make them deeper.
- **Pitfalls:** Extracting tiny "pure functions for testing" that are actually shallow and hide bugs in their call patterns.
- **Example:** "Is this module deep? Its interface looks nearly as complex as its implementation."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Deepening opportunity
- **Definition:** A proposed refactor that turns a shallow module into a deep one, improving testability and AI-navigability — the unit the `improve-codebase-architecture` skill surfaces as cards.
- **When to use:** During periodic architecture audits to combat degradation.
- **Pitfalls:** Re-litigating settled decisions (check ADRs first); refactors that just *relocate* complexity.
- **Example:** "Scan the codebase and present deepening opportunities as a ranked report."
- **Source:** [mattpocock/skills — improve-codebase-architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Seam
- **Definition:** The place where a module's interface lives — the spot where you can alter behavior without editing that spot (Michael Feathers' definition). "One seam is the ideal target" when planning where to test.
- **When to use:** When deciding where to test or substitute behavior; reuse existing seams and keep them few.
- **Pitfalls:** Testing at the wrong seam (one that doesn't exercise the real bug); spawning many new seams scatters the test surface.
- **Example:** "Write the regression test at the seam where the bug actually occurs in real use."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Adapter (one/two-adapter rule)
- **Definition:** A concrete implementation satisfying an interface at a seam. Heuristic: "one adapter signals a *potential* seam; two adapters indicate a *genuine* one."
- **When to use:** To decide whether an abstraction is justified — don't introduce a seam until a second implementation demands it.
- **Pitfalls:** Building speculative abstraction around a single adapter (premature generalization).
- **Example:** "We only have one adapter here — hold off on the interface until a second one appears."
- **Source:** [mattpocock/skills — improve-codebase-architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### The deletion test
- **Definition:** A check for whether a module earns its place: if deleting it eliminates complexity, it was redundant; if the complexity merely scatters across many callers, the module was pulling its weight.
- **When to use:** When auditing whether an abstraction concentrates or just relocates complexity.
- **Pitfalls:** Keeping shallow modules out of habit; deleting a module whose complexity then leaks everywhere.
- **Example:** "Apply the deletion test — does removing this concentrate or scatter complexity?"
- **Source:** [mattpocock/skills — improve-codebase-architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Leverage
- **Definition:** An outcome of deep design — callers gain more capability per unit of interface learned, and one implementation serves many call sites.
- **When to use:** As a goal when judging whether a design is worth it.
- **Pitfalls:** Mistaking lots of small reusable helpers for leverage when they actually add interface surface.
- **Example:** "Does this give us leverage — more behavior per unit of interface?"
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Locality
- **Definition:** An outcome of deep design — changes stay concentrated, so a fix applies everywhere at once rather than scattering.
- **When to use:** When weighing where logic should live; prefer designs that keep related change in one place.
- **Pitfalls:** Shallow modules "lack meaningful locality" — logic extracted for testing but whose real behavior is spread across call sites.
- **Example:** "Refactor for locality so this rule lives in one place."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Ball of mud
- **Definition:** A codebase degraded into tangled, hard-to-maintain complexity — the failure state intentional design fights against.
- **When to use:** Name it to justify architecture investment; the risk rises as AI accelerates code output.
- **Pitfalls:** Assuming faster generation is harmless — it accelerates entropy toward a ball of mud without intentional design.
- **Example:** "Audit for deepening opportunities before this turns into a ball of mud."
- **Source:** [mattpocock/skills — README](https://github.com/mattpocock/skills/blob/main/README.md)

### Architecture Decision Record (ADR)
- **Definition:** A sparsely-created record of a decision, written only when the choice is hard to reverse, non-obvious to future readers, and a genuine trade-off — used to avoid re-litigating settled matters.
- **When to use:** When a real architectural fork is being decided; reference ADRs so the agent doesn't reopen them.
- **Pitfalls:** Over-producing ADRs for trivial choices; ignoring existing ones and re-debating.
- **Example:** "Record this as an ADR — it's hard to reverse and the trade-off isn't obvious."
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### Think before coding (surface assumptions)
- **Definition:** State your interpretation and assumptions explicitly *before* coding; if multiple interpretations exist, present them — don't pick silently.
- **When to use:** Any ambiguous request. The named anti-pattern is "silent decision-making under ambiguity."
- **Pitfalls:** The agent guesses and builds the wrong thing fast.
- **Example:** "I'm assuming 'users' means active users only; confirm? Otherwise here are two readings."
- **Source:** [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md)

### Simplicity first (anti-overengineering)
- **Definition:** Write the minimum code that solves the problem — nothing speculative, no abstractions for single-use code, no unasked-for error handling. Test: "Would a senior engineer call this overcomplicated?"
- **When to use:** As a constant guardrail.
- **Pitfalls:** Speculative features, unnecessary abstractions, premature handling for impossible scenarios.
- **Example:** "Collapse this 200-line 'configurable' helper into the 50 lines that do exactly what was asked."
- **Source:** [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md)

### Surgical changes
- **Definition:** Touch only what the request requires, match existing style, don't refactor working code; remove only imports/functions *your* edits orphaned, and flag (don't delete) pre-existing dead code.
- **When to use:** Editing within an existing codebase you don't fully own.
- **Pitfalls:** Drive-by refactors that balloon the diff and break unrelated things.
- **Example:** "Just fix the one broken function in my screener. Leave all the other code that's already working exactly as it is — if you notice something messy nearby, point it out to me, but don't change it."
- **Source:** [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md)

### YAGNI / DRY
- **Definition:** "You Aren't Gonna Need It" (don't build for hypothetical futures) and "Don't Repeat Yourself" (factor out genuine duplication) — invoked as guardrails.
- **When to use:** YAGNI to resist speculative generality; DRY to consolidate real repetition.
- **Pitfalls:** Over-applying DRY creates the wrong abstraction; YAGNI misused to skip needed robustness.
- **Example:** "Don't add a plugin system 'in case' — but do extract this 10-line block used in three places."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

---

## 8. Learning with an agent

### Teaching workspace (knowledge / skills / wisdom)
- **Definition:** A multi-session learning system grounded in a MISSION.md (why you want the skill), distinguishing three learning types — Knowledge (acquire from high-trust sources), Skills (durability via effortful practice), Wisdom (real-world application).
- **When to use:** When using an agent as a long-running tutor rather than one-off Q&A.
- **Pitfalls:** Abstract lessons disconnected from a real mission; designing for momentary recognition instead of retention.
- **Example:** "/teach — my mission is in MISSION.md; build today's lesson at the edge of what I can do."
- **Source:** [mattpocock/skills — teach](https://github.com/mattpocock/skills/blob/main/skills/productivity/teach/SKILL.md)

### Zone of proximal development
- **Definition:** Lessons should feel "just right": challenging without overwhelming working memory.
- **When to use:** When calibrating the difficulty of agent-delivered instruction.
- **Pitfalls:** Too easy (no growth) or too hard (overwhelm).
- **Example:** "Pitch this lesson just beyond what I already know."
- **Source:** [mattpocock/skills — teach](https://github.com/mattpocock/skills/blob/main/skills/productivity/teach/SKILL.md)

### Storage strength over fluency
- **Definition:** Design learning for long-term retention via retrieval practice, spacing, and interleaving — not momentary recognition that *feels* like learning but doesn't stick.
- **When to use:** When you want durable mastery from agent-led teaching.
- **Pitfalls:** Mistaking re-reading/recognition for actual learning.
- **Example:** "Quiz me with spaced retrieval rather than just re-explaining."
- **Source:** [mattpocock/skills — teach](https://github.com/mattpocock/skills/blob/main/skills/productivity/teach/SKILL.md)

---

## 9. Writing skills — the mechanics

*How an Agent Skill actually works under the hood (Anthropic's official model). Relevant if you want to package your own reusable prompts.*

### Skill (Agent Skill)
- **Definition:** A self-contained folder of instructions, metadata, and optional resources (scripts, templates, reference files) that Claude loads dynamically to specialize at a task — "domain-specific expertise that transforms general-purpose agents into specialists."
- **When to use:** When you keep pasting the same instructions/checklist, or a chunk of CLAUDE.md has grown into a *procedure* rather than a fact.
- **Pitfalls:** Treating a skill as a one-off prompt; skills are reusable and load on demand.
- **Example:** A `pdf-processing` skill bundling extraction instructions plus a `fill_form.py` script.
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### SKILL.md
- **Definition:** The required entry file of every skill: YAML frontmatter (metadata) plus a markdown body of instructions Claude follows when the skill runs.
- **When to use:** Always — it's the one required file; everything else in the folder is optional.
- **Pitfalls:** Letting the body grow too long (keep under ~500 lines); narrating "how/why" instead of stating what to do.
- **Example:** `~/.claude/skills/summarize-changes/SKILL.md` with a `description` and an `## Instructions` section.
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Frontmatter
- **Definition:** The `---`-delimited YAML block atop SKILL.md that configures behavior. Only `name` and `description` are part of the core spec; Claude Code adds many optional fields.
- **When to use:** To set discovery metadata and (in Claude Code) invocation/tool/model controls.
- **Pitfalls:** Relying on Claude Code-only fields (e.g. `allowed-tools`) when running via the SDK, where they're ignored.
- **Example:** `--- name: deploy / description: Deploy to production / disable-model-invocation: true ---`
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### `name` (frontmatter)
- **Definition:** The skill's identifier/label. Max 64 chars; lowercase letters, numbers, hyphens only; no XML tags; can't contain "anthropic" or "claude." Usually does *not* set the `/command` (the directory name does).
- **When to use:** Set a clear display label; prefer gerund form (verb + -ing).
- **Pitfalls:** Vague names (`helper`, `utils`); assuming `name` changes the slash command.
- **Example:** `name: processing-pdfs`
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### `description` (the trigger)
- **Definition:** What the skill does *and when to use it*. The single most important field for discovery — Claude reads it to decide whether the skill is relevant.
- **When to use:** Always. Put the key use case first; include both the action and concrete trigger contexts/keywords. Write in third person.
- **Pitfalls:** Marketing copy or first/second person ("I can help…"); vague text like "Helps with documents."
- **Example:** `description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDFs or when the user mentions PDFs, forms, or document extraction.`
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Progressive disclosure
- **Definition:** The core design principle: Claude loads skill information in *tiers* as needed, not all upfront — like a manual with a table of contents, then chapters, then an appendix. Keeps the context window lean so hundreds of skills can coexist.
- **When to use:** Designing any skill that grows beyond a lean SKILL.md — split detail into bundled files.
- **Pitfalls:** Cramming everything into SKILL.md; or a vague `description` that breaks discovery so the skill never triggers.
- **Example:** Metadata in the system prompt → SKILL.md body on trigger → `forms.md` only when filling a form.
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### Three levels of loading
- **Definition:** The concrete tiers of progressive disclosure. **Level 1** = metadata (`name` + `description`), always in the system prompt (~100 tokens/skill). **Level 2** = SKILL.md body, loaded when triggered (target <5k tokens). **Level 3** = bundled resources/code, loaded or executed only as needed (effectively unlimited).
- **When to use:** As a mental model for where each piece belongs, by cost and frequency.
- **Pitfalls:** Putting rarely-needed reference material in Level 2, where it has recurring token cost.
- **Example:** Database schemas split into `reference/finance.md`, `reference/sales.md` (Level 3) so only the relevant one loads.
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### Bundled resources / supporting files
- **Definition:** Optional files in the skill directory (reference docs, templates, datasets) Claude reads only when referenced from SKILL.md. They cost zero context tokens until accessed.
- **When to use:** To keep SKILL.md lean while still shipping comprehensive API docs, large examples, or schemas.
- **Pitfalls:** Not referencing them from SKILL.md (Claude won't know they exist); nesting references more than one level deep.
- **Example:** `reference.md`, `examples.md`, `FORMS.md` linked from a "## Additional resources" section.
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Executable scripts
- **Definition:** Utility scripts (e.g. `fill_form.py`) Claude runs via bash; the script's *code* never enters context — only its output costs tokens.
- **When to use:** For deterministic, fragile, or repeated operations where reliability matters more than flexibility.
- **Pitfalls:** Scripts that punt errors back to Claude; magic numbers; ambiguity about whether to *run* vs. *read* the script.
- **Example:** `python scripts/migrate.py --verify --backup` with "Do not modify the command."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Degrees of freedom
- **Definition:** Matching instruction specificity to a task's fragility. High freedom = text heuristics (many valid approaches); medium = pseudocode/parameterized scripts; low = exact scripts/commands (fragile, consistency-critical). Analogy: "narrow bridge with cliffs" → low freedom; "open field" → high.
- **When to use:** Decide per task.
- **Pitfalls:** Over-constraining open-ended tasks (code review) or under-constraining fragile ones (DB migrations).
- **Example:** Low freedom: "Run exactly this script… Do not add flags."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Conciseness ("context window is a public good")
- **Definition:** SKILL.md should only add context Claude doesn't already have, since every loaded token competes with conversation history and other skills.
- **When to use:** Always; challenge each line — "Does Claude really need this? Does it justify its token cost?"
- **Pitfalls:** Explaining things Claude already knows (what a PDF is); narrating how/why instead of what to do.
- **Example:** A ~50-token "Extract PDF text" snippet beats a ~150-token version that explains what PDFs are.
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Composability
- **Definition:** Multiple skills can be loaded at once and work together; a skill should function alongside others rather than assume it's the only capability available.
- **When to use:** Layering a domain skill (React best-practices) with a workflow skill (code review) on one task.
- **Pitfalls:** Overlapping or contradictory skills with no arbitration mechanism.
- **Example:** A frontend task activates a UI-library skill + a React-best-practices skill + a testing skill together.
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### Model-invoked vs. user-invoked
- **Definition:** By default Claude can call a skill automatically *and* you can call it with `/skill-name`. Model-invoked skills keep their description always-loaded (constant context cost) so the agent reaches for them on its own; `disable-model-invocation: true` makes a skill user-only (zero context cost, but you must remember it exists).
- **When to use:** Model-invoked for background knowledge the agent should apply automatically; user-only for deliberate, side-effecting commands (`/deploy`, `/commit`).
- **Pitfalls:** Letting Claude auto-trigger destructive actions; over-using model-invoked skills bloats context.
- **Example:** `disable-model-invocation: true` on a `deploy` skill.
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### `allowed-tools` / `disallowed-tools`
- **Definition:** `allowed-tools` pre-approves tools so the skill won't prompt for permission each use (a convenience, *not* a restriction). `disallowed-tools` removes tools from Claude's pool while the skill is active (clears next user message). Claude Code CLI only.
- **When to use:** `allowed-tools` for skills that repeatedly call known tools (git); `disallowed-tools` for autonomous skills that should never call something.
- **Pitfalls:** Expecting `allowed-tools` to *restrict* access; expecting `disallowed-tools` to persist beyond the turn.
- **Example:** `allowed-tools: Bash(git add *) Bash(git commit *) Bash(git status *)`
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### `context: fork` / `agent`
- **Definition:** `context: fork` runs the skill in an isolated subagent — the SKILL.md becomes that subagent's prompt, with no access to conversation history. `agent` picks which subagent type runs it (`Explore`/`Plan`/`general-purpose` or a custom one).
- **When to use:** Skills with explicit, self-contained tasks you want run in isolation (e.g. research).
- **Pitfalls:** Using `fork` on guideline-only skills (no actionable task) returns nothing useful; forked `Explore`/`Plan` skip CLAUDE.md and git status.
- **Example:** `context: fork` + `agent: Explore` for a `deep-research` skill.
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Dynamic context injection (`` !`command` ``)
- **Definition:** The `` !`command` `` syntax runs a shell command as *preprocessing* before the skill is sent to Claude; its output replaces the placeholder. Claude only sees the rendered result, not the command.
- **When to use:** To ground a skill in live data (current diff, PR contents) instead of guesses.
- **Pitfalls:** Recognized only at line start or after whitespace; runs once; disabled by `disableSkillShellExecution`.
- **Example:** `## Current changes` then `` !`git diff HEAD` ``
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Argument substitution (`$ARGUMENTS`, `$1`, `$name`)
- **Definition:** Placeholders replaced with arguments passed at invocation: `$ARGUMENTS` (full string), `$1`/`$ARGUMENTS[N]` (positional), `$name` (named via the `arguments` frontmatter list).
- **When to use:** Parameterized skills like `/fix-issue 123`.
- **Pitfalls:** If `$ARGUMENTS` is absent, input is appended as `ARGUMENTS: <value>`; multi-word positional args need quoting.
- **Example:** `/migrate-component SearchBar React Vue` → `$1`=SearchBar, `$2`=React, `$3`=Vue.
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Skill scope / location
- **Definition:** Where a skill lives sets who can use it and precedence: Enterprise (managed) > Personal (`~/.claude/skills/`) > Project (`.claude/skills/`). Plugin skills are namespaced `plugin:skill`.
- **When to use:** Personal for skills across all your projects; Project committed to a repo for the team; plugin/managed for distribution.
- **Pitfalls:** Custom skills do NOT sync across surfaces (claude.ai, API, Claude Code are separate); name clashes resolve by precedence.
- **Example:** Put a personal helper at `~/.claude/skills/commit-style/SKILL.md` so it works in every project; commit a team one to `.claude/skills/` in the repo so teammates get it on clone.
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Token budget (skill listing)
- **Definition:** The bounded space skills occupy. Per-skill metadata ≈ 100 tokens; SKILL.md body target <5k tokens / 500 lines; the listing budget scales ~1% of the model's context window. Auto-compaction re-attaches invoked skills within a combined budget, keeping the first ~5k tokens of each.
- **When to use:** When you have many skills, or long sessions where compaction may drop older ones.
- **Pitfalls:** Overflowing the listing budget drops least-used skills' descriptions (run `/doctor`).
- **Example:** Keep a SKILL.md body under ~500 lines and push the long reference material into a separate `reference.md` the skill loads only when needed, so the always-listed part stays cheap.
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Security / trust
- **Definition:** Treat installing a skill like installing software — a malicious skill can direct Claude to misuse tools or exfiltrate data. Audit all bundled files; project-skill `allowed-tools` only takes effect after you accept workspace trust.
- **When to use:** Before installing or trusting any third-party skill.
- **Pitfalls:** Skills that fetch external URLs are especially risky (fetched content may carry injected instructions).
- **Example:** Before trusting a downloaded skill, open every bundled file and read its scripts — "this skill runs `scripts/setup.sh`; show me exactly what that does before I accept workspace trust."
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

---

## 10. Skill design — the craft

*What separates a skill that works from one that doesn't. Mostly Pocock's "writing great skills" plus Anthropic's evaluation guidance.*

### Skills extract predictability from stochastic systems
- **Definition:** Pocock's stated purpose of skills: their "root virtue is consistent *process* across runs, not identical outputs." A skill tames an LLM's randomness by fixing *how* it works, not *what* it produces.
- **When to use:** As the design criterion for any skill — optimize for process consistency.
- **Pitfalls:** Chasing identical outputs (impossible/unhelpful) instead of a reliable process.
- **Example:** "This skill should make the agent follow the same steps every time, even if wording varies."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Leading words
- **Definition:** Compact, pretrained concepts (e.g. *tight*, *red*, *tracer bullet*) that carry distributed meaning while anchoring behavior with minimal tokens. Refactor restatements down into a single leading word.
- **When to use:** When you want a prompt/skill to invoke rich behavior cheaply — lean on terms the model already understands deeply. *(This glossary is itself a catalog of leading words.)*
- **Pitfalls:** Weak leading words produce no-ops; coining unfamiliar words gives you no pretrained leverage.
- **Example:** "Say 'keep the loop tight' instead of three sentences explaining speed and determinism."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Branches / triggers (in descriptions)
- **Definition:** A model-invoked description must do two jobs: say what the skill does and list its distinct *branches* (triggers) — one trigger per branch, synonyms collapsed, leading word front-loaded for "invocation power."
- **When to use:** Writing a skill's description so the agent reliably auto-invokes it at the right moments.
- **Pitfalls:** Multiple triggers per branch (bloat), redundant synonyms, weak leading words.
- **Example:** "Diagnose bugs. Triggers: failing test, flaky behavior, crash, perf regression."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Information hierarchy (steps / reference / external)
- **Definition:** Skill content arranged by immediacy: (1) in-skill *steps* — ordered actions with checkable completion criteria; (2) in-skill *reference* — flat rules/facts consulted on demand; (3) *external reference* — pushed behind pointers.
- **When to use:** Structuring any skill or complex prompt so the most immediate guidance is on top and on-demand facts stay out of the way.
- **Pitfalls:** Mixing reference into the action path so the agent wades through facts mid-task.
- **Example:** "Put the ordered steps first; move the rules table to a reference section below."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Completion criteria (anti–premature-completion)
- **Definition:** Checkable, exhaustive criteria attached to steps that stop the agent declaring a task done early.
- **When to use:** Whenever an agent tends to stop short or skip post-completion work.
- **Pitfalls:** Fuzzy criteria invite premature completion; over-splitting to compensate.
- **Example:** "Done = repro fixed AND regression test passes AND all debug logs removed."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Granularity (split by invocation or by sequence)
- **Definition:** Only split a skill when it earns one of two "loads": *by invocation* (a distinct leading word that should trigger independently) or *by sequence* (steps that tempt premature completion, so splitting forces deeper legwork).
- **When to use:** When deciding whether one skill should become two.
- **Pitfalls:** Splitting for neither reason creates sprawl and navigation cost.
- **Example:** "These steps tempt the agent to stop early — split the post-completion work into its own skill."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Skill failure modes (premature completion · duplication · sediment · sprawl · no-op)
- **Definition:** Five named ways skills go wrong: *premature completion* (stops early — fix criteria), *duplication* (same meaning in many places inflates prominence — keep single source of truth), *sediment* (stale layers accumulate without pruning), *sprawl* (over-long, hurts readability — disclose/branch instead), *no-op* (a line the agent already obeys by default — strengthen the leading word).
- **When to use:** As a review checklist when auditing or pruning a skill or prompt.
- **Pitfalls:** Leaving no-op lines (false confidence they help); trimming instead of deleting failed lines.
- **Example:** "Hunt no-ops sentence by sentence — delete any line the model already obeys."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Pruning / single source of truth
- **Definition:** Maintain one canonical location for each fact; test every line for relevance and *delete* failures rather than trimming. Duplication "inflates prominence" and misleads the agent.
- **When to use:** Continuously, when maintaining skills, prompts, or docs.
- **Pitfalls:** Letting sediment build; restating the same rule in multiple files.
- **Example:** "This rule appears in two skills — keep it in CONTEXT.md and reference it."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Router skill
- **Definition:** A single user-invoked skill that names/lists the others — the cure when user-invoked skills "multiply beyond memory."
- **When to use:** When you have many manual skills you can't remember; one entry point routes to them.
- **Pitfalls:** A router that itself grows stale or doesn't cover all skills.
- **Example:** "/ask-matt — which skill fits 'I need to debug a flaky test'?"
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Evaluation-driven development
- **Definition:** Build *evaluations before* extensive docs: run Claude without the skill to find gaps, create ~3 test scenarios, measure a baseline, write minimal instructions, then iterate. Verify by running realistic prompts in fresh sessions with vs. without the skill.
- **When to use:** Before and throughout authoring, to confirm the skill solves real problems and triggers correctly.
- **Pitfalls:** Testing in the authoring session (leftover context masks gaps); assuming "it triggered" means "it worked."
- **Example:** An `evals.json` entry with `skills`, `query`, `files`, `expected_behavior`.
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Claude A / Claude B iteration
- **Definition:** Develop a skill with one Claude instance ("Claude A," author/refiner) and test it with *fresh* instances ("Claude B," the user) on real tasks, bringing observed gaps back to A.
- **When to use:** Iteratively refining skills based on real behavior rather than assumptions.
- **Pitfalls:** Iterating from assumptions instead of observed Claude B behavior.
- **Example:** In a fresh session (Claude B), run "use the `pdf-fill` skill to complete this form," watch where it stumbles, then bring that transcript back to the authoring session (Claude A): "here's where it guessed the field names — tighten the instructions."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Plan-validate-execute
- **Definition:** Have Claude first emit a structured plan (e.g. `changes.json`), validate it with a script, *then* execute — catching errors before applying changes.
- **When to use:** Batch operations, destructive changes, or high-stakes tasks.
- **Pitfalls:** Skipping the intermediate artifact and letting Claude apply unverified changes.
- **Example:** "First write the renames you'll make to `changes.json` and run `validate.py` on it. Don't touch any files until the validation passes, then apply it."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

---

## Sources

This glossary was synthesized from primary skill files and official documentation, not summaries where the originals were reachable.

**Matt Pocock's skills**
- [mattpocock/skills](https://github.com/mattpocock/skills) — README and the `grill-me`/`grilling`, `domain-modeling`, `diagnosing-bugs`, `to-issues`, `to-prd`, `tdd`, `prototype`, `triage`, `improve-codebase-architecture`, `codebase-design`, `handoff`, `teach`, and `writing-great-skills` SKILL.md files.
- Third-party framing: [aihero.dev](https://www.aihero.dev/my-grill-me-skill-has-gone-viral), [Medium — "adds friction"](https://medium.com/@marc.bara.iniesta/the-ai-coding-repo-that-went-viral-because-it-adds-friction-e2b5c4d2fcc2).

**Anthropic official**
- [Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Claude Code — skills](https://code.claude.com/docs/en/skills)
- [Prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

**Community skill collections**
- [obra/superpowers](https://github.com/obra/superpowers/) — brainstorming, writing/executing plans, systematic debugging, TDD, verification, git worktrees.
- [github/spec-kit](https://github.com/github/spec-kit) — spec-driven development, constitutions.
- [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) — think-before-coding, simplicity, surgical changes, goal-driven execution.
- [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills), [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills), [BehiSecc/awesome-claude-skills](https://github.com/BehiSecc/awesome-claude-skills) — curated catalogs.

**Prompt-engineering canon**
- [DAIR — Prompting Guide](https://www.promptingguide.ai/techniques), [OpenAI best practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api), [Google Prompt Engineering whitepaper](https://archive.org/stream/whitepaper-prompt-engineering-v-4/whitepaper_Prompt%20Engineering_v4_djvu.txt), [Google Cloud — RAG](https://cloud.google.com/use-cases/retrieval-augmented-generation).

*Note: a few third-party blog URLs and the Vercel/Anthropic engineering posts returned bot-blocking (HTTP 403) to the automated fetcher during compilation; their concepts were confirmed against the reachable official docs and search snippets. Some exact `mattpocock/skills` sub-paths above point to the repo's skill directory; if a deep link 404s, browse from the repo root, as folder layout occasionally shifts.*
