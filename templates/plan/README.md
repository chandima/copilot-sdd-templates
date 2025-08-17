# Planning Template

This directory was created via the internal new-template Copier scaffold.

Add any files specific to this template here. During generation, the root template will:

- Inject shared assets from `common/` (`.github/instructions`, `.github/prompts`, `.github/chatmodes`, `.vscode/mcp`)
- Merge them with anything you define locally (local files take precedence)

Recommended next steps:

1. Add a `package.json`, infra config, or other starter files.
2. (Optional) Add or override chatmodes/prompts/instructions by placing files under the corresponding `.github/` subfolders here.
3. Test with:
   ```zsh
   copier copy --data template_name=plan . /tmp/test-plan
   ```
4. Commit this new template directory.
