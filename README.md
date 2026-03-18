# Smart Title Plugin

Auto-generates meaningful session titles for your OpenCode conversations using AI.

This fork is maintained at `frankhommers/opencode-smart-title` and published as `@frankhommers/opencode-smart-title`.

## What It Does

- Watches your conversation and generates short, descriptive titles
- Updates automatically when the session becomes idle (you stop typing)
- Uses OpenCode's unified auth - no API keys needed
- Works with any authenticated AI provider
- Custom title format with placeholders (`{title}`, `{cwd}`, `{cwdTip}`, `{cwdTip:git}`)
- Custom prompt support for title generation style/language
- Directory exclusion to skip title generation for specific paths
- Robust config validation and merging

## Installation

```bash
npm install @frankhommers/opencode-smart-title
```

Add to `~/.config/opencode/opencode.json`:

```json
{
  "plugin": ["@frankhommers/opencode-smart-title"]
}
```

## Configuration

The plugin supports both global and project-level configuration:

- **Global:** `~/.config/opencode/smart-title.jsonc` - Applies to all sessions
- **Project:** `.opencode/smart-title.jsonc` - Overrides global config

The plugin creates a default global config on first run.

```jsonc
{
  // Enable or disable the plugin
  "enabled": true,

  // Enable debug logging
  "debug": false,

  // Optional: Use a specific model (otherwise uses smart fallbacks)
  // "model": "anthropic/claude-haiku-4-5",

  // Optional: Custom prompt for title generation
  // "prompt": "Generate a short descriptive title for this conversation.",

  // Update title every N idle events (1 = every time you pause)
  "updateThreshold": 1,

  // Optional: Directories to exclude from title generation
  // "excludeDirectories": ["/home/user/.heartbeat"],

  // Title format with placeholders:
  // - {title} - AI-generated title based on conversation
  // - {cwd} - Full current working directory path
  // - {cwdTip} - Last folder name of cwd (e.g., "my-project")
  // - {cwdTip:N} - Last N folder segments (e.g., {cwdTip:2} -> "github/my-project")
  // - {cwdTip:N:sep} - Last N segments with custom separator
  // - {cwdTip:git} - Segments from git root to cwd (falls back to tip if no .git found)
  // - {cwdTip:git:sep} - Same with custom separator
  "titleFormat": "{title}"
}
```

### Example Formats

| Format | Example Output |
|--------|----------------|
| `{title}` | `Debugging API errors` |
| `[{cwdTip}] {title}` | `[my-project] Debugging API errors` |
| `[{cwdTip:2}] {title}` | `[github/my-project] Debugging API errors` |
| `[{cwdTip:2: - }] {title}` | `[github - my-project] Debugging API errors` |
| `[{cwdTip:git}] {title}` | `[my-project] Debugging API errors` (if .git is in my-project) |
| `[{cwdTip:git: - }] {title}` | `[src - components] Debugging API errors` (if .git is 2 levels up) |
| `{title} ({cwdTip})` | `Debugging API errors (my-project)` |

## License

MIT
