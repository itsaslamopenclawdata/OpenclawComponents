# Core Components of OpenClaw

## ✅ BASIC COMPONENTS (Bullet Overview)

---

### 1️⃣ Soul (Brain / Orchestrator)

**Central decision engine**

**Responsibilities:**
- Reads user requests
- Decides which tool/skill to call
- Routes tasks to agents
- Maintains execution flow

**Key Behavior:**
- 👉 If Soul is weak or misconfigured → agent will talk but not act
- Acts as the "brain" of the system
- Coordinates all other components

**Impact on GitHub Automation:**
- ✅ Critical for deciding when to use git/GitHub commands
- ✅ Routes creation requests to proper agents
- ✅ Ensures correct tools are called for each task

---

### 2️⃣ Heartbeat (Lifecycle Manager)

**Keeps agent alive and maintains session state**

**Responsibilities:**
- Manages background tasks
- Handles retries & async events
- Keeps session active during long operations
- Prevents timeout/dropouts

**Key Behavior:**
- 👉 Without heartbeat → automation dies mid-process
- Maintains connection to user channels (Discord, webchat, etc.)
- Ensures long-running tasks complete successfully

**Impact on GitHub Automation:**
- ✅ Keeps git operations alive during large pushes
- ✅ Maintains session during repository creation
- ✅ Prevents timeout when building/deploying web apps

---

### 3️⃣ Memory

**Two types of memory storage**

**Short-term Memory:**
- Conversation context within current session
- Temporary data and variables
- Recent commands and responses

**Long-term Memory:**
- Persistent memory across sessions
- Stored in `MEMORY.md` and `memory/YYYY-MM-DD.md` files
- Remembers decisions, preferences, and project context

**Critical for GitHub Automation:**
- 👉 Must remember repo name for pushes
- 👉 Must remember which branch to work on
- 👉 Must remember PAT/token configuration
- 👉 Must remember project structures

**Failure Mode:**
- 👉 If memory is disabled → agent forgets context between sessions
- 👉 Can't remember which repos were created
- 👉 Loses GitHub credentials and configurations

**Memory Storage Locations:**
- `MEMORY.md` — Long-term curated memories
- `memory/YYYY-MM-DD.md` — Daily activity logs
- `SOUL.md` — Agent personality and behavior
- `USER.md` — User preferences and goals

---

### 4️⃣ Skills (Tools Layer)

**This is the MOST important component for GitHub automation**

**What Skills Are:**
- Capabilities and tools available to agents
- File operations, shell commands, API calls
- Integrations with external services
- Specialized functionality layers

**Skill Categories:**

**File Operations:**
- ✅ `read` — Read file contents
- ✅ `write` — Create or overwrite files
- ✅ `edit` — Precise file edits

**Shell Execution:**
- ✅ `exec` — Run shell commands
- ✅ `process` — Manage background processes
- ✅ `pty` mode for interactive commands

**GitHub/Git Skills:**
- ✅ Git initialization
- ✅ Git commits and pushes
- ✅ Repository creation via CLI
- ✅ Branch management

**Discord/Communication Skills:**
- ✅ `message` — Send messages and reactions
- ✅ Channel actions
- ✅ User interaction

**Web/API Skills:**
- ✅ `web_search` — Search web (Brave API)
- ✅ `web_fetch` — Fetch webpage content
- ✅ `browser` — Browser automation

**Agent Orchestration Skills:**
- ✅ `sessions_spawn` — Create subagent sessions
- ✅ `subagents` — Manage subagent runs
- ✅ `sessions_list` — List active sessions

**For GitHub Repo Creation, You NEED:**

| Skill | Purpose | Critical? |
|--------|---------|-----------|
| File write | Create README, configs | ✅ Essential |
| Shell execution | Run git commands | ✅ Essential |
| Git CLI access | Create repos, push code | ✅ Essential |
| Sessions spawn | Dispatch specialized agents | ✅ Important |
| Memory read/write | Remember repo details | ✅ Important |

**Failure Mode:**
- 👉 If skills are disabled → agent becomes just a chatbot
- 👉 Can't create repositories
- 👉 Can't execute git commands
- 👉 Can't build or deploy applications

**Skills Configuration:**
- Located in `.agents/skills/` directory
- Each skill has `SKILL.md` with instructions
- Skills can be installed/managed independently
- Some skills are built-in (file ops, shell)

---

### 5️⃣ Agents

**Role-based executors for specialized tasks**

