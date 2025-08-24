---
description: "Prompt template for diagnosing, correcting, and validating Mermaid diagrams using tooling-assisted validation and rendering."
mode: agent
applyTo: "**/*.md"
model: GPT-5 (Preview)
tools: [context7, mermaid]
---
Begin with a concise checklist (3-7 bullets) of the review and correction steps you will take; keep items conceptual, not implementation-level.
Assist in reviewing and fixing Mermaid diagrams following this sequence:

1. Specify the current diagram (raw Mermaid code block) and describe the observed problem(s), such as syntax errors, layout issues, missing elements, or rendering differences between platforms (e.g., GitHub vs. Mermaid Chart).
2. Clearly state the intended outcome—what the corrected diagram is meant to convey (e.g., flow, sequence, dependency, state, architecture).
3. Honor these constraints:
   - Retain node identifiers where possible to reduce downstream diff noise.
   - Use official Mermaid syntax (https://mermaid.js.org); avoid experimental or unofficial features unless necessary.
   - Ensure readability: prefer left-to-right layouts for clarity, concise node/edge labels, and consistent casing.
   - For diagrams exceeding ~25 nodes, recommend splitting into subgraphs or multiple diagrams for clarity.
4. Define validation criteria (for example: “renders successfully in GitHub Markdown and Mermaid Chart with no warnings,” “no label truncation,” “edges clearly defined”).

Tooling Details (
Context7 + Mermaid MCP):

- Look up official snippets and feature support using Context7’s `mermaidchart` library (https://context7.com/websites/mermaidchart).
- Use the `mermaid` MCP server for programmatic validation and rendering. Expected methods:
  - `mermaid.validate(code)`: returns { ok: bool, errors: [ { line, column, message } ] }
  - `mermaid.render(code, format=svg)`: returns { format, data(base64)|path }
  - (Optional) `mermaid.ast(code)`: retrieves a structural AST for detailed diffs if needed.

Before any significant tool call, state in one line the purpose of the call and the minimal inputs used.
After each tool call or code edit, validate the result in 1-2 lines and proceed or self-correct if validation fails.

Repair Cycles:
  1. Query the tools for specifics regarding the construct in question (e.g., “sequenceDiagram activation”, “classDiagram generics”, etc.) and retrieve authoritative example syntax.
  2. Validate the submitted code with `mermaid.validate`; record all messages (tabulate line + message).
  3. Compare to canonical syntax; identify and note syntax or content deltas (e.g., missing arrows, malformed edges, unsupported directives).
  4. Apply only required changes; preserve correct and stylistically valid sections.
  5. If a desired feature is unsupported, suggest a closest valid alternative (e.g., switch diagram type or represent content as a Note).
  6. Reference (briefly) the constructs used for any corrections performed.
  7. Re-run validation on the fixed code; proceed to `mermaid.render` to check that an SVG is generated, free of warnings.

When faced with multiple tool-provided variants, select the latest, stable syntax that works across major renderers (GitHub, VS Code Markdown). If unclear, opt for the simplest rendering-compatible form.

Example parse error scenario (from a sequence diagram):

```
Error: Parse error on line 18:
...olicy/trust registry  U-->>S: JSON { ac
-----------------------^
Expecting 'SOLID_OPEN_ARROW', ... got 'NEWLINE'
```

Root cause: Unquoted braces in message labels are interpreted as invalid token sequences. This breaks parsing in Mermaid sequence diagrams.

Failing minimal example:
```mermaid
sequenceDiagram
  participant U as University
  participant S as Student
  U-->>S: JSON { access_token (OAuth 2.1), mcp_endpoint }
```

Remedies:
- Quote the entire label containing literal braces:
```mermaid
sequenceDiagram
  participant U as University
  participant S as Student
  U-->>S: "JSON { access_token (OAuth 2.1), mcp_endpoint }"
```
- Or, simplify the label to avoid problematic characters:
```mermaid
sequenceDiagram
  participant U as University
  participant S as Student
  U-->>S: access token + mcp endpoint
```
- Or, use a Note for lengthy/complex payloads:
```mermaid
sequenceDiagram
  participant U as University
  participant S as Student
  Note over U,S: Returns JSON (access_token, mcp_endpoint)
  U-->>S: token + endpoint
```

Validation after corrections:
1. Run `mermaid.validate`—expect no errors.
2. Confirm SVG generation using `mermaid.render`—ensure rendering is successful and warning-free.
3. Visually verify output for correct labeling and no unexpected truncation or wrapping.
4. Optionally, trial alternative remedies for best clarity and compactness per use case.
5. Document results; check for pass/fail on parsing, rendering, and semantic accuracy.

Choose quoting for preserving exact characters, Notes for verbose or multiline content.

Response Object Structure:

- `summary` (string): Summary of detected issues.
- `code` (string; Mermaid markdown code block): The corrected diagram.
- `alternatives` (array, optional): Variants with `code` and `tradeoffs` description.
- `validation` (object):
    - `pre_fix_errors` (array): Objects with `line`, `column`, and `message` for each error before correction.
    - `post_fix_result` ('Pass' | 'Fail'): Result after correction.
    - `checklist` (array): Each with `criterion` and pass status.

If a diagram is not provided, request it once. Address syntax blockers before stylistic issues. Focus on actionable fixes—avoid commentary not related to the correction task.
