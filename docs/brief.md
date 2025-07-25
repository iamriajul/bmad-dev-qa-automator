# Project Brief: BMad CLI Automation Tool

## Executive Summary

**NPX CLI tool that automates the SM-Dev-QA collaboration loop in BMad Method development workflows.** This tool eliminates the manual overhead of launching multiple Claude Code sessions and passing context between Story Manager, Developer, and QA agents during the development phase. The solution monitors the stories directory for work items, automatically orchestrates agent interactions based on story status (Draft → Approved → In Development → Ready to Review → Done), and manages the iterative loops between agents without human intervention. The target market is developers using BMad Method who have completed their planning phase (PRD, Architecture, Epics) and need to execute the development workflow efficiently. The key value proposition is transforming a manual, context-switching heavy process into an automated pipeline that maintains development momentum while preserving the collaborative intelligence of BMad agents.

### Key Automation Flow:
1. **Story Discovery**: Scan stories directory for incomplete work
2. **SM Session**: If no stories exist, launch SM to create new ones
3. **QA Review Loop**: Drafted stories trigger QA review sessions
4. **Dev-QA Loop**: Approved stories enter development with automatic handoffs
5. **Status-Based Orchestration**: Story status drives next agent activation

### Critical Edge Cases Considered:
- **State Anomalies**: Orphaned stories, corrupted status fields, concurrent modifications
- **Session Failures**: Rate limiting, timeouts, context loss, permission errors
- **Agent Behaviors**: Confusion, infinite loops, hallucinations, contradictions
- **Complex Workflows**: Dependencies, hotfixes, epic boundaries, rollbacks
- **Human Intervention**: Manual overrides, partial automation, review checkpoints

## Problem Statement

Development teams have access to three tireless AI agents (Story Manager, Developer, and QA) capable of working 24/7, but **these agents sit idle for 16+ hours daily** due to manual orchestration requirements. While human developers sleep, eat, and live their lives, their AI workforce remains dormant - not due to technical limitations, but because someone needs to manually launch Claude Code sessions and pass context between agents.

The stark reality:
- **168 Hours/Week Available**: AI agents could work continuously
- **40 Hours/Week Utilized**: Only active during human work hours
- **128 Hours/Week Wasted**: 76% of potential development time lost
- **Zero Overnight Progress**: Teams wake up to yesterday's status
- **Competitive Disadvantage**: Automated teams ship 4x faster

The three AI agents form a complete development team:
- **Story Manager (SM)**: Creates and refines user stories autonomously
- **Developer (Dev)**: Implements features without human coding
- **QA Agent**: Reviews, tests, and ensures quality automatically

These agents have proven they can collaborate effectively to produce production-ready code. **The ONLY barrier is the manual process** of launching sessions, copying context, and managing handoffs. Teams are essentially paying for a 24/7 workforce but only using them part-time.

**Every night of manual orchestration is a night of lost features, delayed launches, and competitors pulling ahead.** The BMad Method's promise of AI-accelerated development remains unfulfilled without automation.

## Proposed Solution

The **BMad CLI Automation Tool** is a lightweight NPX package that transforms idle AI agents into a 24/7 autonomous development team. It acts as an intelligent orchestrator that monitors project state, launches Claude Code sessions with appropriate agents, manages context handoffs, and ensures continuous development flow without human intervention.

**Core Concept**: A state-machine driven orchestrator that treats story status as triggers for agent activation. The tool runs as a daemon process, continuously monitoring the stories directory and launching the right AI agent at the right time with the right context.

**Key Differentiators**:
- **Zero-Install Execution**: NPX allows instant usage without setup friction
- **Story-Status Intelligence**: Understands BMad workflow states and transitions
- **Context Preservation**: Maintains conversation history across agent handoffs
- **Graceful Error Recovery**: Handles failures without losing progress
- **Human Override Controls**: Allows intervention without breaking automation

**Why This Succeeds**:
1. **Respects BMad Method**: Works within existing workflow, not against it
2. **Claude-Native Integration**: Uses official CLI with proper session management
3. **Progressive Automation**: Can start with single loop, expand over time
4. **Audit Trail**: Every agent action logged for morning review
5. **Safe Defaults**: Conservative decision-making with escalation paths

**Vision**: Teams start their mornings reviewing completed features instead of planning work. The overnight development log shows a symphony of AI agents collaborating - SM creating stories from the backlog, Dev implementing features, QA ensuring quality, and the cycle repeating until the work is done.

## Target Users

### Primary User Segment: BMad Method Development Teams

**Profile**:
- **Demographics**: Software development teams (2-20 members) already using BMad Method
- **Technical Sophistication**: Comfortable with CLI tools, Node.js ecosystem, and AI-assisted development
- **BMad Experience**: Have completed at least one project using manual BMad workflow
- **Pain Level**: High - spending 3+ hours daily on agent orchestration

