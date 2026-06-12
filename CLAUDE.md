# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the app

Open `index.html` directly in a browser — no build step, no server, no dependencies. The file is fully self-contained (HTML + CSS + JS in one file).

## Architecture

Single-file vanilla HTML/CSS/JS application. There are no frameworks, no npm packages, and no external assets. All styles are in a `<style>` block and all logic is in a `<script>` block at the bottom of `index.html`.

**Purpose:** A Spanish-language "Context Prompt Builder" for Trade Marketing, used in a Diplomado (professional diploma course). Users fill out structured sections and the app assembles a formatted prompt to paste into an AI assistant.

## UI component patterns

The app defines a small set of reusable interaction patterns implemented with class toggling:

| Pattern | Toggle function | CSS class | Used for |
|---|---|---|---|
| Accordion sections | `toggleSection(header)` | `.open` on `.section-card` | Expanding/collapsing the 10 sections |
| Role cards (multi-select) | `toggleRole(card)` | `.selected` | Consultant specialization picker |
| Pills (multi-select) | `togglePill(pill)` | `.selected` | Market type picker |
| Checkbox items | `toggleCheck(label)` | `.checked` | Channel selection |
| Checklist items | `toggleChecklist(item)` | `.checked` | Steps, Output Format, Avoid sections |
| Dynamic lists | `addDynamic()` / `removeDynamic()` | — | Competitors, SKUs, priorities, objectives |

Bulk select helpers `selectAll(listId)` and `selectNone(listId)` operate on any `<div class="checklist">` by its `id`.

## Prompt generation

`generatePrompt()` reads all form state and assembles a structured text prompt with these labeled sections in order:

`[ROLE]` → `[OBJECTIVE]` → `[CONTEXT]` → `[DATA]` → `[CONSTRAINTS]` → `[PRIORITIES]` → `[STEPS]` → `[OUTPUT FORMAT]` → `[AVOID]` → `[ARCHIVOS ADJUNTOS]` (only if files attached)

Helper functions used throughout generation:
- `v(id)` — gets trimmed value of any input/textarea by id
- `fv(val, fallback)` — returns value or `'[Sin especificar]'`
- `getListValues(listId)` — collects non-empty inputs from a dynamic list
- `getChecklistValues(listId)` — collects text of `.checked` checklist items
- `listItems(arr)` / `numberedList(arr)` — format arrays as bullet or numbered lists

## File attachment handling

Files are stored in the module-level `attachedFiles` array (objects with `name`, `size`, `type`, `description`). They are never read — only their metadata (name, size, description) is included in the generated prompt. Maximum 10 files; accepted types: PDF, XLSX, XLS, CSV.

## Design system

CSS custom properties on `:root` define the full color palette and spacing. Key tokens: `--primary` (navy `#1e3a5f`), `--accent` (orange `#e8562a`), `--radius`, `--shadow`. Section icons use per-section background color utility classes (`.s-role`, `.s-obj`, `.s-ctx`, etc.).

The Steps and Output Format checklists hide their `.item-num` elements via:
```css
#steps-checklist .item-num,
#output-checklist .item-num { display: none; }
```
This is intentional — those sections use plain text labels, not numbered steps.
