# 飞书多维表格任务管理集成

## 概述

使用飞书多维表格管理项目任务和Bug，通过OpenClaw Skill自动化操作。

## 表格配置

### 多维表格信息
- **App Token**: `Er2fbGeRSaR1C8seFunc6psnnYb`
- **Table ID**: `tblQWdW1zqzBFZXw`
- **名称**: 世界上最好玩的弹珠机游戏

### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| Task | 文本 | 任务标题 |
| Priority | 单选 | **bug / test / feature / Normal / Important / Done** |
| Status | 单选 | Not yet started/Ongoing/Stalled/Completed |
| Task leader | 人员 | 负责人 |
| Progress notes | 文本 | 进度备注 |
| Start date | 日期 | 开始时间 |
| Estimate Deadline | 日期 | 预计截止 |
| End Date | 日期 | 实际结束 |
| Departments | 多选 | 部门(R&D/Product/Design等) |

## 优先级规则

### 优先级层级 (从高到低)

```
🔴 bug (最高)     → Bug修复，阻断性问题
🟡 test           → 测试用例实现
🔵 feature        → 新功能开发
🟢 Important      → 重要任务
⚪ Normal          → 普通任务
✅ Done           → 已完成
```

### 任务类型前缀

| 前缀 | 类型 | 说明 |
|------|------|------|
| `[Bug]` | Bug | 需要修复的问题 |
| `[Test]` | test | 测试用例 |
| `[PI-PinBall]` | feature | 新功能 |

### 优先级使用规则

1. **Bug修复** → 使用 `bug` 优先级
   - 游戏无法运行
   - 场景切换失败
   - 报错无法游戏
   
2. **测试用例** → 使用 `test` 优先级
   - 单元测试
   - 集成测试
   - 截图测试
   
3. **新功能** → 使用 `feature` 优先级
   - UI系统开发
   - 新玩法实现
   
4. **普通任务** → 使用 `Normal`
   - 文档编写
   - 代码重构
   
5. **重要任务** → 使用 `Important`
   - 关键里程碑
   - 发布相关

### 工作流程

```
发现Bug → 创建任务 → Priority设为"bug" → 立即处理
         ↓
编写测试 → 创建任务 → Priority设为"test" → 排期处理
         ↓
新功能   → 创建任务 → Priority设为"feature" → 正常排期
```

## OpenClaw集成

### 创建任务

```python
# 创建Bug任务
feishu_bitable_create_record(
    app_token="Er2fbGeRSaR1C8seFunc6psnnYb",
    table_id="tblQWdW1zqzBFZXw",
    fields={
        "Task": "[Bug] 游戏无法启动",
        "Priority": "bug",
        "Status": "Not yet started"
    }
)
```

### 更新状态

```python
# 更新任务状态
feishu_bitable_update_record(
    record_id="recvbxxx",
    fields={"Status": "Ongoing"}
)
```

### 查询任务

```python
# 查询Bug任务
feishu_bitable_list_records(
    app_token="Er2fbGeRSaR1C8seFunc6psnnYb",
    table_id="tblQWdW1zqzBFZXw"
)
```

## 任务生命周期

1. **创建任务** → 根据Open GDD自动生成或手动创建
2. **设置优先级** → bug > test > feature > Normal > Important
3. **分配负责人** → 设置Task leader (Vanguard001/CodeForge)
4. **更新进度** → 在Progress notes中记录
5. **状态流转** → Not yet started → Ongoing → Completed

---

*最后更新: 2026-02-22*
