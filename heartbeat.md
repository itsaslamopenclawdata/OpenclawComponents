# OpenClaw Component: Heartbeat

> **Source:** https://docs.openclaw.ai/architecture/heartbeat
> **Description:** Autonomous scheduling system that runs tasks and processes in the background
> **Component Type:** Architecture / Core

---

## 🎯 Purpose

The **Heartbeat** system is OpenClaw's autonomous scheduling engine. It's not a medical or biological heartbeat — it's a **pulse-driven task scheduler** that keeps your agents alive and productive by running recurring jobs, scheduled tasks, and background processes.

**Key Philosophy:** "Agents don't sleep. They have a Heartbeat."

---

## 🏗️ Architecture

### Core Components

```mermaid
graph TD
    A[Heartbeat Scheduler] --> B[Cron Jobs]
    A --> C[Scheduled Tasks]
    A --> D[Agent Pool]
    A --> E[Event Triggers]
    A --> F[Agent Health Monitor]

    B --> |Heartbeat Manager|
    B --> G[Job Queue]
    B --> H[Agent Supervisor]

    C -->|Job Runner|
    C --> I[Agent Instance]

    D -->|Task Executor|
    D --> M[Agent Pool]
    D --> N[Context Provider]

    E --> |Event Listener|
    E --> O[Agent Pool]
    E --> R[Task Router]

    F --> |Health Checker|
    F --> S[Metrics Collector]
    F --> T[Alert Manager]
```

---

## 🔧 Technical Details

### Heartbeat Manager

**Purpose:** Coordinate all heartbeat activities and manage job execution

**Key Functions:**
- Schedule cron jobs (e.g., every 5 minutes, hourly, daily)
- Track active jobs and their status
- Monitor agent health and availability
- Handle job failures and retries
- Collect metrics and logs
- Trigger alerts on specific conditions

**Job Types:**
1. **Time-based Jobs**
   - Run at specific intervals (every N minutes/hours)
   - Example: Send summary every 15 minutes
   - Example: Check for new emails every 30 minutes

2. **Event-driven Jobs**
   - Triggered by external events
   - Example: New email received
   - Example: Webhook payload received
   - Example: Agent response received

3. **Agent Health Jobs**
   - Monitor agent uptime and responsiveness
   - Restart stuck agents
   - Report agent performance

---

### Job Queue

**Purpose:** Queue and manage background jobs and tasks

**Queue Management:**
- Priority-based queue (high, medium, low)
- Dependencies between jobs
- Parallel execution where possible
- Resource management (limit concurrent jobs)
- Timeout and cancellation

**Job Execution:**
- Spin up agent instances
- Execute tasks with parameters
- Collect results and metrics
- Handle errors and retries
- Clean up resources

---

### Agent Pool

**Purpose:** Manage available agents and their capabilities

**Agent Types:**
- **Session Agents** - For conversational tasks
- **Code Agents** - For programming and code generation
- **Shell Agents** - For system administration
- **Custom Agents** - For specialized tasks
- **Helper Agents** - For small, repetitive tasks

**Agent Lifecycle:**
```
Created → Idle → Assigned → Busy → Completing → Complete → Idle
    ↓
Available for new tasks
```

**Agent Supervision:**
- Monitor agent health
- Prevent overloading
- Balance resource usage
- Collect performance metrics
- Restart failed agents

---

### Context Provider

**Purpose:** Provide context and memory to agents during job execution

**Context Types:**
1. **Persistent Context**
   - User preferences
   - Agent configurations
   - Project settings
   - Historical context

2. **Session Context**
   - Active session data
   - Conversation history
   - Current task context
   - Recent messages

3. **Temporary Context**
   - Job parameters
   - Intermediate results
   - Agent state
   - Processing queue

---

### Metrics Collector

**Purpose:** Track performance metrics and logs

**Metrics Tracked:**
- Job execution time
- Agent success rate
- Resource utilization (CPU, memory, tokens)
- Error rate by agent type
- Queue depth and wait times
- Token usage per task
- Memory operations

**Logging:**
- Structured log format (timestamp, event, data)
- Log levels: DEBUG, INFO, WARN, ERROR
- Aggregation by agent, job, and time period

---

### Alert Manager

**Purpose:** Trigger and manage notifications based on events

**Alert Types:**
1. **Job Failure Alerts**
   - Immediate notification on job failure
   - Include error details and context
   - Retry logic with exponential backoff

2. **Agent Health Alerts**
   - Agent not responding
   - Agent crashed or stuck
   - Agent performance degradation

3. **Threshold Alerts**
   - Queue depth exceeds limit
   - Resource usage exceeds threshold
   - Memory usage exceeds limit

4. **Status Alerts**
   - Job completion notifications
   - Batch job summaries
   - Daily/weekly/monthly reports

**Alert Channels:**
- In-app notifications
- Webhooks
- Email notifications
- Slack/Discord integrations
- Telegram messages
- Mobile push notifications

---

## ⚙️ Configuration

### Cron Jobs

**Syntax:** Standard cron expression (`* * * * *`)

**Common Cron Patterns:**
```cron
# Every 5 minutes
*/5 * * * * *

# Every 15 minutes
*/15 * * * * *

# Every hour
0 * * * * *

# Every 6 hours
0 */6 * * * *

# Every day at 2:30 AM
30 2 * * * *

# Every Monday at 9:00 AM
0 9 * * * 1

# Every week on Sunday at 11:00 PM
0 11 * * * 0
```

### Job Configuration

