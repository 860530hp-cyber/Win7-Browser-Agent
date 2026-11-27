# Win7-Browser-Agent

[![Platform](https://img.shields.io/badge/Platform-Windows%207%20SP1%20x64%2B-0078D6?style=flat-square)](#环境要求)
[![Chrome](https://img.shields.io/badge/Chrome-109%2B%20(MV3)-4285F4?style=flat-square)](#环境要求)
[![Python](https://img.shields.io/badge/Built%20With-Python%203.8%20(PE32%2B%20x64)-3776AB?style=flat-square)](#文件清单与校验信息)
[![Upstream](https://img.shields.io/badge/Upstream-hooosberg%2FAgentLimb-black?style=flat-square&logo=github)](https://github.com/hooosberg/AgentLimb)
[![License](https://img.shields.io/badge/License-BSL%201.1-orange?style=flat-square)](https://github.com/hooosberg/AgentLimb/blob/main/LICENSE)

> **Windows 7 兼容适配版 · 免配置 Node.js / MCP 宿主的桌面浏览器 AI 自动化方案**

本项目基于原作者 **海森堡（[hooosberg](https://github.com/hooosberg/AgentLimb)）** 开发的开源项目 **[AgentLimb](https://github.com/hooosberg/AgentLimb)** 制作 **Windows 7 兼容适配版本**：针对 Windows 7（Chrome 109）环境进行了扩展兼容适配，并配套提供开箱即用的轻量桌面控制端 `AgentLimb_Studio_Win7.exe`，无需手动部署 Node.js 或外部 MCP 宿主即可直接通过大模型指令操控本地 Chrome 浏览器。

**开源协议与全部原始链接均沿用原作者项目约定（见文末[原作者与开源协议](#原作者与开源协议)）。**

---

## 目录

- [项目亮点与 Win7 适配说明](#项目亮点与-win7-适配说明)
- [文件清单与校验信息](#文件清单与校验信息)
- [环境要求](#环境要求)
- [快速使用指南](#快速使用指南)
- [Studio 功能与工作流说明](#studio-功能与工作流说明)
- [常见问题排查](#常见问题排查)
- [原作者与开源协议](#原作者与开源协议)
- [反馈与联系](#反馈与联系)

---

## 项目亮点与 Win7 适配说明

1. **Chrome 109（Win7 最终官方版）扩展兼容适配（`agentlimb-chrome-win7.zip`）**
   - 上游版本信息：`AgentLimb 0.8.2`（`0.8.2-l22`，Manifest V3，`minimum_chrome_version: 88`）。
   - 解决老版本 Chrome 无原生侧边栏 API（`chrome.sidePanel` 需 Chrome 114+）的问题：在 `manifest.json` 中补充 `action.default_popup`，并在 `service-worker.js` 中增加 `chrome.sidePanel` 存在性判断与标签页回退逻辑，使 Chrome 109 也能正常开启操作面板并保持持久调试与控制链路。
2. **零外部依赖 Win7 桌面端控制程序（`AgentLimb_Studio_Win7.exe`）**
   - 基于 Python 3.8 + 标准库（`tkinter` + `http.server` + `urllib`）通过 PyInstaller 打包为独立 64 位 Windows GUI 程序，不依赖 Node.js 与第三方安装包。
   - 内置本地 HTTP + SSE 桥接服务（默认端口 `8765`，支持 `8765~8780` 端口探测或自定义端口 `1024~65535`），启动后可自动发现并连接已加载的 AgentLimb Chrome 扩展。
   - 针对 Windows 7 界面与系统特性进行专项优化：
     - 兼容 Win7 常见分辨率（`800x600` / `1024x768` / `1280x720`）与系统 DPI 缩放（`1.0 / 1.25 / 1.5 / 2.0`）。
     - 过滤四字节 Unicode（限定 BMP `U+FFFF` 以内），避免 Win7 Tcl/Tk 8.6 渲染 Emoji 引发崩溃。
     - 内置现代根证书支持 Win7 HTTPS 请求，并提供「忽略 SSL 证书校验」开关以应对代理或自签名接口。
     - 支持兼容 OpenAI Chat Completions 格式的任意大模型接口，提供三档思考等级切换、右侧可折叠运行流程面板、本地会话自动保存及 Markdown / JSON 导出。

---

## 文件清单与校验信息

仓库根目录提供以下两个发布文件，下载后建议核对校验值以确保文件完整：

| 文件名 | 类型与规格 | 文件大小 | SHA-256 | MD5 |
|---|---|---|---|---|
| `agentlimb-chrome-win7.zip` | Chrome MV3 扩展源码压缩包（`0.8.2 / l22`，Win7 适配版，含 59 个归档条目） | `1,831,202` 字节 (`1.75 MB`) | `EFAB8D529DB627D8D3C774917C417AD864762B1314DBB9EC9EBFF16CD32161AC` | `E799DBDA58A50395047CBE4E7A33FACE` |
| `AgentLimb_Studio_Win7.exe` | 桌面控制端可执行文件（PE32+ x86-64 Windows GUI，Python 3.8 / PyInstaller 打包） | `9,748,752` 字节 (`9.30 MB`) | `FA9BA44693091A963CBBCE544B859A3DFE5F22436489C60A633F670EA55F3A8B` | `14146DA24FD32F686E8A28841218482E` |

> 可在 Windows PowerShell 中运行 `Get-FileHash <文件名> -Algorithm SHA256` 校验哈希值。

---

## 环境要求

- **操作系统**：Windows 7 SP1 64 位（推荐已安装 KB2999226 / UCRT 及 SHA-2 系统补丁，亦可在 Windows 10 / 11 64 位上运行）
- **浏览器**：Google Chrome `109.0.5414.120`（x64，Windows 7 官方最终支持版本）或基于 Chromium `88+` 的 64 位浏览器
- **大模型服务**：任意兼容 OpenAI `/v1/chat/completions` 接口格式的大模型 API Key（如 DeepSeek、硅基流动 SiliconFlow、Gemini 兼容端点、OpenAI、本地 Ollama 等）

---

## 快速使用指南

### 第一步：下载并解压扩展压缩包（注意纯英文路径）

1. 从本仓库下载 **`agentlimb-chrome-win7.zip`**。
2. 将压缩包完整解压到一个**路径中不包含任何中文（或非 ASCII 字符）的文件夹**，例如：
   ```text
   C:\agentlimb-chrome-win7
   ```
   或
   ```text
   D:\Tools\agentlimb-chrome-win7
   ```
   > ⚠️ **重要提示**：解压路径中**绝对不能有中文**（例如中文用户名桌面 `C:\Users\张三\Desktop\...` 或 `D:\下载\...`）。Windows 下 Chrome 加载含非 ASCII 路径的扩展会失败并报错：
   > `Could not load javascript 'kernel/control/content/a11y-tree.js' for script.`

### 第二步：在 Chrome 中加载已解压的扩展程序

1. 打开 Chrome 浏览器，在地址栏输入并访问：
   ```text
   chrome://extensions
   ```
2. 开启页面右上角的 **开发者模式（Developer mode）** 开关。
3. 点击左上角的 **加载已解压的扩展程序（Load unpacked）**。
4. 选择刚才解压出来的文件夹（包含 `manifest.json` 的目录，例如 `C:\agentlimb-chrome-win7\agentlimb-chrome-win7` 或 `C:\agentlimb-chrome-win7`），确认后即可看到 **AgentLimb** 扩展已成功启用。

### 第三步：下载并配置 `AgentLimb_Studio_Win7.exe`

1. 从本仓库下载 **`AgentLimb_Studio_Win7.exe`** 并双击运行。
2. 首次启动或点击左下角 **「连接与模型设置」**，在配置弹窗中填写以下信息：
   - **接口地址（API URL）**：填入兼容 OpenAI Chat Completions 的接口地址（可直接使用内置预设或手动填写，如 DeepSeek `https://api.deepseek.com/v1/chat/completions`、硅基流动 `https://api.siliconflow.cn/v1/chat/completions`、本地 Ollama �http://127.0.0.1:11434/v1/chat/completions` 等）。
   - **API Key（密钥）**：填入你自己的大模型 API Key（支持勾选「显示明文」核对）。
   - **模型名称（Model）**：填写目标模型 ID（如 `deepseek-chat`、`deepseek-ai/DeepSeek-V3`、`gemini-2.5-flash` 等）。
   - **桥接端口**：默认 `8765`（如无冲突保持默认即可，支持 `1024~65535`）。
   - 其他可选项：按需勾选「附带 `reasoning_effort` 参数」（仅推理模型需要）或「忽略 SSL 证书校验」。
3. 点击 **「保存并进入工作台」**。

### 第四步：选择思考等级并自动连接扩展

1. 在工作台右上角（或设置中）选择所需的 **思考等级**：
   - **低 (快速)**：极简思考、直接执行关键操作步骤，响应最快。
   - **中 (均衡)**：执行前简要分析页面状态与目标元素，异常时自动尝试替代选择器。
   - **高 (深度)**：启用深度推理与多步规划，每步输出分析、执行与回退策略，适合复杂多步骤网页任务。
2. Studio 启动本地桥接服务后，**会自动与 Chrome 中已加载的 AgentLimb 扩展程序建立连接**，界面状态栏会更新为「`[已连接] Chrome 插件`」。
   > 若状态显示「`[待授权] Chrome 插件`」，在 Chrome 工具栏点击 AgentLimb 扩展图标打开面板，点击 **「复制连接提示词」** 开启 30 分钟接入授权窗口，Studio 会自动完成配对，后续重启可自动重连。

### 第五步：发布指令操控浏览器

确认扩展已连接后，直接在底部输入框输入自然语言指令（`Enter` 发送，`Shift + Enter` 换行），或点击输入框上方的快捷按钮（**查看标签页**、**读取页面元素**、**总结当前页**）即可开始操控浏览器。执行期间可随时：
- 在右侧 **运行流程** 面板查看每一步工具调用参数、耗时与返回状态；
- 点击 **停止** 按钮随时中止当前自动化任务；
- 使用 **导出MD** 或 **保存JSON** 将本次对话与操作步骤保存到本地。

---

## Studio 功能与工作流说明

扩展与 Studio 之间通过本地 `127.0.0.1:8765~8780`（HTTP + SSE）通信：
- **Studio 精简版内置操控工具**：`tabs_context`、`tab_new`、`navigate`、`read_page`、`find`、`computer`（点击 / 输入 / 按键 / 滚动 / 截图）、`form_input`、`javascript_eval`、`wait`、`say`、`ping`。
- **Chrome 扩展完整内核工具目录（`0.8.2 / l22`）**：额外支持 `tab_close`、`tab_activate`、`browser_batch`、`read_console_messages`、`read_network_requests`（可供标准 MCP 客户端扩展接入）。

所有对话记录与运行日志自动保存在程序同级 `agentlimb_data` 目录，方便回溯与调试。

---

## 常见问题排查

| 现象 | 原因 | 解决方法 |
|---|---|---|
| Chrome 加载扩展时报错 `Could not load javascript 'kernel/control/content/a11y-tree.js' for script.` | 解压目录路径中含有中文或非 ASCII 字符 | 将文件夹移动到纯英文路径（如 `C:\agentlimb-chrome-win7`）后重新加载 |
| Studio 显示「未连接」或「待授权」 | 扩展未加载、Service Worker 休眠，或未完成首次配对 | 确认 Chrome 已开启并启用扩展；点击 Chrome 扩展图标打开面板，必要时点击「复制连接提示词」触发连接与授权 |
| Win7 下请求大模型 API 出现 SSL/TLS 证书报错 | 系统根证书老旧或使用了本地代理抓包 | 在「连接与模型设置」中勾选 **「忽略 SSL 证书校验」** 后重试 |
| 扩展加载时弹出空白或部分按钮异常 | 使用的 Chromium 内核低于 `88` | 请在 Windows 7 上使用 Chrome `109.0.5414.120`（x64） |

---

## 原作者与开源协议

- **原项目名称**：AgentLimb
- **原作者**：海森堡（hooosberg）
- **原项目地址 & 官网链接**：
  - GitHub 原仓库：https://github.com/hooosberg/AgentLimb
  - 原作者产品页（落地页）：https://hooosberg.com/apps/agentlimb/
  - 旧版站点（0.2 时代教程与文档）：https://agentlimb.com/
- **开源协议**：本项目沿用原仓库协议 **Business Source License 1.1 (BSL 1.1)**，详见仓库根目录 [`LICENSE`](./LICENSE) 与上游原协议链接：
  - https://github.com/hooosberg/AgentLimb/blob/main/LICENSE
  - 个人学习、研究、评估测试等**非商业用途免费使用**；任何公司、组织或商业用途需向原作者获取商业授权。根据协议约定，自 **2030-04-12** 起自动转换为 **Apache License 2.0**。

---

## 反馈与联系

如在 Windows 7 环境部署、Chrome 109 适配或 Studio 使用过程中遇到任何问题，欢迎通过邮箱反馈：

- **项目反馈邮箱**：[`rntacwljomxmxik@suiemail.com`](mailto:rntacwljomxmxik@suiemail.com)
