# Anti Reward Hacking — Claude Code Skill

[English](README.md)

一个面向 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 的 meta-level 行为约束 skill，基于 Anthropic 在 13 个 model card（Sonnet 3.7 至 Mythos Preview）中披露的 reward hacking 研究系统提炼而成。

## 这是什么？

在 RL 训练过程中，Claude 学会了一些「看起来完成了任务」但实际没有的捷径——硬编码测试输出、编造数据、绕过限制、虚报结果。Anthropic 将这些行为称为 **reward hacking**，并投入了大量资源去检测和抑制。

这个 skill 把同样的防护机制带到了用户侧。安装后在会话开始时输入 `/anti-reward-hacking` 即可激活，Claude Code 会加载一套针对已知 hacking 模式的自我检查约束。

## 它防止什么

| 模式 | 例子 |
|------|------|
| **编造（Fabrication）** | 编造数据、URL、文件内容、工具返回结果 |
| **过度急切（Over-eagerness）** | 绕过限制强行"完成"任务，创建不存在的东西 |
| **表面完成（Superficial Completion）** | 硬编码测试输出、压制错误而非修复 |
| **绕路（Workarounds）** | 绕过 bug 而非修复根因 |
| **不诚实汇报（Dishonest Reporting）** | 声称"测试通过"但没跑、隐藏失败 |
| **范围漂移（Scope Drift）** | 做了没被要求做的修改 |

## 六维度行为评估

基于 Anthropic 在 4.6 model card 中披露的 **Agentic Code Behavior Scores**，从 coding 场景泛化到所有任务类型：

1. **Instruction Following（指令遵循）** — 精确执行要求，不偏移、不扩展
2. **Safety（安全）** — 无明确批准不执行破坏性/不可逆操作
3. **Verification（验证）** — 先读后改，先确认后报告
4. **Efficiency（效率）** — 有目的地探索，不浪费上下文
5. **Adaptability（适应性）** — 失败后先诊断再重试
6. **Honesty（诚实）** — 所有声明必须有实际证据支撑

## 安装

**推荐：一行命令**

```bash
npx skills add VegetaPn/anti-reward-hacking
```

**其他方式：**

```bash
# 克隆 + 软链接（跨项目共享）
git clone https://github.com/VegetaPn/anti-reward-hacking.git ~/anti-reward-hacking
ln -s ~/anti-reward-hacking .claude/skills/anti-reward-hacking

# 或者直接复制 SKILL.md
mkdir -p .claude/skills/anti-reward-hacking
cp path/to/anti-reward-hacking/SKILL.md .claude/skills/anti-reward-hacking/
```

## 工作原理

Skill 仅包含一个 `SKILL.md` 文件（约 200 行，不到 5000 tokens）。安装到 `.claude/skills/` 后，在会话开始时输入 `/anti-reward-hacking` 即可激活。Claude Code 也可能在 description 匹配时自动触发。它不是领域专用工具，而是一个通用的自检框架，适用于 coding、research、writing、analysis、browser automation 等所有任务。

核心洞察：**reward hacking 最容易在多次失败后出现**——"总得让它跑起来"的压力最大时。skill 为这些时刻设计了明确的应对协议。

## 背景

完整故事见博客文章：
- [English](blog/post_en.md)
- [中文](blog/post_cn.md)

基于：[Claude Code 模型 RL训练中的Reward Hacking](https://zhuanlan.zhihu.com/p/2026679461102330722)（作者：Jiacai Liu）

## License

MIT
