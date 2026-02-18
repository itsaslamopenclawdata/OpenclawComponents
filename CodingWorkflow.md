# OpenClaw Coding Workflow

> **Purpose:** Define agent hierarchy for planning, building, testing, and deploying projects
> **Repository:** https://github.com/itsaslamopenclawdata/OpenclawComponents
> **Last Updated:** 2026-02-18
> **Component Type:** Workflow / Orchestration

---

## 🎯 Purpose

This document defines the **coding workflow** for using OpenClaw's agent orchestration system to build, test, and deploy projects. It establishes a **hierarchy of specialized agents** where each agent has clear responsibilities and capabilities.

**Core Philosophy:** "Specialized agents working together under a Lead Agent orchestator produce higher quality outcomes than generalist agents."

---

## 🏗️ Agent Hierarchy

### Level 1: Lead Agent (Orchestrator)

**Role:** Central coordinator and decision-maker for all coding activities

**Responsibilities:**
- Break down high-level project goals into actionable tasks
- Sequence tasks based on dependencies and priorities
- Spawn and coordinate specialist agents
- Monitor progress of all specialist agents
- Aggregate results from all agents
- Handle errors and implement recovery strategies
- Make final quality decisions
- Manage project timeline and milestones

**Capabilities:**
- Project planning and decomposition
- Task routing and assignment
- Agent coordination and communication
- Progress monitoring and reporting
- Error handling and retry logic
- Result aggregation and synthesis
- Quality assurance and validation

**Decision Framework:**
```python
def route_task(task, context):
    if is_planning_task(task):
        return "Planner"
    elif is_research_task(task):
        return "Researcher"
    elif is_coding_task(task):
        return "Coder"
    elif is_testing_task(task):
        return "Tester"
    elif is_review_task(task):
        return "Reviewer"
    elif is_documentation_task(task):
        return "Coder"
    else:
        return "Lead"  # Handle unknown tasks
```

---

### Level 2: Specialist Agents

#### Agent 2.1: Planner Agent

**Role:** Responsible for project decomposition and task sequencing

**Responsibilities:**
- Analyze Lead Agent's high-level goal
- Generate detailed project plan with milestones
- Break down goals into phases (foundation, development, testing, deployment)
- Create task dependencies graph
- Estimate time and resource requirements for each task
- Identify critical path and potential blockers
- Generate PLAN.md file in project workspace
- Update Lead Agent with progress

**Capabilities:**
- Project management (task breakdown, scheduling, milestones)
- Risk assessment and mitigation planning
- Resource allocation (time, budget, team)
- Dependency tracking
- Critical path analysis
- Progress monitoring and reporting

**Input:** High-level project goal (e.g., "Build a SaaS application")

**Output:** Detailed project plan with tasks, phases, timelines

---

#### Agent 2.2: Researcher Agent

**Role:** Specialized in web search, data synthesis, and information gathering

**Responsibilities:**
- Search for relevant information using web_search
- Fetch content from specific URLs using web_fetch
- Synthesize and structure research findings
- Extract key data points, trends, and insights
- Validate sources and cross-reference information
- Identify gaps in existing knowledge
- Generate research package for other agents

**Capabilities:**
- Web search (via Brave Search API or DuckDuckGo)
- Content extraction (web_fetch with markdown extraction)
- Data synthesis and structuring
- Source validation (cross-reference multiple sources)
- Trend identification and analysis
- Knowledge gap analysis
- Research report generation

