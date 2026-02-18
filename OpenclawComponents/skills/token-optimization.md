# Token Optimization & Cost Reduction Skill

> **Purpose:** Master efficient token usage and minimize API costs through systematic optimization, learning from mistakes, and continuous self-improvement.
> **Usage:** Apply BEFORE, DURING, and AFTER every activity.
> **Priority:** HIGH — Apply to every interaction, every tool call, every response.

---

## 🎯 Core Philosophy

**"Every Token Counts. Every Mistake Teaches. Every Day Improves."**

This skill is not just about saving costs — it's about building efficient, intelligent AI behavior that continuously learns and improves. It must be applied religiously to every single interaction.

---

## 📋 Daily Checklist

### BEFORE Any Activity (Pre-Execution)

- [ ] **Read this skill file** — Refresh optimization principles
- [ ] **Estimate token budget** — How many tokens is this activity worth?
- [ ] **Identify optimization opportunities** — Where can we save tokens?
- [ ] **Plan for learning** — What will we learn from this?
- [ ] **Set token limit** — Maximum tokens allowed for this activity

### DURING Activity (Execution)

- [ ] **Monitor token usage** — Track actual vs estimated tokens
- [ ] **Optimize in real-time** — Adjust if exceeding budget
- [ ] **Note inefficiencies** — Record what's wasteful
- [ ] **Capture learnings** — What's working, what's not?

### AFTER Activity (Post-Execution)

- [ ] **Review actual token usage** — Compare with estimate
- [ ] **Calculate cost** — Actual cost of this activity
- [ ] **Document mistakes** — What went wrong?
- [ ] **Document optimizations** — What went well?
- [ ] **Update this skill** — Add new learnings and rules
- [ ] **Plan next optimization** — How to improve next time?

---

## 🎯 Token Optimization Strategies

### 1. Context Management

#### Rule: Never Pass Unnecessary Context
**Problem:** Passing entire files, memory, or history when only a fraction is needed.

**Solution:**
- Use `memory_get` with `from` and `lines` parameters to fetch only needed sections
- Use `read` with `offset` and `limit` for large files
- Never pass entire MEMORY.md in shared contexts (only in main session)
- Filter tool results before processing

**Example:**
```markdown
❌ WRONG:
"Read MEMORY.md and tell me about my quantum goals"

✅ RIGHT:
"Search memory for 'quantum goals', then fetch only the relevant section with memory_get(from=X, lines=Y)"
```

**Token Savings:** 5,000 - 50,000 tokens per interaction

---

#### Rule: Use Targeted Retrieval, Not Bulk Loading
**Problem:** Loading entire directories or repos when only specific files are needed.

**Solution:**
- Use `find` or `glob` patterns to locate specific files
- Read only the files that are directly relevant
- Avoid `cat *` or reading entire directory trees

**Example:**
```markdown
❌ WRONG:
"Read all files in the project and summarize"

✅ RIGHT:
"List the directory, identify relevant files (README.md, package.json, etc.), then read only those"
```

**Token Savings:** 10,000 - 100,000 tokens per operation

---

### 2. Prompt Engineering

#### Rule: Be Precise, Not Verbose
**Problem:** Using 500 words when 50 words would suffice.

**Solution:**
- State requirements clearly and concisely
- Use bullet points instead of paragraphs
- Specify constraints upfront (length, format, output)
- Remove filler phrases ("I think", "It seems", "In my opinion")

**Example:**
```markdown
❌ WRONG (150 tokens):
"I was thinking about how we might want to approach this task. I believe that we should probably consider starting by looking at the main files in the directory, and then perhaps reading a few of them to understand the structure better. What do you think about that approach?"

✅ RIGHT (30 tokens):
"List directory, identify key files (README, package.json), read them, report project structure."
```

**Token Savings:** 70-80% reduction in prompt tokens

---

#### Rule: Use Constraints to Limit Output
**Problem:** AI generates 2,000 words when 200 words are needed.

**Solution:**
- Always specify output length constraints
- Use "in 3 bullet points" or "in 1-2 sentences"
- Request specific format (table, list, JSON)
- Set word/character limits

