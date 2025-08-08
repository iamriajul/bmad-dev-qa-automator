# Data Models

Based on the story template and PRD requirements, here are the core data models:

## Story

**Purpose:** Represents an individual development story with its lifecycle state, dependencies, and multi-agent collaboration

**Key Attributes:**
- filename: string - Story filename (e.g., "1.1.setup-auth.md")
- epicNum: string - Epic number (e.g., "1")
- storyNum: string - Story number within epic (e.g., "1")
- storyTitleShort: string - Short title for filename (e.g., "setup-auth")
- status: StoryStatus - Current state (Draft, Approved, InProgress, Review, Done)
- story: StoryDefinition - User story with role, action, benefit
- acceptanceCriteria: string[] - Numbered list from epic
- tasksSubtasks: TaskItem[] - Breakdown with checkboxes
- devNotes: DevNotes - Architecture context for dev agent
- changeLog: ChangeLogEntry[] - Document version history
- devAgentRecord: DevAgentRecord | null - Dev completion details
- qaResults: QAResults | null - QA review findings
- dependencies: string[] - Other story filenames this depends on
- ownerSections: Record<string, string> - Section ownership mapping
- editorSections: Record<string, string[]> - Section edit permissions

**Relationships:**
- Belongs to one Epic
- Has many ChangeLogEntries
- Has one DevAgentRecord (when developed)
- Has one QAResults (when reviewed)

## StoryDefinition

**Purpose:** Structured user story format with role, action, and benefit

**Key Attributes:**
- role: string - User role (e.g., "developer", "BMad team member")
- action: string - What they want to do
- benefit: string - Why they want to do it

**Relationships:**
- Belongs to one Story

## TaskItem

**Purpose:** Hierarchical task breakdown with completion tracking

**Key Attributes:**
- id: string - Task identifier
- description: string - Task description
- completed: boolean - Checkbox state
- acceptanceCriteriaRef: number | null - Links to AC number
- subtasks: TaskItem[] - Nested subtasks
- owner: string - Who's responsible (usually dev-agent)

**Relationships:**
- Belongs to one Story
- Can have many Subtasks (self-referential)

## DevNotes

**Purpose:** Extracted architecture context to prevent dev agent from reading full docs

**Key Attributes:**
- relevantSourceTree: string[] - File paths relevant to story
- previousStoryNotes: string[] - Context from related stories
- architectureContext: string - Key architectural decisions
- testingStandards: TestingStandards - Test requirements

**Relationships:**
- Belongs to one Story

## TestingStandards

**Purpose:** Story-specific testing requirements extracted from architecture

**Key Attributes:**
- testFileLocation: string - Where tests should be placed
- testStandards: string[] - Standards to follow
- frameworks: string[] - Testing frameworks to use
- specificRequirements: string[] - Story-specific test needs

**Relationships:**
- Belongs to DevNotes

## ChangeLogEntry

**Purpose:** Track all modifications to story documents

**Key Attributes:**
- date: Date - When changed
- version: string - Document version
- description: string - What changed
- author: string - Agent or human who made change

**Relationships:**
- Belongs to one Story

## DevAgentRecord

**Purpose:** Development agent's implementation record

**Key Attributes:**
- agentModelUsed: string - AI model name and version
- debugLogReferences: string[] - Log locations
- completionNotes: string[] - Implementation notes
- fileList: FileChange[] - All files touched
- timestamp: Date - When completed
- signature: string - Dev agent signature

**Relationships:**
- Belongs to one Story
- Has many FileChanges

## FileChange

**Purpose:** Track files modified during story implementation

**Key Attributes:**
- path: string - File path
- action: string - Created, Modified, Deleted
- description: string - What was changed

**Relationships:**
- Belongs to DevAgentRecord

## QAResults

**Purpose:** QA agent's review findings and approval status

**Key Attributes:**
- status: string - Pass, Fail, NeedsWork
- findings: Finding[] - Issues found
- testResults: TestResult[] - Test execution results
- coverage: number - Test coverage percentage
- timestamp: Date - When reviewed
- signature: string - QA agent signature

**Relationships:**
- Belongs to one Story
- Has many Findings
- Has many TestResults

## Finding

**Purpose:** QA-identified issues requiring resolution

**Key Attributes:**
- severity: string - Critical, Major, Minor
- category: string - Bug, TestMissing, StandardViolation
- description: string - What's wrong
- location: string | null - File:line reference
- suggestedFix: string - How to fix it

**Relationships:**
- Belongs to QAResults

## StoriesJson

**Purpose:** Persistent state maintaining story processing order and status

**Key Attributes:**
- stories: Record<string, StoryMetadata> - Map of filenames to metadata
- executionOrder: string[] - SM-determined processing order
- epicComplete: boolean - All epics done flag
- lastUpdated: Date - File modification time
- signatures: AgentSignature[] - SM signatures for updates

**Relationships:**
- References all Stories
- Has many AgentSignatures

## AgentSignature

**Purpose:** Cryptographic-style signature for agent actions

**Key Attributes:**
- agentId: string - SM, Dev, or QA
- action: string - What was done
- timestamp: Date - When it happened
- hash: string - Content hash for integrity

**Relationships:**
- Used by multiple entities (Story, StoriesJson, etc.)