**Current Behaviors**:
- Manually launch 10-30 Claude Code sessions per day
- Copy/paste context between agent conversations
- Track story status in spreadsheets or manual notes
- Work standard hours despite having 24/7 AI capability
- Often lose context between sessions

**Specific Needs**:
- Reduce time spent on agent orchestration to near zero
- Enable overnight development cycles
- Maintain quality standards with autonomous agents
- Clear audit trails of AI decisions
- Emergency stop capabilities

**Goals**:
- Ship features 4x faster
- Wake up to completed work
- Focus on strategic decisions, not operational tasks
- Scale BMad Method to larger projects

## Goals & Success Metrics

### Business Objectives
- **Reduce Agent Orchestration Time by 95%**: From 3+ hours/day to <10 minutes/day within 2 weeks of adoption
- **Enable 24/7 Development Cycles**: Achieve 80+ hours/week of active AI development time within 1 month
- **Accelerate Feature Delivery 4x**: Complete average story in 6 hours vs current 24 hours
- **Scale BMad Adoption**: Support 100+ teams using automated BMad within 6 months
- **ROI of 10x**: Save $10 in developer time for every $1 of compute costs

### User Success Metrics
- **Morning Delight Rate**: 90% of mornings start with completed features ready for review
- **Context Preservation**: Zero context loss between agent handoffs
- **Intervention Rate**: <5% of stories require human intervention
- **Setup Time**: Under 5 minutes from NPX command to first automated cycle
- **Trust Score**: 95% confidence in overnight AI work quality

### Key Performance Indicators (KPIs)
- **Stories Completed Per Night**: Average 5-10 stories completed during 8-hour overnight runs
- **Agent Utilization Rate**: AI agents active 80%+ of available time (vs current <25%)
- **Mean Time Between Failures (MTBF)**: 50+ stories between automation failures
- **Recovery Time**: <5 minutes to resume after any failure
- **Cost Per Story**: <$0.50 in Claude API costs per completed story

## MVP Scope

### Core Features (Must Have)

- **Story Directory Analyzer:** Scans `./docs/stories` directory to identify incomplete stories (status != Done) and builds work queue
- **Story Need Evaluator:** When no incomplete stories found, launches SM agent with prompt: "Are there any remaining stories needed to complete the current epic/sprint?" 
- **Dependency Graph Generator:** SM creates `docs/story-dependencies.json` defining relationships and execution order:
  ```json
  {
    "stories": {
      "story-001": { "title": "Setup auth", "dependencies": [], "status": "Draft" },
      "story-002": { "title": "User profile", "dependencies": ["story-001"], "status": "Draft" }
    },
    "executionOrder": ["story-001", "story-002"],
    "epicComplete": false
  }
  ```
- **Dependency-Aware Execution:** Process stories in the order specified by SM's dependency graph
- **Autonomous Story Creation:** If SM indicates more stories needed, triggers story drafting workflow
- **Story Lifecycle Manager:** Tracks story states (Draft → Approved → In Development → Ready to Review → Done) and triggers appropriate agent
- **Claude Session Launcher:** Executes `claude --dangerously-skip-permissions {prompt}` with proper context for each agent type
- **Exit Strategy:** Cleanly exits when SM sets `epicComplete: true` in the dependency JSON
- **Basic Activity Log:** Records all decisions and actions for review

### The Complete Autonomous Flow:
1. **Scan** docs/stories directory for work
2. **Ask SM** to generate/update dependency graph JSON
3. **Process stories** in dependency order
4. **If no pending work**: Ask SM if more stories needed
5. **If yes**: SM drafts story and updates dependency graph
6. **If no**: SM sets epicComplete:true and automation exits
7. **Repeat** until epic complete

### Out of Scope for MVP
- GUI or web interface
- Multiple project/epic support
- Custom workflow configurations
- Parallel story processing (even though we have dependencies)
- Cost tracking
- Advanced retry logic
- Notification systems

### MVP Success Criteria

**A complete autonomous run that:**
1. Starts with empty docs/stories directory
2. SM generates initial dependency graph
3. SM creates first story based on epic needs
4. Processes story respecting any dependencies
5. SM updates dependency graph with new stories as needed
6. Continues until SM marks epic complete
7. Exits with summary of work done

## Post-MVP Vision

### Phase 2 Features

After proving the core autonomous development loop, the next phase focuses on **reliability and scale**:

