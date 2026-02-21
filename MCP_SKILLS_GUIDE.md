# MCP 和 Skills 配置指南

## MCP 服务器 (已安装)

| MCP | 功能 | 命令 |
|-----|------|------|
| **filesystem** | 文件系统读写 | npx @modelcontextprotocol/server-filesystem |
| **github** | GitHub操作 | npx @modelcontextprotocol/server-github |
| **godot** | Godot引擎操作 | python godot-mcp |

### 配置文件位置
- `/workspace/config/mcporter.json`

### 使用方法

```bash
# 列出所有MCP工具
mcporter list <mcp-name>

# 调用MCP工具
mcporter call <mcp-name>.<tool> key=value
```

---

## Skills (已安装)

| Skill | 功能 |
|-------|------|
| **aria2** | 下载磁力链接、种子、HTTP文件 |
| **business** | 业务策略验证规划 |
| **business-model-canvas** | 商业画布构建 |
| **business-plan** | 商业计划书编写 |
| **feishu-wiki-updater** | 飞书Wiki更新 |
| **game-assets-downloader** | 游戏资源下载 |
| **math-tutor** | 数学辅导 |
| **ocr** | 文字识别 |
| **report-viewer** | 报告查看 |

### 安装新Skill

```bash
clawhub install <skill-name>
```

---

## 开发环境配置

### MCP配置示例 (mcporter.json)

```json
{
  "mcpServers": {
    "godot": {
      "command": "/path/to/venv/bin/python",
      "args": ["-m", "godot_mcp.server"]
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/workspace"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"]
    }
  }
}
```

---

## Windows同步

在Windows上需要：
1. 安装Node.js 22+
2. 安装mcporter: `npm install -g mcporter`
3. 复制配置文件

---

*最后更新: 2026-02-21*
