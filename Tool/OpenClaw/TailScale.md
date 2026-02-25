# OpenClaw + Tailscale Serve 配置

[TOC]

## 一、架构概览

```
浏览器 → https://vm-0-17-opencloudos.tailefbf76.ts.net (Tailscale Serve, HTTPS)
       → http://127.0.0.1:18789 (OpenClaw Gateway, HTTP)
```

Tailscale Serve 充当 HTTPS 反向代理，将外部请求转发到本地 OpenClaw 服务。仅 Tailnet 内部可访问。

## 二、环境信息

| 组件 | 版本 |
|------|------|
| OS | OpenCloudOS 9, Linux 6.6.117（CentOS 9 Derivative） |
| Tailscale | v1.94.1 |
| OpenClaw | v2026.2.9 |
| Node.js | v22.22.0 |

## 三、Tailscale Serve

### 3.1 DNF 代理配置（可选）

```shell
echo "proxy=http://10.0.4.13:6840" | sudo tee -a /etc/dnf/dnf.conf
```

### 3.2 Tailscale 安装注册

```shell
sudo dnf config-manager --add-repo https://pkgs.tailscale.com/stable/centos/9/tailscale.repo
sudo dnf install tailscale -y
sudo systemctl enable --now tailscaled
sudo tailscale up
```

## 四、OpenClaw

### 4.1 快速配置

```shell
openclaw config set "gateway" '{"port":18789,"mode":"local","bind":"loopback","auth":{"mode":"token","token":"<placeholder>","allowTailscale":true},"tailscale":{"mode":"serve","resetOnExit":false}}' --json
openclaw gateway restart
```

配置文件路径：`/root/.openclaw/openclaw.json`

```json
{
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "<serve-mode-no-need-token>",
      "allowTailscale": true
    },
    "tailscale": {
      "mode": "serve",
      "resetOnExit": false
    }
  }
}
```

字段说明

- `bind: "loopback"` : 只监听 127.0.0.1，外部无法直连，必须通过 Tailscale Serve 访问
- `tailscale.mode: "serve"` : OpenClaw 启动时自动注册 Tailscale Serve 规则
- `allowTailscale: true` : 允许 Tailscale 身份认证，使 tailnet 可以访问 control ui
- `resetOnExit: false` : OpenClaw 退出时不清除 Tailscale Serve 规则

### 4.2 网关规则

OpenClaw 自动生成的 serve 规则，tailscale serve status --json 查看：

```json
{
  "TCP": { "443": { "HTTPS": true } },
  "Web": {
    "vm-0-17-opencloudos.tailefbf76.ts.net:443": {
      "Handlers": {
        "/": { "Proxy": "http://127.0.0.1:18789" }
      }
    }
  }
}
```

没有自动配置则手动开启代理：`tailscale serve --bg http://127.0.0.1:18789`

参考：https://docs.openclaw.ai/zh-CN/gateway/tailscale

## 五、域名授权

在`/root/.openclaw/openclaw.json`中加入域名白名单（**v2026.2.23**引入）：

```shell
openclaw config set "gateway.controlUi.allowedOrigins" '["http://localhost:5173"]' --json
```

结果如下

```json
{
  "gateway": {
    "controlUi": {
      "allowedOrigins": ["http://localhost:5173"]
    }
  }
}
```

## 六、设备认证

运行 `openclaw status --deep | grep Tailscale`，查看控制面板地址并打开，需要注意 clash 代理

```shell
│ Tailscale       │ serve · vm-0-17-opencloudos.tailefbf76.ts.net · https://vm-0-17-opencloudos.tailefbf76.ts.net     │
```

命令行批准请求，注意认证是和浏览器强绑定的，更好设备或浏览器都需要查询认证

```bash
# 查看已配对设备和申请
openclaw devices list

# 批准设备
openclaw devices approve <requestId>
```

参考：https://docs.openclaw.ai/zh-CN/web/control-ui

## 七、踩坑记录：WebSocket 连接失败

### 7.1 描述

通过 Tailscale Serve 访问 OpenClaw，页面能正常加载，但 WebSocket 连接失败。

### 7.2 排查过程

1. ❌ 怀疑 Tailscale Serve 不支持 WebSocket → 排除，Go 的 `httputil.ReverseProxy` 支持 Upgrade 头
2. ❌ 怀疑 CORS/Origin 不匹配（HTTPS→HTTP）→ 不是根因
3. ✅ 查看 OpenClaw 日志发现真正原因：**设备未配对 (not-paired)**

### 7.3 根本原因

- `allowTailscale: true` 让 HTTP 请求通过了 Tailscale 身份验证，所以页面能加载
- 但 WebSocket 连接还需要完成 OpenClaw 的**设备配对流程**
- 浏览器设备未配对 → WS 握手被 OpenClaw 主动拒绝（close code=1008）

### 7.4 解决方案

1. 在浏览器中访问页面发起配对请求
2. 批准配对：`openclaw devices approve <device-id>`
3. 刷新页面，WebSocket 即可正常连接

## 八、Windows Clash 配置

### 8.1 选项一

直接关闭 Clash 代理模型进行访问

### 8.2 选项二

修改 Clash DNS 解析配置

```yaml
dns:
  fake-ip-range: 198.18.0.1/16
  default-nameserver:
    ...
  enable: true
  enhanced-mode: fake-ip
  fallback-filter:
    ...
  ipv6: false
  nameserver:
    ...
  use-hosts: true
  # 追加配置
  nameserver-policy:
    "+.ts.net": "100.100.100.100"
  fake-ip-filter:
    - "*.ts.net"
```

添加匹配 Rules

```shell
 - IP-CIDR,100.64.0.0/10,🎯 本地直连,no-resolve
 - DOMAIN-SUFFIX,ts.net,🎯 本地直连
```

## 九、要点总结

- Tailscale Serve 本身**支持 WebSocket**，遇到 WS 问题先查应用层
- `allowTailscale` 只解决 HTTP 认证，WS 还需要设备配对
- Windows 访问时需要注意 clash 配置
