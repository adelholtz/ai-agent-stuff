---
description: Save technical findings and learnings from the current session
argument-hint: [custom-filename]
---

# Save Session Memory

## Overview

Automatically analyze the current session, extract technical findings and learnings, and save them to `~/.agents/brain/<current-directory-name>/memory-YYYYMMDD-HHMMSS.md`.

This command captures technical discoveries, patterns, solutions, code changes, and insights for future reference.

## Usage

```bash
/save                           # Save with auto-generated timestamp filename
/save my-findings               # Save as my-findings.md (auto-append .md)
/save kubernetes-debug.md       # Save as kubernetes-debug.md
```

## Implementation Instructions

Follow these steps carefully to analyze the session and create a comprehensive memory file.

### Step 1: Gather Environment Context

Use bash commands to collect environment information:

```bash
# Get current working directory
pwd

# Get basename for folder name
basename $(pwd)

# Get git repository name (optional, for display only)
basename $(git rev-parse --show-toplevel 2>/dev/null) || echo "N/A"

# Generate timestamps
date +"%Y%m%d-%H%M%S"           # For filename: 20260205-143022
date +"%Y-%m-%d %H:%M:%S"       # For document: 2026-02-05 14:30:22
```

Store these values for use in later steps.

### Step 2: Determine Output Filename

Process the filename based on user input:

```
If $ARGUMENTS is empty or not provided:
  filename = "memory-" + YYYYMMDD-HHMMSS + ".md"
  Example: memory-20260205-143022.md
Else:
  filename = $ARGUMENTS
  If filename does NOT end with ".md":
    filename = filename + ".md"
  
  Examples:
  - "my-findings" → "my-findings.md"
  - "debug.md" → "debug.md"
```

### Step 3: Construct Target Path

Build the full path where the file will be saved:

```
basename = $(basename $(pwd))
target_dir = ~/.agents/brain/$basename
target_path = $target_dir/$filename
```

Example:
- Working dir: `/Users/name/projects/my-app`
- Basename: `my-app`
- Target dir: `~/.agents/brain/my-app`
- Target path: `~/.agents/brain/my-app/memory-20260205-143022.md`

### Step 4: Handle File Conflicts

If the target file already exists, append a numeric suffix:

```
original_filename = filename
base_name = filename without ".md" extension
counter = 2

While file exists at target_path:
  filename = base_name + "-" + counter + ".md"
  target_path = target_dir + "/" + filename
  counter = counter + 1

Example conflict resolution:
- memory-20260205-143022.md exists
- Try memory-20260205-143022-2.md
- If that exists, try memory-20260205-143022-3.md
- Continue until finding available filename
```

### Step 5: Create Directory Structure

Ensure the target directory exists:

```bash
mkdir -p ~/.agents/brain/$basename
```

This creates both `~/.agents/brain/` and the project subdirectory if needed.

### Step 6: Discover Related Sessions

Find and link to the 3 most recent memory files in the same directory:

```bash
# Find other memory-*.md files, sort by modification time (newest first)
# Exclude the file being created
ls -t ~/.agents/brain/$basename/memory-*.md 2>/dev/null | grep -v "$filename" | head -n 3
```

Generate markdown links for each found file:

```markdown
**Related Sessions**: 
- [memory-20260204-142344.md](./memory-20260204-142344.md)
- [memory-20260204-091533.md](./memory-20260204-091533.md)
- [memory-20260201-165422.md](./memory-20260201-165422.md)
```

If no related sessions exist:
```markdown
**Related Sessions**: None
```

### Step 7: Collect Metadata

Attempt to collect session metadata, using "N/A" for unavailable information:

- **OpenCode Version**: Try to detect from environment/system. If unavailable, use "N/A"
- **Model**: Use "claude-sonnet-4.5" (from system context)
- **Conversation Turns**: Try to count turns in conversation. If unavailable, use "N/A"

Example:
```markdown
**Session Metadata**:
- OpenCode Version: N/A
- Model: claude-sonnet-4.5
- Conversation Turns: N/A
```

### Step 8: Analyze Conversation History

Review the **entire conversation** from start to finish and extract:

#### Technical Findings
- **Key Discoveries**: Major technical findings about code, architecture, systems, performance
- **Patterns Identified**: Design patterns, code patterns, anti-patterns observed or implemented
- **Solutions & Approaches**: Problems encountered, solutions implemented, why specific approaches were chosen

#### Learnings & Insights
- **Technical Lessons**: Specific lessons about languages, frameworks, tools, behaviors
- **Best Practices**: Practices identified, reinforced, or discovered
- **Gotchas & Edge Cases**: Non-obvious behaviors, pitfalls, edge cases to watch for

#### Code Changes
- **Files Modified**: High-level summary of changes made to existing files
- **Files Created**: New files, modules, or components added and their purposes
- **Architecture Impact**: How changes affect overall system architecture, new dependencies

#### Tools & Technologies
- **Tools Used**: Development tools, CLI utilities, scripts created
- **Libraries & Frameworks**: Libraries explored, integrated, or configured
- **Configuration Changes**: Environment variables, config files, infrastructure changes

#### Outstanding Items
- **Action Items**: Tasks remaining to be completed
- **Future Considerations**: Ideas for improvement, features to add later
- **Open Questions**: Questions needing investigation, uncertainties to resolve

#### References
- **Documentation**: Official docs, blog posts, articles consulted
- **Related Files**: Key files in the codebase referenced
- **Commands Reference**: Useful commands discovered or scripts created

### Step 9: Generate Memory File

Using the **template below**, populate all sections with the extracted information.