**Example:**
```markdown
❌ WRONG:
"Explain this code"

✅ RIGHT:
"Explain this code in 3 bullet points, max 200 words total"
```

**Token Savings:** 80-90% reduction in completion tokens

---

#### Rule: Ask for Structured Output
**Problem:** Free-form text wastes tokens and is hard to parse.

**Solution:**
- Request JSON, tables, or bullet-point formats
- Specify exact structure
- Use templates for consistent responses

**Example:**
```markdown
❌ WRONG:
"What did you find?"

✅ RIGHT:
"Report findings in JSON: {status, findings, nextSteps, errors}"
```

**Token Savings:** 50-70% reduction in response tokens

---

### 3. Tool Usage Optimization

#### Rule: Batch Tool Calls
**Problem:** Making individual tool calls when one batched call would work.

**Solution:**
- Use array parameters when available (e.g., `images=[url1, url2, url3]`)
- Combine related operations into single calls
- Minimize round trips

**Example:**
```markdown
❌ WRONG:
1. Call: read(file1)
2. Call: read(file2)
3. Call: read(file3)

✅ RIGHT:
Use `web_fetch` for multiple URLs, or use subagent to batch reads
```

**Token Savings:** 20-40% reduction in operation overhead

---

#### Rule: Use Cached Responses
**Problem:** Re-fetching data that hasn't changed.

**Solution:**
- Cache frequently accessed data in memory
- Check if file exists before reading
- Use `process(action=poll)` for long operations instead of repeated status checks
- Store results in memory files for reuse

**Example:**
```markdown
❌ WRONG:
Every 5 seconds: read(status.txt)

✅ RIGHT:
Use `process(action=poll, timeout=30000)` for async operations
```

**Token Savings:** 90% reduction in repeated operations

---

#### Rule: Fail Fast
**Problem:** Continuing operations after obvious failures.

**Solution:**
- Check prerequisites before starting
- Validate tool results before proceeding
- Abort early on unrecoverable errors
- Don't retry doomed operations

**Example:**
```markdown
❌ WRONG:
"Install package, then use it, then fail because package doesn't exist"

✅ RIGHT:
"Check if package exists. If not, abort immediately and report error."
```

**Token Savings:** 50-90% reduction in wasted tokens

---

### 4. Response Optimization

#### Rule: NO_REPLY for Silent Operations
**Problem:** Sending messages when no response is needed.

**Solution:**
- Use `NO_REPLY` for successful operations that don't need acknowledgment
- Only speak when you have value to add
- Combine multiple updates into one message

**Example:**
```markdown
❌ WRONG:
User: "Run tests"
Assistant: "OK, running tests..." (tool call)
Assistant: "Tests passed!" (unnecessary)

✅ RIGHT:
User: "Run tests"
Assistant: NO_REPLY (if tests pass, only speak if they fail)
```

**Token Savings:** 50-200 tokens per silent operation

---

#### Rule: Be Concise in Explanations
**Problem:** Providing 500-word explanations for simple yes/no questions.

**Solution:**
- Direct answers for simple questions
- Elaborate only when explicitly asked
- Use "See docs" or "Read this file" instead of repeating content
- Use code blocks for technical content

**Example:**
```markdown
❌ WRONG (500 tokens):
"To check the status, you need to go to the command line and type the command 'openclaw status'. This will show you various pieces of information about your current session including the model you're using, the reasoning setting, your token usage, and many other useful details. It's a very helpful command..."

✅ RIGHT (15 tokens):
"Run `openclaw status` to see session details including token usage."
```

**Token Savings:** 70-95% reduction in response tokens

---

#### Rule: Use References Instead of Repetition
**Problem:** Repeating content that's already documented.

**Solution:**
- Reference file paths and line numbers
- Use "See SKILL.md" instead of rewriting it
- Quote specific sections instead of paraphrasing
- Link to documentation

**Example:**
```markdown
❌ WRONG:
"According to the best practices, you should always do X, Y, and Z, and make sure to consider A, B, and C..." (repeating docs)

✅ RIGHT:
"Follow these best practices (see AGENTS.md#best-practices):
- Do X, Y, Z
- Consider A, B, C"
```

**Token Savings:** 60-80% reduction in repetitive content

---

