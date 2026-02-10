# Agent Teams Skill for Claude Code

A skill that enables multiple Claude instances to collaborate on complex tasks.

## Installation

### Option 1: Manual Installation

1. Clone or download this repository
2. Copy the `agent-teams` folder to your skills directory:

   **Windows:**
   ```
   %USERPROFILE%\.claude\skills\agent-teams\
   ```

   **macOS/Linux:**
   ```
   ~/.claude/skills/agent-teams/
   ```

3. Restart Claude Code

### Option 2: Git Clone

```bash
# Clone directly to skills directory
git clone https://github.com/whatevertogo/agent-team-skill.git "%USERPROFILE%\.claude\skills\agent-teams"
```

## Usage

Once installed, use the skill with the `/agent-teams` command in Claude Code.

## Features

- **Parallel Research Reviews**: Multiple agents explore different research directions simultaneously
- **Multi-perspective Code Reviews**: Different roles review code from various angles (security, performance, architecture)
- **Competitive Hypothesis Debugging**: Agents test different hypotheses in parallel
- **Cross-layer Feature Development**: Teams work across frontend, backend, and infrastructure layers

## Quick Example

```
1. TeamCreate(team_name=audit, agent_type=general-purpose)
2. Spawn teammates with specific roles
3. TaskList to monitor progress
4. SendMessage to coordinate
5. TeamDelete() when complete
```

## Documentation

See [SKILL.md](agent-teams/SKILL.md) for complete documentation.

## License

MIT
