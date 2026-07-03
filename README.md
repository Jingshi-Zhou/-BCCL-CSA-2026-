# CCL2026 第一届跨语言文学情感分析一致性评测

西交利物浦大学 & 哈尔滨工业大学（深圳）

## 评测组织者

- 张海洋（西交利物浦大学）
- 张霄军（西交利物浦大学）
- 徐睿峰（哈尔滨工业大学（深圳））

## 联系人及联系方式

任务联系人：周静狮（Jingshi.Zhou@outlook.com）



第二十五届中国计算语言学大会（The 25th China National Conference on Computational Linguistics, CCL 2026）将于 2026 年 10 月 15 至 18 日在湖北省宜昌市举行。中国计算语言学大会创办于 1991 年，由中国中文信息学会计算语言学专业委员会主办。经过 30 余年的发展，中国计算语言学大会已成为国内自然语言处理领域权威性最高、规模和影响最大的学术会议。作为中国中文信息学会（国内一级学会）的旗舰会议，CCL 聚焦于中国境内各类语言的智能计算和信息处理，为研讨和传播计算语言学最新学术和技术成果提供了最广泛的高层次交流平台。



## 1 任务介绍

跨语言情感分析一致性（**Cross-lingual Sentiment Consistency**）是指同一语义内容在不同语言表达下，模型给出的情感判断是否保持一致。这一能力是衡量多语言模型语义对齐质量与跨语言鲁棒性的关键指标。

- **背景挑战：** 中国古典文学常通过意象、典故、隐喻与语境传递情感，显性情感词较少，跨语言迁移与一致性判断难度高。
- **评测缺口：** 当前公开评测中，针对"文学文本 + 中英平行语料 + 情感一致性"的专门基准仍较少。

为此，BCCL-CSA 2026 聚焦古典文学语境下的大模型跨语言情感理解、迁移与一致性评估，具体包含以下三个子任务：

- *子任务一：单语言情感分类（Monolingual Sentiment Classification）**
在中文或英文单语输入条件下，预测该文本的情感极性标签（3 分类）与情绪分布（7 维概率向量），评估模型对文学文本细粒度情感的单语理解能力。

- **子任务二：跨语言零样本迁移（Cross-lingual Zero-shot Transfer）**
在固定划分上进行跨语言迁移评估（中文训练/验证→英文测试；英文训练/验证→中文测试），重点考察模型跨语言迁移能力与语义对齐稳定性。

- **子任务三：平行文本一致性评估（Parallel Consistency Evaluation）**
对同一 `zh-en` 平行句对进行一致性建模，评估中英文侧预测在极性与情绪分布上的一致程度，重点衡量模型在双语场景中的情感对齐质量。

---

## 2 评测数据

本次评测数据集 **CCL-SEL** 由中文文学文本及其人工英文译文构成的平行语料组成，数据来源涵盖哲学、小说、戏曲等体裁的 **11 部**经典著作，总规模为 **2607 组**中英句对。数据划分为训练集、验证集和测试集三部分。

### 2.1 标注信息

| 标注维度 | 类型 | 说明 |
|---------|------|------|
| 情感极性（Sentiment） | 3 分类 | `Positive` / `Neutral` / `Negative` |
| 情绪分布（Emotion） | 7 维概率向量 | Anger, Disgust, Fear, Joy, Sadness, Surprise, Neutral |


### 2.2 发布策略

<!--| 数据集 | 文本 | 标签 |
|--------|------|------|
| 训练集 | 公开 | 公开 |
| 验证集（A 榜） | 公开 | 公开 |
| 测试集（B 榜） | 公开 | 隐藏，用于最终评测 |
-->

### 2.3 数据样例

本次评测提供 JSON 格式的数据集，以下为数据样例：

```json
{
  "id": 1,
  "source_book": "caigentan",
  "zh": {
    "text": "栖守道德者，寂寞一时；依附权势者，凄凉万古。故达人观物外之物，思身后之身，宁受一时之寂寞，毋取万古之凄凉。",
    "sentiment": {
      "label": "neutral",
      "point": -0.5
    },
    "emotion": {
      "angry": 0.05,
      "disgusted": 0.05,
      "fearful": 0.1,
      "happy": 0,
      "sad": 0.4,
      "surprised": 0,
      "neutral": 0.4
    }
  },
  "en": {
    "text": "If you strive to make Truth your home, You may sometimes be lonely; But fawning on the powerful and influential, You will feel the chill of solitude for the ages.",
    "sentiment": {
      "label": "negative",
      "point": -0.5
    },
    "emotion": {
      "angry": 0.1,
      "disgusted": 0,
      "fearful": 0.2,
      "happy": 0,
      "sad": 0.4,
      "surprised": 0,
      "neutral": 0.3
    }
  }
}
```