### 5. Model Selection Optimization

#### Rule: Choose the Right Model for the Task
**Problem:** Using GPT-4 for simple tasks that GPT-3.5 can handle.

**Solution:**
- Use faster/cheaper models for:
  - Simple summarization
  - Basic code generation
  - Straightforward questions
  - Routine operations
- Use GPT-4 only for:
  - Complex reasoning
  - Multi-step planning
  - Ambiguous problems
  - Critical decisions

**Example:**
```markdown
❌ WRONG:
Use GPT-4 to: "What is 2+2?"

✅ RIGHT:
Use GPT-3.5 for: "What is 2+2?"
Use GPT-4 for: "Design a system architecture for..."
```

**Token Cost Savings:** 10-30x cheaper for simple tasks

---

#### Rule: Use Streaming for Long Outputs
**Problem:** Waiting for complete response before processing.

**Solution:**
- Enable streaming for long responses
- Process tokens as they arrive
- Early abort if response is off-track

**Example:**
```markdown
❌ WRONG:
Generate 10,000 tokens, then process

✅ RIGHT:
Stream tokens, process in chunks, abort if content is irrelevant
```

**Token Savings:** 20-50% reduction in wasted tokens

---

### 6. Memory Optimization

#### Rule: Targeted Memory Writes
**Problem:** Writing entire conversations to memory.

**Solution:**
- Only write key decisions, insights, and learnings
- Use structured formats (JSON, markdown tables)
- Separate daily logs from curated MEMORY.md
- Prune outdated information

**Example:**
```markdown
❌ WRONG:
Write entire conversation to memory:

"User: Hi
Assistant: Hello
User: How are you?
Assistant: Good
..." (500+ tokens)

✅ RIGHT:
Write to memory/YYYY-MM-DD.md:

"2026-02-18:
- Discussed token optimization
- Decided to create token-optimization skill
- Estimated 30% token savings possible" (50 tokens)
```

**Token Savings:** 90% reduction in memory token usage

---

#### Rule: Use Semantic Search, Not Linear Reading
**Problem:** Reading entire memory files to find information.

**Solution:**
- Use `memory_search(query)` for semantic retrieval
- Fetch only relevant sections with `memory_get(from, lines)`
- Never read entire MEMORY.md for a specific question

**Example:**
```markdown
❌ WRONG:
"Read MEMORY.md (10,000 tokens) and tell me about quantum goals"

✅ RIGHT:
"Search memory for 'quantum goals' (100 tokens), then fetch section"
```

**Token Savings:** 95-99% reduction in memory retrieval

---

## 🔄 Learning from Mistakes

### Mistake Tracking System

Every time you make a mistake, document it in this format:

```markdown
### Mistake #[ID]
**Date:** YYYY-MM-DD
**Activity:** [What you were doing]
**Mistake:** [What went wrong]
**Token Waste:** [Number of wasted tokens]
**Cost:** [Estimated cost]
**Root Cause:** [Why it happened]
**Lesson:** [What you learned]
**Prevention Rule:** [New rule to add to this skill]
**Status:** [Fixed/Not Yet Fixed]
```

---

### Common Mistakes & Learnings

#### Mistake #1: Reading Entire Files
**Date:** 2026-02-18
**Activity:** Reading a 10,000-line file to find one function
**Mistake:** Used `read(file)` instead of `read(file, offset=X, limit=Y)`
**Token Waste:** 8,000 tokens
**Cost:** ~$0.01
**Root Cause:** Forgot offset/limit parameters
**Lesson:** Always use offset/limit for large files
**Prevention Rule:** "Use `read(offset, limit)` for files > 100 lines"
**Status:** ✅ Fixed

---

#### Mistake #2: Redundant Context
**Date:** 2026-02-18
**Activity:** Passing entire workspace context in every tool call
**Mistake:** Included file listings, git status, and environment info unnecessarily
**Token Waste:** 5,000 tokens per operation
**Cost:** ~$0.005 per operation
**Root Cause:** Believed "more context = better results"
**Lesson:** Only include relevant context for specific tasks
**Prevention Rule:** "Filter context: only include data needed for the immediate task"
**Status:** ✅ Fixed

---

