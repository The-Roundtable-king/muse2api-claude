# MUSE2API-CLAUDE

<p align="center">
  <img src="https://img.shields.io/badge/Anthropic-Messages%20API-8A2BE2?logo=anthropic" alt="Anthropic Messages API" />
  <img src="https://img.shields.io/badge/Claude%20Code-Ready-blueviolet?logo=anthropic" alt="Claude Code Ready" />
  <img src="https://img.shields.io/badge/Anti--Abuse-Industrial%20Stealth-success" alt="Anti-Abuse Stealth" />
  <img src="https://img.shields.io/badge/FastAPI-0.115%2B-009688?logo=fastapi" alt="FastAPI" />
  <img src="https://img.shields.io/badge/License-MIT-orange" alt="License" />
</p>

> **专为 Claude Code / Cursor 打造的工业级高防 Muse 逆向反代系统**  
> 原生打通 Anthropic `/v1/messages` 标准协议，深度搭载大厂红蓝对抗级**反作弊风控穿透装甲**。让你的 Muse 算力无缝接入 Claude Code 生产力工作流，拒绝假人封号与指纹露馅。

---

## 🛡️ 为什么开发 muse2api-claude？（反作弊穿透装甲）

市面上大部分开源逆向反代停留在“能跑通即可”的初级水平，在面对现代大厂（Cloudflare Turnstile、DataDome、Akamai 及平台自研反作弊模型）的前端行为探针与设备指纹比对时，破绽百出。

本项目在保留多模态逆向能力的基础上，**由资深风控安全对抗视角重构了底层输入链路与生命周期管理**：

### ⚔️ 市面普通逆向 vs 本项目工业级高防装甲对比

| 风险与对抗维度 | 市面普通逆向 / 原版反代 | **muse2api-claude (本项目)** | 穿透防御原理与工业级优势 |
| :--- | :--- | :--- | :--- |
| **鼠标点击事件** | 代码调用 `b.click()`，强制暴露 **`event.isTrusted: false`** | **CDP 物理坐标映射 (`Input.dispatchMouseEvent`)** | Blink 内核原生硬件级事件，**`isTrusted === true` 铁证通过**，杜绝脚本标记 |
| **长文本输入指纹** | React 原型 setter 瞬间注入（0ms 塞入 50KB，击键动力学异常） | **模拟物理剪贴板 (`ClipboardEvent.paste`) + 动态注入** | 完全符合真实程序员复制粘贴长代码的人类生物力学特征 |
| **网络与操作系统指纹** | 后台硬编码 Linux UA，本地却用 Windows Chrome，**同会话系统精神分裂** | **动态宿主平台指纹对齐（Windows/macOS/Linux Client Hints 严丝合缝）** | TLS JA4、HTTP/2 伪头与操作系统声明 100% 吻合，杜绝网络层双面人特征 |
| **GPU 与渲染特征** | 携带 `--disable-gpu`，WebGL 暴露 **SwiftShader 软件渲染**（虚拟机特征） | **开启物理显卡硬件直通 + 常见桌面标准分辨率 (1440x900)** | WebGL UNMASKED_RENDERER 返回真实桌面显卡驱动，彻底消除沙盒假指纹 |
| **Cookie 规范性** | 违规为 `__Host-` 安全前缀指定 `Domain`，被 Blink 解析器静默丢弃 | **严格遵循 RFC 6265bis 规范化 Cookie 作用域注入** | 杜绝关键网关安全会话凭证解析脱靶导致的异常 401/403 |
| **心跳与算力占用** | 24/7 固定 900 秒机械打卡，凌晨疯狂唤醒 VM（触发云端算力滥用风控） | **引入 Jitter 拟人随机时间漂移 + 凌晨生物钟入睡休眠退避** | 模拟人类自然作息，避免因空耗云端 GPU/CPU 调度配额被官方封禁 |
| **自动化特征抹除** | 仅依赖参数，易被 `window.chrome` 或 CDP Runtime 探针刺探 | **启动级参数隐形 + 页面运行时深度 Stealth 对象级注入补丁** | 深度隐匿 `navigator.webdriver`，修复 Notification 探针一致性 |

---

## 🌟 核心特性与架构

