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
| Priority | 单选 | Normal/Important/Done |
| Status | 单选 | Not yet started/Ongoing/Stalled/Completed |
| Task leader | 人员 | 负责人 |
| Progress notes | 文本 | 进度备注 |
| Start date | 日期 | 开始时间 |
| Estimate Deadline | 日期 | 预计截止 |
| End Date | 日期 | 实际结束 |
| Departments | 多选 | 部门(R&D/Product/Design等) |

## OpenClaw集成

### 创建任务

```python
def create_task(title, priority, status, leader_id, departments, deadline):
    # 调用飞书API创建任务记录
```

### 更新状态

```python
def update_task_status(record_id, new_status):
    # 更新任务状态
```

### 查询任务

```python
def get_tasks_by_status(status):
    # 查询特定状态的任务
```

## 工作流程

1. **创建任务** → 根据Open GDD自动生成或手动创建
2. **分配负责人** → 设置Task leader (Vanguard001/CodeForge)
3. **更新进度** → 在Progress notes中记录
4. **状态流转** → Not yet started → Ongoing → Completed

---

*最后更新: 2026-02-21*
