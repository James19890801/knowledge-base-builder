---
name: knowledge-base-builder
description: A complete methodology for building personal knowledge bases. Triggers when users say "build my knowledge base", "organize my knowledge assets", "scan my files", or "create a knowledge base". Core workflow: Full disk scan → Three-layer architecture (raw/wiki/schema) → Continuous archiving. Based on Karpathy's methodology, implementing "build once, accumulate continuously" knowledge management system.
---

# Knowledge Base Builder Skill

## Core Philosophy

> **"Knowledge is not stored to be read, it is consumed during production and transformed into output"**

Based on Andrej Karpathy's LLM Wiki methodology, helping users build a **continuously accumulating** personal knowledge base.

Difference from one-time organization:
| Traditional Knowledge Management | This Skill's Approach |
|--------------------------------|----------------------|
| One-time organization | Continuous ingestion |
| Manual categorization | LLM auto-compilation |
| Stored for reading | Called during production |
| No cumulative effect | Every exploration compounds |

---

## Execution Flow

### Phase 1: Full Disk Asset Scanning

**Scan Scope**: C drive, D drive, E drive... All disks

**Scan Targets**:
- Documents: *.md / *.docx / *.txt / *.pdf
- Code: *.py / *.js / *.html
- Configuration: SKILL.md / CLAUDE.md
- Others: *.drawio / *.xlsx / *.pptx

**Scan Command** (Windows PowerShell):
```powershell
# Scan all disks for Markdown files
Get-PSDrive -PSProvider FileSystem | ForEach-Object {
    Get-ChildItem -Path "$($_.Root)" -Recurse -Include "*.md" -ErrorAction SilentlyContinue
}

# Scan all disks for Word documents
Get-PSDrive -PSProvider FileSystem | ForEach-Object {
    Get-ChildItem -Path "$($_.Root)" -Recurse -Include "*.docx" -ErrorAction SilentlyContinue
}

# Scan for SKILL.md files
Get-PSDrive -PSProvider FileSystem | ForEach-Object {
    Get-ChildItem -Path "$($_.Root)" -Recurse -Include "SKILL.md" -ErrorAction SilentlyContinue
}
```

**Scan Output**: Generate asset inventory report

```
📊 Asset Inventory Report
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Documents: XXX files (.md: XX, .docx: XX)
Skills: XX files
Code: XX files
Flowcharts: XX files
PPTs: XX files
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Total: XXX files
```

---

### Phase 2: Establish Three-Layer Architecture

**Principle**: Preserve user's existing structure, only create new index layer

```
Project Root/
├── wiki/                    # LLM-maintained knowledge compilation layer
│   ├── index.md             # Master index
│   ├── log.md               # Change log
│   ├── concepts/            # Concept pages
│   ├── entities/            # Entity pages
│   ├── topics/              # Topic pages
│   └── insights/            # Insight pages
│
├── raw/                     # Read-only raw material layer
│   ├── meetings/            # Meeting notes
│   ├── articles-external/   # External articles
│   └── notes/               # Temporary notes
│
├── [User's existing directories remain unchanged]
│   ├── courses/
│   ├── skills/
│   ├── articles/
│   └── ...
│
└── CLAUDE.md                # Rule constraint file
```

**Key Principles**:
- ✅ Existing directories remain unchanged
- ✅ wiki/ only stores indexes and extractions
- ✅ Original files linked via relative paths
- ✅ Version consistency guaranteed

---

### Phase 3: Batch Ingestion

**Ingestion Flow**:
```
1. Read source files
2. Extract core content
   - Concepts → concepts/
   - Entities → entities/
   - Topics → topics/
   - Insights → insights/
3. Update wiki/index.md
4. Append to wiki/log.md
```

**Page Format Specification**:
```markdown
---
title: Page Title
type: concept | entity | topic | insight
sources:
  - relative/path/to/source.md
related:
  - [[Related Page 1]]
  - [[Related Page 2]]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Title

## Definition
...

## Core Content
...

## Source
...
```

**Robustness Rules**:
- If file cannot be read: Skip and log error, continue with other files
- If extraction fails: Create placeholder page, mark for manual review
- If index update fails: Retry once, then continue without index update
- If log append fails: Continue silently, do not block ingestion

---

### Phase 4: Continuous Archiving Mechanism ⭐ MOST IMPORTANT

**Knowledge base is not built once, it accumulates continuously.**

**Trigger Timing**:
| Scenario | Trigger Condition | AI Behavior |
|----------|-------------------|-------------|
| **Content creation complete** | User finishes an article, PPT, or proposal | Proactively ask: "Archive to knowledge base?" |
| **Consulting complete** | Finished a consulting project | Prompt to archive key deliverables |
| **Skill development complete** | Created or updated a Skill | Auto-update wiki |
| **Meeting complete** | Generated meeting notes | Prompt to archive |