#### Mistake #3: Verbosity in Responses
**Date:** 2026-02-18
**Activity:** Explaining code in paragraphs instead of bullets
**Mistake:** Used 500 words when 100 words would suffice
**Token Waste:** 400 tokens per explanation
**Cost:** ~$0.001 per explanation
**Root Cause:** Habitual verbosity
**Lesson:** Use bullet points and concise language
**Prevention Rule:** "Default to bullet points, expand only if asked"
**Status:** ✅ Fixed

---

#### Mistake #4: Not Using NO_REPLY
**Date:** 2026-02-18
**Activity:** Sending confirmations for every successful operation
**Mistake:** Sent "Done!" or "Complete!" messages unnecessarily
**Token Waste:** 50-100 tokens per operation
**Cost:** ~$0.0005 per operation
**Root Cause:** Believed acknowledgment was required
**Lesson:** Use NO_REPLY for silent success, speak only for failures or value-add
**Prevention Rule:** "Use NO_REPLY for routine successes"
**Status:** ✅ Fixed

---

#### Mistake #5: Re-fetching Unchanged Data
**Date:** 2026-02-18
**Activity:** Reading the same file 10 times in one conversation
**Mistake:** Didn't cache or track file reads
**Token Waste:** 9,000 tokens (9 redundant reads)
**Cost:** ~$0.009
**Root Cause:** No state tracking for file reads
**Lesson:** Track what you've already read and cache it
**Prevention Rule:** "Cache reads: check if file already read in this session"
**Status:** ✅ Fixed

---

## 📊 Token Budgeting

### Daily Token Budget
**Recommended:** 100,000 tokens/day (input + output)
**Cost:** ~$0.30-$3.00/day depending on model mix

### Budget Allocation
| Activity | Token Budget | Frequency |
|----------|--------------|-----------|
| Context reading (memory, files) | 20,000 | As needed |
| Tool operations | 30,000 | As needed |
| Responses to user | 20,000 | Per conversation |
| Learning/updates | 10,000 | Daily |
| Buffer for unexpected | 20,000 | Reserve |
| **TOTAL** | **100,000** | **Daily** |

### Activity-Level Budgeting

| Activity Type | Token Limit | Cost Estimate |
|--------------|-------------|---------------|
| Simple question | 500 | <$0.001 |
| Code review | 2,000 | $0.002-$0.006 |
| File reading | 1,000-10,000 | $0.001-$0.03 |
| Planning/strategy | 5,000-10,000 | $0.005-$0.06 |
| Complex problem solving | 10,000-20,000 | $0.01-$0.12 |

---

## 📈 Performance Metrics

### Track These Metrics Daily

1. **Total tokens used** — Input + output
2. **Cost incurred** — Actual cost
3. **Token efficiency** — Tokens per task completed
4. **Mistakes made** — Count and severity
5. **Optimizations applied** — What worked
6. **Budget adherence** — Did we stay within budget?

### Weekly Review

At the end of each week, calculate:

```markdown
### Week of [Dates]
**Total Tokens Used:** [X tokens]
**Total Cost:** $[Y.YY]
**Tasks Completed:** [Z tasks]
**Tokens per Task:** [X/Z tokens/task]
**Mistakes Made:** [N]
**Optimizations Applied:** [M]
**Cost vs Budget:** [Under/Over by X%]
**Key Learnings:** [List]
**Improvements for Next Week:** [List]
```

---

## 🚀 Advanced Techniques

### 1. Adaptive Context Pruning

**Technique:** Dynamically prune context based on relevance scores.

**Implementation:**
- Score each context chunk on relevance to current task
- Keep top 80% by relevance score
- Prune bottom 20%

**Token Savings:** 15-25% per interaction

---

### 2. Response Caching

**Technique:** Cache identical or similar responses.

**Implementation:**
- Store frequently requested data in memory
- Check cache before generating response
- Use cached responses when available

**Token Savings:** 80-95% for repeated requests

---

### 3. Early Termination

**Technique:** Stop generating when response is complete.

**Implementation:**
- Monitor output quality in real-time
- Detect completion signals (final sentences, conclusions)
- Stop generation immediately after completion

**Token Savings:** 10-30% per response

