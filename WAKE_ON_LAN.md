# 远程唤醒与任务调度

## 概述

配置远程唤醒 Mac mini 并调度任务。

---

## Wake-on-LAN 配置

### Mac mini 设置

1. 进入 **系统设置 → 节能**
2. 开启 **"唤醒以供网络访问"**
3. 设置静态 IP 或 DHCP 保留

### 树莓派安装 wakeonlan

```bash
pip install wakeonlan
```

---

## 唤醒脚本

```python
#!/usr/bin/env python3
"""远程唤醒 Mac mini"""
from wakeonlan import send_magic_packet
import time
import subprocess

# Mac mini 配置
MAC_ADDRESS = "XX:XX:XX:XX:XX:XX"  # 替换为实际 MAC 地址

def wake_mac():
    """发送唤醒包"""
    print(f"发送 Wake-on-LAN 到 {MAC_ADDRESS}...")
    send_magic_packet(MAC_ADDRESS)
    print("等待 Mac mini 启动...")
    time.sleep(30)  # 等待 30 秒

def check_service(url: str) -> bool:
    """检查服务是否可用"""
    import urllib.request
    try:
        urllib.request.urlopen(url, timeout=5)
        return True
    except:
        return False

def wait_for_service(url: str, timeout: int = 60):
    """等待服务就绪"""
    for i in range(timeout):
        if check_service(url):
            print(f"服务已就绪! ({i}秒)")
            return True
        time.sleep(2)
    return False

def main():
    # 1. 唤醒 Mac mini
    wake_mac()
    
    # 2. 等待服务
    service_url = "http://192.168.x.x:8000/health"  # 替换为实际 IP
    if wait_for_service(service_url):
        print("执行任务...")
        # 执行任务
    else:
        print("服务启动失败")

if __name__ == "__main__":
    main()
```

---

## 调度配置

### 定时任务

```bash
# 每天 9:00 唤醒 Mac mini 并执行任务
0 9 * * * /path/to/wake_and_work.py
```

---

## Tailscale 访问

在 Mac mini 上配置 Tailscale:

```json
{
  "gateway": {
    "port": 18789,
    "bind": "loopback",
    "tailscale": {
      "mode": "serve"
    }
  }
}
```

---

## 硬件 MAC 地址

| 设备 | MAC 地址 | IP | 状态 |
|------|----------|-----|------|
| Mac mini 1 | 待配置 | DHCP | 待设置 |
| Mac mini 2 | 待配置 | DHCP | 待设置 |

---

*最后更新: 2026-02-25*
