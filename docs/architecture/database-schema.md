# Database Schema

The BMad CLI Automation Tool uses file-based state management with these essential schemas:

## stories.json Schema

```typescript
interface StoriesJson {
  stories: Record<string, StoryMetadata>;
  executionOrder: string[];
  epicComplete: boolean;
  lastUpdated: string; // ISO date
}

interface StoryMetadata {
  filename: string;
  status: "Draft" | "Approved" | "InProgress" | "Review" | "Done";
  dependencies: string[];
  epicId: string;
}
```

## Story File YAML Frontmatter Schema

```yaml
---
status: Draft | Approved | InProgress | Review | Done
epic_num: string
story_num: string
story_title_short: string
dependencies: string[] # other story filenames
---
```

## Configuration Schema (.bmad-automator.json)

```typescript
interface Config {
  apiKey: string; // environment variable name
  storiesPath: string; // default: "docs/stories"
  retryLimit: number; // default: 3
  agentTimeout: number; // ms, default: 120000
}
```

## Log File Schema (simple text format)

Standard Winston text logs with timestamps, levels, and messages. No complex JSON structure needed for MVP.

**File Organization:**
```
project-root/
├── stories.json                    # Main state file
├── .bmad-automator.json           # Configuration
├── docs/stories/                  # Story markdown files
│   ├── 1.1.setup-auth.md
│   └── 1.2.user-profile.md
└── logs/                          # Simple log files
    └── automator.log
```