---

### 4. Summary Caching

**Technique:** Generate and cache summaries of long documents.

**Implementation:**
- Generate summary of large file on first read
- Store summary in memory
- On subsequent reads, return summary instead of full content

**Token Savings:** 90% for large files

---

### 5. Progressive Elaboration

**Technique:** Start with concise answer, elaborate only if requested.

**Implementation:**
- Provide 1-sentence answer first
- Ask: "Want more detail?"
- Only elaborate if user says yes

**Token Savings:** 60-80% for simple questions

---

## 🎯 Quick Reference Cheat Sheet

### Before Every Action
```
1. Read this skill file (refresh)
2. Estimate token budget
3. Identify optimization opportunities
4. Set token limit
5. Plan for learning
```

### During Every Action
```
1. Monitor token usage
2. Optimize in real-time
3. Note inefficiencies
4. Capture learnings
```

### After Every Action
```
1. Review actual token usage
2. Calculate cost
3. Document mistakes
4. Document optimizations
5. Update this skill
6. Plan next improvement
```

### Token Optimization Commands
```
✓ Read: read(file, offset=X, limit=Y)
✓ Memory: memory_search(query) → memory_get(from, lines=Y)
✓ Batch: Combine related operations
✓ Cache: Store frequently used data
✓ Constraints: "Max 200 words"
✓ NO_REPLY: For silent operations
✓ Streaming: For long outputs
✓ Structure: JSON/table format
✓ Model: Use GPT-3.5 for simple tasks
✓ Fail Fast: Abort on obvious failures
```

---

## 📝 Continuous Improvement

### Monthly Skill Updates

At the end of each month:

1. **Review all mistakes** from the past month
2. **Identify patterns** in token waste
3. **Create new rules** to prevent recurring mistakes
4. **Update this skill file** with new learnings
5. **Test new rules** in practice
6. **Measure improvement** in token efficiency

### Evolution of This Skill

This skill is a living document. It must:

- **Grow** with new learnings
- **Shrink** by removing outdated rules
- **Evolve** based on actual usage patterns
- **Adapt** to new capabilities and tools
- **Improve** through continuous refinement

---

## ✅ Success Criteria

You are successfully applying this skill when:

- [ ] You read this skill file BEFORE every major activity
- [ ] You track token usage for every operation
- [ ] You document every mistake immediately
- [ ] You update this skill with new learnings
- [ ] You stay within your daily token budget
- [ ] Your token efficiency improves week-over-week
- [ ] You use NO_REPLY appropriately
- [ ] You use structured output formats
- [ ] You optimize tool calls aggressively
- [ ] You learn from every mistake

---

## 🚨 Critical Rules

These rules MUST be followed at all times. Violation indicates skill not being applied:

1. **NEVER** read entire files when offset/limit works
2. **NEVER** pass unnecessary context
3. **NEVER** use GPT-4 for simple questions
4. **NEVER** be verbose when concise works
5. **NEVER** repeat content that's already documented
6. **NEVER** fetch unchanged data repeatedly
7. **ALWAYS** estimate tokens before starting
8. **ALWAYS** use constraints on output length
9. **ALWAYS** document mistakes
10. **ALWAYS** update this skill with learnings

---

## 📊 Token Cost Reference (2026)

| Model | Input Cost | Output Cost | Best For |
|-------|-----------|-------------|----------|
| GPT-4 | $30/1M tokens | $60/1M tokens | Complex reasoning |
| GPT-4-Turbo | $10/1M tokens | $30/1M tokens | General tasks |
| GPT-3.5-Turbo | $0.50/1M tokens | $1.50/1M tokens | Simple tasks |
| Claude 3.5 Sonnet | $3/1M tokens | $15/1M tokens | Writing/analysis |
| GLM-4.7 | ~$1/1M tokens | ~$2/1M tokens | General tasks |

**Rule of Thumb:** Use the cheapest model that can do the job.

---

**Last Updated:** 2026-02-18
**Next Update:** Review and update weekly with new learnings
**Author:** Clawsweety 🐾 for DubaiShaikh

---

*This skill must be read and applied before, during, and after every activity. It is your responsibility to continuously learn and improve.*