```text
       [Claude Code / Cursor / Claude CLI]
                        │
                        ▼ (Anthropic Messages API: /v1/messages)
 ┌──────────────────────────────────────────────────────────────┐
 │                    muse2api-claude 核心服务                  │
 │  ┌──────────────────────────┐  ┌──────────────────────────┐  │
 │  │ Anthropic ↔ Muse 格式转换 │  │   Jitter 拟人随机时间漂移 │  │
 │  └─────────────┬────────────┘  └──────────────────────────┘  │
 │                ▼                                             │
 │  ┌────────────────────────────────────────────────────────┐  │
 │  │                 Anti-Abuse Stealth Armor               │  │
 │  │  • isTrusted=true 物理鼠标点击与合法 Paste 剪贴板事件  │  │
 │  │  • 宿主 OS (Windows/Mac) 动态 Client Hints 对齐        │  │
 │  │  • 物理显卡硬件直通 (No SwiftShader CPU 光栅化)       │  │
 │  │  • RFC 6265bis __Host- 隔离作用域自动纠偏             │  │
 │  │  • 严格单并发互斥锁 (Concurrency=1) 防多端碰撞        │  │
 │  └─────────────────────────────┬──────────────────────────┘  │
 └────────────────────────────────┼─────────────────────────────┘
                                  ▼ (CDP over WebSocket)
                      [宿主机真实 Google Chrome 进程]
                                  │ (TLS 1.3 / HTTP/2 真实设备指纹)
                                  ▼
                         [https://muse.ai/]
```

1. **原汁原味 Anthropic Messages API (`POST /v1/messages`)**
   - 专为 **Claude Code**、**Cursor** 量身打造。
   - 原生支持 Anthropic 标准的 Server-Sent Events (SSE) 事件流：`message_start`、`content_block_start`、`content_block_delta`、`content_block_stop`、`message_delta`、`message_stop`。
   - 自动解析 `system` 指令、多轮历史对话与长文本上下文。
2. **多模态与兼容支持**
   - 保留原版对 OpenAI `/v1/chat/completions`、生图 `/v1/images/generations`、生视频 `/v1/videos` 的全面兼容。

---

## 🚀 快速上手

### 1. 环境准备
- Python 3.10+
- 已经安装官方 Google Chrome 浏览器

```bash
git clone https://github.com/<your-username>/muse2api-claude.git
cd muse2api-claude
pip install -r requirements.txt
```

### 2. 配置环境 (.env)
复制 `.env.example` 为 `.env`：
```ini
MUSE2API_KEY=your_secret_key_here
MUSE2API_HOST=127.0.0.1
MUSE2API_PORT=18610
MUSE2API_CHROMIUM=C:\Program Files\Google\Chrome\Application\chrome.exe
MUSE2API_CONCURRENCY=1
```

### 3. 导入 Cookie 并启动
你可以通过管理面板导入 muse.ai 的登录 Cookie，或者直接使用内置工具。
启动服务：
```bash
python -m uvicorn app:app --host 127.0.0.1 --port 18610
```

---

## 💻 对接 Claude Code

在终端中只需配置两个环境变量，Claude Code 即可无缝直连本地反代：

```bash
# Windows PowerShell
$env:ANTHROPIC_BASE_URL="http://127.0.0.1:18610"
$env:ANTHROPIC_API_KEY="your_secret_key_here"
claude

# Linux / macOS
export ANTHROPIC_BASE_URL="http://127.0.0.1:18610"
export ANTHROPIC_API_KEY="your_secret_key_here"
claude
```

---

## ☕ 极客声明与免责条约 (Disclaimer)

- **关于代码**：本项目属于个人极客自用实验研究作品，专为 Claude Code 生产力协同与反爬虫安全对抗研究打造。代码均按现状（**AS-IS**）提供，不提供任何形式的商业售后或人工客服答疑。
- **关于答疑**：作者目前处于**考研最后冲刺备考**阶段，仓库 Issues 标签默认关闭。请勿提交非代码缺陷类的日常咨询。若发现安全漏洞或改进空间，欢迎直接提交 Pull Request，共同维护开源极客生态。
- **关于合规**：请使用者严格遵守各平台服务条款（ToS），本项目仅用于学术与安全测试用途。
