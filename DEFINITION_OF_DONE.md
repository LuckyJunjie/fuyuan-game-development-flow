# Definition of Done (DoD) - 游戏开发

**版本:** 1.0  
**创建日期:** 2026-02-24  
**适用范围:** 所有游戏开发任务 (PI-PinBall, pinball-experience)

---

## ✅ 开发任务完成标准

### 1. 代码实现
- [ ] 功能代码已完成并通过自测
- [ ] 无编译错误或运行时崩溃

### 2. 测试验证
- [ ] 单元测试通过 (如适用)
- [ ] 集成测试通过
- [ ] 0.1-0.5 基础功能验证通过

### 3. Git 流程
- [ ] 代码已提交到本地仓库 (`git commit`)
- [ ] 已创建功能分支 (feature/fix-xxx)
- [ ] **已合并到 master 分支** (`git checkout master && git merge`)
- [ ] **已推送到远程** (`git push origin master`)

### 4. 文档更新
- [ ] 相关文档已更新 (如需要)
- [ ] CHANGELOG 或开发状态已记录

---

## 🔄 Bug 修复流程

```
1. 创建分支:     git checkout -b fix/xxx
2. 修复代码:     修改并测试
3. 提交:        git add . && git commit -m "fix: xxx"
4. 推送:        git push --set-upstream origin fix/xxx
5. 合并到master: git checkout master
                 git merge fix/xxx
                 git push origin master
6. 删除分支(可选): git branch -d fix/xxx
```

---

## 📋 示例

### 场景: 修复挡板位置问题

| 步骤 | 操作 | 验证 |
|------|------|------|
| 1 | `git checkout -b fix/flipper-position` | 新分支创建 |
| 2 | 修改 scenes/Main.tscn 调整位置 | 本地测试通过 |
| 3 | `git commit -m "fix: adjust flipper position"` | 提交成功 |
| 4 | `git push --set-upstream origin fix/flipper-position` | 远程分支存在 |
| 5 | `git checkout master && git merge fix/flipper-position` | 无冲突 |
| 6 | `git push origin master` | GitHub 显示最新 |

---

## ⚠️ 常见问题

### 冲突解决
```
git pull --no-rebase --no-edit
# 手动解决冲突
git add .
git commit -m "Merge: resolve conflict"
git push origin master
```

### 推送到 GitHub 失败
- 检查网络连接
- 确认 GitHub 认证
- 使用 `gh auth status` 检查

---

## 🎯 验收检查清单

每次任务完成后检查:

- [ ] 代码在 master 分支?
- [ ] GitHub 显示最新提交?
- [ ] 游戏功能可正常运行?
- [ ] 无未解决的 Bug?

---

*此文档为开发团队标准流程*
