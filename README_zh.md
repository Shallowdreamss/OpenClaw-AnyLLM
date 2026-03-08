# OpenClaw-AnyLLM

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform: 无需 Root](https://img.shields.io/badge/Platform-无需%20Root-green)](https://github.com/Shallowdreamss/openclaw-anyllm)
[![OpenClaw 兼容](https://img.shields.io/badge/OpenClaw-兼容-blue)](https://openclaw.ai)

[English](./README.md) | [中文](#)

**在无 root 权限环境下部署 OpenClaw，并接入任意自定义 LLM API 的终极指南** — 包括 OpenClaw 原生不支持的模型。

> 📌 **项目来源**: 这是一个教程性质的项目，改编和扩展自[百度 AI Studio 原始教程](https://aistudio.baidu.com/projectdetail/10013178)。我们将原本针对 ERNIE 5.0 的具体教程推广为适用于任意 LLM 的通用部署方法论。
> 
> 🔗 **在线体验**: 访问我们的[飞桨 AI Studio 项目](https://aistudio.baidu.com/projectdetail/10043604)在线运行完整教程!

---

## 🎯 OpenClaw-AnyLLM 是什么?

本项目演示如何:

- ✅ **在受限环境下部署 OpenClaw**(无需 sudo/root 权限)
- ✅ **集成不受支持的 LLM 提供商**(使用 OpenAI 兼容 API 包装器)
- ✅ **使用本地或自定义模型**(配合 OpenClaw 的 Agent 框架)
- ✅ **通过隧道实现公网访问**(ngrok/frp)以便远程设备管理

本教程以**百度 AI Studio + ERNIE 5.0**为实际案例,但**方法论适用于任何自定义 LLM API**。

---

## 🚀 为什么需要这个项目

OpenClaw 是一个强大的 AI Agent 框架,但它仅原生支持有限的几个模型提供商(OpenAI、Anthropic 等)。如果你想使用:

- 🇨🇳 **中文大模型**: 文心一言、通义千问、智谱 GLM、DeepSeek、Kimi
- 🏢 **企业内部模型**: 私有化部署的 LLM
- 🔬 **研究模型**: LLaMA、Mistral 微调版本、学术实验模型
- 💻 **自托管模型**: Ollama、vLLM、LocalAI、LM Studio

**本指南告诉你如何做** — 即使在无法安装系统软件包的云平台上。

---

## 📋 适用场景

| 场景 | 为什么选择本方案 |
|------|----------------|
| 🎓 **学生用户** | 使用免费云平台(AI Studio、Colab、Kaggle)部署,无需付费商业 API |
| 🏢 **企业用户** | 在受限环境中使用内部模型,保持数据合规性 |
| 🌍 **非英语地区** | 利用本地化 LLM(如中国的文心一言、通义千问) |
| 🔒 **受限服务器** | 学校/公司服务器无管理员权限也能部署 |
| 🧪 **研究人员** | 快速测试前沿模型与 OpenClaw Agent 能力的结合 |
| 💡 **开发者** | 学习如何将任意 API 集成到 Agent 框架 |

---

## 🏗️ 架构概览

```
┌─────────────────────────────────────────────────────┐
│  云平台 (百度 AI Studio / Colab / Kaggle / 等)        │
│                                                      │
│  ┌────────────────────────────────────────────┐    │
│  │  终端 1: OpenClaw 网关                      │    │
│  │  • Node.js (通过 NVM - 无需 root)          │    │
│  │  • OpenClaw 守护进程                        │    │
│  │  • 端口: 18789                              │    │
│  └────────────────────────────────────────────┘    │
│                                                      │
│  ┌────────────────────────────────────────────┐    │
│  │  终端 2: 隧道服务 (ngrok/frp)               │    │
│  │  • 公网 URL → localhost:18789               │    │
│  └────────────────────────────────────────────┘    │
│                                                      │
│  ┌────────────────────────────────────────────┐    │
│  │  终端 3: 设备管理                           │    │
│  │  • 批准/列出已连接设备                      │    │
│  └────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
                      ↕
         ┌────────────────────────┐
         │   自定义 LLM API        │
         │  (任何 OpenAI 兼容)     │
         │  • ERNIE (百度)         │
         │  • Qwen (阿里)          │
         │  • GLM (智谱)           │
         │  • Ollama (本地)        │
         │  • vLLM (自托管)        │
         └────────────────────────┘
```

---

## 🛠️ 前提条件

- **平台**: 任何 Linux 环境(已在百度 AI Studio 上测试)
- **权限**: 无需 sudo/root
- **网络**: 互联网接入
- **LLM API**: 任何 OpenAI 兼容端点
- **可选**: ngrok 账号以实现公网访问([免费注册](https://ngrok.com))

---

## 📦 项目文件说明

```
OpenClaw-AnyLLM/
├── README.md                          # 英文文档
├── README_zh.md                       # 中文文档(本文件)
├── install_nvm.sh                     # NVM 安装脚本
├── ngrok-v3-stable-linux-amd64.tgz   # ngrok 二进制文件(可选)
├── main.ipynb                         # 交互式教程笔记本
└── LICENSE                            # MIT 许可证
```

---

## 🚀 快速开始指南

### 步骤 1: 安装 Node.js(无需 Root)

我们使用 **NVM (Node Version Manager)** 在用户空间安装 Node.js:

```bash
# 方法 1: 使用包含的脚本
bash install_nvm.sh

# 方法 2: 直接下载安装
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 加载 NVM
source ~/.bashrc

# 安装 Node.js 22
nvm install 22
nvm use 22
node --version  # 应输出 v22.x.x
```

---

### 步骤 2: 安装和配置 OpenClaw

```bash
# 全局安装 OpenClaw
npm install -g openclaw@latest

# 初始化守护进程
openclaw onboard --install-daemon

# 交互式设置
openclaw setup

# 配置网关模式
openclaw config set gateway.mode local

# 安全设置(开发环境)
openclaw config set gateway.auth.mode "none"
openclaw config set gateway.trustedProxies '["127.0.0.1"]'
```

---

### 步骤 3: 集成自定义 LLM

这是**核心技术** — 让 OpenClaw 支持原生不支持的模型。

#### 🔵 示例 1: 百度文心一言 ERNIE 5.0

```bash
openclaw config set models.providers.ernie-profile '{
  "baseUrl": "https://aistudio.baidu.com/llm/lmapi/v3",
  "apiKey": "你的_ERNIE_API_KEY",
  "api": "openai-completions",
  "models": [
    {
      "id": "ernie-5.0-thinking-preview",
      "name": "ERNIE 5.0 Thinking"
    }
  ]
}'

# 设置为主要模型
openclaw config set agents.defaults.model.primary "ernie-profile/ernie-5.0-thinking-preview"
```

**获取 API Key**: 访问 [百度 AI Studio](https://aistudio.baidu.com) 注册并获取免费额度

---

#### 🟢 示例 2: 阿里通义千问 Qwen

```bash
openclaw config set models.providers.qwen-profile '{
  "baseUrl": "https://dashscope.aliyuncs.com/compatible-mode/v1",
  "apiKey": "你的_QWEN_API_KEY",
  "api": "openai-completions",
  "models": [
    {
      "id": "qwen-turbo",
      "name": "Qwen Turbo"
    },
    {
      "id": "qwen-plus",
      "name": "Qwen Plus"
    }
  ]
}'

openclaw config set agents.defaults.model.primary "qwen-profile/qwen-turbo"
```

**获取 API Key**: 访问 [阿里云百炼](https://dashscope.aliyun.com/)

---

#### 🟣 示例 3: 智谱 GLM-4

```bash
openclaw config set models.providers.glm-profile '{
  "baseUrl": "https://open.bigmodel.cn/api/paas/v4",
  "apiKey": "你的_GLM_API_KEY",
  "api": "openai-completions",
  "models": [
    {
      "id": "glm-4",
      "name": "GLM-4"
    }
  ]
}'

openclaw config set agents.defaults.model.primary "glm-profile/glm-4"
```

**获取 API Key**: 访问 [智谱AI开放平台](https://open.bigmodel.cn/)

---

#### 🟠 示例 4: DeepSeek

```bash
openclaw config set models.providers.deepseek-profile '{
  "baseUrl": "https://api.deepseek.com",
  "apiKey": "你的_DEEPSEEK_API_KEY",
  "api": "openai-completions",
  "models": [
    {
      "id": "deepseek-chat",
      "name": "DeepSeek Chat"
    }
  ]
}'

openclaw config set agents.defaults.model.primary "deepseek-profile/deepseek-chat"
```

**获取 API Key**: 访问 [DeepSeek 平台](https://platform.deepseek.com/)

---

#### 🔴 示例 5: 本地 Ollama

```bash
# 确保 Ollama 服务正在运行(本地或远程)
# 安装 Ollama: https://ollama.com/download

openclaw config set models.providers.ollama-profile '{
  "baseUrl": "http://localhost:11434/v1",
  "apiKey": "ollama",
  "api": "openai-completions",
  "models": [
    {
      "id": "llama3.2",
      "name": "LLaMA 3.2"
    },
    {
      "id": "qwen2.5",
      "name": "Qwen 2.5"
    }
  ]
}'

openclaw config set agents.defaults.model.primary "ollama-profile/llama3.2"
```

---

#### 💡 配置要点说明

所有配置都遵循相同的模式:

```bash
openclaw config set models.providers.你的名称 '{
  "baseUrl": "API端点地址",           # 必须是 OpenAI 兼容格式
  "apiKey": "你的密钥",                # 某些本地模型可以是任意字符串
  "api": "openai-completions",        # 固定值,表示使用 OpenAI 格式
  "models": [                          # 可以配置多个模型
    {
      "id": "模型ID",                  # API 中的实际模型标识符
      "name": "显示名称"                # 在 OpenClaw UI 中显示的名称
    }
  ]
}'
```

---

### 步骤 4: 启动网关

打开**终端 1**并运行:

```bash
openclaw gateway --port 18789 --verbose
```

**保持此终端运行**。你应该看到类似以下的日志输出:

```
✓ Gateway started on port 18789
✓ Control UI available at http://localhost:18789
✓ Ready to accept device connections
```

---

### 步骤 5: 设置公网访问(可选但推荐)

要允许远程设备连接,使用 **ngrok** 或 **frp** 暴露你的网关。

#### 选项 A: 使用 ngrok(推荐初学者)

打开**终端 2**:

```bash
# 1. 解压 ngrok(如果使用项目提供的)
tar -xzf ngrok-v3-stable-linux-amd64.tgz

# 2. 认证(从 https://dashboard.ngrok.com 获取令牌)
./ngrok config add-authtoken 你的_NGROK_TOKEN

# 3. 启动隧道
# 免费版(随机域名):
./ngrok http 18789

# 付费版(固定域名):
./ngrok http --domain=你的域名.ngrok-free.app 18789
```

成功后会显示:

```
Forwarding  https://xxxx-xx-xx-xx-xx.ngrok-free.app -> http://localhost:18789
```

#### 选项 B: 使用 frp(适合长期使用)

frp 需要自己的服务器,但配置后更稳定。参考 [frp 文档](https://github.com/fatedier/frp)

---

#### 更新 CORS 设置

获取公网 URL 后,**必须**更新 OpenClaw 配置:

```bash
# 替换为你的实际 ngrok 域名
openclaw config set gateway.controlUi.allowedOrigins '["https://你的域名.ngrok-free.app"]'
```

然后在**终端 1**中重启网关(Ctrl+C 停止,然后重新运行):

```bash
openclaw gateway --port 18789 --verbose
```

---

### 步骤 6: 批准设备连接

当客户端设备(如手机、平板、其他电脑)尝试连接时,在**终端 3**中管理:

```bash
# 列出所有待处理的连接请求
openclaw devices list

# 输出示例:
# Pending Requests:
# - UUID: abc-123-def-456
#   Name: iPhone 15
#   Requested: 2 minutes ago

# 批准特定设备
openclaw devices approve abc-123-def-456

# 查看所有已连接设备
openclaw devices list --approved
```

---

## 🎓 详细教程

### 使用 Jupyter Notebook

项目包含一个交互式教程 `main.ipynb`,涵盖:

1. ✅ 环境准备和 NVM 安装
2. ✅ OpenClaw 配置步骤详解
3. ✅ ERNIE 5.0 API 接入实战
4. ✅ ngrok 公网穿透配置
5. ✅ 设备管理和故障排查
6. ✅ 常见问题解答

**在百度 AI Studio 中运行**:

1. 上传 `main.ipynb` 到你的项目
2. 按照笔记本中的单元格逐步执行
3. 根据提示填入你的 API 密钥

---

## 🔧 故障排查

### 问题 1: "Command not found: nvm"

**原因**: NVM 未正确加载到当前 shell

**解决方案**:

```bash
# 重新加载 shell 配置
source ~/.bashrc
# 如果使用 zsh:
source ~/.zshrc

# 验证 NVM 是否可用
command -v nvm
# 应输出: nvm

# 如果仍然无效,手动添加到配置文件
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc
source ~/.bashrc
```

---

### 问题 2: 端口 18789 已被占用

**原因**: 其他程序正在使用该端口

**解决方案**:

```bash
# 查看占用端口的进程
netstat -tulpn | grep 18789
# 或使用 lsof
lsof -i :18789

# 方案 1: 杀死占用进程(如果可以)
kill -9 <进程ID>

# 方案 2: 使用其他端口
openclaw gateway --port 18790 --verbose
# 记得同步修改 ngrok 配置
```

---

### 问题 3: LLM API 认证失败

**症状**: 网关启动正常,但调用模型时报错

**排查步骤**:

1. **验证 API 密钥**:

```bash
# 查看当前配置
openclaw config get models.providers.ernie-profile

# 确认 apiKey 是否正确
```

2. **测试 API 连通性**:

```bash
# 使用 curl 直接测试(以 ERNIE 为例)
curl -X POST "https://aistudio.baidu.com/llm/lmapi/v3/chat/completions" \
  -H "Authorization: Bearer 你的_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "ernie-5.0-thinking-preview",
    "messages": [{"role": "user", "content": "你好"}]
  }'
```

3. **常见错误码**:
   - `401 Unauthorized`: API 密钥错误或已过期
   - `403 Forbidden`: 账号未开通该模型权限
   - `429 Too Many Requests`: 超出速率限制
   - `500 Server Error`: API 服务端问题

---

### 问题 4: ngrok 连接断开

**原因**: 免费版 ngrok 有 2 小时会话限制

**解决方案**:

```bash
# 重新启动隧道
./ngrok http 18789

# 如果域名变了,记得更新 CORS
openclaw config set gateway.controlUi.allowedOrigins '["https://新域名.ngrok-free.app"]'
```

**长期方案**:
- 升级到 ngrok 付费版(固定域名)
- 使用自托管的 frp
- 使用 Cloudflare Tunnel

---

### 问题 5: 设备连接后无法使用

**排查清单**:

```bash
# 1. 确认设备已批准
openclaw devices list --approved

# 2. 检查网关日志(终端 1)
# 应该能看到设备连接的日志

# 3. 验证模型配置
openclaw config get agents.defaults.model.primary

# 4. 测试模型可用性(在 OpenClaw UI 中手动发送消息)
```

---

## 🌍 测试通过的平台

| 平台 | 状态 | 注意事项 |
|------|------|---------|
| 百度 AI Studio | ✅ 完全测试 | 本教程主要环境,稳定可靠 |
| Google Colab | ✅ 兼容 | 会话有时间限制,需要定期重新运行 |
| Kaggle Notebooks | ✅ 兼容 | 网络访问受限,某些 API 可能无法访问 |
| Gitpod | ✅ 兼容 | 自动端口转发,无需 ngrok |
| GitHub Codespaces | ✅ 兼容 | 内置端口转发,体验最佳 |
| AWS Cloud9 | ✅ 兼容 | 使用 IAM 角色管理凭据 |
| 学校/公司服务器 | ✅ 兼容 | 非常适合无管理员权限的场景 |
| 本地 Docker | ✅ 兼容 | 需要挂载卷保存数据 |

---

## 🧪 支持的 LLM 提供商

### 已测试的商业模型

| 提供商 | 模型示例 | OpenAI 兼容 | 备注 |
|--------|---------|------------|------|
| 百度 | ERNIE 3.5/4.0/5.0 | ✅ | 免费额度充足 |
| 阿里 | Qwen-Turbo/Plus/Max | ✅ | 性价比高 |
| 智谱 | GLM-4/GLM-4V | ✅ | 支持视觉理解 |
| DeepSeek | DeepSeek-Chat/Coder | ✅ | 编程能力强 |
| 月之暗面 | Moonshot (Kimi) | ✅ | 长上下文 |
| MiniMax | abab-6.5 | ✅ | 多模态支持 |

### 自托管方案

| 工具 | 说明 | 难度 |
|------|------|------|
| Ollama | 最简单的本地部署方案 | ⭐ 简单 |
| vLLM | 高性能推理引擎 | ⭐⭐ 中等 |
| LocalAI | 支持多种模型格式 | ⭐⭐ 中等 |
| LM Studio | GUI 工具,适合非技术用户 | ⭐ 简单 |
| text-generation-webui | 功能丰富的 Web UI | ⭐⭐⭐ 复杂 |

### OpenAI 兼容云服务

- Together.ai
- Groq
- Fireworks AI
- Replicate
- Perplexity

---

## 📚 学习资源

### OpenClaw 相关

- **官方文档**: https://docs.openclaw.ai
- **GitHub 仓库**: https://github.com/openclaw/openclaw
- **社区论坛**: https://discord.gg/openclaw

### LLM API 文档

- **百度千帆**: https://cloud.baidu.com/doc/WENXINWORKSHOP
- **阿里百炼**: https://help.aliyun.com/zh/dashscope
- **智谱AI**: https://open.bigmodel.cn/dev/api
- **DeepSeek**: https://platform.deepseek.com/api-docs
- **OpenAI API 参考**: https://platform.openai.com/docs/api-reference

### 工具文档

- **NVM**: https://github.com/nvm-sh/nvm
- **ngrok**: https://ngrok.com/docs
- **frp**: https://github.com/fatedier/frp

### 原始教程与在线体验

- **本项目飞桨 AI Studio 版本**: [在线运行](https://aistudio.baidu.com/projectdetail/10043604)
  - 完整的交互式 Jupyter Notebook 教程
  - 无需本地配置,在线即可体验
  - 预配置的 AI Studio 环境
- **原始灵感来源**: [百度 AI Studio 原始教程](https://aistudio.baidu.com/projectdetail/10013178)
  - 感谢原作者提供的 ERNIE 集成思路
  - 本项目在此基础上扩展为通用方案

---

## 🤝 贡献指南

我们非常欢迎社区贡献!你可以通过以下方式参与:

### 可以贡献的内容

- 📝 **文档改进**: 修正错误、补充细节、翻译
- 🐛 **问题报告**: 提交你遇到的 bug
- ✨ **新功能**: 添加对新 LLM 提供商的支持
- 💡 **最佳实践**: 分享你的部署经验
- 🎨 **示例代码**: 提供具体使用场景的示例

### 贡献流程

1. **Fork 本仓库**

2. **创建特性分支**:
   ```bash
   git checkout -b feature/支持某某模型
   ```

3. **进行修改并测试**

4. **提交更改**:
   ```bash
   git commit -m "添加对某某模型的支持"
   ```

5. **推送到你的 Fork**:
   ```bash
   git push origin feature/支持某某模型
   ```

6. **创建 Pull Request**

### 提交 Issue 的建议

请包含以下信息:

- 操作系统和平台(AI Studio/Colab 等)
- Node.js 和 OpenClaw 版本
- 完整的错误信息
- 复现步骤

---

## 📝 许可证

本项目采用 **MIT 许可证**。

这意味着你可以:
- ✅ 自由使用本项目代码和文档
- ✅ 修改和分发
- ✅ 用于商业目的

唯一要求:
- 📌 保留原始许可证声明
- 📌 声明你所做的修改

详见 [LICENSE](LICENSE) 文件。

---

## 🙏 致谢

### 核心依赖

- **[OpenClaw](https://github.com/openclaw/openclaw)** - 强大的 AI Agent 框架
- **[NVM](https://github.com/nvm-sh/nvm)** - Node.js 版本管理器
- **[ngrok](https://ngrok.com)** - 内网穿透工具

### 灵感来源与资源

- **[本项目飞桨 AI Studio 版本](https://aistudio.baidu.com/projectdetail/10043604)** - 在线运行完整教程
- **[百度 AI Studio 原始教程](https://aistudio.baidu.com/projectdetail/10013178)** - 本项目的起点,感谢原作者的开创性工作
- **百度 AI Studio** - 提供免费云计算资源

### 社区支持

- 所有提交 Issue 和 PR 的贡献者
- 在各平台测试并反馈的用户
- 分享使用经验的社区成员

---

## ⭐ 支持本项目

如果本项目对你有帮助,请考虑:

- ⭐ **Star 本仓库** - 让更多人发现这个项目
- 🐛 **报告问题** - 帮助我们改进
- 📖 **分享经验** - 在博客/社交媒体上分享你的使用心得
- 🤝 **贡献代码** - 添加新功能或文档
- 💬 **参与讨论** - 在 Issues 中分享想法

---

## 💡 常见问题 (FAQ)

### Q1: 我必须使用百度 AI Studio 吗?

**A**: 不是。本教程以 AI Studio 为例,但方法适用于任何 Linux 环境。你可以在 Colab、Kaggle、自己的服务器等任何平台使用。

### Q2: 我可以同时配置多个 LLM 吗?

**A**: 可以!只需为每个 LLM 创建不同的 profile:

```bash
openclaw config set models.providers.ernie-profile '{...}'
openclaw config set models.providers.qwen-profile '{...}'
openclaw config set models.providers.glm-profile '{...}'
```

然后在使用时选择需要的模型。

### Q3: 免费版 ngrok 够用吗?

**A**: 对于学习和测试足够了。但有以下限制:
- 会话 2 小时后断开
- 域名每次重启都会变化
- 有请求数量限制

如果需要稳定的长期服务,建议:
- 升级 ngrok 付费版
- 使用 frp 自托管
- 使用 Cloudflare Tunnel

### Q4: 我的 LLM API 不兼容 OpenAI 格式怎么办?

**A**: 你可以:
1. 使用适配器/代理服务(如 LiteLLM)转换格式
2. 自己编写一个简单的转换服务
3. 联系 LLM 提供商看是否支持 OpenAI 兼容模式

### Q5: 性能如何?是否有延迟?

**A**: 延迟主要取决于:
- LLM API 的响应速度
- 网络连接质量
- 如果使用隧道,隧道的延迟

本地 Ollama 通常延迟最低,云 API 取决于地理位置。

### Q6: 数据安全吗?

**A**: 
- 如果使用商业 API(ERNIE/Qwen 等),数据会发送到提供商服务器
- 如果使用本地 Ollama,数据完全在本地处理
- OpenClaw 本身不会收集你的数据
- 建议生产环境启用认证(`gateway.auth.mode`)

### Q7: 我可以用这个做什么?

**A**: OpenClaw 支持:
- 💬 聊天对话
- 🖥️ 电脑控制(需要授权)
- 📁 文件操作
- 🌐 网页浏览
- 🔧 工具使用
- 📱 多设备协同

具体功能取决于你连接的 LLM 能力。

---

## 📧 联系方式

- **GitHub Issues**: [提交问题](https://github.com/Shallowdreamss/openclaw-anyllm/issues)
- **GitHub Discussions**: [参与讨论](https://github.com/Shallowdreamss/openclaw-anyllm/discussions)
- **邮箱**: 2702220119@ahstu.edu.cn

---

## 🎉 快速参考

### 一键复制安装命令

```bash
# 完整安装流程(在 Linux 终端中执行)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash && \
source ~/.bashrc && \
nvm install 22 && \
nvm use 22 && \
npm install -g openclaw@latest && \
openclaw onboard --install-daemon && \
openclaw setup && \
openclaw config set gateway.mode local && \
openclaw config set gateway.auth.mode "none"

# 然后配置你的 LLM(替换为实际值):
openclaw config set models.providers.my-llm '{
  "baseUrl": "你的_API_端点",
  "apiKey": "你的_API_密钥",
  "api": "openai-completions",
  "models": [{"id": "模型ID", "name": "显示名称"}]
}'

openclaw config set agents.defaults.model.primary "my-llm/模型ID"

# 启动网关
openclaw gateway --port 18789 --verbose
```

---

**🌟 祝你部署顺利!有问题随时在 Issues 中提问。**

---

<div align="center">

**用 ❤️ 为 OpenClaw 社区和在受限环境中工作的开发者打造**

*改编自 [百度 AI Studio 原始教程](https://aistudio.baidu.com/projectdetail/10013178) | [在线体验本项目](https://aistudio.baidu.com/projectdetail/10043604)*

</div>
