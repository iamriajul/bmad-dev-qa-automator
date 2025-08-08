# Core Workflows

These sequence diagrams illustrate the key system workflows for the BMad CLI Automation Tool:

## 1. System Initialization and Story Discovery

```mermaid
sequenceDiagram
    participant User
    participant CLI
    participant WE as Workflow Engine
    participant SM as Story Manager
    participant SP as State Processor
    participant AO as Agent Orchestrator
    participant Claude as Claude SDK

    User->>CLI: bmad-automator start
    CLI->>WE: initialize()
    WE->>SM: discoverStories()
    SM->>SM: scan docs/stories/*.md
    SM-->>WE: Story[] found
    
    WE->>SP: loadState()
    alt stories.json exists
        SP-->>WE: existing StoriesJson
        WE->>AO: sendToAgent(SM, "update stories.json")
        AO->>Claude: "Identify new stories and update"
        Claude-->>AO: updated stories.json
    else stories.json not found
        WE->>AO: sendToAgent(SM, "generate stories.json")
        AO->>Claude: "Scan stories and create dependency graph"
        Claude-->>AO: new stories.json with dependencies
    end
    
    AO->>SP: saveState(storiesJson)
    SP->>SP: atomic write with backup
    WE->>WE: startProcessingLoop()
```

## 2. Story Approval Workflow (Draft → Approved)

```mermaid
sequenceDiagram
    participant WE as Workflow Engine
    participant SP as State Processor
    participant AO as Agent Orchestrator
    participant QA as QA Agent (Claude)
    participant SM as SM Agent (Claude)
    participant FS as File System

    WE->>SP: getNextStory()
    SP-->>WE: Story (status: Draft)
    
    WE->>AO: sendToAgent(story, QA)
    Note over AO: Prompt includes TDD requirements
    AO->>QA: "Review story for TDD approach..."
    
    alt Story passes QA review
        QA-->>AO: Status: Approved + Signature
        AO->>FS: updateStoryFile(approved)
        AO->>SP: transitionStory(Approved)
    else Story needs revision
        QA-->>AO: Status: Draft + Findings + Signature
        AO->>FS: updateStoryFile(findings)
        
        loop Max 3 revision cycles
            AO->>SM: "Review QA findings and revise"
            SM-->>AO: Revised story + Signature
            AO->>FS: updateStoryFile(revised)
            
            AO->>QA: "Review revised story"
            alt Approved
                QA-->>AO: Status: Approved
                AO->>SP: transitionStory(Approved)
                Note over WE: Break loop
            else Still needs work
                QA-->>AO: Status: Draft + Findings
            end
        end
        
        Note over WE: If max revisions hit, escalate
    end
```

## 3. Development and Testing Workflow (Approved → Done)

```mermaid
sequenceDiagram
    participant WE as Workflow Engine
    participant AO as Agent Orchestrator
    participant Dev as Dev Agent (Claude)
    participant QA as QA Agent (Claude)
    participant FS as File System
    participant SP as State Processor

    WE->>SP: getNextStory()
    SP-->>WE: Story (status: Approved)
    
    WE->>AO: sendToAgent(story, Dev)
    Note over AO: "Implement with TDD approach"
    AO->>Dev: "Write tests first, then implement"
    
    Dev->>Dev: Write failing tests
    Dev->>Dev: Implement code
    Dev->>Dev: Ensure tests pass
    Dev-->>AO: Implementation + File list + Signature
    
    AO->>FS: updateStoryFile(devRecord)
    AO->>SP: transitionStory(ReadyForReview)
    
    WE->>AO: sendToAgent(story, QA)
    AO->>QA: "Verify tests exist, pass, 90%+ coverage"
    
    QA->>QA: Run test suite
    QA->>QA: Check coverage
    QA->>QA: Validate implementation
    
    alt Tests pass and coverage met
        QA-->>AO: Status: Done + TestResults + Signature
        AO->>FS: updateStoryFile(done)
        AO->>SP: transitionStory(Done)
    else Issues found
        QA-->>AO: Status: Dev + Findings + Signature
        AO->>FS: updateStoryFile(findings)
        AO->>SP: transitionStory(Dev)
        
        loop Max 5 Dev-QA cycles
            WE->>AO: sendToAgent(story, Dev)
            AO->>Dev: "Address QA findings"
            Dev-->>AO: Fixes + Signature
            
            AO->>SP: transitionStory(ReadyForReview)
            
            WE->>AO: sendToAgent(story, QA)
            AO->>QA: "Re-review implementation"
            alt Fixed
                QA-->>AO: Status: Done
                Note over WE: Break loop
            else Still has issues
                QA-->>AO: Status: Dev + Findings
            end
        end
    end
```

## 4. Epic Completion Detection Workflow

```mermaid
sequenceDiagram
    participant WE as Workflow Engine
    participant SP as State Processor
    participant AO as Agent Orchestrator
    participant SM as SM Agent (Claude)
    participant MDG as Monitoring Dashboard
    participant User

    WE->>SP: getNextStory()
    SP-->>WE: null (no incomplete stories)
    
    WE->>AO: sendToAgent(SM, "check epic completion")
    AO->>SM: "Are more stories needed to complete any epic?"
    
    alt More stories needed
        SM-->>AO: New stories + Updated stories.json
        AO->>SP: saveState(updated)
        WE->>WE: Continue processing loop
    else All epics complete
        SM-->>AO: epicComplete: true + Signature
        AO->>SP: saveState(epicComplete)
        
        WE->>MDG: generateFinalReport()
        MDG->>MDG: Calculate metrics
        MDG->>MDG: Generate HTML summary
        MDG-->>WE: Report generated
        
        WE->>WE: Graceful shutdown
        WE-->>CLI: Exit code 0
        CLI-->>User: "All epics completed successfully!"
    end
```

## 5. Error Recovery Workflow

```mermaid
sequenceDiagram
    participant WE as Workflow Engine
    participant AO as Agent Orchestrator
    participant CB as Circuit Breaker
    participant Claude as Claude SDK
    participant LOG as Logger
    participant SP as State Processor

    WE->>AO: sendToAgent(story, agent)
    AO->>CB: protect(apiCall)
    
    loop Exponential backoff (max 3 attempts)
        CB->>Claude: API call
        alt Success
            Claude-->>CB: Response
            CB-->>AO: Parsed response
            Note over WE: Continue normal flow
        else Rate limit error
            Claude-->>CB: 429 Too Many Requests
            CB->>CB: Wait with backoff
            CB->>LOG: Log retry attempt
        else API error
            Claude-->>CB: 5xx error
            CB->>CB: Increment failure count
            alt Circuit open
                CB-->>AO: Circuit breaker open
                AO->>LOG: Log circuit breaker trip
                AO->>SP: Mark story as blocked
                Note over WE: Skip to next story
            else Retry
                CB->>CB: Wait and retry
            end
        else Parse error
            Claude-->>CB: Malformed response
            AO->>AO: Try fallback parser
            alt Fallback succeeds
                AO-->>WE: Partial data extracted
            else All parsers fail
                AO->>LOG: Log parse failure
                AO->>SP: Mark story needs manual review
            end
        end
    end
    
    Note over WE: Continue with next story
```
