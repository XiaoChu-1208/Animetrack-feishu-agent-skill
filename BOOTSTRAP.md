# 首次启动指南 BOOTSTRAP

> **读这份文件的两类对象**
> - **A. 用户**：第一次拿到这个文件夹（或 zip 包），不知道怎么接进飞书 Aily
> - **B. Agent**：被触发时还没有绑定的 base，必须先走 bootstrap 流程才能干活
>
> 两边内容都写在这一份里，重叠的部分不重复。

---

## 1. 包里有什么

```
制片表v4.1SKILL_feishu/
├── SKILL.md              ← agent 主入口（含 15 个章节、报告风格、流程规范）
├── BOOTSTRAP.md          ← 你正在读的这份
├── fields.json           ← 主表 46 个字段定义
├── workflows.json        ← 13 个自动化清单
├── team.json             ← 团队配置表 schema
├── scheduling.json       ← 工时表 + 排期参数 + 完成度权重
├── customizations.md     ← 用户改动日志（运行中 append）
├── runtime.json          ← 当前会话绑定的 base 信息（首次启动后产出）
├── .history/             ← 备份目录（运行中产出）
└── 制片表 v4.1 （公版） .base   ← 飞书多维表格模板，需要单独导入
```

打 zip 直接全选这个文件夹整体压缩。`.history/` 可以为空但保留目录。

---

## 2. 用户视角：怎么导入

### Step 1 — Aily 智能体吃 zip（知识库部分）

1. 打开 Aily → 你的智能体 → 知识库
2. 上传 zip（或解压后批量上传）
3. Aily 自动索引 `.md` / `.json`；**`.base` 文件不会被索引**，是死文件，要走 Step 2
4. 在智能体的「人设/指令」里加一句：

   > 「你的所有操作规则、字段映射、流程规范都在 `SKILL.md` 里。每次接到指令前先 read SKILL.md，按里面的章节执行。第一次没有绑定 base 时按 BOOTSTRAP.md §3 走。」

### Step 2 — 飞书多维表格导入 .base（数据表部分）

**两条路**：

**A. 全新项目**
1. 从 zip 解出 `制片表 v4.1 （公版） .base`
2. 飞书 → 我的空间 / 团队空间 → 新建 → 多维表格 → 选「**从 .base 文件导入**」
3. 上传 `制片表 v4.1 （公版） .base`
4. 等飞书生成完毕，会得到一张空的制片表（含 46 个字段、10 个视图、13 个自动化）
5. 复制这张新表的链接，**保留备用**

**B. 已经有制片表（沿用旧项目的或别人给的）**
1. 跳过 Step 2，直接复制现有表链接
2. 如果不是从同一个 .base 模板来的，agent 会在 §3 流程里检测到字段不一致，按 SKILL §13 走

### Step 3 — 把 base 链接告诉 agent

第一次找 Aily 智能体说话，**第一句话**直接给链接，比如：

```
我的制片表在这里：https://xxx.feishu.cn/base/AbCdEf123456...
项目叫《海岸线》，DDL 大概 8/30。
```

Agent 会自动从链接抽 `app_token`，写到 `runtime.json`，然后接着问没问到的（比如团队人员）。

---

## 3. Agent 视角：第一次被触发的必走流程

> **核心约束**：没拿到 base 的 `app_token` 之前**不要假装在操作**。读不到表就老实说"还没绑定 base，先走个简单接入"，不要瞎编数据。

### Step 1 — 检查 runtime.json

每轮会话开始**先 read** `runtime.json`：

- 如果文件不存在 / `bound_base.app_token` 为空 → 进入 bootstrap 模式
- 如果有值 → 直接进入正常工作模式（按 SKILL §4 等 playbook 响应）

### Step 2 — Bootstrap 模式：4 个问题串行问，一次一个

**报告风格遵守 SKILL 顶部的「短、平、柔」三原则**。

**Q1**（如果用户没在第一句话给链接）：

```
还没绑制片表呢。是新项目还是已经有表了？
- 新的 → 我先告诉你怎么从模板建一张
- 已有 → 把链接发我
```

- 用户答"新的" → 引用 BOOTSTRAP.md §2 Step 2A 的步骤，让 ta 操作完发链接回来
- 用户答"已有" → 直接 Q3

**Q2**（仅新项目）：

```
新项目叫什么？
```

如果用户不想取名 → 默认 `未命名项目`，写到 runtime.json，**不催**。

**Q3**（拿到链接）：

```
谢谢，我提取一下信息……
```

从链接抽 `app_token`。两种合法格式（用户在飞书表右上角「分享 → 复制链接」拿到的就是这个）：
- `https://*.feishu.cn/base/{app_token}` — 直接 base 链接
- `https://*.feishu.cn/wiki/{wiki_token}` — wiki 链接（需要先用 `wiki/v2/spaces/get_node` API 把 wiki_token 换成 obj_token，那才是真正的 app_token）

