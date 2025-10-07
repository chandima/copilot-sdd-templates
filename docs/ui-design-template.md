---
post_title: UI Design Template
author1: Chandima Cumaranatunge
post_slug: ui-design-template
microsoft_alias: chandima
featured_image: ../media/vibe_coding_continuum.png
categories: engineering
tags: ui, figma, mcp, prompts, automation
ai_note: "Initial draft assisted by AI; human reviewed."
summary: Documentation for the ui-design template: purpose, features, Figma MCP integration, reusable prompt usage, configuration, and troubleshooting.
post_date: 2025-10-06
---

## UI-Design template Overview

The **ui-design** template accelerates converting Figma designs into production‑ready, accessible UI code by pairing a **reusable Copilot prompt** with Model Context Protocol (MCP) access to Figma. It standardizes how you request generation of HTML/CSS (or React) plus exported image assets, ensuring consistent structure and design tokens.

Use it when you want a repeatable, parameterized workflow instead of copy‑pasting long freeform instructions each time you translate a design element or screen.

## Key Features

- **Reusable prompt** (`Generate Web UI from Figma`) with typed inputs (URL, target directories, framework, CSS strategy, breakpoints)
- **Design token extraction** for colors, typography scale, spacing
- **Semantic + accessible markup guidance** (landmarks, heading hierarchy, alt text TODOs)
- **Image export convention** (`image_dir` with @1x and @2x variants grouped by layer name)
- **Responsive scaffolding** driven by a configurable breakpoint list
- **Opinionated class/name hygiene** (derives utility or token-based names instead of arbitrary hashes)
- Extensible: add your own instructions or chatmodes later without modifying existing prompt usage

## Folder Structure (Template Portion)

```
templates/ui-design/
  .github/
    prompts/
      Generate Web UI from Figma.prompt.md
    instructions/   (empty placeholder – add shared rules if they become stable)
    chatmodes/      (empty placeholder – add iterative refinement modes if needed)
```

## Prerequisites

- Access to a running **Figma MCP server** in your Copilot environment (configured per GitHub Copilot + MCP docs)
- A valid Figma file or node URL you can share (ensure permissions allow API access)
- (Optional) Chosen front-end stack (vanilla HTML/CSS/JS or React) aligned with your repository

## Reusable Prompt Inputs

| Input | Required | Default | Allowed / Format | Purpose |
|-------|----------|---------|------------------|---------|
| `figma_url` | Yes | — | Full file or node URL | Source of design nodes/components |
| `target_dir` | No | `src/ui` | Path | Where generated code files go |
| `image_dir` | No | `public/images` | Path | Output path for exported bitmap assets |
| `framework` | No | `vanilla` | `vanilla`, `react` | Implementation style |
| `css_strategy` | No | `plain` | `plain`, `modules`, `tailwind` | How styles are organized |
| `breakpoints` | No | `375,768,1024,1440` | Comma list of integers | Responsive cut points |

## Using the Prompt

1. Open Copilot Chat.
2. Invoke the reusable prompt by its title (type part of the name): `Generate Web UI from Figma`.
3. Supply parameter values inline or via the UI field inputs.
4. Submit and review the generated file list + code blocks.
5. Ask follow-up chat refinements (e.g., "Convert CSS to modules" or "Extract color tokens to :root").

### Example Invocation (Vanilla)

```
Generate Web UI from Figma
figma_url=https://www.figma.com/design/EXAMPLE_FILE?node-id=108-84
framework=vanilla
css_strategy=plain
target_dir=src/landing
image_dir=public/images/landing
breakpoints=375,768,1024,1440
```

### Example Invocation (React + CSS Modules)

```
Generate Web UI from Figma
figma_url=https://www.figma.com/design/EXAMPLE_FILE?node-id=301-22
framework=react
css_strategy=modules
target_dir=src/components/hero
image_dir=public/images/hero
breakpoints=360,768,1200
```

### Expected Output Shape

- List of proposed files (e.g., `src/landing/index.html`, `src/landing/styles.css`, `public/images/landing/hero-banner@1x.png`)
- Code blocks for each file
- Notes section (unresolved alt text TODOs, vector assets needing manual export, approximation warnings)

## Generated Code Conventions

- **Accessibility**: Landmarks (`<header>`, `<main>`, `<nav>`, `<footer>`), logical heading order, alt text from node descriptions
- **Design Tokens**: Root CSS custom properties or theme object (React) for colors, font sizes, spacing scale
- **Responsive CSS**: Media queries at provided breakpoints in ascending order
- **Images**: Filenames derived from Figma layer names; retina variant suffixed with `@2x`
- **Class Naming**: Token/role-based (e.g., `hero`, `cta-button`, `card-grid`) not auto-generated hashes

## Customization Ideas

Add stable rules to `.github/instructions/` if they apply across *every* UI generation (e.g., brand color tokens, required aria patterns). Create a chatmode for iterative tasks (e.g., "Refine UI for dark mode") if you find yourself repeating the same refinement steps.

## Troubleshooting

| Issue | Cause | Resolution |
|-------|-------|-----------|
| Missing images | Node names not exportable or rasterization unsupported | Manually export from Figma, place in `image_dir`, update references |
| Unstyled output | Wrong `css_strategy` vs project setup | Switch strategy or integrate the produced CSS file into your build pipeline |
| Incorrect breakpoints | Typo or unsorted list | Provide a comma list of integers ascending (e.g., `360,768,1200`) |
| Alt text TODOs remain | Figma node lacked description | Supply real alt text manually after generation |
| Repeated unstable class names | Design lacks semantic layer naming | Rename layers in Figma to meaningful names and re-run |

## When to Create New Instructions or Chatmodes

Create **instructions** when a rule is universal (e.g., always use CSS variables for color tokens). Create **chatmodes** when a multi-step refinement is repeatedly requested (e.g., performance pass, a11y audit, dark mode adaptation).

## Related Docs

- SDD Overview: [sdd-overview](./sdd-overview.md)
- Main README: [README](../README.md)
- Model Context Protocol: https://docs.github.com/en/copilot/concepts/about-mcp

---
Return to the main [README](../README.md).
