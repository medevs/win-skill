# WIN: Complete Coverage Framework

An [Agent Skill](https://agentskills.io) that forces AI coding agents to be systematically thorough — catching edge cases, failure modes, security issues, and blind spots that get missed in typical AI-assisted development.

## The Problem

AI coding assistants tend to:
- Code the happy path and call it done
- Say "looks good" without proving coverage
- Miss error states, security implications, and race conditions
- Fix symptoms instead of root causes
- Build for the test case in front of them, not for every real user and scenario

## The Solution

WIN injects a structured 5-phase methodology that runs automatically on non-trivial tasks:

| Phase | Focus | Key Question |
|---|---|---|
| **1. Planning** | Think like an attacker | What could go wrong? |
| **2. Implementation** | Defensive by default | Is every path handled? |
| **3. Testing** | Prove it fails gracefully | Can I break this? |
| **4. Analysis & Review** | Audit like a senior engineer | What did I miss? |
| **5. Final Verification** | The "Ship It" checklist | Is this actually ready? |

### What It Enforces

- **6 mandatory UI states** for every component: loading, empty, error, success, partial, stale
- **7 mandatory test categories**: happy path, boundary, error path, state transition, security, assumption, regression
- **Assumption discovery**: forces the agent to identify what the current context is assuming about users, data, and usage — then challenge each assumption
- **Input space analysis**: valid, boundary, invalid, malicious, concurrent inputs
- **Failure mode analysis**: slow, failed, unexpected, partial success, retry-while-in-flight
- **Security threat model**: auth, authz, data exposure, injection, rate limiting
- **18-point ship-it checklist** across code quality, completeness, safety, and resilience

### When It Activates

**Auto-invokes on:** planning features, implementing code, fixing bugs, writing tests, analyzing code, auditing features, reviewing architecture, investigating issues.

**Skips:** typos, renames, single-line fixes, adding imports, updating config values.

## Compatible Tools

This skill follows the [Agent Skills open standard](https://agentskills.io) and works with:

- [Claude Code](https://claude.ai/code)
- [OpenAI Codex CLI](https://github.com/openai/codex)
- [Cursor](https://cursor.com)
- [Amp](https://amp.dev)
- [OpenCode](https://opencode.ai)
- Any tool adopting the Agent Skills spec

## Installation

### One-command install (recommended)

Using [skills.sh](https://skills.sh):

```bash
npx skills add medevs/win-skill
```

This works with Claude Code, Cursor, Codex, Copilot, Windsurf, and [18+ other agents](https://skills.sh).

### Manual install

Copy the skill to your personal skills directory (applies to all projects):

```bash
git clone https://github.com/medevs/win-skill.git
mkdir -p ~/.claude/skills/win
cp win-skill/skills/win/SKILL.md ~/.claude/skills/win/SKILL.md
```

Or add to a specific project only:

```bash
mkdir -p .claude/skills/win
cp win-skill/skills/win/SKILL.md .claude/skills/win/SKILL.md
```

## Usage

Once installed, WIN activates automatically on non-trivial tasks. No slash command needed.

You can also reference it explicitly:

```
Use the win framework to review this authentication flow.
```

```
Plan this feature using the win methodology.
```

## File Structure

```
win-skill/
├── skills/
│   └── win/
│       └── SKILL.md    # The skill definition
├── LICENSE             # MIT
└── README.md           # You are here
```

## License

MIT
