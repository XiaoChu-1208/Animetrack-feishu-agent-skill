# 术语表 · Glossary

中英双语对照，方便跨语言搜索 / 跨语言 LLM 检索。

## 一、动画制作流程 · Animation production pipeline

| 中文 | English | 缩写 / 同义 | 解释 |
|---|---|---|---|
| 镜头 / 镜次 | Shot · Cut | shot, cut, **C** | 一段连续画面，制片表的一行 = 一个镜头。命名缩写 `C` |
| 场次 | Scene · Sequence | seq, scene, **S** | 多个镜头组成一场戏，比如 S1（第一场）。命名缩写 `S` |
| 镜头编号 / 命名规范 | Shot ID convention | `S{n}C{nnn}` | **本 base 标准命名**：`S1C001` / `S1C002` / `S2C015` ……。`S` = Scene、`C` = Cut。`S1` 前缀自动对应「场次」字段的 S1 选项 |
| 分镜 / 分镜表 | Storyboard | sb | 把剧本拆成一个个镜头的设计文档 |
| 制片表 | Production table · Shot tracker | prod sheet | 跟踪每个镜头各阶段进度的表格，本 repo 的核心 |
| 参考 | Reference | ref | 透视、Book、美术分配等前置参考材料 |
| 角色原画 | Character key animation · 1st key animation | 一原 / key | 角色动作关键帧（中文有时也叫 layout） |
| 清线 | Clean-up · Line art | line, lineart, 二原 | 把原画清成干净线稿 |
| 上色 | Coloring · Painting | color, paint | 给清线后的画面填色 |
| 绘景 | Background painting · BG | bg, background, 美术 | 场景背景，与角色独立绘制 |
| 3D / 特效 | 3D / VFX | 3d, vfx, fx | 三维或特效元素 |
| 合成 | Compositing | comp, comp+ | 把上色 + 绘景 + 3D/特效层合到一起 |
| 返修 | Rework · Retake | retake, fix | 已交付但需要修改的镜头，本 skill 标记为「返修中」 |
| 定稿 | Approved · Sign-off | approve, lock | 角色原画通过审核 |
| 难度等级 S/A/B/C | Difficulty grade | grade S/A/B/C | S 最难（5 工作日原画），C 最简单（1 工作日） |
| 关键路径 | Critical path | CP | 排期里最长的串行链，决定最早完工时间 |
| Buffer / 缓冲期 | Buffer · Slack time | — | 排期里留给返修和意外的时间，本 skill 默认 15% |
| DDL / 死线 | Deadline · Delivery deadline | DDL, ddl, due | 项目交付死线 |
| 里程碑 | Milestone | ms | 关键交付节点，本 skill 含 DDL + ☠️ 两个里程碑 |
| 排期始 / 终 | Start date / End date | start/end | 每个工序的计划开始 / 结束日期 |

## 二、人员 / 角色 · Roles

| 中文 | English | 解释 |
|---|---|---|
| 制片 / 制片人 | Producer · Production manager | 项目总管 |
| 导演 | Director | 创作总负责 |
| 主美 / 美术指导 | Art director | 美术风格统一负责人 |
| 原画师 / 一原 | Key animator · KA | 画原画的人 |
| 二原 / 清线师 | Clean-up artist · 2nd key animator | 把一原稿清成干净线稿 |
| 上色师 | Colorist · Paint artist | 上色 |
| 美术 / 背景师 | BG artist · Background painter | 画绘景 |
| 3D 师 / 三维师 | 3D artist | 三维元素 |
| 合成师 | Compositor | 后期合成 |
| 负责人 | Owner · Assignee | 某个工序某张镜头的当前责任人 |

## 三、飞书多维表格 · Feishu Bitable

