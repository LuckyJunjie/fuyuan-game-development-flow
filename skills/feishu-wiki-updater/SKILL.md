# Feishu Wiki Updater Skill

## Purpose
Simplify updating Feishu Wiki pages with structured content. Handles the limitations of Feishu API (no tables, no Mermaid) by using text-based formatting.

## Prerequisites
- Feishu bot with wiki access
- Document token or space/node IDs

## Usage

### Method 1: Write Full Document (Recommended for Large Updates)
Use ``feishu_doc action `write` with markdown content. Note: Tables and complex blocks may be skipped.

### Method 2: Append Content (For Incremental Updates)
Use `feishu_doc` action `append` with content blocks.

### Method 3: Create Wiki Node + Write Document

```python
# Step 1: Create wiki node
feishu_wiki(action="create", space_id="...", parent_node_token="...", title="...")

# Step 2: Get document token from response
# node_token -> obj_token is the doc

# Step 3: Write content
feishu_doc(action="write", doc_token="...", content="...")
```

## Best Practices

### For Best Results: Use Local File + Export
1. Keep master content in local markdown files
2. For critical wiki pages, manually copy-paste from local
3. Use wiki for quick references only

### Formatting Guidelines (Feishu-Friendly)
- Use headings (# ## ###) for hierarchy
- Use bullet lists (-) instead of tables
- Use **bold** for emphasis
- Avoid complex markdown (tables, code blocks with syntax)
- Keep paragraphs short

### Example: Convert Table to List

❌ Avoid (Table):
```
| Step | Role | Action |
|------|------|--------|
| 1.1 | 制作人 | 写简报 |
```

✅ Use (Bullet List):
```
## 阶段1：创意孵化

- 1.1 👤 制作人：在飞书撰写游戏创意简报
- 1.2 🤖 创意Agent：监听飞书文档更新
- 1.3 🟡 AI：生成核心玩法提案
```

## Troubleshooting

### "Table not supported" Warning
- Normal - Feishu API doesn't support tables
- Convert to bullet lists manually

### Content Missing
- Try `append` instead of `write`
- Check revision_id for conflicts

### Permission Denied
- Ensure bot is added to wiki space as member
- Space ID required for wiki operations

## File Locations
- Wiki space: 7585774525300640954 (V-Employees)
- Root node: MMYYwPUGbiAWdNkXkBOcwyFYnmg
- Game pipeline doc: NYaRw7HidiQNa7k9mXHcta3Mnch

---
*Last updated: 2026-02-21*
