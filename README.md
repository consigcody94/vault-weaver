# vault-weaver

A production-ready Model Context Protocol (MCP) server for Obsidian knowledge management integration.

## Features

- **5 powerful tools** for Obsidian vault management
- Full TypeScript strict mode implementation
- File-based operations on Obsidian vaults
- Frontmatter support with YAML parsing
- Graph visualization capabilities
- Production-ready error handling

## Tools

### create_note
Create new markdown notes with optional frontmatter metadata and folder organization.

### search_notes
Search through your vault by content, title, tags, or folder with configurable result limits.

### get_backlinks
Find all notes that link to a specific note, enabling bi-directional link discovery.

### create_graph
Generate a graph of note connections starting from a root note or across the entire vault.

### update_frontmatter
Update or add YAML frontmatter to existing notes with merge support.

## Installation

```bash
npm install
npm run build
```

## Configuration

Set your Obsidian vault path as an environment variable:

```bash
export OBSIDIAN_VAULT_PATH="/path/to/your/obsidian/vault"
```

## Usage with Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "vault-weaver": {
      "command": "node",
      "args": ["/path/to/vault-weaver/build/index.js"],
      "env": {
        "OBSIDIAN_VAULT_PATH": "/path/to/your/obsidian/vault"
      }
    }
  }
}
```

## Example Usage

```typescript
// Create a note
{
  "tool": "create_note",
  "arguments": {
    "title": "Meeting Notes 2024-01-15",
    "content": "# Team Sync\n\n## Attendees\n- Alice\n- Bob",
    "folder": "meetings",
    "frontmatter": {
      "tags": ["meeting", "team"],
      "date": "2024-01-15"
    }
  }
}

// Search notes
{
  "tool": "search_notes",
  "arguments": {
    "query": "project planning",
    "tag": "project",
    "limit": 5
  }
}

// Get backlinks
{
  "tool": "get_backlinks",
  "arguments": {
    "notePath": "projects/new-feature.md"
  }
}

// Create graph
{
  "tool": "create_graph",
  "arguments": {
    "rootNote": "index.md",
    "depth": 3
  }
}

// Update frontmatter
{
  "tool": "update_frontmatter",
  "arguments": {
    "notePath": "projects/feature-x.md",
    "frontmatter": {
      "status": "completed",
      "completed_date": "2024-01-15"
    },
    "merge": true
  }
}
```

## Features

- **Wikilink Support**: Parses `[[internal links]]` for backlink detection
- **Tag Extraction**: Automatically extracts `#tags` from content
- **Frontmatter Management**: Full YAML frontmatter support with gray-matter
- **Recursive Scanning**: Traverses entire vault structure
- **Graph Generation**: Creates connection graphs with configurable depth

## Requirements

- Node.js 18+
- Obsidian vault (local file system)

## License

MIT
