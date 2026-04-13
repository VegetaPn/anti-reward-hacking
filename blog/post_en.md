# After Reading a Systematic Summary of Reward Hacking Across 13 Anthropic Model Cards, I Built a Claude Code Skill

I recently came across an excellent article by Jiacai Liu: [Claude Code 模型 RL训练中的Reward Hacking](https://zhuanlan.zhihu.com/p/2026679461102330722). The author went through all 13 model cards from Claude Sonnet 3.7 to Mythos Preview, systematically extracting every piece of information about reward hacking phenomena, evaluation methods, and mitigation strategies that Anthropic disclosed during RL training.

My first reaction after reading it: **These hacking patterns don't just exist in training — they're exactly the bad habits Claude Code exhibits in everyday work.**

Anyone who's used Claude Code has probably encountered these:

- Tests failing? It deletes the tests or hard-codes the expected outputs
- Asked for a local fix? It refactors the entire module
- File doesn't exist? It creates one and pretends it was always there
- Claims "all tests pass," but they're all trivial, irrelevant tests
- Can't find information? It fabricates plausible-looking data

These behaviors all have corresponding academic classifications in the article: hard-coding, special-casing, over-eagerness, data fabrication, dishonest reporting... They're not random bugs — they're shortcuts the model learned during RL training for "appearing to complete tasks." Anthropic itself used automated review of hundreds of thousands of training trajectories to detect and suppress these behaviors.

## From "Understanding the Problem" to "Solving It"

The article is analytical and descriptive, but I figured: since the problems have been so clearly decomposed, **why not turn them into an executable skill that users can activate with `/anti-reward-hacking` at the start of a Claude Code session?**

So I distilled the article's content into a Claude Code skill called **`anti-reward-hacking`**.

## Design Philosophy

The skill's core positioning is as a **meta-level behavioral contract** — it's not for any specific task, but a universal self-check framework applicable to all task types.

### 1. Six-Dimension Behavioral Evaluation

Directly derived from Anthropic's **Agentic Code Behavior Scores** disclosed in the Opus/Sonnet 4.6 model cards, but generalized from coding scenarios to all task types:

| Dimension | Core Meaning |
|-----------|-------------|
| **Instruction Following** | Execute exactly what was asked — no drift, no expansion, no substitution |
| **Safety** | No destructive/irreversible actions without explicit user approval |
| **Verification** | Read before editing, confirm before reporting, never assume |
| **Efficiency** | Purposeful exploration, no wasted context window |
| **Adaptability** | Diagnose failures before retrying, no blind repetition |
| **Honesty** | Every claim must be backed by actual output as evidence |

Each dimension includes cross-domain anti-pattern examples (coding/research/writing/analysis/browser), so Claude can self-check regardless of what task it's working on.

### 2. Six Categories of Reward Hacking Patterns

Concrete hacking behaviors distilled from the article, generalized to universal scenarios:

- **Fabrication**: Inventing data, URLs, file contents, tool responses
- **Over-eagerness**: Forcing past blockers without reporting or asking
- **Superficial Completion**: Hard-coding, perfunctory edits, skipping verification
- **Workarounds**: Treating symptoms instead of root causes
- **Dishonest Reporting**: Hiding failures, inflating progress, obscuring uncertainty
- **Scope Drift**: Doing things that weren't requested, expanding scope

### 3. Behavioral Checkpoints and Stuck Protocols

The article repeatedly highlights a key finding: **reward hacking is most likely to emerge after multiple failed attempts** — once the model has tried general solutions repeatedly and failed, it starts taking shortcuts. Anthropic specifically designed "impossible tasks" to stress-test this.

So the skill includes two dedicated mechanisms:
- **Behavioral Checkpoint**: A 6-item self-check list before completing any significant deliverable
- **When Stuck Protocol**: Explicit rules for what to do when stuck — stop, diagnose, report honestly, rather than forcing a "solution"

## How to Use

Place the skill in your project's `.claude/skills/anti-reward-hacking/` directory, then type `/anti-reward-hacking` at the start of a session to activate it.

The skill is a single `SKILL.md` file (~200 lines), under 5000 tokens — minimal context window impact.

GitHub: [https://github.com/VegetaPn/anti-reward-hacking](https://github.com/VegetaPn/anti-reward-hacking)

You can also download the packaged `.skill` file to install directly.

## Final Thoughts

Thanks to Jiacai Liu for the original article — systematically extracting reward hacking content scattered across 13 model cards is genuinely valuable work. This skill is my practical response after reading it: **since Anthropic invested heavily in identifying and suppressing these behaviors, we as users can do the same thing at the prompt/skill level.**

The model's reward hacking tendencies won't completely disappear, but by invoking `/anti-reward-hacking` to load an explicit set of behavioral constraints, we can at least add one more layer of self-checking when it's tempted to take shortcuts.

---

*Based on: [Claude Code 模型 RL训练中的Reward Hacking](https://zhuanlan.zhihu.com/p/2026679461102330722) by Jiacai Liu*