| 中文 | English | 解释 |
|---|---|---|
| 飞书 | Feishu · Lark | 字节跳动办公协作平台 |
| 飞书 Aily | Feishu Aily · Lark Aily | 飞书的智能体平台，本 skill 的运行环境，本质是飞书集成的 LobeChat 类智能体 |
| 智能体 / 智能伙伴 | Agent · AI companion | Aily 里创建的对话式 AI 实例 |
| 多维表格 | Bitable · Multi-dimensional Table · MDT | 类似 Notion DB / Airtable 的飞书表格产品 |
| .base 文件 | Bitable template file | 多维表格的导出 / 导入文件格式 |
| app_token | App token · Base token | 一张多维表格的全局唯一 ID，URL 里 `/base/{这串}` |
| table_id | Table ID | 一张多维表格里某张子表的 ID，形如 `tblXXXXXX` |
| field_id | Field ID | 字段 ID，形如 `fldXXXXXX`，**永远稳定**（即使字段改名也不变） |
| record_id | Record ID | 一行记录的 ID，形如 `recXXXXXX` |
| 视图 | View | 同一张表的不同筛选 / 分组 / 着色 / 甘特展示，形如 `vewXXXXXX` |
| 自动化 | Automation · Workflow | 触发器 + 动作链，形如 `wkfXXXXXX` |
| 附件字段 | Attachment field | 字段类型 17，存图片 / 文件，需要先上传到飞书云端拿 token 才能写入 |
| 人员字段 | Person field | 字段类型 11，存飞书用户，可单选 / 多选 |
| 单选 / 多选 | Single select / Multi select | 字段类型 3，含 options 列表 |
| 日期字段 | Date field | 字段类型 5，可设格式 yyyy/MM/dd 等 |
| Stage 字段 | Stage field | 字段类型 24，本 skill 的「进度」字段，会根据附件上传自动推进到下一阶段 |
| Open API | Open API | 飞书的 REST API，scope 含 `bitable:read` / `bitable:write` 等 |
| Webhook | Webhook | 自动化里发飞书消息的钩子 |
| 群机器人 | Group bot | 用 webhook 在群里发消息的机器人 |

## 四、本 skill 的特定术语 · Skill-specific terms

| 中文 | English | 解释 |
|---|---|---|
| Bootstrap | Bootstrap | 智能体第一次绑定 base 的初始化流程，见 BOOTSTRAP.md §3 |
| Runtime | Runtime state | 当前会话绑定的 base 信息，存在 runtime.json |
| 加权完成度 | Weighted completion | 按 S/A/B/C 难度的工时占比加权计算的项目完成度 |
| 平均完成度 | Simple-average completion | 每个镜头平等加权的完成度，作为对比 |
| 差额 | Delta | 加权 − 平均，负值说明剩下的偏难，实际比表面进度慢 |
| Dry-run / 预览 | Dry-run · Preview | 批量写表前先输出预览，用户回「确认」才落库 |
| 巡检 | Patrol · Daily check | 每日扫描 11 项检查，发现问题私信负责人，不发群 |
| 自定义识别 | Customization intake | 发现 schema 与预期不符时先猜后问再回写 SKILL，见 SKILL.md §13 |
| 回滚 | Rollback | 任何改动都可以撤回到 .history/ 备份的之前状态 |
| 报告风格三原则 | Three reporting principles | 短（≤ 200 字）/ 平（不渲染严重）/ 柔（不催促），见 SKILL.md 顶部 |

## 五、相似工具 · Related tools

便于搜索时建立联想：

- **Shotgun Studio / ShotGrid** — Autodesk 的影视制片管理工具，本 skill 是它的"轻量飞书版"
- **Ftrack** — 类似的影视制片管理 SaaS
- **CGTeamWork / 协作平台** — 国内动画行业用的制片管理软件
- **Notion / Airtable / Coda** — 通用多维表格产品；飞书多维表格是国内对标
- **Aily / LobeChat / Coze** — Aily 是飞书集成的对话式 AI 智能体平台
- **MCP servers** — Anthropic 的 Model Context Protocol；本 skill 走 Aily 不是 MCP，但思路类似

## 六、缩写速查 · Acronyms

```
DDL  Deadline · 死线
KA   Key animator · 原画师
BG   Background · 绘景
VFX  Visual effects · 特效
CP   Critical path · 关键路径
MDT  Multi-dimensional Table · 多维表格
DAG  Directed acyclic graph · 有向无环图（工序依赖）
MVP  Minimum viable product · 最小可用版
SaaS Software as a Service · 软件即服务
```
