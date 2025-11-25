# Vault Weaver

A Model Context Protocol (MCP) server for Obsidian knowledge management. Create notes, search content, discover backlinks, and visualize your knowledge graph.

## Overview

Vault Weaver bridges your Obsidian vault with AI assistants, enabling natural language interactions with your personal knowledge base. Ask questions about your notes, create new content, and explore connections you might have missed.

### Why Use Vault Weaver?

**Without Vault Weaver:**
- Manually searching through hundreds of notes
- Missing connections between related topics
- Forgetting to add proper metadata and tags
- Difficulty maintaining consistent note structure

**With Vault Weaver:**
```
"Find all my notes about machine learning"
"Create a meeting note for today's standup with proper frontmatter"
"What notes link to my project roadmap?"
"Show me the knowledge graph around my 'productivity' note"
```

## Features

- **Note Creation** - Create markdown notes with YAML frontmatter and folder organization
- **Full-Text Search** - Search by content, title, tags, or folder
- **Backlink Discovery** - Find all notes that reference a specific note
- **Graph Visualization** - Generate connection graphs with configurable depth
- **Frontmatter Management** - Update metadata on existing notes

## Installation

```bash
# Clone the repository
git clone https://github.com/consigcody94/vault-weaver.git
cd vault-weaver

# Install dependencies
npm install

# Build the project
npm run build
```

## Configuration

### Environment Variable

Set your Obsidian vault path:

```bash
export OBSIDIAN_VAULT_PATH="/path/to/your/obsidian/vault"
```

### Claude Desktop Integration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "vault-weaver": {
      "command": "node",
      "args": ["/absolute/path/to/vault-weaver/build/index.js"],
      "env": {
        "OBSIDIAN_VAULT_PATH": "/path/to/your/obsidian/vault"
      }
    }
  }
}
```

**Config file locations:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

**Finding your vault path:**
- macOS: Usually `~/Documents/Obsidian/YourVaultName`
- Windows: Usually `C:\Users\YourName\Documents\Obsidian\YourVaultName`
- Linux: Usually `~/Documents/Obsidian/YourVaultName`

## Tools Reference

### create_note

Create a new markdown note in your vault.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | Note title (used as filename) |
| `content` | string | Yes | Note content in markdown |
| `folder` | string | No | Folder path relative to vault root |
| `frontmatter` | object | No | YAML frontmatter metadata |

**Example - Simple note:**

```json
{
  "title": "Quick Thought",
  "content": "# Quick Thought\n\nRemember to review the API documentation."
}
```

**Example - Note with frontmatter and folder:**

```json
{
  "title": "Meeting Notes 2024-01-15",
  "content": "# Team Sync\n\n## Attendees\n- Alice\n- Bob\n- Carol\n\n## Topics\n- Sprint review\n- Q1 planning\n\n## Action Items\n- [ ] Review PRs\n- [ ] Update documentation",
  "folder": "meetings/2024",
  "frontmatter": {
    "tags": ["meeting", "team", "sprint"],
    "date": "2024-01-15",
    "type": "meeting",
    "attendees": ["Alice", "Bob", "Carol"]
  }
}
```

**Generated file structure:**

```markdown
---
tags:
  - meeting
  - team
  - sprint
date: 2024-01-15
type: meeting
attendees:
  - Alice
  - Bob
  - Carol
---

# Team Sync

## Attendees
- Alice
- Bob
- Carol
...
```

### search_notes

Search notes by content, title, tags, or folder.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search query (searches content and title) |
| `tag` | string | No | Filter by specific tag |
| `folder` | string | No | Filter by folder path |
| `limit` | number | No | Max results (default: 10, max: 100) |

**Example - Basic search:**

```json
{
  "query": "machine learning"
}
```

**Example - Search with filters:**

```json
{
  "query": "project",
  "tag": "active",
  "folder": "projects",
  "limit": 20
}
```

**Response includes:**
- Matching note paths
- Relevant content snippets
- Frontmatter metadata
- Match relevance scores

### get_backlinks

Find all notes that link to a specific note using `[[wikilinks]]`.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `notePath` | string | Yes | Path to note relative to vault root |

**Example:**

```json
{
  "notePath": "projects/new-feature.md"
}
```

**Response includes:**
- List of linking notes
- Link context (surrounding text)
- Link types (direct link, alias, embed)

### create_graph

Generate a graph of note connections.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rootNote` | string | No | Starting note (omit for entire vault) |
| `depth` | number | No | Traversal depth (default: 2, max: 5) |