**Important guidelines:**
- **All sections must be present** in the output
- **For empty sections**: Use "None in this session" or similar rather than leaving blank
- **Be concise**: Focus on information valuable for future reference
- **Be accurate**: Ensure file paths, code references, technical details are correct
- **Use proper markdown**: Correct heading levels, lists, code blocks, links
- **Session Context**: Write 2-4 sentences summarizing what was worked on

### Step 10: Save File and Confirm

Write the generated content to the target file and output a confirmation message:

```
✓ Session memory saved successfully!

Location: ~/.agents/brain/<basename>/<filename>
Sections populated: X/9
Main findings: 
  - [Most important finding 1]
  - [Most important finding 2]
  - [Most important finding 3]

Related sessions: X previous sessions linked
```

If fewer than 3 main findings, list what's available. If no significant findings, note that.

## Memory File Template

Use this exact template structure when generating the memory file:

```markdown
# Technical Memory - [Project Name/Basename]

**Date**: YYYY-MM-DD HH:MM:SS  
**Working Directory**: /full/path/to/directory  
**Repository**: [git repo name if applicable, or "N/A"]

**Session Metadata**:
- OpenCode Version: [version or "N/A"]
- Model: claude-sonnet-4.5
- Conversation Turns: [count or "N/A"]

**Related Sessions**: 
[List of up to 3 most recent memory-*.md files with links, or "None"]

---

## Session Context

[Brief 2-4 sentence overview of what was worked on during this session]

---

## Technical Findings

### Key Discoveries
[Major technical findings made during the session, or "None in this session"]

### Patterns Identified
[Design patterns observed or implemented, or "None in this session"]

### Solutions & Approaches
[Problems encountered and how they were solved, or "None in this session"]

---

## Learnings & Insights

### Technical Lessons
[Specific technical lessons learned, or "None in this session"]

### Best Practices
[Best practices identified or reinforced, or "None in this session"]

### Gotchas & Edge Cases
[Edge cases discovered, pitfalls to avoid, or "None in this session"]

---

## Code Changes Summary

### Files Modified
[High-level overview of changes, or "No files modified"]

### Files Created
[New files/modules added, or "No files created"]

### Architecture Impact
[How changes affect overall architecture, or "No architectural impact"]

---

## Tools & Technologies

### Tools Used
[Development tools utilized, or "No new tools used"]

### Libraries & Frameworks
[New libraries explored or integrated, or "No new libraries"]

### Configuration Changes
[Environment variables, config files updated, or "No configuration changes"]

---

## Outstanding Items

### Action Items
[Tasks that remain to be completed, or "None"]

### Future Considerations
[Ideas for improvement, features to add later, or "None"]

### Open Questions
[Questions needing investigation, or "None"]

---

## References

### Documentation
[Official docs, articles referenced, or "None"]

### Related Files
[Key files in the codebase, or "None"]

### Commands Reference
[Useful commands discovered, or "None"]

---

## Notes

[Additional observations or context that doesn't fit elsewhere, or "None"]
```

## Quality Checks

Before saving the file, verify:

- ✅ All timestamps are accurate and in correct format
- ✅ Paths are correct and absolute where specified
- ✅ Markdown formatting is valid (headers, lists, links)
- ✅ Technical content is accurate (file paths, code references)
- ✅ Cross-references point to actual existing files
- ✅ Metadata shows "N/A" where unavailable (not blank or error messages)
- ✅ Sections contain "None" or similar if truly empty (not generic boilerplate)
- ✅ File was successfully written to disk
- ✅ Filename conflict resolution worked if needed

## Edge Cases & Error Handling

| Scenario | Handling |
|----------|----------|
| No meaningful technical content | Create minimal file noting "Brief session with no significant technical findings" |
| Permission denied writing to `~/.agents/brain/` | Alert user with clear error message and path that failed |
| Not in git repository | Set Repository field to "N/A" |
| Very long session (>50 turns) | Prioritize most important findings, summarize repetitive work |
| Multiple working directories during session | Note all directories in Session Context |
| Unable to detect metadata | Use "N/A" for all unavailable metadata fields |
| Custom filename without extension | Auto-append `.md` extension |
| File conflict (many existing versions) | Keep incrementing suffix (-2, -3, -4, etc.) |
| Empty conversation | Create file noting minimal session activity |

## Example Outputs

### Example 1: Technical Session

```
✓ Session memory saved successfully!

Location: ~/.agents/brain/opencode-commands/memory-20260205-143522.md
Sections populated: 8/9
Main findings: 
  - Created new /save command for capturing session insights
  - Discovered OpenCode commands use markdown instruction files
  - Implemented file conflict resolution with numeric suffixes

Related sessions: 2 previous sessions linked
```

### Example 2: Brief Session

```
✓ Session memory saved successfully!

Location: ~/.agents/brain/my-app/memory-20260205-091033.md
Sections populated: 3/9
Main findings: 
  - Brief exploratory session reviewing codebase structure

Related sessions: None
```

### Example 3: Custom Filename

```
✓ Session memory saved successfully!

Location: ~/.agents/brain/infra/kubernetes-debugging.md
Sections populated: 7/9
Main findings: 
  - Identified pod restart loop caused by misconfigured liveness probe
  - Discovered memory leak in data processing container
  - Implemented resource limits to prevent OOM kills

Related sessions: 3 previous sessions linked
```

## Notes

- This command is designed to capture technical value from sessions for future reference
- The standardized template ensures consistency across all memory files
- Automatic analysis reduces manual effort while maintaining quality
- Cross-referencing enables discovering related work across sessions
- Files are organized by project (directory basename) for easy navigation