**Tools Required:**
- web_search capability (Brave API key configured)
- web_fetch capability
- Markdown parsing
- Data synthesis (Claude's analysis capability)

**Configuration:**
```yaml
agent_config:
  search_depth: "deep"  # or "quick"
  max_sources: 20
  cross_reference: true
  extract_format: "markdown"
  synthesis_style: "structured"
```

**Sandbox Requirements:**
- Read-only access to project workspace
- Can spawn web_fetch subagents for parallel research
- No file system access (for security)

---

#### Agent 2.3: Coder Agent

**Role:** Responsible for writing code, creating files, and implementing features

**Responsibilities:**
- Read and interpret project requirements and specifications
- Write code in appropriate languages (Python, TypeScript, JavaScript, etc.)
- Create and update project files
- Execute shell commands via shell tool
- Run build processes (npm install, docker build, etc.)
- Debug code issues using debugger tools
- Implement features according to specifications
- Write unit tests and integration tests
- Generate documentation (README.md, API docs, etc.)

**Capabilities:**
- File system access (read/write)
- Shell command execution
- Code generation (any programming language)
- Build tool usage (npm, cargo, pip, etc.)
- Docker operations (build, run, exec)
- Debugging and logging
- Version control operations (git commands)
- Test execution and reporting
- Documentation generation

**Tools Required:**
- File system access (read/write to project workspace)
- Shell tool access
- Debugger (if needed)
- Build tools (npm, cargo, pip, docker, etc.)
- Version control (git)
- Test frameworks (pytest, jest, playwright, etc.)

**Configuration:**
```yaml
agent_config:
  workspace_path: "/path/to/project"
  sandbox_mode: "restricted"  # Can't modify files outside workspace
  shell_timeout: 300  # seconds for shell commands
  git_timeout: 600  # seconds for git commands
  allow_build: true  # Can run build processes
  max_file_size_mb: 10  # Max file size for editing
  code_style: "pep8"  # or "google" or "facebook"
```

**Sandbox Requirements:**
- Full read/write access to project workspace
- Can create, modify, delete files
- Can execute shell commands
- Can run build tools
- Can install dependencies
- Can commit to version control

**Security Considerations:**
- Coder agent should be sandboxed in Docker container
- Network access should be restricted
- File operations should be limited to workspace
- Shell commands should be validated and logged

---

#### Agent 2.4: Verifier Agent

**Role:** Responsible for quality assurance, validation, and testing

**Responsibilities:**
- Review code generated by Coder agent for correctness
- Run unit tests and verify all tests pass
- Check code against specifications and requirements
- Validate documentation accuracy and completeness
- Perform security analysis and vulnerability scanning
- Test edge cases and boundary conditions
- Verify that PLAN.md has been properly implemented
- Generate quality report with scores and recommendations

**Capabilities:**
- Code review and analysis
- Test execution and reporting
- Documentation validation
- Security scanning
- Performance analysis
- Compliance checking
- Quality scoring and metrics

**Tools Required:**
- Test frameworks (pytest, jest, playwright, etc.)
- Code analysis tools (ESLint, Pylint, etc.)
- Security scanners (bandit, semgrep, etc.)
- Performance profilers
- Documentation linters

**Configuration:**
```yaml
agent_config:
  testing_framework: "pytest"
  linting_tool: "eslint"  # or "pylint" or "flake8"
  security_scanner: "bandit"
  performance_profiler: "cProfile"
  quality_threshold: 0.95  # Minimum quality score required
  test_coverage_target: 0.80  # Minimum test coverage required
  security_severity_threshold: "medium"  # Block if high or critical
```

**Quality Gates:**
- All unit tests must pass
- Minimum 80% code coverage
- No high or critical security vulnerabilities
- No documentation gaps
- Code meets style guidelines
- Performance within acceptable thresholds

---

#### Agent 2.5: Reviewer Agent

**Role:** Responsible for final audit, polish, and deployment readiness

**Responsibilities:**
- Perform final code review after all features are implemented
- Ensure code is production-ready and follows best practices
- Verify all documentation is complete and accurate
- Check that all tests pass and have good coverage
- Review and approve deployment configuration
- Generate final project status report
- Make go/no-go decisions for production deployment
- Create deployment checklist and verify all requirements are met

**Capabilities:**
- Code review and analysis
- Documentation review and validation
- Test result verification
- Deployment readiness assessment
- Production sign-off
- Post-deployment monitoring setup

**Tools Required:**
- Code review tools
- Documentation generators
- Deployment platforms (Vercel, Netlify, AWS, GCP)
- Monitoring tools
- Log analysis and error tracking

**Configuration:**
```yaml
agent_config:
  production_readiness_checklist: ".github/checklist.md"
  deployment_platform: "vercel"  # or "netlify", "aws", "gcp"
  monitoring_enabled: true
  alert_channels: ["email", "slack", "discord"]
  rollback_capability: true
  performance_thresholds:
    error_rate: 0.01  # 1% error rate threshold
    response_time_ms: 1000  # 1 second response time threshold
    uptime_percent: 99.9  # Minimum uptime percentage
```

**Production Readiness Criteria:**
- All critical bugs fixed
- All security vulnerabilities addressed
- All tests passing with good coverage
- Documentation complete and accurate
- Performance metrics within acceptable thresholds
- Deployment checklist completed and verified
- Monitoring and alerting configured
- Rollback procedure tested and ready

---

### Agent Interaction Model

#### Communication Flow

```
Lead Agent
    ↓
[BREAK DOWN PROJECT GOAL]
    ↓
[PLAN TASK DECOMPOSITION]
    ↓
[ROUTE TO PLANNER]
    ↓
[MONITOR PLANNER PROGRESS]
    ↓
[AGGREGATE PLANNER OUTPUT]
    ↓
[IDENTIFY SPECIALIST NEEDS]
    ↓
[SPAWN SPECIALIST AGENTS]
    ├─→ Planner Agent
    ├─→ Researcher Agent
    ├─→ Coder Agent
    ├─→ Verifier Agent
    ├─→ Reviewer Agent
    └─→ (Additional specialists as needed)
    ↓
[MONITOR ALL AGENT PROGRESS]
    ↓
[HANDLE ERRORS AND RETRY FAILED AGENTS]
    ↓
[AGGREGATE RESULTS FROM ALL AGENTS]
    ↓
[GENERATE FINAL OUTPUT PACKAGE]
    ↓
[SEND TO USER]
```

#### Agent Coordination Rules

1. **Task Ownership:** Each task is owned by exactly one agent
2. **No Duplication:** Tasks are not assigned to multiple agents
3. **Dependencies:** Agents can depend on outputs from other agents
4. **Parallel Execution:** Independent tasks are executed in parallel
5. **Sequential Dependencies:** Dependent tasks wait for prerequisite completion
6. **Progress Updates:** Each agent reports progress to Lead Agent
7. **Error Handling:** Failed tasks are retried with new agent instances
8. **Result Aggregation:** Lead Agent combines all outputs into final result

#### Agent Lifecycle

```
Spawn (Created by Lead)
    ↓
Initialize (Load context, tools, instructions)
    ↓
Execute Task (Do the work)
    ↓
Report Progress (Send updates to Lead)
    ↓
Complete (Mark task as complete, return results)
    ↓
Terminate (Agent instance destroyed, resources released)
```

---

## 🔄 Workflow Process

### Phase 1: Project Planning

**Agent:** Lead Agent

**Steps:**
1. **Receive Project Goal** (from user)
   - Example: "Build a SaaS landing page"
   - Parse requirements and constraints

2. **Break Down Goal**
   - Identify major components (frontend, backend, database, auth, deployment)
   - Determine tech stack (Next.js, Supabase, Tailwind, Vercel)
   - Define milestones (MVP, beta launch, production)

3. **Generate Project Plan**
   - Create phases with timelines
   - Define dependencies between phases
   - Estimate effort for each task
   - Identify risks and mitigation strategies

4. **Create PLAN.md**
   - Structure: Project overview, phases, milestones, tasks, timeline
   - Include: Team composition, technology stack, deployment strategy
   - Add: Resource requirements (time, budget, team skills)
   - Add: Risk assessment and contingency plans

**Output:** PLAN.md file in project workspace

**Duration:** 30-60 minutes

---

### Phase 2: Research & Planning

**Agent:** Planner + Researcher

**Steps:**
1. **Planner Delegates** to Researcher
   - Researcher receives project requirements
   - Breaks down into research tasks

2. **Researcher Researches**
   - Research existing solutions (GitHub, docs.openclaw.ai, Stack Overflow)
   - Identify best practices and patterns
   - Find open source tools and libraries
   - Research deployment options (Vercel, Netlify, AWS, etc.)
   - Analyze competitive landscape

3. **Researcher Synthesizes**
   - Compile research findings into structured format
   - Identify key recommendations
   - Create options analysis (pros/cons of each approach)
   - Document potential blockers and how to overcome them

4. **Planner Updates Plan**
   - Incorporate research findings into project plan
   - Adjust timelines and effort estimates
   - Add research sources to documentation
   - Refine tech stack decisions based on research

**Output:** Updated PLAN.md with research insights

**Duration:** 60-120 minutes

---

### Phase 3: Development

**Agent:** Coder + Planner

**Steps:**
1. **Planner Delegates Coding Tasks**
   - Planner breaks down project plan into specific coding tasks
   - Creates task dependencies graph
   - Prioritizes tasks based on dependencies

2. **Coder Receives Tasks**
   - Coder receives task from Planner with context
   - Task includes: requirements, file paths, specifications

3. **Coder Executes Task**
   - Reads project structure and existing code
   - Writes code according to specifications
   - Follows style guidelines and best practices
   - Implements features according to plan
   - Writes documentation for code
   - Runs tests to verify implementation

4. **Coder Reports Progress**
   - Coder sends progress updates to Planner
   - Reports completion percentage
   - Alerts on blockers or issues

5. **Planner Monitors Progress**
   - Planner tracks completion of all tasks
   - Adjusts project timeline as needed
   - Reassigns failed tasks to Coder
   - Identifies and resolves dependencies

**Output:** Code files, documentation, test results

**Duration:** Depends on project scope (hours to weeks)

---

### Phase 4: Quality Assurance

**Agent:** Verifier + Reviewer

**Steps:**
1. **Verifier Reviews Code**
   - Verifier analyzes code written by Coder
   - Runs unit tests
   - Performs static code analysis
   - Checks against coding standards and best practices
   - Reports quality scores

2. **Tester Runs Tests**
   - Tester executes test suites
   - Generates test reports with coverage metrics
   - Identifies failing tests and bugs
   - Reports performance benchmarks

3. **Verifier Generates Quality Report**
   - Compiles findings from code review and testing
   - Calculates overall quality score
   - Identifies issues by severity
   - Provides recommendations for improvement

4. **Reviewer Performs Final Audit**
   - Reviewer does final code review before deployment
   - Ensures all documentation is complete
   - Verifies all quality gates are met
   - Assesses production readiness
   - Creates final status report

5. **Quality Gates Check**
   - All unit tests passing? (minimum 80% coverage)
   - No critical security vulnerabilities?
   - Code follows style guidelines?
   - Documentation complete and accurate?
   - Performance within thresholds?
   - Deployment checklist completed?

**Output:** Quality report, test results, deployment readiness

**Duration:** 30-60 minutes (per iteration)

---

### Phase 5: Deployment

**Agent:** Coder + Reviewer + Lead

**Steps:**
1. **Reviewer Approves Deployment**
   - Reviewer verifies all deployment criteria are met
   - Checks that production environment is ready
   - Approves go/no-go decision

2. **Coder Deploys Application**
   - Coder builds application for production
   - Coder configures deployment settings
   - Coder executes deployment commands (vercel deploy, etc.)
   - Coder sets up monitoring and alerting

3. **Lead Monitors Deployment**
   - Lead monitors deployment progress
   - Tracks metrics and alerts
   - Receives deployment confirmation
   - Updates project status

**Output:** Deployed application URL, status confirmation

**Duration:** 30-60 minutes

---

## 📊 Agent Capabilities Matrix

| Agent | Type | Sandbox Level | Primary Tools | Key Capabilities |
|---------|------|---------------|----------------|------------------|
| **Lead** | Orchestrator | None | Planning, coordination, aggregation | Project decomposition, task routing, progress monitoring |
| **Planner** | Specialist | Read-only | Task breakdown, scheduling, resource allocation | Project planning, milestone tracking, dependency management |
| **Researcher** | Specialist | Read-only | Web search, content fetch, synthesis | Information gathering, source validation, trend analysis |
| **Coder** | Specialist | Full | File system, shell, build tools, debugger, git | Code generation, file operations, testing, documentation, deployment |
| **Verifier** | Specialist | Read-only | Code review, testing frameworks, security scanners, performance profilers | Quality assurance, validation, security analysis, compliance checking |
| **Reviewer** | Specialist | Read-only | Code review, deployment platforms, monitoring tools | Final audit, production sign-off, deployment readiness assessment |

---

## 🎯 Decision Framework

### Task Routing Logic

```python
def route_task(task, context):
    """Determine which agent should handle a task based on task type and complexity"""
    
    # Planning tasks
    if task.is_planning():
        return "Planner"
    elif task.is_research():
        return "Researcher"
    elif task.is_architecture():
        return "Planner"
    elif task.is_breakdown():
        return "Planner"
    
    # Research tasks
    elif task.is_information_gathering():
        return "Researcher"
    elif task.is_source_validation():
        return "Researcher"
    elif task.is_trend_analysis():
        return "Researcher"
    elif task.is_knowledge_gap_analysis():
        return "Researcher"
    elif task.is_synthesis():
        return "Researcher"
    
    # Coding tasks
    elif task.is_code_generation():
        return "Coder"
    elif task.is_file_creation():
        return "Coder"
    elif task.is_file_modification():
        return "Coder"
    elif task.is_build_process():
        return "Coder"
    elif task.is_deployment():
        return "Coder"
    elif task.is_configuration():
        return "Coder"
    
    # Testing tasks
    elif task.is_unit_test():
        return "Tester"
    elif task.is_integration_test():
        return "Tester"
    elif task.is_e2e_test():
        return "Tester"
    elif task.is_performance_test():
        return "Tester"
    elif task.is_load_test():
        return "Tester"
    
    # Quality tasks
    elif task.is_code_review():
        return "Verifier"
    elif task.is_security_analysis():
        return "Verifier"
    elif task.is_compliance_check():
        return "Verifier"
    elif task.is_documentation_validation():
        return "Verifier"
    
    # Documentation tasks
    elif task.is_readme_generation():
        return "Coder"
    elif task.is_api_documentation():
        return "Coder"
    elif task.is_deployment_documentation():
        return "Coder"
    
    # Production tasks
    elif task.is_production_readiness():
        return "Reviewer"
    elif task.is_deployment():
        return "Coder"
    elif task.is_monitoring_setup():
        return "Coder"
    elif task.is_alerting_setup():
        return "Coder"
    
    # Unknown tasks
    else:
        return "Lead"  # Default to orchestrator
```

---

## 🔐 Security Considerations

### Agent Sandbox Isolation

**Principles:**
1. **Per-Agent Sandboxing** - Each agent type has specific sandbox requirements
2. **Network Isolation** - Agent communications are isolated and logged
3. **Data Isolation** - Each agent has its own memory/context space
4. **Resource Limits** - CPU, memory, and disk quotas per agent
5. **Timeout Enforcement** - Maximum execution time per task per agent

**Sandbox Configurations:**

```yaml
# Planner Agent (read-only)
planner:
  workspace_access: "read_only"  # No file system access
  network_access: "none"  # No web access
  sandbox_type: "none"  # Runs in same process as orchestrator

# Researcher Agent (read-only)
researcher:
  workspace_access: "read_only"  # No file system access
  network_access: "full"  # Can fetch from web
  sandbox_type: "restricted"  # Can spawn web_fetch subagents
  max_sources: 20  # Can search up to 20 sources
  web_fetch_timeout: 300  # 5 minutes per source

# Coder Agent (full access)
coder:
  workspace_access: "full"  # Read/write access to project workspace
  network_access: "full"  # Can fetch from web, can install packages
  sandbox_type: "restricted"  # Docker container for security
  shell_timeout: 300  # 5 minutes for shell commands
  git_timeout: 600  # 10 minutes for git commands
  allow_build: true  # Can run build processes
  max_file_size_mb: 10  # Max file size for editing
  docker_access: true  # Can run docker commands
  debugger_enabled: true  # Can use debugger tools
  testing_enabled: true  # Can run tests
  allowed_packages: ["npm", "pip", "cargo", "pipenv"]  # Can install these packages
  banned_commands: ["rm -rf /", "dd if=", ":() { :; };:"]  # Dangerous commands
  code_style: "pep8"  # or "google" or "facebook"

# Verifier Agent (read-only)
verifier:
  workspace_access: "read_only"  # No file system access
  network_access: "full"  # Can fetch from web
  sandbox_type: "none"  # Runs in same process as orchestrator
  testing_framework: "pytest"  # or "jest", "playwright"
  linting_tool: "eslint"  # or "pylint" or "flake8"
  security_scanner: "bandit"
  performance_profiler: "cProfile"
  quality_threshold: 0.95
  test_coverage_target: 0.80

# Reviewer Agent (read-only)
reviewer:
  workspace_access: "read_only"  # No file system access
  network_access: "full"  # Can access deployment platforms
  deployment_platforms: ["vercel", "netlify", "aws", "gcp"]  # Approved platforms
  monitoring_enabled: true  # Can set up monitoring
  alert_channels: ["email", "slack", "discord"]
  rollback_capability: true
  performance_thresholds:
    error_rate: 0.01
    response_time_ms: 1000
    uptime_percent: 99.9
```

---

## 📝 Notes

**Key Principles:**

1. **Specialization Beats Generalization**
   - Each agent type is specialized for specific tasks
   - Planner focuses on planning and coordination
   - Researcher focuses on information gathering
   - Coder focuses on writing code and building
   - Verifier focuses on quality assurance
   - Reviewer focuses on final polish

2. **Clear Ownership and Accountability**
   - Each task is owned by exactly one agent
   - Progress is tracked per agent and per task
   - Agents report their status independently

3. **Parallel Execution with Coordination**
   - Independent tasks execute in parallel (multiple Coders)
   - Dependent tasks wait for prerequisites (sequenced by Planner)
   - Lead Agent coordinates all parallel and sequential work

4. **Quality Gates**
   - Every phase has quality gates that must be passed
   - Verifier ensures code meets quality standards
   - Tester ensures all tests pass
   - Reviewer ensures production readiness

5. **Error Recovery and Resilience**
   - Failed tasks are automatically retried
   - New agent instances are spawned for retries
   - Graceful degradation if quality gates can't be met
   - Full error logging and tracking for learning

6. **Scalability and Flexibility**
   - Agent pool can be expanded to 100+ concurrent agents
   - Different agent types can be added as needed
   - Workflow can be adapted for different project types

**Best Practices:**
- Use Planner for project decomposition (don't skip)
- Let Researcher inform tech stack decisions
- Use Coder for all code work (maintain quality)
- Use Verifier for quality assurance (don't skip testing)
- Use Reviewer for final approval (don't deploy to production unverified)
- Monitor progress continuously and adjust plans accordingly
- Document all decisions and learnings for future reference

---

## ✅ Implementation Checklist

For implementing this coding workflow:

- [ ] Define agent hierarchy and responsibilities
- [ ] Create task routing logic for Lead Agent
- [ ] Implement communication protocols between agents
- [ ] Set up progress monitoring and reporting
- [ ] Implement error handling and retry logic
- [ ] Define sandbox configurations for each agent type
- [ ] Create quality gates for each phase
- [ ] Define security policies and restrictions
- [ ] Test agent coordination with simple project
- [ ] Test parallel execution with multiple agents
- [ ] Test error recovery mechanisms
- [ ] Test quality assurance workflow
- [ ] Test deployment readiness process
- [ ] Document best practices and learnings

---

**Last Updated:** 2026-02-18
**Repository:** https://github.com/itsaslamopenclawdata/OpenclawComponents
**Component:** Coding Workflow (Orchestration)

---

*Specialized agents working together under a Lead Agent orchestrator produce higher quality outcomes than generalist agents. Use this hierarchy for all coding projects built with OpenClaw.*
