# AI Agent Commands & Tools

Collection of  commands and utilities for AI agents including:

* memory management
* semantic searchs

## Overview

This repository provides a comprehensive toolkit for enhancing AI agent workflows (Claude Code, Copilot, etc.) with persistent memory, semantic search, session management, and other things(wip).

## Core Systems

### Memory Index System

Intelligent knowledge management with semantic search capabilities.

**Commands:**
- **`/save`** - Capture session learnings with automatic analysis and cross-referencing
- **`/recall`** - Semantic search across all saved memories using natural language

**Key Features:**
- Automatic session analysis and insight extraction
- Neural embeddings for semantic similarity search
- Cross-project knowledge discovery
- 91% faster than filesystem scanning (~28ms vs 330ms)
- Scales efficiently to 100+ memory files

### Other Commands

WIP - Stay tuned for more tools to enhance your AI agent workflows

## Quick Start

### Installation

1. Clone this repository:
```bash
git clone https://github.com/adelholtz/ai-memory.git ~/.agents/commands
cd ~/.agents/commands
```

2. Install memory-index dependencies:
```bash
cd memory-index
npm install
```

3. (Optional) Generate embeddings for semantic search:
```bash
./build-index.js --embed
```

The index will be generate at the first run of `/save` if not already created.
After this, the system will automatically update the index with each new saved session.

4. Add symlinks for command accessibility:

```bash
ln -s ~/.claude/commands/recall.md ~/.agents/commands/recall.md
ln -s ~/.claude/commands/save.md ~/.agents/commands/save.md
```

### Usage Examples

#### Memory System

```bash
# Save session insights
/save                                # Auto-generated filename
/save my-findings                    # Custom filename
/save debug-notes --tags k8s,debug   # With custom tags

# Search past sessions
/recall kubernetes network debugging
/recall "how did I fix the auth bug?"
/recall docker container memory leak
```

## Architecture

### Memory Index Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    AI Agent Session                         │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
        ┌────────────────┐
        │   /save cmd    │ ──────► Analyze session
        └────────┬───────┘         Extract insights
                 │                 Generate tags
                 ▼
        ┌────────────────┐
        │  Generate      │ ──────► MiniLM-L6-v2 model
        │  Embedding     │         384-dim vector
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │  Update Index  │ ──────► ~/.agents/brain/memory-index.json
        └────────┬───────┘         Tags, keywords, embedding
                 │
                 ▼
        ┌────────────────┐
        │   Save File    │ ──────► ~/.agents/brain/<project>/memory-*.md
        └────────────────┘         Structured markdown

┌─────────────────────────────────────────────────────────────┐
│                  Search for Past Work                       │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
        ┌────────────────┐
        │  /recall cmd   │ ──────► Natural language query
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │  Cosine        │ ──────► Rank by similarity
        │  Similarity    │         Return top 5 matches
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │  Format &      │ ──────► Display results
        │  Display       │         Offer to read files
        └────────────────┘
```

## Performance Highlights

| Operation | Time | Notes |
|-----------|------|-------|
| Session discovery (indexed) | ~28ms | 91% faster than filesystem scan |
| Session discovery (unindexed) | ~330ms | Fallback mode |
| Large codebase (100+ files) | ~30ms | Scales efficiently |
| Semantic search | ~200-300ms | Includes model load + search |
| Embedding generation | ~170ms | Per session description |

## Repository Structure

```
.
├── README.md                    # This file
├── LICENSE
├── recall.md                    # Symlink → memory-index/commands/recall.md
├── save.md                      # Symlink → memory-index/commands/save.md
│
├── memory-index/                # Memory system implementation
│   ├── README.md                # Full system documentation
│   ├── commands/                # Command specifications
│   │   ├── recall.md
│   │   └── save.md
│   ├── build-index.js           # Index builder
│   ├── update-index.js          # Incremental updates
│   ├── embed.js                 # Embedding utility
│   ├── search.js                # Search logic
│   └── extract-keywords.js      # Keyword extraction
```

## Documentation

- **Memory Index System**: [memory-index/README.md](./memory-index/README.md)
- **Command Specs**: [memory-index/commands/](./memory-index/commands/)

## Contributing

This is a personal project for AI agent workflow optimization. Feel free to fork and adapt for your own use.

## License

MIT