如果抽不到 → 「这个链接看不出来是哪张表，要不你在飞书里点「分享 → 复制链接」再发我一次？」

抽到 token 后立即用 Open API 拿表元信息，对照 `fields.json`：
- 字段 ID 全对得上 → 是模板克隆出来的，干净接入
- 有多 / 少字段 → 走 SKILL §13 自定义识别，逐个问用户

**Q4**（DDL，软问）：

```
DDL 大概什么时候？没定也行。
```

不催，不回就跳过。

### Step 3 — 写 runtime.json

```json
{
  "bound_base": {
    "app_token": "AbCdEf123456...",
    "base_url": "https://xxx.feishu.cn/base/AbCdEf123456...",
    "project_name": "海岸线",
    "ddl_date": "2026-08-30",
    "bound_at": "2026-05-10T15:30:00+08:00"
  },
  "bound_tables": {
    "main": "tblxJGAeyRCOWOwa",
    "team_config": null,
    "archive_tables": []
  },
  "schema_match": "exact",
  "last_synced_at": "2026-05-10T15:30:00+08:00"
}
```

`schema_match` 取值：
- `exact` — 字段全对，是模板克隆
- `with_customizations` — 有自定义字段，已通过 §13 流程吸收
- `unrecognized` — 主表里完全找不到 fldG04l9e4 等核心字段，可能不是制片表

### Step 4 — 后续动作

按需触发，但**一次一个，不堆**：

1. 把主表名（`Named it（on）`）改成项目名 — 告诉用户「我把主表改名叫《{项目名}》了，行吗？」
2. 更新 milestoneMap 的 DDL（`mlsGqRT4`）— 如果用户给了 DDL
3. 问要不要现在建 `团队配置` 表（SKILL §8）— 不急可以以后再说
4. 跑一次 SKILL §11 巡检的精简版（仅检查 1-2 项：是否有镜头、是否有团队），输出一句话报告

完成后**回执一句**：

```
绑好啦：《{项目名}》→ {base_url}。{若有} DDL {date}。
随时叫我。
```

---

## 4. 边界

| 情况 | 处置 |
|---|---|
| 用户给的链接抽不出 app_token | 一句话重问，给一个正确格式的示例链接 |
| 链接拿到但表里没有 fldG04l9e4 等核心字段 | 「这个表好像不是从我们的 .base 模板来的。要不要用模板新建一张？或者你告诉我现有表的字段对应关系，我适配一下？」（不要默认替换 / 也不要假装能用） |
| 多个候选 base（用户在群里发了好几个链接） | 列出来让用户选，不自己猜 |
| 用户拒绝任何问题 | 用合理默认值（项目名=未命名项目、DDL=空、团队=空），写 runtime.json，**不要因为缺信息卡住主流程** |
| 已绑了 base，用户又发新链接 | 询问「换表还是再加一个？」；换表 → 备份当前 runtime.json 到 `.history/runtime.bak.{ts}.json` 再覆盖 |
| Aily 没有 Open API 权限 | 早期发现就直接告诉用户「我现在没拿到 base 的读写权限，需要你在 Aily 控制台给智能体配 `bitable:read/write` scope，配好告诉我一声。」不要硬试调用反复失败 |
| 用户把 zip 重新上传了一份新版（比如 SKILL 改了） | runtime.json 不要被 zip 里的覆盖（zip 里是空模板），保留当前实例值 |

---

## 5. 重导入 / 切换 base

用户说「换个表」「重新导入了」「这个项目结束了，下一部用新表」：

1. **不要**直接覆盖 `runtime.json`。先：
   ```
   现在绑的是《{当前项目名}》。要换成新表吗？换了之前的还能切回来。
   ```
2. 用户确认 → 把当前 `runtime.json` 复制到 `.history/runtime.bak.{ts}.json`
3. 进入 bootstrap Step 2，跑一遍
4. 在 `customizations.md` 加一行：`{ts} | rebind | {old project} → {new project} | 用户切换绑定 | runtime.json`

---

## 6. 跨设备 / 跨会话

- 整个 `制片表v4.1SKILL_feishu/` 文件夹放在云盘同步目录（用户已经放在 `Desktop\同步\`）→ 多机自动同步
- `.history/` 也跟着同步，回滚不丢
- Aily 那边的知识库需要每次手动重传（zip 上传），或在 Aily 控制台开启"自动同步"如果支持
- `runtime.json` 是本地状态，**不要**手动改它，只让 agent 写

---

## 7. 一句话速查

| 我想…… | 看 SKILL.md 的 |
|---|---|
| 第一次接入 / 还没绑表 | BOOTSTRAP.md（你正在读的这份）§3 |
| 项目流程到下一部了 | §7 |
| 加新功能 / 改 SKILL | §14 |
| Base 里我自己加了字段 | §13（agent 会主动问） |
| 查完成度 / 进度 | §10 / §4.5 |
| 排期 / 分配 | §9 |
| 团队人员 | §8 |
| 巡检 / 兜底 | §11 |