**What Agents Are:**
- Role-specific instances spawned by Soul
- Each agent has specific expertise
- Agents collaborate to complete complex tasks
- Soul coordinates which agent handles what

**Agent Types:**

**Coding Agent:**
- Writes code
- Implements features
- Follows best practices
- Tests and debugs

**DevOps Agent:**
- Manages infrastructure
- Handles deployments
- Configures CI/CD
- Manages services

**Web Builder Agent:**
- Creates web applications
- Implements UI/UX
- Sets up frameworks
- Deploys to production

**Git Manager Agent:**
- Manages version control
- Handles branches
- Creates repositories
- Manages pull requests

**Workflow:**

```
User Request → Soul (Brain)
           ↓
     Decides which agent to call
           ↓
     Dispatches specialized agent
           ↓
     Agent executes task
           ↓
     Returns results to Soul
           ↓
     Soul presents to user
```

**Key Behavior:**
- 👉 Soul decides → which agent handles specific task
- 👉 Agents don't coordinate directly → Soul manages
- 👉 Each agent is stateless (fresh context per task)
- 👉 Soul aggregates all agent outputs

**Impact on GitHub Automation:**

| Agent | Task in GitHub Workflow |
|--------|----------------------|
| Git Manager | Create repos, manage branches |
| DevOps | Set up CI/CD pipelines |
| Web Builder | Deploy web apps to GitHub |
| Coding Agent | Write automation scripts |
| Soul | Orchestrate entire workflow |

**Failure Mode:**
- 👉 If agents can't be spawned → Soul must do everything alone
- 👉 Can't parallelize tasks
- 👅 Can't handle specialized workflows

---

## 🔄 Component Interactions

**Typical GitHub Repo Creation Flow:**

```
User: "Create GitHub repo called my-project"
  ↓
Soul reads request
  ↓
Soul routes to Git Manager agent
  ↓
Git Manager uses Shell skill → runs `gh repo create`
  ↓
Git Manager uses File write skill → creates README
  ↓
Git Manager uses Shell skill → initializes git
  ↓
Git Manager uses Shell skill → pushes to GitHub
  ↓
Memory saves: "Created repo: my-project"
  ↓
Heartbeat keeps session alive during push
  ↓
Soul presents URL to user
```

**Typical Web App Build Flow:**

```
User: "Build a Next.js app"
  ↓
Soul reads request
  ↓
Soul spawns Web Builder agent
  ↓
Web Builder uses Shell skill → creates project
  ↓
Web Builder uses File write skill → writes components
  ↓
Soul spawns Git Manager agent
  ↓
Git Manager creates GitHub repo
  ↓
Git Manager pushes code
  ↓
Memory saves project details
  ↓
Soul presents deployment URL
```

---

## ⚙️ Configuration

### Skills Location
```
C:\Users\Hp\.openclaw\skills\
├── built-in/
│   ├── file-operations/
│   ├── shell-execution/
│   └── communication/
└── installed/
    ├── github-automation/
    ├── web-development/
    └── discord-integration/
```

### Memory Storage
```
C:\Users\Hp\.openclaw\
├── MEMORY.md              # Long-term memory
├── memory/
│   └── 2026-02-18.md    # Daily logs
├── SOUL.md               # Agent personality
└── USER.md               # User preferences
```

### GitHub Configuration
```
C:\Users\Hp\.openclaw\
├── github-config.json      # Token and settings
└── .gitignore             # Security rules
```

---

## 🚀 Summary

**All 5 Components Work Together:**

1. **Soul** — The brain that decides and coordinates
2. **Heartbeat** — Keeps system alive during tasks
3. **Memory** — Remembers everything across sessions
4. **Skills** — Provides capabilities (git, files, shell)
5. **Agents** — Role-based executors for tasks

**For GitHub Automation, Critical Path:**
```
Soul (decides) → Skills (execute git/CLI) → Memory (remembers) → Heartbeat (keeps alive) → Agents (specialized tasks)
```

**If ANY Component Fails:**
- Weak Soul → Agent talks but doesn't act
- No Heartbeat → Automation dies mid-process
- Disabled Memory → Loses context between sessions
- Disabled Skills → Becomes just a chatbot
- No Agents → Soul must do everything alone

**Success Requires:**
- ✅ Strong Soul configuration
- ✅ Active Heartbeat system
- ✅ Enabled Memory
- ✅ All relevant Skills enabled
- ✅ Agent spawning capability

---

**Created by:** Clawsweety 🐾
**Date:** 2026-02-18
**Purpose:** Documentation of OpenClaw core components
