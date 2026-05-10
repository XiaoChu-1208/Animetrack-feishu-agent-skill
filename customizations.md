# 自定义变更日志

由 SKILL.md §13 流程产出。每次扫描 base 发现自定义差异并问过用户后，append 一行到这里。**只追加，不修改/删除**已有条目（用户回退时也再加一行说明回退）。

格式：
```
- YYYY-MM-DD HH:MM | <类型> | <对象名 + ID> | <用户解释> | <回写文件>
```

类型枚举：
- `field_new` 新字段
- `field_rename` 字段改名
- `field_deprecated` 字段废弃
- `option_new` 选项新增
- `view_new` 视图新增
- `table_new` 新表
- `automation_new` 新自动化
- `automation_changed` 自动化修改
- `stage_change` Stage 字段流转规则改动
- `revert` 回退（指向之前哪条）

---

## 变更记录

<!-- 第一条用户实际记录会替换这一段示例 -->

> _示例（实际使用时删除，按时间倒序追加真实条目）_
>
> - 2026-06-15 14:30 | field_new | `fld新ABC` 后期备注（Text） | 用于合成阶段补充对白/混音说明，按镜头维度填 | fields.json
> - 2026-06-18 09:10 | option_new | `fldR8qXhdV 原画难度` 新增 `D` | 极简贴图卡，权重 3.5（用户确认） | fields.json + scheduling.json.completion
> - 2026-06-22 16:45 | view_new | `vew制片日报` 视图 | 制片人每天早会用，按 `flde54MR7a 修改人` 分组看昨日产出 | fields.json.views
> - 2026-06-30 11:20 | field_rename | `fldp7RYAQ5` 场次镜头号 → 镜次号 | 团队习惯叫"镜次号"，aliases 加上「场次镜头号」 | fields.json
