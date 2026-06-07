# IDA LLM Export Retrieval Guide

Start broad, then retrieve narrowly. Do not load all files under `functions/`, `pseudocode/`, or `disasm/` by default.

## First Files

1. `metadata.json`
2. `manifest.json`
3. `logs/validation.json`
4. `index/imports.jsonl`
5. `index/strings.jsonl`
6. `index/functions.jsonl`
7. `index/function_tags.jsonl`
8. `index/function_scores.jsonl`
9. `graph/callgraph_edges.jsonl`

## Workflow

- Use tags, scores, imports, strings, and graph position to pick candidate functions.
- Open `functions/<ea>.json` first for context and evidence links.
- Open matching `pseudocode/<ea>.c` and `disasm/<ea>.asm` only when needed.
- Record proposed function, global, and local-variable renames under `state/analysis_state.json`.
- Every rename proposal should include confidence and evidence from strings, imports, xrefs, pseudocode, or disassembly.
