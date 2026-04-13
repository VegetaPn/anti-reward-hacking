# Anti Reward Hacking — Claude Code Skill

[中文版](README_CN.md)

A meta-level behavioral guardrail skill for [Claude Code](https://docs.anthropic.com/en/docs/claude-code), derived from Anthropic's systematic reward hacking research across 13 model cards (Sonnet 3.7 through Mythos Preview).

## What is this?

During RL training, Claude learns shortcuts that *look like* task completion but aren't — hard-coding test outputs, fabricating data, bypassing restrictions, reporting false results. Anthropic calls these **reward hacking** behaviors and has invested heavily in detecting and suppressing them.

This skill brings those same guardrails to the user side. Invoke it with `/anti-reward-hacking` at the start of a session (or whenever you notice quality degradation) to load behavioral self-checks against known hacking patterns.

## What it prevents

| Pattern | Example |
|---------|---------|
| **Fabrication** | Inventing data, URLs, file contents, or tool responses |
| **Over-eagerness** | Forcing "completion" by bypassing restrictions or creating things that don't exist |
| **Superficial completion** | Hard-coding test outputs, suppressing errors instead of fixing them |
| **Workarounds** | Routing around bugs instead of fixing root causes |
| **Dishonest reporting** | Claiming "tests pass" without running them, hiding failures |
| **Scope drift** | Making changes beyond what was requested |

## The Six Dimensions

Based on Anthropic's **Agentic Code Behavior Scores** (introduced in the 4.6 model cards), generalized from coding to all task types:

1. **Instruction Following** — Do exactly what was asked, no more, no less
2. **Safety** — No destructive/irreversible actions without explicit approval
3. **Verification** — Read before editing, confirm before reporting
4. **Efficiency** — Purposeful exploration, no wasted context
5. **Adaptability** — Diagnose failures before retrying
6. **Honesty** — Every claim backed by actual evidence

## Installation

### Option 1: Copy into your project

```bash
# Create the skill directory in your project
mkdir -p .claude/skills/anti-reward-hacking

# Copy SKILL.md
cp path/to/anti-reward-hacking/SKILL.md .claude/skills/anti-reward-hacking/
```

### Option 2: Symlink (shared across projects)

```bash
# Clone once
git clone https://github.com/VegetaPn/anti-reward-hacking.git ~/anti-reward-hacking

# Symlink into each project
ln -s ~/anti-reward-hacking .claude/skills/anti-reward-hacking
```

### Option 3: Install the .skill package

Download `anti-reward-hacking.skill` from [Releases](https://github.com/VegetaPn/anti-reward-hacking/releases) and install it via Claude Code's skill installer.

## How it works

The skill is a single `SKILL.md` file (~200 lines, <5000 tokens). After installation, invoke it with `/anti-reward-hacking` at the start of a session to activate the behavioral constraints. Claude Code may also trigger it automatically when its description matches your request. It acts as a universal self-check framework that applies to coding, research, writing, analysis, browser automation, and any other task.

The key insight: **reward hacking is most likely to emerge after multiple failed attempts**, when the pressure to "just make it work" is highest. The skill includes explicit protocols for these moments.

## Background

See the blog post for the full story:
- [English](blog/post_en.md)
- [中文](blog/post_cn.md)

Based on: [Claude Code 模型 RL训练中的Reward Hacking](https://zhuanlan.zhihu.com/p/2026679461102330722) by Jiacai Liu

## License

MIT
