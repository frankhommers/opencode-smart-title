# Smart Title Plugin

Auto-generates meaningful session titles for your OpenCode conversations using AI.

This fork is maintained at `frankhommers/opencode-smart-title` and published as `@frankhommers/opencode-smart-title`.

## Why This Fork

- Custom title formatting with placeholders like `{cwdTip}` and `{cwdTip:git}`
- Custom prompt support for title style or language
- Directory exclusion for background or noisy automation sessions
- Safer config parsing and merging
- Better model-selection diagnostics when generation fails or slows down

## Quick Install

Install the package:

```bash
npm install @frankhommers/opencode-smart-title
```

Enable it in `~/.config/opencode/opencode.json`:

```json
{
  "plugin": ["@frankhommers/opencode-smart-title"]
}
```

## Quick Start

Configure it globally in `~/.config/opencode/smart-title.jsonc`:

```jsonc
{
  "enabled": true,
  "debug": false,
  "prompt": "Generate short Dutch titles for this conversation.",
  "updateThreshold": 1,
  "excludeDirectories": ["/Users/frankhommers/.heartbeat"],
  "titleFormat": "[{cwdTip}] {title}"
}
```

The plugin also supports project-level overrides in `.opencode/smart-title.jsonc`.

## How It Works

- Listens for OpenCode `session.status` events
- Waits until the session becomes `idle`
- Skips subagent sessions and excluded directories
- Builds a compact conversation summary
- Selects a configured model or falls back to authenticated providers
- Generates a title and applies your `titleFormat`

## Configuration Reference

Global config: `~/.config/opencode/smart-title.jsonc`

Project config: `.opencode/smart-title.jsonc`

Project config overrides global config.

```jsonc
{
  "enabled": true,
  "debug": false,
  // Optional: specific model in provider/model format
  // "model": "anthropic/claude-haiku-4-5",
  // Optional: custom prompt for title generation
  // "prompt": "Generate a short descriptive title for this conversation.",
  // Update title every N idle events
  "updateThreshold": 1,
  // Optional: skip title generation in these directories
  // "excludeDirectories": ["/home/user/.heartbeat"],
  // Format the final title
  "titleFormat": "{title}"
}
```

### Options

- `enabled`: turns the plugin on or off
- `debug`: writes detailed logs to the Smart Title log directory
- `model`: forces a specific `provider/model` choice before fallbacks are tried
- `prompt`: changes the generation instruction, useful for language or tone
- `updateThreshold`: controls how many idle events must happen before a title update runs
- `excludeDirectories`: skips sessions whose directory matches one of the configured prefixes
- `titleFormat`: formats the final title after AI generation

## Title Format Examples

Available placeholders:

- `{title}` - AI-generated title
- `{cwd}` - full working directory path
- `{cwdTip}` - last folder name
- `{cwdTip:N}` - last `N` path segments
- `{cwdTip:N:sep}` - last `N` path segments joined with a custom separator
- `{cwdTip:git}` - path segments from git root to the current working directory
- `{cwdTip:git:sep}` - same as above with a custom separator

| Format | Example Output |
|--------|----------------|
| `{title}` | `Debugging API errors` |
| `[{cwdTip}] {title}` | `[my-project] Debugging API errors` |
| `[{cwdTip:2}] {title}` | `[github/my-project] Debugging API errors` |
| `[{cwdTip:2: - }] {title}` | `[github - my-project] Debugging API errors` |
| `[{cwdTip:git}] {title}` | `[my-project] Debugging API errors` |
| `[{cwdTip:git: - }] {title}` | `[src - components] Debugging API errors` |
| `{title} ({cwdTip})` | `Debugging API errors (my-project)` |

## How `updateThreshold` Works

The plugin counts `idle` events per session.

- `1` - update on every idle pause
- `2` - update on every second idle pause
- `3` - update on every third idle pause

Example with `updateThreshold: 3`:

- idle #1 -> no update
- idle #2 -> no update
- idle #3 -> update
- idle #4 -> no update
- idle #5 -> no update
- idle #6 -> update

Use lower values for faster title updates and higher values to reduce churn or model usage.

## Local Development

For local testing, you can point OpenCode directly at the built file instead of the npm package:

```json
{
  "plugin": [
    "file:///absolute/path/to/opencode-smart-title/dist/index.js"
  ]
}
```

Useful commands:

```bash
npm install
npm run typecheck
npm run build
```

## Publishing

Publish a new version with:

```bash
npm version patch
npm publish --access public
```

You can also bump versions manually in `package.json` if you prefer.

## License

MIT
