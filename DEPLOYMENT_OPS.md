# 部署与运维职责

## 概述

管理 Web 服务的部署、外网访问和长期运行。

---

## 职责范围

### 1. Web 服务部署

| 服务 | 端口 | 状态 | 外网访问 |
|------|------|------|----------|
| securities-analysis-tool | 8000 | ✅ 运行中 | ⏳ |
| poetry-english-tool | 3000 | 待部署 | ⏳ |
| Agent Tool Server | 8765 | 待部署 | ⏳ |

### 2. 远程唤醒 (Wake-on-LAN)

| 设备 | MAC 地址 | 状态 |
|------|----------|------|
| Mac mini 1 | 待配置 | ⏳ |
| Mac mini 2 | 待配置 | ⏳ |

详见 [WAKE_ON_LAN.md](./WAKE_ON_LAN.md)

### 3. 内网穿透

使用 natapp 或 Cloudflare Tunnel 实现外网访问。

### 3. 监控与日志

- 服务健康检查
- 日志管理
- 故障恢复

---

## 部署流程

### securities-analysis-tool

```bash
# 1. 克隆项目
git clone https://github.com/LuckyJunjie/securities-analysis-tool.git
cd securities-analysis-tool

# 2. 创建虚拟环境
python -m venv venv
source venv/bin/activate

# 3. 安装依赖
pip install -r requirements.txt

# 4. 启动服务
uvicorn src.backend.main:app --host 0.0.0.0 --port 8000

# 5. 启动内网穿透 (后台运行)
nohup ./natapp -config=~/.natapp/config.yml &
```

### 开机自启

```bash
# 创建 systemd 服务
sudo nano /etc/systemd/system/securities-tool.service

[Unit]
Description=Securities Analysis Tool
After=network.target

[Service]
Type=simple
User=pi
WorkingDirectory=/home/pi/securities-analysis-tool
ExecStart=/home/pi/securities-analysis-tool/venv/bin/uvicorn src.backend.main:app --host 0.0.0.0 --port 8000
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## 外网访问

### natapp 配置

1. 注册 https://natapp.cn
2. 获取 authtoken
3. 配置并启动

### Cloudflare Tunnel (备选)

免费方案，无需客户端。

---

## 健康检查

```bash
# 检查服务状态
curl http://localhost:8000/health
curl http://localhost:8765/health
```

---

*最后更新: 2026-02-25*
