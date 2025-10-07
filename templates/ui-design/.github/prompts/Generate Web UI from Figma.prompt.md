Title: Generate Web UI from Figma
Inputs:
  - figma_url (required)  // Full Figma file or node URL
  - target_dir (default: src/ui)
  - image_dir (default: public/images)
  - framework (choices: vanilla, react; default: vanilla)
  - css_strategy (choices: plain, modules, tailwind; default: plain)
  - breakpoints (default: 375,768,1024,1440)
Prompt:
Using the Figma MCP server:
1. If any required or optional inputs are omitted:
   - Always require `figma_url`; ask: "Please provide the Figma file or node URL (figma_url=...)".
   - For missing optional inputs, ask concise follow‑ups one at a time offering defaults, e.g.,
     - target_dir? (default: src/ui)
     - image_dir? (default: public/images)
     - framework? (vanilla | react; default: vanilla)
     - css_strategy? (plain | modules | tailwind; default: plain)
     - breakpoints? (comma list, default: 375,768,1024,1440)
   - After collecting values, echo a summary table and ask for a simple confirm: YES to proceed or list corrections.
2. Once confirmed, fetch the component(s) at {{figma_url}}.
3. Extract:
   - Semantic structure (landmarks, headings hierarchy)
   - Text content
   - Colors, typography tokens, spacing scale
   - Images (export @1x and @2x to {{image_dir}})
4. Generate {{framework}} implementation in {{target_dir}}:
   - HTML (semantic, accessible)
   - CSS ({{css_strategy}}) with variables for colors, font sizes, spacing
   - Responsive rules for breakpoints: {{breakpoints}}
5. Provide:
   - File list
   - Code blocks per file
   - Notes: any approximations or missing vector assets
Constraints:
- No arbitrary class names; derive utility or token-based names.
- Use alt text from Figma node descriptions; if missing, leave TODO.
- Group exported images by original Figma layer name.
- If the user requests changes post-generation, apply diffs incrementally without regenerating unchanged files.
Return only code + concise notes unless clarifying missing inputs.