- **Parallel Story Processing**: Run independent stories simultaneously based on SM's dependency analysis
- **Smart Retry Logic**: Automatic recovery from common failures with exponential backoff
- **Cost Optimization**: Track API costs per story and suggest optimizations
- **Progressive Context Loading**: Start with minimal context, add more if agent struggles
- **Health Check System**: Verify Claude API availability before starting sessions
- **Resume Capability**: Pick up where left off after interruptions
- **Custom Workflow Support**: Configure different agent sequences for different project types

### Long-term Vision

Within 1-2 years, the BMad CLI becomes the **standard orchestration layer** for AI-driven development:

- **Multi-Model Support**: Orchestrate different AI models for different tasks (Claude for complex logic, smaller models for simple tasks)
- **IDE Integration**: VS Code extension showing real-time agent activity
- **Team Collaboration**: Multiple developers' automations coordinate to avoid conflicts
- **Learning System**: Improves prompts based on success patterns across all users
- **Workflow Marketplace**: Share and download custom agent workflows
- **Enterprise Features**: Audit logs, compliance controls, spend limits
- **Self-Improving**: Agents suggest improvements to their own workflows

### Expansion Opportunities

- **Beyond Development**: Extend to documentation, testing, deployment, and monitoring workflows
- **Multi-Project Orchestration**: Manage portfolios of projects with resource allocation
- **AI Team Scaling**: Dynamically spin up more agent instances based on workload
- **Integration Hub**: Connect with Jira, GitHub, Slack for full lifecycle automation
- **Performance Analytics**: Detailed metrics on AI team productivity and ROI
- **Custom Agent Training**: Fine-tune agents on your codebase for better results

## Technical Considerations

### Platform Requirements
- **Target Platforms:** macOS, Linux, Windows (via WSL)
- **Node.js Version:** 18.x or higher (for native fetch and other modern features)
- **Claude CLI:** Latest version installed and authenticated
- **File System:** Write access to project directory for story updates and logs
- **Performance Requirements:** Minimal - mostly waiting for Claude API responses

### Technology Preferences
- **Language:** TypeScript for type safety and better developer experience
- **Runtime:** Node.js with ES modules
- **Process Management:** Native Node.js child_process for Claude CLI execution
- **File Watching:** Chokidar for cross-platform directory monitoring
- **JSON Handling:** Native JSON with schema validation (Zod or similar)
- **Logging:** Winston or Pino for structured logging
- **Testing:** Vitest for unit tests, mock Claude CLI responses

### Architecture Considerations
- **Repository Structure:** 
  ```
  bmad-automator/
  ├── src/
  │   ├── agents/      # Agent-specific prompts and handlers
  │   ├── core/        # State machine, dependency resolver
  │   ├── utils/       # File operations, CLI executor
  │   └── index.ts     # Main entry point
  ├── docs/
  │   ├── stories/     # Monitored directory (BMad standard)
  │   └── story-dependencies.json
  └── logs/           # Automation logs
  ```
- **Service Architecture:** Single Node.js process with event-driven architecture
- **State Management:** In-memory state with file-based persistence (docs/story-dependencies.json)
- **Integration Requirements:** 
  - Claude CLI via child_process
  - File system for story management
  - Optional: GitHub API for future PR creation
- **Security/Compliance:**
  - No credentials stored in code
  - Relies on Claude CLI's authentication
  - Audit logs for all agent actions
  - Configurable rate limits to prevent runaway costs

## Constraints & Assumptions

### Constraints
- **Budget:** Open source project - zero budget for paid tools or services
- **Timeline:** MVP in 2-4 weeks of part-time development
- **Resources:** Single developer with BMad Method expertise
- **Technical:** 
  - Must use official Claude TypeScript SDK (more reliable than CLI)
  - Limited by Claude API rate limits and quotas
  - Cannot modify core BMad agent behaviors
  - Requires API key management (environment variables)

### Key Assumptions
- Claude TypeScript SDK provides stable programmatic access to Claude
- BMad agents (SM, Dev, QA) maintain consistent output formats for status parsing
- Story files follow standard BMad YAML frontmatter format
- Projects have already completed planning phase (PRD, Architecture, Epics exist)
- Single epic/sprint in progress (no parallel epic support needed for MVP)
- Developers trust AI agents to work unsupervised overnight
- Network connectivity remains stable during overnight runs
- Claude API costs remain within acceptable limits (<$1 per story)
- File system operations are atomic (no corruption during concurrent access)
- SM agent can accurately determine when an epic is complete
- Developers comfortable setting ANTHROPIC_API_KEY environment variable

### SDK Advantages Over CLI:
- **Better Error Handling**: Programmatic exceptions vs parsing CLI output
- **Streaming Responses**: Real-time agent output monitoring
- **Proper Context Management**: Direct message history control
- **Rate Limit Handling**: Built-in retry logic and backoff
- **Type Safety**: Full TypeScript types for requests/responses