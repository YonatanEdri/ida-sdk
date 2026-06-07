# AI Analysis Prompt

Analyze this IDA LLM export bundle as a reverse engineer. The input database was expected to be an already-analyzed `.i64`/`.idb`; do not assume raw binary analysis is needed.

## Operating Rules

- Start from compact indexes. Do not load all files under `functions/`, `pseudocode/`, or `disasm/`.
- Keep conclusions evidence-backed. Cite addresses, strings, imports, xrefs, pseudocode, or disassembly.
- Prefer behavioral names over family guesses or vague labels.
- Track continuity in `state/analysis_state.json` and append rename decisions to `state/rename_events.jsonl` when useful.

## First Read

1. `metadata.json`
2. `manifest.json`
3. `logs/validation.json`
4. `retrieval_guide.md`
5. `index/segments.jsonl`
6. `index/imports.jsonl`
7. `index/strings.jsonl`
8. `index/functions.jsonl`
9. `index/function_tags.jsonl`
10. `index/function_scores.jsonl`
11. `graph/callgraph_edges.jsonl`

## Analysis Workflow

Use imports, strings, tags, scores, xrefs, and graph position to select candidate functions. For each candidate, open `functions/<ea>.json` first, then open matching `pseudocode/<ea>.c` and `disasm/<ea>.asm` only if needed. Follow callers/callees through `graph/callgraph_edges.jsonl` and bounded xrefs in `graph/xrefs_code.jsonl` or `graph/xrefs_data.jsonl`.

## Rename Workflow

For every proposed rename, record target type, address, old name, proposed name, confidence, status, and evidence. Function names should describe behavior, for example `decode_config_xor`, `resolve_imports_by_hash`, or `http_beacon_send_system_info`. Local-variable names should be tied to their `function_ea` and evidence from pseudocode/disassembly.
