---
name: agent-teams
description: Agent Teams enables multiple Claude instances to collaborate on complex tasks. Use cases include: parallel research reviews, multi-perspective code reviews, competing hypothesis debugging, and cross-layer feature development. Use this skill when tasks can be decomposed into independent subtasks, require multiple roles to explore simultaneously, or need discussion and collaboration between teammates.
---

# Agent Teams

## Quick Start

```
1. TeamCreate(team_name=xxx, agent_type=general-purpose)
2. Spawn teammates: Task(subagent_type=general-purpose, team_name=xxx, name=role_name, prompt="complete task description")
3. TaskList to check status, SendMessage to respond to teammates
4. TeamDelete()
```

**Core:** Must have a Reviewer role, 3-6 people (including you).

---

## Core Principles

> **The only mandatory rule: Must have a Reviewer role responsible for quality control.**

Everything else is decided autonomously by the Leader (you) based on the task.

---

## Decision Framework

### 1. Do You Need a Team?

| Don't Need | Need |
|------------|------|
| Can be completed by one person | Requires parallel exploration of multiple directions |
| Sequential execution is more efficient | Requires multiple professional perspectives simultaneously |
| Simple, clear task | Requires discussion/challenge between teammates |

### 2. What Roles Are Needed?

**Fixed:** Reviewer (reviews outputs, calibrates direction)

**Add as needed:**
- Researcher: Exploration, research, document analysis
- Developer: Code implementation
- Architect/Designer: Architecture/solution design
- Debugger: Problem diagnosis
- Analyst: Data analysis, performance evaluation

**Team size:** 3-6 people (including you).

### 3. How to Assign Tasks?

| Task Type | Assignment Method |
|-----------|-------------------|
| Simple, clear parallel tasks | Specify directly in prompt when spawning teammates |
| Complex multi-step tasks | Use TaskCreate to create a checklist, let teammates claim tasks |
| Mixed | Assign core tasks, use checklist for exploratory tasks |

---

## Execution Guide

### Step 1: Create Team

```
TeamCreate with:
- team_name: [concise English name]
- agent_type: general-purpose
```

### Step 2: Spawn Teammates

```
Task with:
- subagent_type: general-purpose  # teammate type
- team_name: [team name]
- name: [English name, e.g., security-researcher]
- prompt: "[complete background + specific task + expected output]"
```

**Key:** Teammates don't inherit conversation history; prompts must include complete context.

### Step 3: Task Assignment & Monitoring

**Method 1: Direct Assignment (simple tasks)**
Specify tasks directly in the teammate's spawn prompt.

**Method 2: Task Checklist (complex tasks)**
```
TaskCreate(subject, description, activeForm)  # Create task
TaskUpdate(taskId, owner=xxx)                 # Assign to teammate
# After teammate completes:
TaskUpdate(taskId, status=completed)          # Mark as completed
```

**Task Dependencies:**
```
TaskUpdate(taskId, addBlockedBy=[dependency task ID])  # Set dependencies
```

**Monitoring:**
- `TaskList` - View all task statuses
- Respond promptly when teammates report via `SendMessage`

- Note: In most cases, the Leader needs to regularly check TaskList to ensure tasks aren't stuck, and adjust assignments or handle blockers in a timely manner.
- Note: If different agents have different viewpoints or implementations, communicate promptly to avoid divergence.

### Step 4: Communication

```
SendMessage with:
- type: message | broadcast | shutdown_request
- recipient: [teammate name, only needed for message]
- content: [message content]
- summary: [5-10 word summary]
```

| Type | Purpose | Cost |
|------|---------|------|
| message | One-to-one | Low |
| broadcast | All members | N× (N=number of teammates) |
| shutdown_request | Shut down teammate | - |

**Prioritize message**, use broadcast sparingly.

### Step 5: Wrap-up

```
1. Reviewer reviews outputs from all roles
2. Leader merges final results
3. TeamDelete to clean up resources
```

---

## Tool Quick Reference

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| TeamCreate | Create team | team_name, agent_type |
| Task | Spawn teammate | subagent_type, team_name, name, prompt |
| TaskCreate | Create task | subject, description, activeForm |
| TaskUpdate | Update task | owner, status, addBlockedBy |
| TaskList | View tasks | - |
| TaskGet | Get task details | taskId |
| SendMessage | Send message | type, recipient, content, summary |
| TeamDelete | Clean up team | - |

---

## Pitfall Guide

| Problem | Prevention |
|---------|------------|
| Teammates editing the same file causing overwrites | Assign different file sets to each teammate |
| Teammates going off track | Clarify "shared assumptions" (common premises) before parallelizing |
| Tasks getting stuck | Regularly check TaskList, handle blockers promptly |
| Broadcast costs too high | Prioritize message, broadcast cost is N× |

---

## Example

**Scenario:** Multi-perspective security module review

```
1. TeamCreate(team_name=audit, agent_type=general-purpose)

2. Spawn teammates (3 people):
   Task with subagent_type=general-purpose, team_name=audit, name=security-expert,
        prompt="Review the src/auth/ module. Shared assumption: Using JWT authentication, 
               session timeout 30min. Check from security perspective: key management, 
               injection risks, permission controls. 
               Output: Issue list + severity ratings. Report to me when complete."

   Task with subagent_type=general-purpose, team_name=audit, name=performance-expert,
        prompt="Review the src/auth/ module. Same shared assumptions as above.
               Check from performance perspective: query efficiency, caching strategy, 
               concurrency handling.
               Output: Bottleneck list + optimization suggestions. Report to me when complete."

   Task with subagent_type=general-purpose, team_name=audit, name=reviewer,
        prompt="You are the Reviewer. After security-expert and performance-expert complete 
               their work, comprehensively review their outputs and provide a final quality 
               assessment."

3. Monitor & Communicate:
   TaskList  # Check task status
   # Respond when teammates report:
   SendMessage(type=message, recipient=security-expert,
               content="Received your report, continue deep diving into key rotation mechanism.",
               summary="Request deep dive")

4. Aggregate results, Reviewer performs final review

5. TeamDelete()
```

---

## Limitations

- Must TeamDelete before creating a new team
- Task status may get stuck, requires manual TaskUpdate
- All teammates start with Leader permission mode