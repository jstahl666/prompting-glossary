# Prompting & Skills

A personal reference for writing better prompts and skills for AI coding/assistant
agents — what the words mean, when to reach for each move, and where they come from.

Built by surveying the most popular public skill repositories (Matt Pocock's skills,
Anthropic's official skills + docs, Superpowers, Spec Kit, the Karpathy CLAUDE.md, the
`awesome-*` lists) and the prompt-engineering canon (Anthropic, OpenAI, Google, DAIR).

## Files

- **🔎 [Searchable web app](https://jstahl666.github.io/prompting-glossary/)** — search all 122
  terms by keyword *or* by what you're trying to do, from any browser. No install, no login.
- **[cheatsheet.md](./cheatsheet.md)** — one-liner per term, grouped by purpose. Start here
  when you just need to jog your memory or scan for the right word.
- **[glossary.md](./glossary.md)** — the full reference. Each of the 122 terms has a
  definition, when-to-use, pitfalls, a concrete example you could type, and a citation.

## How to use it

Open the [searchable web app](https://jstahl666.github.io/prompting-glossary/) and type what
you're trying to do. Or, when you hit a term in someone's skill you don't recognize, scan the
cheat-sheet, then open the glossary entry for depth.

**Fastest wins:** be specific · give examples (few-shot) · separate instructions from
pasted data (delimiters) · ask for step-by-step reasoning (chain-of-thought) · set
standing rules once (system prompt) · align *before* building (grilling).

## Keeping it current

This is meant to grow. When you come across a new term or pattern worth keeping, add an
entry to `glossary.md` (full format) and a one-liner to `cheatsheet.md`.

**After editing `glossary.md`, regenerate the search data so the web app stays in sync:**

```
python build_search_data.py
```

That rewrites `glossary-data.js` (which the search page reads). Commit both files. The prompting
ecosystem moves fast, so the sources at the bottom of the glossary are worth re-checking periodically.
