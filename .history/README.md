# .history/ — 变更备份目录

由 SKILL.md §14 流程自动维护。**不要手动改这个目录的内容**，除非是清理过老备份。

## 文件格式

### A 类 — SKILL 文件备份

```
{原文件名}.bak.{YYYYMMDD-HHMMSS}
```

举例：
- `SKILL.md.bak.20260615-143022`
- `fields.json.bak.20260615-143022`
- `scheduling.json.bak.20260620-091500`

同一次变更涉及多个文件 → 用同一个时间戳，方便整体回滚。

### B 类 — base 操作 undo log

```
undo_{txnId}.json
```

txnId 格式：`{YYYYMMDD-HHMMSS}-{功能名片段}`，举例：`undo_20260615-143022-add_postnote.json`

JSON 结构：
```json
{
  "txnId": "20260615-143022-add_postnote",
  "timestamp": "2026-06-15T14:30:22+08:00",
  "feature": "添加后期备注字段",
  "operations": [
    {
      "type": "field_add",
      "tableId": "tblxJGAeyRCOWOwa",
      "fieldId": "fld新ABC",
      "reverse": "field_delete",
      "snapshot": { /* 字段定义快照 */ }
    },
    {
      "type": "record_update",
      "tableId": "tblxJGAeyRCOWOwa",
      "recordId": "rec...",
      "field": "fldXXX",
      "oldValue": "...",
      "newValue": "...",
      "reverse": "record_update with oldValue"
    }
  ]
}
```

## 保留策略

- **最近 10 个版本** + **30 天内全部备份**（取并集）
- 超出后清理最旧的，清理前在 customizations.md 记一条 `cleanup` 类型日志
- 用户说「保留这次的备份」→ 在文件名后加 `.pinned` 后缀，**永不清理**

## 回滚操作

参见 SKILL.md §14.5。简单流程：

1. `ls .history/` 列最近 10 个备份
2. 让用户选时间戳（用人话描述功能名，不让用户记 txnId）
3. A 类：把 `.bak.X` 复制回原路径覆盖
4. B 类：读 `undo_X.json`，按 `operations` 数组反向调用飞书 API
5. customizations.md append 一行 `revert` 记录