### 2.4 数据字段说明

| 字段名称 | 类型 | 描述 |
|---------|------|------|
| id | string | 样本唯一编号 |
| zh_text | string | 中文原文文本 |
| en_text | string | 对应英文译文文本 |
| sentiment | string | 情感极性标签：`Positive` / `Neutral` / `Negative` |
| emotion | object | 7 维情绪概率分布，各维度值之和为 1 |
| source | string | 文本所属作品及作者 |
| genre | string | 文本体裁（诗歌/小说/戏曲/哲学等） |



### 2.5 结果提交
<!--
提交的结果文件须遵循数据样例的 JSON 格式，每条数据应为一个 JSON 对象，包含样例中的所有字段，并补充模型预测字段。格式示意如下：

```json
[
  {
    "_说明": "子任务一：中文预测（每行一条 JSON，文件如 zh_submit.jsonl）",
    "id": 2,
    "sentiment_label": "neutral",
    "sentiment_point": 0.0,
    "emotion": {
      "angry": 0.05,
      "disgusted": 0.05,
      "fearful": 0.1,
      "happy": 0.0,
      "sad": 0.35,
      "surprised": 0.0,
      "neutral": 0.45
    }
  },
  {
    "_说明": "子任务一：英文预测（每行一条 JSON，文件如 en_submit.jsonl）",
    "id": 2,
    "sentiment_label": "negative",
    "sentiment_point": -0.35,
    "emotion": {
      "angry": 0.08,
      "disgusted": 0.02,
      "fearful": 0.15,
      "happy": 0.0,
      "sad": 0.45,
      "surprised": 0.0,
      "neutral": 0.3
    }
  },
  {
    "_说明": "子任务二：跨语迁移时增加 target_lang，表示预测目标语种（zh 或 en）",
    "id": 2,
    "target_lang": "en",
    "sentiment_label": "negative",
    "sentiment_point": -0.35,
    "emotion": {
      "angry": 0.08,
      "disgusted": 0.02,
      "fearful": 0.15,
      "happy": 0.0,
      "sad": 0.45,
      "surprised": 0.0,
      "neutral": 0.3
    }
  },
  {
    "_说明": "子任务三：平行句对一致性（每行一条 JSON）",
    "id": 2,
    "consistency_score": 0.92
  }
]
```


将结果文件直接压缩为 `submit.zip`，提交时请确保上传文件名为 `submit.zip`。

-->

---


## 3 赛道设置
<!--
本次评测设置两个赛道，分为不微调和微调赛道。两条赛道的评分指标相同，最终榜单各自独立排名。

| 赛道 | 允许资源/方法 |
|------|------------|
| Track-A（不微调） | 不允许对模型参数进行微调，可使用 Prompt Engineering、In-Context Learning、RAG 等方法 |
| Track-B（微调） | 允许在官方提供的数据集上微调模型参数 |

**注意：**

- Track-A 赛道不限制模型选型和参数规模；
- Track-A 赛道允许使用外部数据，但最终须确切提供数据源及对应外部数据说明；
- Track-B 赛道限制模型参数不超过 7B（按通常认知的 7B 级别计算，而非绝对数值严格限制），参赛者可选择主流的 7B 级开源模型进行微调；
- Track-B 赛道要求开发阶段采用的策略不会改变最终提交模型的单模型属性和参数规模。
-->
---

## 4 评价标准
<!--
### 4.1 子任务一 & 子任务二（情感分类与零样本迁移）

- **Sentiment（情感极性）：** Accuracy、Macro-F1 等分类指标
- **Emotion（情绪分布）：** 分布距离类指标（如 KL 散度、JS 散度、MSE 等）

各指标计算方式如下：

$$\text{Precision} = \frac{TP}{TP + FP}$$

$$\text{Recall} = \frac{TP}{TP + FN}$$