```yaml
jobs:
  - name: "Daily Digest Generation"
    cron: "0 9 * * *"
    description: "Generate daily digest briefs for all goals"
    agent_type: "session_agent"

  - name: "Email Check"
    cron: "*/30 * * * *"
    description: "Check for new emails every 30 minutes"
    agent_type: "shell_agent"

  - name: "Agent Health Monitor"
    cron: "*/5 * * * *"
    description: "Monitor agent health every 5 minutes"
    agent_type: "shell_agent"

  - name: "Metrics Collection"
    cron: "0 */6 * * * *"
    description: "Collect and aggregate metrics every 6 hours"
    agent_type: "session_agent"

  - name: "Memory Cleanup"
    cron: "0 4 * * * *"
    description: "Clean up expired memory entries at 4 AM"
    agent_type: "shell_agent"
```

---

## 🔄 Workflows

### Job Execution Flow

```
Start
  ↓
Validate Job
  - Check dependencies
  - Verify permissions
  - Validate parameters
  ↓
Assign to Agent
  - Select appropriate agent type
  - Allocate resources
  ↓
Execute Job
  - Spin up agent instance
  - Provide context and parameters
  - Monitor execution
  - Collect results
  ↓
Handle Result
  - Process output
  - Update memory
  - Update metrics
  - Trigger alerts if needed
  ↓
Cleanup
  - Terminate agent instance
  - Release resources
  ↓
Complete
  - Mark job as complete
  - Report status
  - Schedule next execution if recurring
```

### Scheduled Task Flow

```
Scheduled Time Reached
  ↓
Check Preconditions
  - Is job still valid?
  - Are dependencies met?
  - Have resources available?
  ↓
Execute Task
  - Run the scheduled operation
  - Generate output
  - Update state
  ↓
Schedule Next Run
  - Calculate next run time
  - Update cron schedule
  - Set reminder
```

---

## 📊 Metrics & Monitoring

### Key Metrics

**Job Performance:**
- Average job execution time
- Success rate by job type
- Failure rate and retry attempts
- Queue throughput (jobs/minute)
- Agent utilization (total active / total available)

**Agent Performance:**
- Average response time
- Success rate per agent
- Error rate per agent
- Token usage per agent
- Uptime and availability
- Memory operations per agent

**System Performance:**
- Queue depth and wait times
- Resource utilization (CPU, memory, disk)
- Memory usage and growth
- Network I/O and bandwidth
- Job scheduler latency

**Business Metrics:**
- Jobs completed per day/week/month
- Agent-hours utilized
- Tokens consumed
- Cost per job type
- ROI (value generated / cost incurred)

---

## 🔐 Security Considerations

### Job Isolation

**Principles:**
1. **Per-job sandboxing** - Each job runs in isolated environment
2. **Resource limits** - CPU, memory, token limits per job
3. **Timeout enforcement** - Maximum execution time per job
4. **Memory isolation** - Separate memory space per agent
5. **Network isolation** - Separate network contexts where needed

### Access Control

**Principles:**
1. **Agent-specific access** - Agents only access resources they need
2. **Principle of least privilege** - Minimum necessary access
3. **Audit logging** - Track all resource access
4. **Permission revocation** - Ability to revoke access at any time
5. **Data encryption** - Encrypt sensitive data at rest

---

## 🚨 Troubleshooting

### Common Issues

**1. Job Not Running**
   **Symptom:** Scheduled job doesn't execute
   **Causes:**
   - Cron configuration error
   - Job disabled
   - System overload
   **Solution:** Check cron syntax, verify job enabled, check system logs

**2. Agent Not Responding**
   **Symptom:** Agent assigned to job becomes unresponsive
   **Causes:**
   - Agent crashed
   - Network issue
   - Token limit exceeded
   **Solution:** Implement timeout, check agent health, restart agent if needed

**3. Resource Exhaustion**
   **Symptom:** System runs out of available resources
   **Causes:**
   - Too many concurrent jobs
   - Memory leak
   - Token budget exceeded
   **Solution:** Implement resource limits, optimize job queue, clear memory

**4. Duplicate Jobs**
   **Symptom:** Same job runs multiple times
   **Causes:**
   - Cron misconfiguration
   - Job not marked complete
   - Scheduler failure
   **Solution:** Implement job deduplication, add job ID tracking, improve completion detection

**5. Memory Overflow**
   **Symptom:** Memory usage grows unbounded
   **Causes:**
   - Too much context stored
   - No memory cleanup
   - Duplicate entries
   **Solution:** Implement memory limits, scheduled cleanup, compress old entries

---

## 📝 Notes

**Key Principles:**
- **Autonomy First:** Agents run autonomously with heartbeat monitoring
- **Reliability:** Jobs must complete successfully and on schedule
- **Observability:** Everything is logged and metrics are collected
- **Scalability:** System must handle increasing load efficiently
- **Recovery:** Jobs and agents must be able to recover from failures

**Best Practices:**
1. Use cron jobs for recurring, time-based tasks
2. Use event-driven jobs for reactive operations
3. Monitor agent health continuously
4. Implement proper timeouts and resource limits
5. Use structured logging for troubleshooting
6. Collect and analyze metrics for optimization

---

**Last Updated:** 2026-02-18
**Source:** https://docs.openclaw.ai/architecture/heartbeat
**Component:** Heartbeat (Scheduler/Orchestrator)
**Repository:** https://github.com/itsaslamopenclawdata/OpenclawComponents

---

*Agents don't sleep. They have a Heartbeat that keeps them alive, productive, and on schedule.*
