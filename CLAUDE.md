# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A satirical "AI Bullshit Generator" — a single static HTML page that combines random words from three lists into buzzword phrases. The README is intentionally absurd marketing fiction; ignore its "system requirements," Polymarket/crypto features, and Kubernetes install steps. None of that exists. The real app is `index.html` and nothing else.

## Running it

No build, no dependencies, no tests, no package manager. Just open the file:

```bash
open index.html   # or xdg-open on Linux / double-click
```

Everything (HTML, CSS in a `<style>` block, JS in a `<script>` block) lives in `index.html`. Edit it directly.

## Architecture

The entire app is in `index.html`:

- **`words`** — a 3-element array of word lists (Strategic Verbs, AI Adjectives, Emergent Nouns). `generate()` picks one random word from each list and joins them. To add vocabulary, append to these arrays.
- **`generate(showPanel)`** — produces a phrase, fades it into `#result`, updates the combination-count stat, and (unless `showPanel=false`) reveals the next-steps panel. Called once at load with `false` to seed a phrase without showing the panel.
- **Next-steps panel** (`#next-steps`) — hidden by default (`display:none`), revealed by `showNextSteps()`, which toggles `.visible` and forces a reflow (`void panel.offsetWidth`) to restart the `flash-in` animation on every generation.
- **`buildPrompt(type, phrase)`** — returns one of three crafted LLM prompts (`deck`, `pitch`, `viral`) with the live phrase interpolated. The prompt is built at click time from the current `#result` text, not at generation time.
- **`copyAndOpen(type, llm)`** — copies the prompt to the clipboard and opens the chosen LLM (`LLM_URLS`: claude/chatgpt/perplexity, defaults to claude) in a new tab. `openLink(url)` is the plain version for NotebookLM and YC, which have no prefilled-prompt support.

## Conventions

- Styling uses CSS custom properties defined in `:root` (`--bg`, `--primary`, etc.) — reuse these rather than hardcoding the orange/teal palette.
- LLM buttons get a per-provider class (`.claude`, `.chatgpt`, etc.) that colors the inline SVG icon.
- The tone is deliberately over-the-top startup/VC satire. Keep new copy and vocabulary in that voice.

## Plans

`docs/plans/` holds implementation plans for features (e.g. the next-steps panel). These are historical/reference — the panel described there is already built and the live `index.html` is the source of truth where it diverges from the plan.
