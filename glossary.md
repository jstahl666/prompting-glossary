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
- **Example:** "Here's the part of my stock screener that ranks the stocks. Rewrite it so it's easier to read, but make sure it still picks the same stocks in the same order."
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

### Token
- **Definition:** The chunks of text an AI reads and writes — roughly a word or word-piece (~4 characters in English). Usage, cost, speed, and limits are all measured in tokens, not words.
- **When to use:** Understanding length caps and why long prompts/answers cost more. "Max tokens" sets how long a reply can be.
- **Pitfalls:** Assuming one token = one word. Both your prompt and the answer consume the budget.
- **Example:** "Keep your reply short — about 150 words, which is roughly 200 tokens — so it doesn't ramble."
- **Source:** [Google Cloud — RAG](https://cloud.google.com/use-cases/retrieval-augmented-generation)

### Context window
- **Definition:** The total text the AI can consider at once — your prompt + the conversation so far + the answer. Anything beyond it falls out of view.
- **When to use:** When pasting large files or running long sessions; the relevant material has to fit.
- **Pitfalls:** A big window doesn't mean you should fill it. Too much irrelevant text *degrades* quality — it's a shared resource, not free storage.
- **Example:** "Here's the one file from my task widget that you need to fix the bug. Ignore everything we talked about earlier and just focus on this."
- **Source:** [Redis — RAG vs. large context window](https://redis.io/blog/rag-vs-large-context-window-ai-apps/)

### In-context learning
- **Definition:** The model "learns" your task from what's in the prompt (instructions + examples) without being retrained. Zero-shot and few-shot are both forms of it.
- **When to use:** Any time you steer behavior with examples or instructions rather than expecting fine-tuning.
- **Pitfalls:** It's temporary — it lasts only for that prompt/session, not permanently.
- **Example:** "Here are two sample names showing how I want my music samples labeled, like 'Piano_Cmin_120BPM'. Name the rest of my files the exact same way."
- **Source:** [DAIR — Prompting Guide techniques](https://www.promptingguide.ai/techniques)

### Temperature
- **Definition:** A setting (often 0–1) controlling randomness. Low = focused and predictable; high = varied and creative.
- **When to use:** Near 0 for code, classification, and facts; higher for brainstorming.
- **Pitfalls:** High temperature on precision tasks gives inconsistent or wrong output. Not every chat UI exposes the knob.
- **Example:** "Turn the creativity setting (temperature) all the way down to zero for this. I want the same exact, predictable answer every time I ask, not a different guess each run."
- **Source:** [Google — Prompt Engineering whitepaper](https://archive.org/stream/whitepaper-prompt-engineering-v-4/whitepaper_Prompt%20Engineering_v4_djvu.txt)

### Hallucination
- **Definition:** When an AI produces confident-sounding output that is actually false or invented — a fake citation, a non-existent function, a wrong fact.
- **When to use:** A risk to guard against, especially for facts, APIs, and library names.
- **Pitfalls:** Trusting fluent answers without checking. Coding agents love to invent plausible-but-fake function names.
- **Example:** "When you write code for my home server, if you aren't totally sure a tool or command actually exists, tell me you're unsure instead of inventing one that sounds right."
- **Source:** [K2view — grounding & hallucinations](https://www.k2view.com/blog/what-is-grounding-and-hallucinations-in-ai/)

### Grounding
- **Definition:** Anchoring the answer in specific source material you provide (or it retrieves), so responses tie to real, verifiable information rather than the model's memory.
- **When to use:** When accuracy matters and you have the docs, code, or data.
- **Pitfalls:** Reduces hallucination but doesn't eliminate it.
- **Example:** "Answer my synth question using only the gear manual I pasted below. If the manual doesn't cover it, just say 'that's not in the manual' instead of guessing."
- **Source:** [K2view — grounding & hallucinations](https://www.k2view.com/blog/what-is-grounding-and-hallucinations-in-ai/)

### Retrieval-Augmented Generation (RAG)
- **Definition:** A setup where the system automatically looks up relevant information from an external knowledge base and inserts it into the prompt before the AI answers.
- **When to use:** When answers must reflect a large or changing body of documents (a codebase, a wiki) that won't all fit in one prompt.
- **Pitfalls:** Garbage retrieval → garbage answers. If the wrong snippets are pulled in, the model is grounded in irrelevant material.
- **Example:** "My StudioAssistant app pulls the matching pages out of my gear manuals automatically. Treat those pulled-in pages as the truth and base your answer on them."
- **Source:** [Google Cloud — RAG](https://cloud.google.com/use-cases/retrieval-augmented-generation)

---

## 2. Prompt structure

### System prompt
- **Definition:** A top-level instruction that sets the AI's role, rules, and behavior for the *whole* conversation, separate from your individual questions. The "constitution" of the session.
- **When to use:** To establish persistent ground rules — tone, constraints, what "done" looks like, what to never do.
- **Pitfalls:** Cramming task-specific details into it that belong in the message; or leaving it empty and re-explaining rules every turn.
- **Example:** "For our whole chat, act as a patient coding helper for someone non-technical. Always explain what each piece of code does in plain English, and never assume I already know the jargon."
- **Source:** [DAIR — Prompting Guide](https://www.promptingguide.ai/techniques)

### User prompt
- **Definition:** The specific request you type each turn, as opposed to the system prompt's standing rules. The system prompt is the constitution; the user prompt is today's task.
- **When to use:** For the actual question or task; keep persistent rules in the system prompt.
- **Pitfalls:** Mixing persistent rules and one-off requests so the AI can't tell which is which.
- **Example:** "Okay, now add a feature to my task widget that lets me mark a to-do as 'important' so it shows up at the top of the list."
- **Source:** [OpenAI — prompt engineering best practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)

### Role / persona prompting
- **Definition:** Telling the AI to act as a specific kind of expert or character, which shapes its tone, depth, and assumptions.
- **When to use:** To set expertise level and style — "senior code reviewer," "patient teacher for beginners."
- **Pitfalls:** A persona alone doesn't guarantee accuracy; "act as an expert" won't supply missing facts. Overly theatrical roles waste words.
- **Example:** "Act like a careful security expert and look over my home server setup. Point out anything that would let a stranger get into my files."
- **Source:** [Google — Prompt Engineering whitepaper](https://archive.org/stream/whitepaper-prompt-engineering-v-4/whitepaper_Prompt%20Engineering_v4_djvu.txt)

### Delimiters
- **Definition:** Visual separators — triple quotes, tags, markdown headings — that mark where one part of your prompt ends and another begins (instructions vs. data vs. examples).
- **When to use:** Whenever a prompt has multiple parts, especially when you paste code or data alongside instructions.
- **Pitfalls:** Without them, the AI confuses your data for commands.
- **Example:** "Everything between the lines '=== MY CODE ===' below is my actual program — only change what's inside those lines, and treat the rest of my message as instructions, not code."
- **Source:** [OpenAI — best practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)

### XML tags
- **Definition:** Anthropic's recommended delimiter style for Claude — labeled tags like `<instructions>`, `<data>`, `<example>` that keep prompt sections cleanly separated.
- **When to use:** With Claude especially, when a prompt has context, instructions, examples, and data all at once.
- **Pitfalls:** Inconsistent or unclosed tags; over-nesting until it's unreadable.
- **Example:** "I'll put my request inside <task> tags and my song's feedback notes inside <notes> tags. Read the notes and do what the task says, and don't mix them up."
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

### Output formatting
- **Definition:** Telling the AI exactly what shape the answer should take — bullet list, JSON, table, code block only, a set number of items.
- **When to use:** Whenever you'll reuse the output or need it scannable/machine-readable.
- **Pitfalls:** Vague format requests ("make it nice"); not showing an example when the format is precise.
- **Example:** "Give me your take on these three stocks as a simple table with two columns — 'Stock' and 'My verdict' — and nothing else, no extra paragraphs."
- **Source:** [OpenAI — best practices](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)

### Prompt template
- **Definition:** A reusable prompt skeleton with blank slots (variables) you fill in each time, so you don't rewrite structure for repeated tasks.
- **When to use:** For tasks you do often — code reviews, commit messages, bug triage.
- **Pitfalls:** Templates drift out of date; leaving placeholders unfilled.
- **Example:** "Save this as a reusable request I can fill in later: 'Look over my [song name] and give me feedback on the [drums / melody / mix].' I'll just swap in the parts each time."
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

---

## 3. Prompting techniques

### Zero-shot prompting
- **Definition:** Asking the AI to do a task with only instructions and no examples.
- **When to use:** For simple, common tasks the model already handles well.
- **Pitfalls:** Fails on nuanced or format-sensitive tasks where it guesses wrong — add examples instead.
- **Example:** "Without me giving you any examples, just tell me: is this song more 'chill and relaxing' or 'energetic and upbeat'? Here's the track."
- **Source:** [Google — Prompt Engineering whitepaper](https://archive.org/stream/whitepaper-prompt-engineering-v-4/whitepaper_Prompt%20Engineering_v4_djvu.txt)

### Few-shot prompting (multishot)
- **Definition:** Including a few worked input→output examples in the prompt so the AI mimics the pattern. One of the highest-impact techniques for accuracy and consistency.
- **When to use:** When zero-shot gives the wrong format or misses nuance; great for enforcing a house style.
- **Pitfalls:** Examples that are inconsistent, biased toward one answer, or unrepresentative — the model copies their flaws.
- **Example:** "Here are three to-do notes I already wrote and how I'd shorten each one. Follow that same style and shorten this new batch of notes for my task widget the same way."
- **Source:** [Anthropic — prompt engineering overview](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)

### Chain-of-thought (CoT)
- **Definition:** Asking the AI to reason step by step before giving its final answer, which improves accuracy on math, logic, and multi-step problems.
- **When to use:** Anything needing reasoning — debugging logic, planning a multi-step change, weighing trade-offs.
- **Pitfalls:** Wasteful on trivial tasks; some newer "reasoning" models do it internally and you needn't ask.
- **Example:** "My sample tagger is putting the wrong tempo in some file names. Walk through your reasoning one step at a time before you suggest a fix."
- **Source:** [DAIR — Chain-of-Thought](https://www.promptingguide.ai/techniques/cot)

### Self-consistency
- **Definition:** Generating several independent step-by-step answers to the same question, then picking the one that comes up most often (a kind of voting).
- **When to use:** Tricky reasoning where a single pass is unreliable and accuracy beats speed/cost.
- **Pitfalls:** Slower and more expensive (multiple runs); overkill for easy tasks.
- **Example:** "Figure out this song's key three separate times, working it out fresh each time, then tell me which answer you landed on most often."
- **Source:** [DAIR — Prompting Guide](https://www.promptingguide.ai/techniques)

### ReAct (Reason + Act)
- **Definition:** A loop where the AI alternates between reasoning and taking actions (using a tool, searching, reading a file), observing the result, then reasoning again. The backbone of most coding agents.
- **When to use:** Tasks needing tools or external steps — running code, reading files, looking things up.
- **Pitfalls:** Loops where the agent keeps acting without progress; needs clear stopping criteria.
- **Example:** "Track down why my stock screener won't start: open the file that's failing, make a guess about the cause, test that guess, and then tell me what you found."
- **Source:** [DAIR — ReAct](https://www.promptingguide.ai/techniques/react)

### Prompt chaining
- **Definition:** Breaking a big task into a sequence of smaller prompts, where each step's output feeds the next.
- **When to use:** Complex work — outline, then draft, then review — so each step stays focused and checkable.
- **Pitfalls:** Errors compound down the chain if you don't verify intermediate steps.
- **Example:** "Let's do my vocal separator upgrade in stages. First just list the parts of the program you'd need to touch. I'll check the list, then we'll do the actual changes after."
- **Source:** [DAIR — Prompt Chaining](https://www.promptingguide.ai/techniques/prompt_chaining)

### Least-to-most prompting
- **Definition:** Explicitly decomposing a problem into sub-problems from simplest to hardest, then solving them in order and carrying results forward.
- **When to use:** Problems that build on themselves, where solving easy parts first unlocks the hard part.
- **Pitfalls:** Over-engineering simple tasks; getting the decomposition order wrong.
- **Example:** "First get my sample tagger working for just one file, then once that works, build up to handling a whole folder of files one step at a time."
- **Source:** [DAIR — Prompting Guide](https://www.promptingguide.ai/techniques)

### Meta-prompting
- **Definition:** Using the AI to *write or improve prompts* (or to set up a task's structure) rather than to answer directly.
- **When to use:** When you're unsure how to phrase something — ask the AI to draft a better prompt.
- **Pitfalls:** Accepting a generated prompt without checking it fits your goal; adds unnecessary abstraction.
- **Example:** "I'm bad at asking for music feedback. Write me a really good prompt I can paste in that gets you to critique my track like a tough mentor."
- **Source:** [DAIR — Meta-Prompting](https://www.promptingguide.ai/techniques/meta-prompting)

### Negative prompting
- **Definition:** Explicitly telling the AI what to *avoid* — topics, formats, tones, or approaches you don't want.
- **When to use:** To rule out known failure modes; pairs with positive instructions.
- **Pitfalls:** Long "don't" lists are often weaker than stating clearly what you DO want; pure negatives can confuse the model.
- **Example:** "Fix this part of my task widget, but don't add any new outside tools, don't rename anything, and don't touch the parts that already work."
- **Source:** [FlowHunt — negative prompt](https://www.flowhunt.io/glossary/negative-prompt/)

### Stop sequences
- **Definition:** Specific text that, when produced, tells the model to stop generating immediately — used to cap output at a natural boundary.
- **When to use:** Mostly in API/tool settings, to prevent rambling or stop after a delimiter.
- **Pitfalls:** Choosing a sequence that appears in normal output cuts answers off early. Often not exposed in chat UIs.
- **Example:** "Stop writing as soon as you reach the line 'END OF SUMMARY' so you don't keep going past the part I actually need."
- **Source:** [Daniel Corin — prefill & stop sequences](https://www.danielcorin.com/til/prompting/prefill-and-stop-sequences/)

### Prefilling *(legacy — see note)*
- **Definition:** Starting the AI's answer for it (putting the first words in its mouth) to force a format or skip preambles.
- **When to use:** On older models/APIs that support it, to enforce structure (e.g., begin with `{` for JSON).
- **Pitfalls:** **Not supported on current Claude models** (Opus 4.8, Sonnet 4.6, etc.). On today's models, ask for the format explicitly or use structured outputs instead.
- **Example:** (this old trick no longer works on current Claude) On older AI you could type the start of the answer yourself to force a format; today just ask plainly, e.g. "Reply with only a list, no intro sentence."
- **Source:** [Anthropic — prefill Claude's response](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/prefill-claudes-response)

### Guardrails
- **Definition:** Rules and safeguards — in the system prompt or a separate screening step — that keep the AI's behavior within safe, acceptable bounds and help it refuse bad requests.
- **When to use:** When the assistant faces untrusted input or must stay strictly on-task and in-character.
- **Pitfalls:** Vague guardrails the model talks around; relying on prompt rules alone for real security.
- **Example:** "You're only here to help with my music gear questions. If I ask about anything else, just reply 'That's outside what I help with here.'"
- **Source:** [Anthropic — keep Claude in character](https://docs.anthropic.com/en/docs/test-and-evaluate/strengthen-guardrails/keep-claude-in-character)

---

## 4. Alignment & collaboration

*The heart of Matt Pocock's approach: most failures come from the agent and you wanting different things. Fix that before any code is written.*

### Misalignment
- **Definition:** The communication gap between you and the agent — it builds something subtly (or wildly) different from what you wanted. Pocock calls it "the most common failure mode in software development."
- **When to use:** Recognize it whenever you're about to hand over a vague, one-line request. The cure is to align *before* building, not debug after.
- **Pitfalls:** Easy to ignore because the agent confidently produces *something*; the cost shows up later as rework. Fluent output isn't aligned output.
- **Example:** "Before you build anything for my music feedback tool, ask me questions until you're sure you understand exactly what I want. I don't want you guessing and building the wrong thing."
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
- **Example:** "Let's decide the big things first. Don't ask me what to name buttons in my task widget until we've settled what the app actually does."
- **Source:** [mattpocock/skills — grilling](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md)

### Rubber-ducking, made argumentative
- **Definition:** Grilling reframed: it automates classic "rubber-duck debugging" (explaining a problem aloud to find the answer) but makes the duck *push back* — challenging assumptions instead of passively listening.
- **When to use:** When you want to pressure-test your own thinking, not just narrate it.
- **Pitfalls:** If the agent only validates ("great idea!"), it isn't grilling — adversarial refinement is the point.
- **Example:** "Don't just agree with my plan for the vocal separator. Push back and tell me the weak spots — I want you to argue with me, not flatter me."
- **Source:** [aihero.dev — my Grill Me skill has gone viral](https://www.aihero.dev/my-grill-me-skill-has-gone-viral)

### Adding friction
- **Definition:** The philosophy behind Pocock's repo: skills deliberately insert steps the agent would otherwise skip. "They do not make the agent smarter; they make it slower in exactly the places where slowing down matters."
- **When to use:** When fast, unconstrained generation is producing low-quality or misaligned results.
- **Pitfalls:** Friction *everywhere* is just slowness — apply it at high-leverage points (alignment, design, architecture), not every keystroke.
- **Example:** "Before you start coding my stock screener change, slow down and walk me through the plan first — I'd rather take the extra time now than fix a mess later."
- **Source:** [Medium — the AI coding repo that went viral because it adds friction](https://medium.com/@marc.bara.iniesta/the-ai-coding-repo-that-went-viral-because-it-adds-friction-e2b5c4d2fcc2)

### Vibe coding *(anti-pattern)*
- **Definition:** Casual, unconstrained "just let the AI build it" workflow. Pocock's skills point *away* from this, putting AI inside mature engineering constraints.
- **When to use:** Name it to recognize when you've slid into it; fine for throwaway prototypes, dangerous for production.
- **Pitfalls:** Produces a "ball of mud" over time — speed now, entropy later.
- **Example:** "Let's not just wing this and let you throw code at it until it sort of works. My home server holds my real files, so plan it out carefully and test it properly."
- **Source:** [mattpocock/skills — README](https://github.com/mattpocock/skills/blob/main/README.md)

### Shared language / ubiquitous language
- **Definition:** Consistent, project-specific vocabulary shared between you and the agent, so a single word stands in for a verbose explanation. Cuts tokens and improves consistency.
- **When to use:** Establish early (via domain modeling) so later prompts can be terse and unambiguous.
- **Pitfalls:** Letting the agent invent synonyms for an existing term; vague language that drifts.
- **Example:** "Let's agree that when I say 'a clip' I mean one short audio sample, and stick to that word the whole time so we don't confuse each other."
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### Domain modeling
- **Definition:** The *active* discipline of building and sharpening a project's shared vocabulary during design — challenging vague terms, proposing canonical ones, testing relationships with edge cases — not just reading existing docs.
- **When to use:** During grilling and design, whenever new concepts or ambiguous terms surface.
- **Pitfalls:** Treating it as passive reference; not calling out when your terminology conflicts with the glossary.
- **Example:** "We keep using 'track' to mean two different things — the whole song and one instrument. Pick one clear word for each so we stop talking past each other."
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### CONTEXT.md
- **Definition:** A root-level glossary file holding *only* domain vocabulary, stripped of implementation detail or specs. Multi-domain repos use a CONTEXT-MAP.md pointing to per-domain files. Created lazily — only when something needs recording.
- **When to use:** As the single source of truth for shared language; reference it in prompts to keep the agent aligned.
- **Pitfalls:** Polluting it with specs or code; creating it eagerly before there's anything to record.
- **Example:** "Start a notes file for my project and write down what I mean by 'sample tagger' in one sentence, so any future chat knows the term."
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### Handoff
- **Definition:** A compact transition document that lets a *fresh* agent resume work without replaying full context — it references existing artifacts (PRDs, ADRs, issues, diffs) rather than duplicating them, redacts secrets, and lists suggested skills.
- **When to use:** When a conversation grows long or you're passing work to another agent/session.
- **Pitfalls:** Copying artifacts wholesale (breaks single-source-of-truth); leaking secrets.
- **Example:** "Write a short summary of where we got to on my home server setup so I can paste it into a fresh chat tomorrow and pick up without re-explaining everything."
- **Source:** [mattpocock/skills — handoff](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md)

### Agent brief
- **Definition:** The concise spec posted on a "ready-for-agent" issue that tells an agent exactly what to build — a delegable handoff at the issue level.
- **When to use:** When promoting a triaged issue to agent-executable status.
- **Pitfalls:** A brief that's actually still ambiguous; grill it to sharpness first.
- **Example:** "Write a clear, short work order for this task — exactly what to build for my task widget and how we'll know it's done — so a helper could pick it up and just do it."
- **Source:** [mattpocock/skills — triage](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

### Triage state machine
- **Definition:** Moving issues through states (needs-triage → needs-info → ready-for-agent / ready-for-human / wontfix), each carrying one category label (bug/enhancement) and one state label.
- **When to use:** Managing an issue tracker that feeds agent work; "ready-for-agent" means fully specified and delegable.
- **Pitfalls:** Marking under-specified issues ready-for-agent; not disclosing AI involvement in comments.
- **Example:** "Look at this bug I logged for my stock screener — first check you can actually make it happen, then tell me if it's ready to hand off or if you still need more info from me."
- **Source:** [mattpocock/skills — to-issues](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

---

## 5. Planning & delivery

### Spec-driven development (SDD)
- **Definition:** A methodology that puts a written specification at the center of AI coding: describe what to build, refine through structured phases, then let the agent implement — rather than prompting straight to code.
- **When to use:** Non-trivial features where inferring intent from a vague prompt is unreliable.
- **Pitfalls:** Heavy ceremony for tiny tasks; the spec drifts from reality if it isn't kept the single source of truth.
- **Example:** "Before any code, let's write down exactly what my fantasy football breakout tool should do. Once that's locked, then make a plan, then build it — in that order."
- **Source:** [github/spec-kit](https://github.com/github/spec-kit)

### Constitution / non-negotiable constraints
- **Definition:** A file of fixed rules (allowed tech, testing requirements, style, forbidden libraries) the agent checks against continuously and is flagged for violating.
- **When to use:** Enforcing standards across a whole session so the agent can't silently reach for a banned dependency.
- **Pitfalls:** Stale constraints block legitimate work; too many rules and the agent loses focus.
- **Example:** "Set these rules for the whole project and stick to them: don't add any new outside tools, write a quick test for anything new, and keep the same style as my existing stock screener code."
- **Source:** [github/spec-kit](https://github.com/github/spec-kit)

### Brainstorming (Socratic design refinement)
- **Definition:** A pre-coding phase where the agent refines a rough idea by asking questions and exploring alternatives, presenting the design in chunks for your sign-off before any code.
- **When to use:** When requirements are fuzzy and you want design alignment before a plan.
- **Pitfalls:** Can stall in endless questioning; skipping it builds the wrong thing fast.
- **Example:** "Let's brainstorm my chord sketchpad idea before building. Walk me through the options one chunk at a time and wait for me to approve each part before moving on."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### PRD (As-a / I-want / so-that)
- **Definition:** A Product Requirements Document synthesized from your conversation (no new interview): problem, solution, user stories in "As a [actor], I want [feature], so that [benefit]" form, implementation/testing decisions, out-of-scope.
- **When to use:** To crystallize a discussed feature into a publishable spec before slicing into issues.
- **Pitfalls:** Stuffing file paths/code into it; conducting fresh interviews instead of synthesizing what was said.
- **Example:** "Take everything we just talked about for my scale trainer and turn it into one tidy write-up — the problem, what we're building, and what's out of scope — without asking me anything new."
- **Source:** [mattpocock/skills — to-prd](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-prd/SKILL.md)

### Writing plans (bite-sized tasks)
- **Definition:** Turning an approved design into a plan of tiny tasks (~2–5 minutes each), every task carrying exact file paths, complete code, and verification steps.
- **When to use:** Before execution, to make work reviewable and stop the agent wandering.
- **Pitfalls:** "No placeholders" — vague directives like "add appropriate error handling" without the actual code defeat the purpose.
- **Example:** "Break the work on my task widget into tiny steps, each just a few minutes long, and for every step tell me exactly which file to change and how I'll check it worked."
- **Source:** [obra/superpowers — writing-plans](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md)

### Task right-sizing
- **Definition:** Sizing each task as "the smallest unit that carries its own test cycle and is worth a fresh reviewer's gate" — folding setup together but splitting where independent review is meaningful.
- **When to use:** Structuring a plan so each step is independently verifiable.
- **Pitfalls:** Too-large tasks can't be reviewed cleanly; too-small tasks fragment context and add overhead.
- **Example:** "Make each step in the plan small enough that I can check it on its own, but don't chop it so fine that I'm approving twenty trivial steps in a row."
- **Source:** [obra/superpowers — writing-plans](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md)

### Vertical slice
- **Definition:** A thin, end-to-end cut through *all* layers (schema, API, UI, tests) that is independently demoable or verifiable — the unit work should be broken into.
- **When to use:** When decomposing a spec into issues or tasks; favor complete narrow paths over whole layers.
- **Pitfalls:** Slicing *horizontally* instead (see below) leaves nothing demoable until the very end.
- **Example:** "Build my chord sketchpad one complete little feature at a time, where each one actually does something I can try out — not half-finished pieces I can't use yet."
- **Source:** [mattpocock/skills — to-issues](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

### Tracer bullet
- **Definition:** A narrow-but-complete implementation that goes the whole distance through the system to prove the path works — the mechanism behind vertical slices and TDD ("one test → one implementation, repeat").
- **When to use:** When you want early end-to-end validation and to learn before building breadth.
- **Pitfalls:** Don't confuse it with a prototype — a tracer bullet is real, kept code; a prototype is throwaway.
- **Example:** "Get one simple thing working end to end in my vocal separator first — load a song, split it, and save the result — so we know the whole path works before adding the fancy stuff."
- **Source:** [mattpocock/skills — tdd](https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md)

### Horizontal slicing *(anti-pattern)*
- **Definition:** Building or testing one whole layer at a time (all schema, then all API…), or in TDD writing all tests up front then all implementation. Produces tests that reflect *imagined* rather than actual behavior.
- **When to use:** Name it to avoid it; recognize when work isn't independently verifiable.
- **Pitfalls:** Tests validate data shape rather than user-facing behavior and go blind to real regressions.
- **Example:** "Don't build the whole behind-the-scenes part of my stock screener and leave nothing I can actually run. Finish one usable feature at a time instead."
- **Source:** [mattpocock/skills — tdd](https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md)

### Interfaces block / contracts
- **Definition:** A section in a plan task showing consumed inputs and produced outputs with exact signatures, so sequential tasks have clear contracts between them.
- **When to use:** Multi-task plans where later steps depend on earlier outputs.
- **Pitfalls:** Undefined references to types/methods across tasks break sequential execution.
- **Example:** "For each step, write down what it takes in and what it hands off to the next step, so the later parts of my tool know exactly what to expect from the earlier ones."
- **Source:** [obra/superpowers — writing-plans](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md)

### Prefactoring
- **Definition:** Refactoring *before* implementing a slice so the actual change becomes trivial — "make the change easy, then make the easy change."
- **When to use:** When the current structure makes the intended change awkward; reshape first.
- **Pitfalls:** Bundling prefactor + feature into one messy commit; or forcing a hard change into hostile code.
- **Example:** "Before you add the new feature to my task widget, tidy up the surrounding code first if that makes the new part simpler to drop in. Make the change easy, then make it."
- **Source:** [mattpocock/skills — to-issues](https://github.com/mattpocock/skills/blob/main/skills/engineering/to-issues/SKILL.md)

### Executing plans (with human checkpoints)
- **Definition:** Batch execution of a plan's tasks where the agent stops at defined checkpoints for your approval rather than running unattended end to end.
- **When to use:** When you want autonomy *within* a step but a gate *between* steps.
- **Pitfalls:** Too many checkpoints kill throughput; too few lose the safety they exist for.
- **Example:** "Work through the plan for my stock screener a few steps at a time, then stop and show me what you did. Wait for me to say 'go' before doing the next batch."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Subagent-driven development
- **Definition:** A workflow where subagents work through each task and their output is inspected in a two-stage review (spec compliance, then code quality) before moving on.
- **When to use:** Parallelizing or isolating task execution while keeping a review gate.
- **Pitfalls:** Subagents lack the parent's full context and can drift; review must catch spec deviations.
- **Example:** "Have a separate helper do each step of my project, then you check its work twice — first that it did what I asked, then that the code is clean — before moving on."
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
- **Example:** "Give each helper its own separate copy of my project to work in, so their changes don't trip over each other while they run at the same time."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Goal-driven execution (success criteria)
- **Definition:** Define verifiable success criteria *before* starting — turning vague imperatives into testable outcomes — then loop independently toward that target with explicit verification checkpoints.
- **When to use:** Multi-step work where the agent should self-verify instead of asking constantly.
- **Pitfalls:** No measurable target means the agent can't tell when it's done (or claims done too early).
- **Example:** "Here's how we'll know my sample tagger is finished: it can read a folder of songs and rename every file with the right key and tempo, with no errors. Work toward that and check it yourself."
- **Source:** [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md)

### Throwaway prototype / spike
- **Definition:** Code written to answer one specific design question, then deleted. A "logic" spike tests a state model in a terminal app; a "UI" spike generates several radically different UI variants. Mark it throwaway from the start; expose state visibly; clean up after.
- **When to use:** When you're unsure whether a state model works or what something should look like — validate before committing to production code.
- **Pitfalls:** Letting prototype code stagnate into production; over-polishing it.
- **Example:** "Make me a quick throwaway version of my scale trainer just to see if the idea feels right. We'll delete it after — don't make it polished, just make it work enough to try."
- **Source:** [mattpocock/skills — prototype](https://github.com/mattpocock/skills/blob/main/skills/engineering/prototype/SKILL.md)

---

## 6. Debugging & testing

### Test-Driven Development (TDD) / red-green-refactor
- **Definition:** Write a failing test (RED), implement the minimum to pass (GREEN), then refactor with tests green. Pocock's rule: **never refactor while RED** — you lose your safety signal.
- **When to use:** Any change whose behavior can be expressed as a test; gives the agent a verifiable target.
- **Pitfalls:** Agents fake it by writing always-passing tests or implementing before the test fails. Refactoring during red is the classic mistake.
- **Example:** "First write a quick check that fails, then write just enough code for my task widget to make it pass, and only tidy things up once that check is passing — not before."
- **Source:** [mattpocock/skills — tdd](https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md), [obra/superpowers](https://github.com/obra/superpowers/)

### Behavior over implementation (testing)
- **Definition:** Tests should verify *behavior* through public interfaces, acting as integration-style specs of real code paths — not couple to internal structure.
- **When to use:** Whenever specifying tests; ask "does this survive a refactor that keeps behavior?"
- **Pitfalls:** Implementation-coupled tests break on harmless refactors and validate data shapes instead of capabilities.
- **Example:** "When you test my sample tagger, check the thing I actually care about — that the file ends up with the right name — not the little behind-the-scenes steps that might change later."
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
- **Example:** "My vocal separator is leaving echo on the vocals. Before changing anything, give me your top three guesses for why, ranked, and how you'd test each one."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Feedback loop
- **Definition:** A rapid, automatic signal (types, tests, browser access, CLI checks) that tells the agent whether code works, preventing "blind" development. Pocock: "Build the right feedback loop, and the bug is 90% fixed."
- **When to use:** Establish one *before* iterating; the tighter and more deterministic, the better.
- **Pitfalls:** Letting the agent code without any pass/fail signal; slow or flaky loops it can't run itself.
- **Example:** "Before you try to fix my stock screener, set up a quick way to run it and instantly see whether it's broken or working, so we're not guessing whether your fix helped."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Loop qualities: red-capable / deterministic / fast / agent-runnable
- **Definition:** The four properties of a good feedback loop: it catches the specific symptom (red-capable), gives the same result every run (deterministic), runs in seconds (fast), and the agent can run it without you (agent-runnable).
- **When to use:** As a checklist when building any repro harness or test signal.
- **Pitfalls:** A loop that's green even when the bug is present (not red-capable) is worse than none.
- **Example:** "Before guessing at the cause, find a way to make this bug happen the same way every time, fast, and one that you can run yourself without me."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Falsifiable hypothesis
- **Definition:** A debugging guess phrased as a testable prediction: "If X is the cause, then changing Y will…" Hypotheses are ranked and shared with you for domain input.
- **When to use:** Before instrumenting any bug; forces the agent to commit to predictions it can disprove.
- **Pitfalls:** Vague guesses ("maybe it's a race condition") that can't be tested or ruled out.
- **Example:** "Phrase each guess about my song-feedback tool's bug as something we can actually test, like 'if the problem is the file size, then shrinking the file should fix it.'"
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Root-cause tracing
- **Definition:** Tracing a bug backward through the call stack to its original trigger rather than patching where the symptom appears.
- **When to use:** When the error surfaces far from its cause.
- **Pitfalls:** Patching the symptom layer masks the real defect and spawns more bugs.
- **Example:** "Don't just patch the spot where my stock screener shows the error. Follow it back to where things actually go wrong and fix it there."
- **Source:** [obra/superpowers — systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md)

### Tagged debug instrumentation
- **Definition:** Adding targeted logs with unique prefixes (e.g. `[DEBUG-a4f2]`) mapped to specific hypotheses, then removing them all in cleanup.
- **When to use:** When you need observability for a hypothesis test and want to find/strip the logs reliably afterward.
- **Pitfalls:** Untagged logs get lost in noise or shipped to production.
- **Example:** "While we hunt this bug, add little progress messages that all start with a tag like [CHECK1] so they're easy to spot, and remove every one of them once we've fixed it."
- **Source:** [mattpocock/skills — diagnosing-bugs](https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md)

### Condition-based waiting
- **Definition:** Replacing arbitrary timeouts/sleeps with polling on an actual condition.
- **When to use:** Flaky tests or async code that "fixed" itself with a `sleep`.
- **Pitfalls:** Fixed sleeps are non-deterministic — too short flakes, too long wastes time.
- **Example:** "Instead of just waiting a fixed two seconds and hoping the song finished loading, have it wait until the song is actually loaded before moving on."
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
- **Example:** "Before you tell me my task widget change is ready, look over your own work twice — first did it do what I asked, then is the code clean — and fix anything you catch."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

### Property-based testing
- **Definition:** Validating that *properties* hold across many generated inputs rather than asserting fixed example cases.
- **When to use:** Logic with broad input spaces (parsers, encoders, math) where examples miss edge cases.
- **Pitfalls:** Poorly chosen properties or generators give false confidence; slow/flaky if unbounded.
- **Example:** "Instead of testing my sample tagger on just a couple of files, throw hundreds of random songs at it and check that the tempo it writes is always a sensible number."
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
- **Example:** "Look at the piece of my tool that checks tempo as its own little building block, and tell me whether it's well put together on its own."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Interface (as test surface)
- **Definition:** Everything a caller must know to use a module — signatures, invariants, constraints, errors, config, performance — far broader than just types. "The interface is the test surface."
- **When to use:** When defining what to test (test through the interface, not internals) and when judging module depth.
- **Pitfalls:** Treating the interface as just the function signature; testing implementation details.
- **Example:** "Test my sample tagger the way I'd actually use it — point it at a file and check the result — rather than poking at its little inner workings."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Depth (deep vs. shallow module)
- **Definition:** Depth = "the amount of behaviour a caller can exercise per unit of interface they have to learn." A *deep* module has a small interface hiding substantial logic (good); a *shallow* one has a large interface for thin/pass-through logic (avoid).
- **When to use:** When designing or auditing modules; aim to make them deeper.
- **Pitfalls:** Extracting tiny "pure functions for testing" that are actually shallow and hide bugs in their call patterns.
- **Example:** "Is this part of my stock screener pulling its weight? It seems like I have to learn just as much to use it as it actually does for me."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Deepening opportunity
- **Definition:** A proposed refactor that turns a shallow module into a deep one, improving testability and AI-navigability — the unit the `improve-codebase-architecture` skill surfaces as cards.
- **When to use:** During periodic architecture audits to combat degradation.
- **Pitfalls:** Re-litigating settled decisions (check ADRs first); refactors that just *relocate* complexity.
- **Example:** "Look over my whole stock screener and give me a ranked list of places where the code could be reorganized to be simpler and easier to work with."
- **Source:** [mattpocock/skills — improve-codebase-architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Seam
- **Definition:** The place where a module's interface lives — the spot where you can alter behavior without editing that spot (Michael Feathers' definition). "One seam is the ideal target" when planning where to test.
- **When to use:** When deciding where to test or substitute behavior; reuse existing seams and keep them few.
- **Pitfalls:** Testing at the wrong seam (one that doesn't exercise the real bug); spawning many new seams scatters the test surface.
- **Example:** "Put your test right at the spot where my vocal separator actually goes wrong in real use, not at some unrelated point that wouldn't catch this bug."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Adapter (one/two-adapter rule)
- **Definition:** A concrete implementation satisfying an interface at a seam. Heuristic: "one adapter signals a *potential* seam; two adapters indicate a *genuine* one."
- **When to use:** To decide whether an abstraction is justified — don't introduce a seam until a second implementation demands it.
- **Pitfalls:** Building speculative abstraction around a single adapter (premature generalization).
- **Example:** "My sample tagger only reads one file type right now. Don't build a fancy flexible system for 'any file type' until I actually need to support a second one."
- **Source:** [mattpocock/skills — improve-codebase-architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### The deletion test
- **Definition:** A check for whether a module earns its place: if deleting it eliminates complexity, it was redundant; if the complexity merely scatters across many callers, the module was pulling its weight.
- **When to use:** When auditing whether an abstraction concentrates or just relocates complexity.
- **Pitfalls:** Keeping shallow modules out of habit; deleting a module whose complexity then leaks everywhere.
- **Example:** "If we deleted this helper from my task widget, would things get simpler, or would the messiness just spread out into a bunch of other places? Tell me which."
- **Source:** [mattpocock/skills — improve-codebase-architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Leverage
- **Definition:** An outcome of deep design — callers gain more capability per unit of interface learned, and one implementation serves many call sites.
- **When to use:** As a goal when judging whether a design is worth it.
- **Pitfalls:** Mistaking lots of small reusable helpers for leverage when they actually add interface surface.
- **Example:** "Does setting it up this way actually buy me a lot for a little — does one simple piece end up doing a ton of useful work across my stock screener?"
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Locality
- **Definition:** An outcome of deep design — changes stay concentrated, so a fix applies everywhere at once rather than scattering.
- **When to use:** When weighing where logic should live; prefer designs that keep related change in one place.
- **Pitfalls:** Shallow modules "lack meaningful locality" — logic extracted for testing but whose real behavior is spread across call sites.
- **Example:** "Right now the tempo-rounding rule for my sample tagger is copied in a few spots. Put it in one place so I only ever have to change it once."
- **Source:** [mattpocock/skills — codebase-design](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)

### Ball of mud
- **Definition:** A codebase degraded into tangled, hard-to-maintain complexity — the failure state intentional design fights against.
- **When to use:** Name it to justify architecture investment; the risk rises as AI accelerates code output.
- **Pitfalls:** Assuming faster generation is harmless — it accelerates entropy toward a ball of mud without intentional design.
- **Example:** "My music tool's code is getting tangled and hard to follow. Go through it and suggest ways to clean it up before it turns into a hopeless mess I can't change."
- **Source:** [mattpocock/skills — README](https://github.com/mattpocock/skills/blob/main/README.md)

### Architecture Decision Record (ADR)
- **Definition:** A sparsely-created record of a decision, written only when the choice is hard to reverse, non-obvious to future readers, and a genuine trade-off — used to avoid re-litigating settled matters.
- **When to use:** When a real architectural fork is being decided; reference ADRs so the agent doesn't reopen them.
- **Pitfalls:** Over-producing ADRs for trivial choices; ignoring existing ones and re-debating.
- **Example:** "We just made a big choice about how my home server stores files that'd be a pain to undo later. Write down what we picked and why, so future-me doesn't second-guess it."
- **Source:** [mattpocock/skills — domain-modeling](https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md)

### Think before coding (surface assumptions)
- **Definition:** State your interpretation and assumptions explicitly *before* coding; if multiple interpretations exist, present them — don't pick silently.
- **When to use:** Any ambiguous request. The named anti-pattern is "silent decision-making under ambiguity."
- **Pitfalls:** The agent guesses and builds the wrong thing fast.
- **Example:** "When I say 'tag my samples,' tell me what you think I mean before you start. If there's more than one way to read it, lay out the options and let me pick."
- **Source:** [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md)

### Simplicity first (anti-overengineering)
- **Definition:** Write the minimum code that solves the problem — nothing speculative, no abstractions for single-use code, no unasked-for error handling. Test: "Would a senior engineer call this overcomplicated?"
- **When to use:** As a constant guardrail.
- **Pitfalls:** Speculative features, unnecessary abstractions, premature handling for impossible scenarios.
- **Example:** "This part of my task widget got way more complicated than it needs to be. Strip it down to the simplest version that just does what I actually asked for."
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
- **Example:** "Don't build extra features for my stock screener 'just in case' I want them someday. But if you see the same chunk of code copied in three spots, do combine it into one."
- **Source:** [obra/superpowers](https://github.com/obra/superpowers/)

---

## 8. Learning with an agent

### Teaching workspace (knowledge / skills / wisdom)
- **Definition:** A multi-session learning system grounded in a MISSION.md (why you want the skill), distinguishing three learning types — Knowledge (acquire from high-trust sources), Skills (durability via effortful practice), Wisdom (real-world application).
- **When to use:** When using an agent as a long-running tutor rather than one-off Q&A.
- **Pitfalls:** Abstract lessons disconnected from a real mission; designing for momentary recognition instead of retention.
- **Example:** "Be my ongoing music teacher. My goal is to make tracks that sound like Porter Robinson, and I'm a beginner. Build today's lesson around that, just past what I can already do."
- **Source:** [mattpocock/skills — teach](https://github.com/mattpocock/skills/blob/main/skills/productivity/teach/SKILL.md)

### Zone of proximal development
- **Definition:** Lessons should feel "just right": challenging without overwhelming working memory.
- **When to use:** When calibrating the difficulty of agent-delivered instruction.
- **Pitfalls:** Too easy (no growth) or too hard (overwhelm).
- **Example:** "Teach me about chord progressions at a level just a step above what I know now — challenging enough to grow, but not so much that I'm totally lost."
- **Source:** [mattpocock/skills — teach](https://github.com/mattpocock/skills/blob/main/skills/productivity/teach/SKILL.md)

### Storage strength over fluency
- **Definition:** Design learning for long-term retention via retrieval practice, spacing, and interleaving — not momentary recognition that *feels* like learning but doesn't stick.
- **When to use:** When you want durable mastery from agent-led teaching.
- **Pitfalls:** Mistaking re-reading/recognition for actual learning.
- **Example:** "Don't just re-explain how to build a melody — quiz me on it now, and again in a few days, so it actually sticks in my memory."
- **Source:** [mattpocock/skills — teach](https://github.com/mattpocock/skills/blob/main/skills/productivity/teach/SKILL.md)

---

## 9. Writing skills — the mechanics

*How an Agent Skill actually works under the hood (Anthropic's official model). Relevant if you want to package your own reusable prompts.*

### Skill (Agent Skill)
- **Definition:** A self-contained folder of instructions, metadata, and optional resources (scripts, templates, reference files) that Claude loads dynamically to specialize at a task — "domain-specific expertise that transforms general-purpose agents into specialists."
- **When to use:** When you keep pasting the same instructions/checklist, or a chunk of CLAUDE.md has grown into a *procedure* rather than a fact.
- **Pitfalls:** Treating a skill as a one-off prompt; skills are reusable and load on demand.
- **Example:** "I keep pasting the same long instructions to get music feedback. Turn that into a reusable skill I can trigger with one word, so you do it the same way every time."
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### SKILL.md
- **Definition:** The required entry file of every skill: YAML frontmatter (metadata) plus a markdown body of instructions Claude follows when the skill runs.
- **When to use:** Always — it's the one required file; everything else in the folder is optional.
- **Pitfalls:** Letting the body grow too long (keep under ~500 lines); narrating "how/why" instead of stating what to do.
- **Example:** "Set up my new 'feedback' skill: write the main instructions file that tells you, step by step, exactly how to critique one of my tracks."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Frontmatter
- **Definition:** The `---`-delimited YAML block atop SKILL.md that configures behavior. Only `name` and `description` are part of the core spec; Claude Code adds many optional fields.
- **When to use:** To set discovery metadata and (in Claude Code) invocation/tool/model controls.
- **Pitfalls:** Relying on Claude Code-only fields (e.g. `allowed-tools`) when running via the SDK, where they're ignored.
- **Example:** "At the top of my skill file, fill in the little settings box — give it a name, a one-line description of what it does, and set it so it only runs when I ask for it."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### `name` (frontmatter)
- **Definition:** The skill's identifier/label. Max 64 chars; lowercase letters, numbers, hyphens only; no XML tags; can't contain "anthropic" or "claude." Usually does *not* set the `/command` (the directory name does).
- **When to use:** Set a clear display label; prefer gerund form (verb + -ing).
- **Pitfalls:** Vague names (`helper`, `utils`); assuming `name` changes the slash command.
- **Example:** "Give my skill a short, clear name like 'tagging-samples' — all lowercase with dashes, no spaces — so it's easy to recognize."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### `description` (the trigger)
- **Definition:** What the skill does *and when to use it*. The single most important field for discovery — Claude reads it to decide whether the skill is relevant.
- **When to use:** Always. Put the key use case first; include both the action and concrete trigger contexts/keywords. Write in third person.
- **Pitfalls:** Marketing copy or first/second person ("I can help…"); vague text like "Helps with documents."
- **Example:** "Write the one-line description for my feedback skill so it says what it does and when to use it, like: 'Critiques a song and suggests improvements. Use when I share a track or ask for feedback.'"
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Progressive disclosure
- **Definition:** The core design principle: Claude loads skill information in *tiers* as needed, not all upfront — like a manual with a table of contents, then chapters, then an appendix. Keeps the context window lean so hundreds of skills can coexist.
- **When to use:** Designing any skill that grows beyond a lean SKILL.md — split detail into bundled files.
- **Pitfalls:** Cramming everything into SKILL.md; or a vague `description` that breaks discovery so the skill never triggers.
- **Example:** "Keep my skill's main file short. Move the long reference details into a separate file that you only open when you actually need it, so it doesn't slow everything down."
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### Three levels of loading
- **Definition:** The concrete tiers of progressive disclosure. **Level 1** = metadata (`name` + `description`), always in the system prompt (~100 tokens/skill). **Level 2** = SKILL.md body, loaded when triggered (target <5k tokens). **Level 3** = bundled resources/code, loaded or executed only as needed (effectively unlimited).
- **When to use:** As a mental model for where each piece belongs, by cost and frequency.
- **Pitfalls:** Putting rarely-needed reference material in Level 2, where it has recurring token cost.
- **Example:** "Split my skill so the short name and summary are always available, the main steps load when I trigger it, and the bulky extra notes only open if they're actually needed."
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### Bundled resources / supporting files
- **Definition:** Optional files in the skill directory (reference docs, templates, datasets) Claude reads only when referenced from SKILL.md. They cost zero context tokens until accessed.
- **When to use:** To keep SKILL.md lean while still shipping comprehensive API docs, large examples, or schemas.
- **Pitfalls:** Not referencing them from SKILL.md (Claude won't know they exist); nesting references more than one level deep.
- **Example:** "Put my long list of mixing tips in a separate notes file next to my skill, and point to it from the main file so you read it only when you need those details."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Executable scripts
- **Definition:** Utility scripts (e.g. `fill_form.py`) Claude runs via bash; the script's *code* never enters context — only its output costs tokens.
- **When to use:** For deterministic, fragile, or repeated operations where reliability matters more than flexibility.
- **Pitfalls:** Scripts that punt errors back to Claude; magic numbers; ambiguity about whether to *run* vs. *read* the script.
- **Example:** "My sample tagger has a little ready-made script that does the renaming. When my skill needs it, just run that exact script as-is and don't change how it's called."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Degrees of freedom
- **Definition:** Matching instruction specificity to a task's fragility. High freedom = text heuristics (many valid approaches); medium = pseudocode/parameterized scripts; low = exact scripts/commands (fragile, consistency-critical). Analogy: "narrow bridge with cliffs" → low freedom; "open field" → high.
- **When to use:** Decide per task.
- **Pitfalls:** Over-constraining open-ended tasks (code review) or under-constraining fragile ones (DB migrations).
- **Example:** "For the risky file-renaming step, give me strict, exact instructions to follow. For the loose 'give me feedback' step, you can use your own judgment."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Conciseness ("context window is a public good")
- **Definition:** SKILL.md should only add context Claude doesn't already have, since every loaded token competes with conversation history and other skills.
- **When to use:** Always; challenge each line — "Does Claude really need this? Does it justify its token cost?"
- **Pitfalls:** Explaining things Claude already knows (what a PDF is); narrating how/why instead of what to do.
- **Example:** "Keep my skill's instructions tight — don't explain things you already know, like what a music file is. Only include the stuff that's actually specific to my project."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Composability
- **Definition:** Multiple skills can be loaded at once and work together; a skill should function alongside others rather than assume it's the only capability available.
- **When to use:** Layering a domain skill (React best-practices) with a workflow skill (code review) on one task.
- **Pitfalls:** Overlapping or contradictory skills with no arbitration mechanism.
- **Example:** "Make sure my new feedback skill plays nicely with my other skills, so I can have it and my chord-helper skill both switched on at once without them stepping on each other."
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

### Model-invoked vs. user-invoked
- **Definition:** By default Claude can call a skill automatically *and* you can call it with `/skill-name`. Model-invoked skills keep their description always-loaded (constant context cost) so the agent reaches for them on its own; `disable-model-invocation: true` makes a skill user-only (zero context cost, but you must remember it exists).
- **When to use:** Model-invoked for background knowledge the agent should apply automatically; user-only for deliberate, side-effecting commands (`/deploy`, `/commit`).
- **Pitfalls:** Letting Claude auto-trigger destructive actions; over-using model-invoked skills bloats context.
- **Example:** "Set my 'wipe and reset the sample folder' skill so it only ever runs when I type its name — I don't want you deciding to run something that drastic on your own."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### `allowed-tools` / `disallowed-tools`
- **Definition:** `allowed-tools` pre-approves tools so the skill won't prompt for permission each use (a convenience, *not* a restriction). `disallowed-tools` removes tools from Claude's pool while the skill is active (clears next user message). Claude Code CLI only.
- **When to use:** `allowed-tools` for skills that repeatedly call known tools (git); `disallowed-tools` for autonomous skills that should never call something.
- **Pitfalls:** Expecting `allowed-tools` to *restrict* access; expecting `disallowed-tools` to persist beyond the turn.
- **Example:** "My skill saves my work a lot, so set it up to pre-approve those save commands — that way you don't stop and ask me for permission every single time."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### `context: fork` / `agent`
- **Definition:** `context: fork` runs the skill in an isolated subagent — the SKILL.md becomes that subagent's prompt, with no access to conversation history. `agent` picks which subagent type runs it (`Explore`/`Plan`/`general-purpose` or a custom one).
- **When to use:** Skills with explicit, self-contained tasks you want run in isolation (e.g. research).
- **Pitfalls:** Using `fork` on guideline-only skills (no actionable task) returns nothing useful; forked `Explore`/`Plan` skip CLAUDE.md and git status.
- **Example:** "Set up my research skill so it runs off on its own in a separate helper to go dig things up, instead of cluttering our main chat while it works."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Dynamic context injection (`` !`command` ``)
- **Definition:** The `` !`command` `` syntax runs a shell command as *preprocessing* before the skill is sent to Claude; its output replaces the placeholder. Claude only sees the rendered result, not the command.
- **When to use:** To ground a skill in live data (current diff, PR contents) instead of guesses.
- **Pitfalls:** Recognized only at line start or after whitespace; runs once; disabled by `disableSkillShellExecution`.
- **Example:** "Set up my skill so it automatically grabs the list of my latest changes and drops it in, so you're always working from what I just did instead of guessing."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Argument substitution (`$ARGUMENTS`, `$1`, `$name`)
- **Definition:** Placeholders replaced with arguments passed at invocation: `$ARGUMENTS` (full string), `$1`/`$ARGUMENTS[N]` (positional), `$name` (named via the `arguments` frontmatter list).
- **When to use:** Parameterized skills like `/fix-issue 123`.
- **Pitfalls:** If `$ARGUMENTS` is absent, input is appended as `ARGUMENTS: <value>`; multi-word positional args need quoting.
- **Example:** "Make my feedback skill take the song name as input, so I can type '/feedback MidnightDrive' and it knows which of my tracks to listen to."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Skill scope / location
- **Definition:** Where a skill lives sets who can use it and precedence: Enterprise (managed) > Personal (`~/.claude/skills/`) > Project (`.claude/skills/`). Plugin skills are namespaced `plugin:skill`.
- **When to use:** Personal for skills across all your projects; Project committed to a repo for the team; plugin/managed for distribution.
- **Pitfalls:** Custom skills do NOT sync across surfaces (claude.ai, API, Claude Code are separate); name clashes resolve by precedence.
- **Example:** "Save my feedback skill in my personal folder so it's available in every project I work on, not just this one music project."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Token budget (skill listing)
- **Definition:** The bounded space skills occupy. Per-skill metadata ≈ 100 tokens; SKILL.md body target <5k tokens / 500 lines; the listing budget scales ~1% of the model's context window. Auto-compaction re-attaches invoked skills within a combined budget, keeping the first ~5k tokens of each.
- **When to use:** When you have many skills, or long sessions where compaction may drop older ones.
- **Pitfalls:** Overflowing the listing budget drops least-used skills' descriptions (run `/doctor`).
- **Example:** "Keep my skill's main file short and move the long notes into a separate file, so the part that's always loaded stays small and doesn't crowd out my other skills."
- **Source:** [Claude Code — skills](https://code.claude.com/docs/en/skills)

### Security / trust
- **Definition:** Treat installing a skill like installing software — a malicious skill can direct Claude to misuse tools or exfiltrate data. Audit all bundled files; project-skill `allowed-tools` only takes effect after you accept workspace trust.
- **When to use:** Before installing or trusting any third-party skill.
- **Pitfalls:** Skills that fetch external URLs are especially risky (fetched content may carry injected instructions).
- **Example:** "I downloaded a skill from the internet. Before I turn it on, open every file in it and tell me in plain English what it does — especially if it runs anything or reaches out to the internet."
- **Source:** [Anthropic — Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

---

## 10. Skill design — the craft

*What separates a skill that works from one that doesn't. Mostly Pocock's "writing great skills" plus Anthropic's evaluation guidance.*

### Skills extract predictability from stochastic systems
- **Definition:** Pocock's stated purpose of skills: their "root virtue is consistent *process* across runs, not identical outputs." A skill tames an LLM's randomness by fixing *how* it works, not *what* it produces.
- **When to use:** As the design criterion for any skill — optimize for process consistency.
- **Pitfalls:** Chasing identical outputs (impossible/unhelpful) instead of a reliable process.
- **Example:** "I want my feedback skill to walk through the same checklist every single time I use it, even if the exact wording of your reply is a bit different each run."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Leading words
- **Definition:** Compact, pretrained concepts (e.g. *tight*, *red*, *tracer bullet*) that carry distributed meaning while anchoring behavior with minimal tokens. Refactor restatements down into a single leading word.
- **When to use:** When you want a prompt/skill to invoke rich behavior cheaply — lean on terms the model already understands deeply. *(This glossary is itself a catalog of leading words.)*
- **Pitfalls:** Weak leading words produce no-ops; coining unfamiliar words gives you no pretrained leverage.
- **Example:** "Instead of writing three sentences in my skill about giving short, punchy feedback, find one well-known word that already means that and use it to save space."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Branches / triggers (in descriptions)
- **Definition:** A model-invoked description must do two jobs: say what the skill does and list its distinct *branches* (triggers) — one trigger per branch, synonyms collapsed, leading word front-loaded for "invocation power."
- **When to use:** Writing a skill's description so the agent reliably auto-invokes it at the right moments.
- **Pitfalls:** Multiple triggers per branch (bloat), redundant synonyms, weak leading words.
- **Example:** "Write my feedback skill's description so it lists exactly when to kick in — like 'when I share a new track, ask for a critique, or mention a mix problem' — so it turns on at the right moments."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Information hierarchy (steps / reference / external)
- **Definition:** Skill content arranged by immediacy: (1) in-skill *steps* — ordered actions with checkable completion criteria; (2) in-skill *reference* — flat rules/facts consulted on demand; (3) *external reference* — pushed behind pointers.
- **When to use:** Structuring any skill or complex prompt so the most immediate guidance is on top and on-demand facts stay out of the way.
- **Pitfalls:** Mixing reference into the action path so the agent wades through facts mid-task.
- **Example:** "In my skill, put the do-this-then-that steps at the top, and shove the list of rules and facts into a section lower down that you only check when you need it."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Completion criteria (anti–premature-completion)
- **Definition:** Checkable, exhaustive criteria attached to steps that stop the agent declaring a task done early.
- **When to use:** Whenever an agent tends to stop short or skip post-completion work.
- **Pitfalls:** Fuzzy criteria invite premature completion; over-splitting to compensate.
- **Example:** "In my skill, spell out exactly when the job is finished — like 'the bug is gone AND the test passes AND all the temporary notes are removed' — so you don't stop early."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Granularity (split by invocation or by sequence)
- **Definition:** Only split a skill when it earns one of two "loads": *by invocation* (a distinct leading word that should trigger independently) or *by sequence* (steps that tempt premature completion, so splitting forces deeper legwork).
- **When to use:** When deciding whether one skill should become two.
- **Pitfalls:** Splitting for neither reason creates sprawl and navigation cost.
- **Example:** "The cleanup steps in my skill keep getting skipped because they come after the 'done' part. Pull them out into their own separate skill so they actually get done."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Skill failure modes (premature completion · duplication · sediment · sprawl · no-op)
- **Definition:** Five named ways skills go wrong: *premature completion* (stops early — fix criteria), *duplication* (same meaning in many places inflates prominence — keep single source of truth), *sediment* (stale layers accumulate without pruning), *sprawl* (over-long, hurts readability — disclose/branch instead), *no-op* (a line the agent already obeys by default — strengthen the leading word).
- **When to use:** As a review checklist when auditing or pruning a skill or prompt.
- **Pitfalls:** Leaving no-op lines (false confidence they help); trimming instead of deleting failed lines.
- **Example:** "Go through my skill line by line and delete any instruction you'd follow anyway without being told — those lines just add clutter and don't actually change anything."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Pruning / single source of truth
- **Definition:** Maintain one canonical location for each fact; test every line for relevance and *delete* failures rather than trimming. Duplication "inflates prominence" and misleads the agent.
- **When to use:** Continuously, when maintaining skills, prompts, or docs.
- **Pitfalls:** Letting sediment build; restating the same rule in multiple files.
- **Example:** "This same rule is written out in two of my skills. Keep it in just one place and have the other one point to it, so I only ever have to update it once."
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Router skill
- **Definition:** A single user-invoked skill that names/lists the others — the cure when user-invoked skills "multiply beyond memory."
- **When to use:** When you have many manual skills you can't remember; one entry point routes to them.
- **Pitfalls:** A router that itself grows stale or doesn't cover all skills.
- **Example:** "I have a bunch of skills and can't remember them all. Make me one 'menu' skill I can ask, like 'which of my skills should I use to fix a bug in my stock screener?'"
- **Source:** [mattpocock/skills — writing-great-skills](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-great-skills/SKILL.md)

### Evaluation-driven development
- **Definition:** Build *evaluations before* extensive docs: run Claude without the skill to find gaps, create ~3 test scenarios, measure a baseline, write minimal instructions, then iterate. Verify by running realistic prompts in fresh sessions with vs. without the skill.
- **When to use:** Before and throughout authoring, to confirm the skill solves real problems and triggers correctly.
- **Pitfalls:** Testing in the authoring session (leftover context masks gaps); assuming "it triggered" means "it worked."
- **Example:** "Before we polish my feedback skill, try the task a few times without it to see where you fall short, then write the smallest set of instructions that fixes those gaps."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Claude A / Claude B iteration
- **Definition:** Develop a skill with one Claude instance ("Claude A," author/refiner) and test it with *fresh* instances ("Claude B," the user) on real tasks, bringing observed gaps back to A.
- **When to use:** Iteratively refining skills based on real behavior rather than assumptions.
- **Pitfalls:** Iterating from assumptions instead of observed Claude B behavior.
- **Example:** "Let's test my feedback skill in a brand-new chat as if I'm a first-time user, see where it trips up, then come back here and fix those rough spots based on what actually went wrong."
- **Source:** [Anthropic — skill best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### Plan-validate-execute
- **Definition:** Have Claude first emit a structured plan (e.g. `changes.json`), validate it with a script, *then* execute — catching errors before applying changes.
- **When to use:** Batch operations, destructive changes, or high-stakes tasks.
- **Pitfalls:** Skipping the intermediate artifact and letting Claude apply unverified changes.
- **Example:** "Before you actually rename my sample files, first show me the full list of old-name-to-new-name changes you're about to make. Don't touch a single file until I've looked it over and said go."
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