**Example - From specific note:**

```json
{
  "rootNote": "index.md",
  "depth": 3
}
```

**Example - Entire vault:**

```json
{
  "depth": 2
}
```

**Response includes:**
- Nodes (notes) with metadata
- Edges (links) between notes
- Connection counts per note
- Cluster identification

### update_frontmatter

Update or add YAML frontmatter to an existing note.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `notePath` | string | Yes | Path to note relative to vault root |
| `frontmatter` | object | Yes | Frontmatter fields to update |
| `merge` | boolean | No | Merge with existing (default: true) |

**Example - Merge with existing:**

```json
{
  "notePath": "projects/feature-x.md",
  "frontmatter": {
    "status": "completed",
    "completed_date": "2024-01-15",
    "tags": ["done"]
  },
  "merge": true
}
```

**Example - Replace entirely:**

```json
{
  "notePath": "projects/feature-x.md",
  "frontmatter": {
    "status": "archived",
    "archived_date": "2024-01-20"
  },
  "merge": false
}
```

## Workflow Examples

### Daily Note Workflow

1. **Create today's note:**
   ```
   create_note with title: "2024-01-15", folder: "daily", frontmatter: {date: "2024-01-15", type: "daily"}
   ```

2. **Link to relevant projects:**
   ```
   search_notes with query: "current sprint", tag: "active"
   ```

3. **Check what links to yesterday:**
   ```
   get_backlinks with notePath: "daily/2024-01-14.md"
   ```

### Research Organization

1. **Create research note:**
   ```
   create_note with title: "ML Paper Review", folder: "research", frontmatter: {tags: ["ml", "paper"], status: "reading"}
   ```

2. **Find related notes:**
   ```
   search_notes with query: "neural network", limit: 10
   ```

3. **Visualize connections:**
   ```
   create_graph with rootNote: "research/ML Paper Review.md", depth: 2
   ```

### Project Tracking

1. **Update project status:**
   ```
   update_frontmatter with notePath: "projects/api-redesign.md", frontmatter: {status: "in-progress", sprint: 5}
   ```

2. **Find all active projects:**
   ```
   search_notes with tag: "project", folder: "projects"
   ```

3. **Check project references:**
   ```
   get_backlinks with notePath: "projects/api-redesign.md"
   ```

## Obsidian Compatibility

### Supported Features

- **Wikilinks**: `[[note]]` and `[[note|alias]]` syntax
- **Tags**: Both `#tag` inline and YAML frontmatter tags
- **Frontmatter**: Full YAML frontmatter support via gray-matter
- **Folders**: Complete folder hierarchy support
- **Embeds**: `![[note]]` embed syntax recognition

### File Naming

- Notes are saved as `.md` files
- Title becomes filename (special characters sanitized)
- Folder paths are created automatically if they don't exist

## Requirements

- Node.js 18 or higher
- Obsidian vault (local file system access)
- Read/write permissions to vault directory

## Troubleshooting

### "OBSIDIAN_VAULT_PATH environment variable is required"

Set the environment variable in your shell or Claude Desktop config:
```bash
export OBSIDIAN_VAULT_PATH="/path/to/vault"
```

### Notes not appearing in Obsidian

1. Ensure Obsidian is monitoring the correct vault
2. Try clicking "Reload app without saving" in Obsidian
3. Check file permissions

### Search not finding expected results

1. Ensure notes have been indexed (newly created notes may take a moment)
2. Try broader search terms
3. Check if notes are in excluded folders

### Backlinks missing

1. Ensure links use proper `[[wikilink]]` syntax
2. Check for typos in note names
3. Verify the target note exists

## Security Notes

- Vault Weaver only accesses the specified vault directory
- No data is sent to external services
- All operations are local file system only
- Backup your vault before bulk operations

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Author

consigcody94
