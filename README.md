# WIN: Complete Coverage Framework

An [Agent Skill](https://agentskills.io) that forces AI coding agents to be systematically thorough — catching edge cases, failure modes, security issues, and hidden assumptions that get missed in typical AI-assisted development.

## The Problem

AI coding agents have two blind spots:

**1. Technical blind spots** — They code the happy path and call it done. Error states, race conditions, security implications, and failure modes get missed. When fixing bugs, they patch the symptom instead of finding the root cause.

**2. Assumption blind spots** — They build for the test case in front of them, not for every real user and scenario. If you're testing with one type of data, they assume that's the only type. If you're working in one language, they build only for that language. They never step back and ask: *"who else will use this and how?"*

## How It Works

WIN injects a structured 5-phase methodology that runs automatically whenever the agent works on non-trivial tasks (planning, implementing, fixing bugs, writing tests, reviewing code, investigating issues). It skips trivial changes like typos, renames, and config updates.

### The 5 Phases

| Phase | Focus | Core Question |
|---|---|---|
| **1. Planning** | Think like an attacker | What could go wrong? What am I assuming? |
| **2. Implementation** | Defensive by default | Is every path handled? |
| **3. Testing** | Prove it fails gracefully | Can I break this? |
| **4. Analysis & Review** | Audit like a senior engineer | What did I miss? |
| **5. Final Verification** | The "Ship It" checklist | Is this actually ready? |

### Phase 1: Planning — Think Like an Attacker

Before writing any code, the agent must work through:

- **Input space analysis** — all valid, boundary, invalid, malicious, and concurrent inputs for every parameter
- **State space analysis** — every state the system can be in: loading, empty, error, success, partial, stale
- **Failure mode analysis** — for every external dependency: what if it's slow, fails, returns unexpected data, partially succeeds, or the user retries while it's in-flight
- **Security threat model** — authentication, authorization, data exposure, injection, rate limiting
- **Assumption discovery** — identify what the current context is assuming about who uses this, what data they provide, and how they use it. Challenge each assumption. Enumerate what SHOULD work but isn't being considered, and what should NOT work but isn't being explicitly handled
- **Impact analysis** — what existing features could break, what data could corrupt, what's the rollback plan
- **9-point completeness checklist** before the plan is finalized

### Phase 2: Implementation — Defensive by Default

- Every `await` has error handling, every array access is bounds-checked, every optional chain has a fallback
- 6 mandatory UI states for every data-displaying component: loading, empty, error, success, partial, stale
- 8-point completeness check after every piece of code — including whether the implementation is built around assumptions from the current test case, and whether unsupported scenarios are explicitly handled or will silently fail

### Phase 3: Testing — Prove It Works, Then Prove It Fails Gracefully

7 mandatory test categories:

- **Happy path** — verify exact output, not just "no errors"
- **Input boundary** — empty, min, max, over-limit, special characters
- **Error path** — network failure, expired auth, invalid API response, rate limit
- **State transition** — loading to success, loading to error to retry, navigation during async
- **Security** — unauthorized returns 401/403 not 500, cross-user blocked, injection safe
- **Assumption** — test with scenarios beyond the immediate test case, and test scenarios that should NOT work to verify they're properly rejected
- **Regression** — existing functionality still works

### Phase 4: Analysis & Review — Audit Like a Senior Engineer

When reviewing code, investigating bugs, or improving features:

- Trace the full data flow, not just the mentioned file
- Check ALL callers of shared functions
- Search for the same bug pattern elsewhere in the codebase
- Challenge the assumption that caused the bug — does the fix hold for all real-world scenarios, not just the reported case?
- Verify the fix prevents recurrence for new users, new data, new features
- Check if the current implementation only handles the tested scenario or the full range of real-world usage

### Phase 5: Final Verification — The "Ship It" Checklist

18-point checklist across four categories before anything is called done:

- **Code quality** — zero TypeScript errors, no lint warnings, no `any` types, no debug statements, no hardcoded values
- **Functional completeness** — every requirement addressed, every edge case handled, error states have user-facing feedback, the feature works beyond the specific scenario it was built and tested with
- **Safety** — no OWASP top 10 vulnerabilities, no data leaks, auth enforced on every endpoint, migrations backwards-compatible
- **Resilience** — external failures handled gracefully, user can recover from errors, data integrity maintained through interruptions, no race conditions

## Assumption Discovery: The Key Differentiator

Most quality frameworks stop at technical thoroughness — error handling, security, testing. WIN goes further by forcing the agent to **discover and challenge hidden assumptions** at every phase.

The skill does NOT give the agent a predefined checklist of assumptions to look for. Instead, it forces the agent to figure out what the assumptions are for the specific app and feature it's working on. The assumptions for an e-commerce app are completely different from a scheduling app or a chatbot. The agent has to discover them from context.

This applies across all task types:

- **Planning a feature:** *"What am I assuming about who uses this, what data they'll provide, and how they'll use it?"*
- **Implementing code:** *"Is this built around assumptions from my current test case? Would it break for a user in a different context?"*
- **Fixing a bug:** *"Did this bug exist because the code assumed a specific type of user, data, or context? Does my fix hold for all scenarios?"*
- **Testing:** *"Am I only testing the scenario in front of me? What other scenarios should work? What scenarios should explicitly NOT work?"*
- **Reviewing code:** *"Is this code making assumptions about who uses it that would break for someone in a different situation?"*

Both sides are covered: what SHOULD work that isn't being considered, and what should NOT work that isn't being explicitly rejected or handled.

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

## Compatible Tools

This skill follows the [Agent Skills open standard](https://agentskills.io) and works with:

- [Claude Code](https://claude.ai/code)
- [OpenAI Codex CLI](https://github.com/openai/codex)
- [Cursor](https://cursor.com)
- [Amp](https://amp.dev)
- [OpenCode](https://opencode.ai)
- Any tool adopting the Agent Skills spec

## File Structure

```
win-skill/
├── skills/
│   └── win/
│       └── SKILL.md    # The skill definition
├── LICENSE             # MIT
└── README.md
```

## License

MIT