**AI Behavior Specification**:

```
1. Evaluate output value
   - Does it contain new insights?
   - Does it contain new cases?
   - Is it reusable?

2. If valuable, proactively ask:
   "This [XXX] contains valuable content. Archive to knowledge base? (yes/no)"

3. After user confirms, execute ingestion:
   - Extract concepts/entities/insights
   - Update wiki index
   - Append to log

4. Report result:
   "Archived to wiki/concepts/XXX.md, linked with [[YYY]]"
```

**Memory Integration**:
- Archive behavior written to AI memory system
- AI will automatically prompt archiving in future sessions
- User does not need to remember to archive - AI reminds them

---

## User Interaction Protocol

### First-time Build

```
AI: I will help you build a knowledge base. Here's the process:
    1. Full disk scan of your file assets
    2. Establish three-layer architecture (raw/wiki/schema)
    3. Batch ingest existing content
    4. Setup continuous archiving mechanism

    Please confirm:
    - Where should the knowledge base be created? (default: current project root)
    - Scan all disks? (default: yes)
    - Confirm to proceed?
```

### Daily Usage

```
User: [Completes an article]

AI: Article generated.
    
    💡 This article contains:
    - Core insight: XXX
    - Enterprise case: XXX
    - Methodology: XXX
    
    Archive to knowledge base? (yes/no)
    
    [If user says "yes"]
    → Execute ingestion flow
    → Report: "Archived, created 3 new wiki pages"
```

---

## Schema File Template (CLAUDE.md)

```markdown
# [User Name] Knowledge Base Schema

> This file defines LLM behavior constraints, automatically inherited each session.

## Identity
I am [User Name], [Professional Role].

## Project Structure
```
Knowledge Base/
├── wiki/                    # LLM-maintained
├── raw/                     # Read-only
└── CLAUDE.md                # This file
```

## Core Principles
1. Raw materials are immutable - LLM only reads, never writes
2. LLM is the knowledge architect - wiki/ is entirely LLM-maintained
3. Knowledge compounds - every ingestion and query adds value

## Three Operations

### Ingest
When user says "archive [content]" or provides new material:
1. Store in raw/ if it's new external material
2. Extract concepts/entities/topics/insights
3. Create/update wiki pages
4. Update wiki/index.md
5. Append entry to wiki/log.md
6. Report results to user

### Query
When user asks a question:
1. Read wiki/index.md to find relevant pages
2. Read relevant wiki pages
3. Synthesize answer with [[wiki-link]] citations
4. If answer is valuable, suggest archiving as new insight page

### Lint (Health Check)
When user says "check knowledge base":
1. Find contradictions between pages
2. Find orphan pages (no inbound links)
3. Identify mentioned concepts without pages
4. Suggest next research directions
5. Report findings to user

## Source File Mapping
[List of user's existing directories and their purposes]

## Naming Conventions
- Concept pages: `concepts/concept-name.md`
- Entity pages: `entities/entity-name.md`
- Topic pages: `topics/topic-name.md`
- Insight pages: `insights/insight-summary.md`

## Version History
- V1.0 YYYY-MM-DD Initial creation
```

---

## Deliverables Checklist

| Deliverable | Description |
|-------------|-------------|
| `wiki/` directory | Knowledge compilation layer |
| `raw/` directory | Raw material layer |
| `CLAUDE.md` | Rule constraint file |
| `wiki/index.md` | Master index |
| `wiki/log.md` | Change log |
| **Memory update** | Archive behavior written to AI memory |

---

## Robustness Guarantee

### Error Handling
| Error Scenario | Handling Strategy |
|----------------|-------------------|
| File read failure | Skip file, log error, continue |
| Directory creation failure | Use fallback location, notify user |
| Index update failure | Retry once, continue without update |
| Duplicate content detected | Skip ingestion, update existing page |
| Invalid file format | Attempt parsing, create placeholder if failed |

### Idempotency
- Running ingestion twice on same content produces same result
- No duplicate pages created
- Links properly maintained

### Graceful Degradation
- If full scan fails, fall back to partial scan
- If wiki update fails, raw files still preserved
- If memory write fails, file-based log still works

---

## Important Notes

1. **Don't break existing structure** — Only build indexes, don't move files
2. **Continuous beats perfect** — Start first, optimize later
3. **AI proactively asks** — User doesn't need to remember archiving, AI reminds them
4. **Connection beats storage** — Wiki's core is [[links]]

---

## References

- Andrej Karpathy's LLM Wiki methodology
- Angel Capaci's practical experience sharing
- Karpathy's GitHub Gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f