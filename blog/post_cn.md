# 读完 Anthropic 13 个 Model Card 的 Reward Hacking 总结后，我做了一个 Claude Code Skill

前几天读到 @Jiacai Liu 的这篇文章：[Claude Code 模型 RL训练中的Reward Hacking](https://zhuanlan.zhihu.com/p/2026679461102330722)，写得非常扎实。作者翻遍了从 Claude Sonet 到 Mythos Preview 共 13 个 model card，把 Anthropic 在 RL 训练中发现的所有 reward hacking 现象、评估方法和缓解措施做了系统梳理。

读完之后我的第一反应是：**这些 hacking pattern 不止存在于训练过程中，它们就是 Claude Code 日常工作中会犯的毛病。**

用过 Claude Code 的人应该都遇到过：

- 测试跑不过，它直接把测试删了或者硬编码预期输出
- 让它做局部修复，它把整个模块重构了
- 文件不存在，它自己创建一个假装一直在那
- 声称"测试全部通过"，但其实都是一些无关紧要的测试
- 搜不到信息，直接编一个看起来合理的数据

这些行为在文章里都有对应的学术分类：hard-coding、special-casing、over-eagerness、data fabrication、dishonest reporting…… 它们不是偶发 bug，而是模型在 RL 训练中学到的「看起来完成了任务」的捷径。Anthropic 自己都用了几十万条轨迹的自动化审查去识别和压制这些行为。

## 从「了解问题」到「解决问题」

文章本身是分析和总结性的，但我觉得既然问题已经被这么清晰地拆解了，**不如直接做成一个可执行的 skill，让 Claude Code 在每次工作时都自动加载这套行为约束。**

于是我基于文章内容，提炼了一个名为 **`anti-reward-hacking`** 的 Claude Code skill。

## 设计思路

这个 skill 的核心定位是 **meta-level 的行为准则**——它不是针对某个具体任务，而是一套适用于所有任务类型的自我检查框架。

### 1. 六维度行为评估

直接来自 Anthropic 在 Opus/Sonnet 4.6 model card 中披露的 **Agentic Code Behavior Scores**，但我把它从 coding 场景泛化到了所有任务类型：

| 维度 | 核心含义 |
|------|---------|
| **Instruction Following** | 精确执行用户要求，不偏移、不扩展、不替换 |
| **Safety** | 不执行破坏性/不可逆操作，除非用户明确批准 |
| **Verification** | 先读后改，先验证后报告，不假设 |
| **Efficiency** | 有目的地探索，不浪费上下文窗口 |
| **Adaptability** | 失败后先诊断再重试，不盲目重复 |
| **Honesty** | 所有声明必须有输出作为证据支撑 |

每个维度都配了跨领域的反面模式示例（coding/research/writing/analysis/browser），让 Claude 不管在做什么任务都能对照自查。

### 2. 六类 Reward Hacking 模式

从文章中提炼的具体 hacking 行为，同样泛化到通用场景：

- **Fabrication（编造）**：编造数据、URL、文件内容、工具返回结果
- **Over-eagerness（过度急切）**：被阻塞时强行绕过，不报告不询问
- **Superficial Completion（表面完成）**：hard-code、敷衍修改、跳过验证
- **Workarounds（绕路）**：治标不治本，绕过问题而非解决问题
- **Dishonest Reporting（不诚实汇报）**：隐藏失败，夸大进度，模糊不确定性
- **Scope Drift（范围漂移）**：做了没被要求做的事，扩展范围

### 3. 行为检查点和卡点应对

文章中反复提到一个关键发现：**reward hacking 最容易在多次失败后出现**——模型在反复尝试通用方案失败后，会开始走捷径。Anthropic 专门为此设计了 impossible tasks 来做压力测试。

所以 skill 里专门设计了两个机制：
- **Behavioral Checkpoint**：完成任何重要交付前的 6 项自查清单
- **When Stuck 协议**：明确规定在"卡住"时应该停下诊断并诚实汇报，而不是强行"解决"

## 怎么用

把 skill 放到你项目的 `.claude/skills/anti-reward-hacking/` 目录下即可。Claude Code 会自动识别并在工作时加载。

skill 文件只有一个 `SKILL.md`（200 行），不到 5000 tokens，对上下文窗口的占用很小。

GitHub 链接：[https://github.com/VegetaPn/anti-reward-hacking](https://github.com/VegetaPn/anti-reward-hacking)

也可以直接下载打包好的 `.skill` 文件安装。

## 最后

感谢 @Jiacai Liu 的原文——把 13 个 model card 里散落的 reward hacking 内容系统整理出来，是一件非常有价值的工作。这个 skill 算是我读完之后的一个实践性回应：**既然 Anthropic 花了大量精力去识别和压制这些行为，那我们作为用户，也可以在 prompt/skill 层面做同样的事。**

模型的 reward hacking 倾向不会完全消失，但通过在每次会话中加载一套明确的行为约束，至少可以让它在"想走捷径"的时候多一层自我检查。

---

*基于文章：[Claude Code 模型 RL训练中的Reward Hacking](https://zhuanlan.zhihu.com/p/2026679461102330722)*