$$\text{F1} = \frac{2 \times \text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$$

### 4.2 子任务三（平行文本一致性评估）

- **Consistency（跨语一致性）：** 面向平行句对的跨语一致性评分指标，具体计算方式以最终评测细则为准。
-->
---

## 5 榜单说明
<!--
**子任务一 & 子任务二评分：**

$$\text{Score}_{\text{Task1/2}} = 0.5 \times F1 + 0.25 \times \text{Precision} + 0.25 \times \text{Recall}$$

**子任务三评分：**

$$\text{Score}_{\text{Task3}} = \text{Consistency Score（以最终评测细则为准）}$$

**最终榜单分数：**

$$\text{Total Score} = w_1 \times \text{Score}_{\text{Task1}} + w_2 \times \text{Score}_{\text{Task2}} + w_3 \times \text{Score}_{\text{Task3}}$$

> ⚠️ **注**：各子任务权重 $w_1, w_2, w_3$ 及最终加权公式以官方最终说明为准，上式为格式示意。

Track-A 和 Track-B 将分别发布综合榜单，每条赛道的奖项设置如下：一等奖 1 个，二等奖 2 个，三等奖 3 个。

---

## 6 Baseline

> ⚠️ **注**：以下 Baseline 性能数据待补充，将于训练集发布后更新。

| Track | Score |
|-------|-------|
| Track-A | 待补充 |
| Track-B | 待补充 |

- Track-A 基于 Prompt Engineering，调用主流大语言模型完成情感分析任务；
- Track-B 基于主流微调框架，对 7B 级开源模型进行监督微调（SFT）并完成后续推理与评估。
-->

---

## 7 评测报名

本次评测任务报名入口：[Google 表单报名](https://docs.google.com/forms/d/1_6uvZvzkRY70egxr7KpQM5i6oQcucDGY0PSqfyf9vRI/edit)

---

## 8 评测赛程

| 时间 | 事项 |
|------|------|
| 2026 年 2 月 | 评测任务发布 & 宣传 |
| 2026 年 5 月 15 日 | 数据发布 |
| 2026 年 6 月 18 日 | 评分细则发布 |
| 2026 年 6 月 30 日 | Leaderboard 发布 |
| 2026 年 7 月 15 日 | 评测任务结束 |
| 2026 年 7 月 15-20 日 | 任务组织者、获奖队伍撰写技术报告 |
| 2026 年 7 月 20 日 | 提交技术报告 |
| 2026 年 7 月 31 日 | 评测论文审稿 & 录用通知 （暂定）|
| 2026 年 8 月 15 日 | Camera-ready 版本提交（暂定） |
| 2026 年 9 月 15 日 | 论文排版 & 提交 ACL/CCL Anthology |
| 2026 年 10 月 | 会议召开（湖北省宜昌市） |

---

## 9 论文格式

- 提交论文的格式统一使用 CCL 2026 的论文模板。
- 论文可由中文或英文撰写，最多 6 页正文，参考文献页数不限。
- 采用双盲审稿，不可出现作者姓名和单位信息，不符合要求将被拒稿。
- 报告应至少包含以下四个部分：
  - 模型介绍
  - 评测结果
  - 结果分析与讨论
  - 参考文献

---

## 10 奖项设置

本届评测将发布各子任务分项榜单（可展示前列队伍），并按赛道综合榜设置第一、二、三名，由**中国中文信息学会**颁发荣誉证书（以最终通知为准）。

仅完成该赛道全部必选子任务并产生综合总分（Total Score）的队伍具备正式奖项评定资格；仅参加部分子任务的队伍仅参与对应分项榜展示，不纳入综合奖项评定。

---

## 模型参数规模

- Track-A（不微调）：不限制模型参数规模。
- Track-B（微调）：基础模型参数规模不超过 7B。
- 本澄清不改变评分公式，仅补充赛道执行口径。

---

## 项目网址

- GitHub：[https://github.com/Jingshi-Zhou/-BCCL-CSA-2026-](https://github.com/Jingshi-Zhou/-BCCL-CSA-2026-)

BCCL-CSA 2026 官方排行榜（CodaBench）：
- Leaderboard：https://www.codabench.org/competitions/17329/
  
提交说明、评分细则与常见问题请见：
- 评分细则：`评分细则.md`
- 提交模板：`提交模板.md`

---
