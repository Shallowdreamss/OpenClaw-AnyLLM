# OpenClaw-AnyLLM

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform: No Root Required](https://img.shields.io/badge/Platform-No%20Root%20Required-green)](https://github.com/Shallowdreamss/openclaw-anyllm)
[![OpenClaw Compatible](https://img.shields.io/badge/OpenClaw-Compatible-blue)](https://openclaw.ai)

[English](#) | [中文](./README_zh.md)

**The ultimate guide to deploying OpenClaw without root privileges and integrating ANY custom LLM APIs** — including models that OpenClaw doesn't natively support.

> 📌 **Project Origin**: This is a tutorial project adapted and expanded from the [original Baidu AI Studio tutorial](https://aistudio.baidu.com/projectdetail/10013178). We've generalized the ERNIE 5.0-specific approach into a universal methodology for any LLM.
> 
> 🔗 **Try Online**: Visit our [PaddlePaddle AI Studio project](https://aistudio.baidu.com/projectdetail/10043604) to run the complete tutorial online!

---

## 🎯 What is OpenClaw-AnyLLM?

This project demonstrates how to:

- ✅ **Deploy OpenClaw in restricted environments** (no sudo/root required)
- ✅ **Integrate unsupported LLM providers** using OpenAI-compatible API wrappers
- ✅ **Use local or custom models** with OpenClaw's agent framework
- ✅ **Set up public access** via tunneling (ngrok/frp) for remote device management

This tutorial uses **Baidu AI Studio + ERNIE 5.0** as a real-world example, but the **methodology applies to any custom LLM API**.

---

## 🚀 Why This Matters

OpenClaw is a powerful AI agent framework, but it has native support for only a limited set of model providers (OpenAI, Anthropic, etc.). What if you want to use:

- 🇨🇳 **Chinese LLMs**: ERNIE, Qwen, GLM, DeepSeek, Kimi
- 🏢 **Enterprise internal models**: Privately deployed LLMs
- 🔬 **Research models**: LLaMA, Mistral fine-tuned versions, academic models
- 💻 **Self-hosted models**: Ollama, vLLM, LocalAI, LM Studio

**This guide shows you how** — even on cloud platforms where you can't install system packages.

---

## 📋 Use Cases

| Scenario | Why OpenClaw-AnyLLM? |
|----------|---------------------|
| 🎓 **Students** | Use free cloud platforms (AI Studio, Colab, Kaggle) without paying for commercial APIs |
| 🏢 **Enterprise** | Deploy with internal models while maintaining compliance |
| 🌍 **Non-English Markets** | Leverage region-specific LLMs (e.g., ERNIE in China, Qwen) |
| 🔒 **Restricted Environments** | Run on corporate/academic servers without admin rights |
| 🧪 **Researchers** | Test cutting-edge models with OpenClaw's agent capabilities |
| 💡 **Developers** | Learn how to integrate any API into an agent framework |

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│  Cloud Platform (Baidu AI Studio / Colab / etc.)    │
│                                                      │
│  ┌────────────────────────────────────────────┐    │
│  │  Terminal 1: OpenClaw Gateway              │    │
│  │  • Node.js (via NVM - no root needed)      │    │
│  │  • OpenClaw daemon                         │    │
│  │  • Port: 18789                             │    │
│  └────────────────────────────────────────────┘    │
│                                                      │
│  ┌────────────────────────────────────────────┐    │
│  │  Terminal 2: Tunnel (ngrok/frp)            │    │
│  │  • Public URL → localhost:18789            │    │
│  └────────────────────────────────────────────┘    │
│                                                      │
│  ┌────────────────────────────────────────────┐    │
│  │  Terminal 3: Device Management             │    │
│  │  • Approve/list connected devices          │    │
│  └────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
                      ↕
         ┌────────────────────────┐
         │   Custom LLM API       │
         │  (OpenAI-compatible)   │
         │  • ERNIE (Baidu)       │
         │  • Qwen (Alibaba)      │
         │  • GLM (Zhipu)         │
         │  • Ollama (Local)      │
         │  • vLLM (Self-hosted)  │
         └────────────────────────┘
```

---

## 🛠️ Prerequisites

- **Platform**: Any Linux environment (tested on Baidu AI Studio)
- **Permissions**: No sudo/root required
- **Network**: Internet access
- **LLM API**: Any OpenAI-compatible endpoint
- **Optional**: ngrok account for public access ([sign up free](https://ngrok.com))

---

## 📦 What's Included

```
OpenClaw-AnyLLM/
├── README.md                          # This file (English)
├── README_zh.md                       # Chinese version
├── install_nvm.sh                     # NVM installer script
├── ngrok-v3-stable-linux-amd64.tgz   # ngrok binary (optional)
├── main.ipynb                         # Interactive tutorial notebook
└── LICENSE                            # MIT License
```

---

## 🚀 Quick Start Guide

### Step 1: Install Node.js (No Root Required)

We use **NVM (Node Version Manager)** to install Node.js in user space:

```bash
# Method 1: Use included script
bash install_nvm.sh

# Method 2: Direct download
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# Load NVM
source ~/.bashrc

# Install Node.js 22
nvm install 22
nvm use 22
node --version  # Should output v22.x.x
```

---

### Step 2: Install and Configure OpenClaw

```bash
# Install OpenClaw globally
npm install -g openclaw@latest

# Initialize daemon
openclaw onboard --install-daemon

# Interactive setup
openclaw setup

# Configure gateway mode
openclaw config set gateway.mode local

# Security settings (for development)
openclaw config set gateway.auth.mode "none"
openclaw config set gateway.trustedProxies '["127.0.0.1"]'
```

---

### Step 3: Integrate Your Custom LLM

This is the **core technique** — making OpenClaw work with unsupported models.

#### ✅ Verified Example: Baidu ERNIE 5.0

```bash
openclaw config set models.providers.ernie-profile '{
  "baseUrl": "https://aistudio.baidu.com/llm/lmapi/v3",
  "apiKey": "YOUR_ERNIE_API_KEY",
  "api": "openai-completions",
  "models": [
    {
      "id": "ernie-5.0-thinking-preview",
      "name": "ERNIE 5.0 Thinking"
    }
  ]
}'

# Set as primary model
openclaw config set agents.defaults.model.primary "ernie-profile/ernie-5.0-thinking-preview"
```

**Get API Key**: Visit [Baidu AI Studio](https://aistudio.baidu.com) to register and get free credits

**✅ This configuration has been fully tested in AI Studio environment**

---

#### 🧪 Other Models (Theoretically Compatible - Unverified)

The following configurations are **based on providers' claimed OpenAI compatibility** but have **not been actually tested**. If you try them successfully, please submit a PR or Issue to share your findings!

<details>
<summary>📋 Alibaba Qwen (Click to expand)</summary>

```bash
# ⚠️ Unverified - Theoretical configuration
openclaw config set models.providers.qwen-profile '{
  "baseUrl": "https://dashscope.aliyuncs.com/compatible-mode/v1",
  "apiKey": "YOUR_QWEN_API_KEY",
  "api": "openai-completions",
  "models": [
    {
      "id": "qwen-turbo",
      "name": "Qwen Turbo"
    }
  ]
}'

openclaw config set agents.defaults.model.primary "qwen-profile/qwen-turbo"
```

**Get API Key**: [Alibaba Cloud DashScope](https://dashscope.aliyun.com/)  
**Note**: May need to adjust `baseUrl` and `model id` based on actual API docs
</details>

<details>
<summary>📋 Zhipu GLM-4 (Click to expand)</summary>

```bash
# ⚠️ Unverified - Theoretical configuration
openclaw config set models.providers.glm-profile '{
  "baseUrl": "https://open.bigmodel.cn/api/paas/v4",
  "apiKey": "YOUR_GLM_API_KEY",
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

**Get API Key**: [Zhipu AI Open Platform](https://open.bigmodel.cn/)  
**Note**: GLM API format may have subtle differences from OpenAI
</details>

<details>
<summary>📋 DeepSeek (Click to expand)</summary>

```bash
# ⚠️ Unverified - Theoretical configuration
openclaw config set models.providers.deepseek-profile '{
  "baseUrl": "https://api.deepseek.com/v1",
  "apiKey": "YOUR_DEEPSEEK_API_KEY",
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

**Get API Key**: [DeepSeek Platform](https://platform.deepseek.com/)
</details>

<details>
<summary>📋 Local Ollama (Click to expand)</summary>

```bash
# ⚠️ Unverified - Theoretical configuration
# Make sure Ollama is running (locally or remotely)
# Install Ollama: https://ollama.com/download

openclaw config set models.providers.ollama-profile '{
  "baseUrl": "http://localhost:11434/v1",
  "apiKey": "ollama",
  "api": "openai-completions",
  "models": [
    {
      "id": "llama3.2",
      "name": "LLaMA 3.2"
    }
  ]
}'

openclaw config set agents.defaults.model.primary "ollama-profile/llama3.2"
```

**Note**: Ollama's OpenAI-compatible mode needs version confirmation
</details>

---

#### 💬 Help Us Verify!

If you successfully configure any of the above models, or have other working LLM examples, please:
- 📝 Submit an Issue sharing your configuration
- 🎉 Create a Pull Request to update docs
- ⭐ Note which parameters needed adjustment

We'll mark verified configurations as ✅ and add them to the main docs!

---

#### 💡 Configuration Pattern

All configurations follow the same pattern:

```bash
openclaw config set models.providers.your-name '{
  "baseUrl": "API_ENDPOINT",           # Must be OpenAI-compatible
  "apiKey": "YOUR_KEY",                # Can be any string for local models
  "api": "openai-completions",         # Fixed value for OpenAI format
  "models": [                          # Can configure multiple models
    {
      "id": "model-id",                # Actual model identifier in API
      "name": "Display Name"           # Name shown in OpenClaw UI
    }
  ]
}'
```

---

### Step 4: Start the Gateway

Open **Terminal 1** and run:

```bash
openclaw gateway --port 18789 --verbose
```

**Keep this terminal running**. You should see log output similar to:

```
✓ Gateway started on port 18789
✓ Control UI available at http://localhost:18789
✓ Ready to accept device connections
```

---

### Step 5: Set Up Public Access (Optional but Recommended)

To allow remote devices to connect, use **ngrok** or **frp** to expose your gateway.

#### Option A: Using ngrok (Recommended for Beginners)

Open **Terminal 2**:

```bash
# 1. Extract ngrok (if using the included file)
tar -xzf ngrok-v3-stable-linux-amd64.tgz

# 2. Authenticate (get token from https://dashboard.ngrok.com)
./ngrok config add-authtoken YOUR_NGROK_TOKEN

# 3. Start tunnel
# Free version (random domain):
./ngrok http 18789

# Paid version (fixed domain):
./ngrok http --domain=your-domain.ngrok-free.app 18789
```

Success looks like:

```
Forwarding  https://xxxx-xx-xx-xx-xx.ngrok-free.app -> http://localhost:18789
```

#### Option B: Using frp (Better for Long-term)

frp requires your own server but is more stable. See [frp documentation](https://github.com/fatedier/frp)

---

#### Update CORS Settings

After getting your public URL, **must** update OpenClaw config:

```bash
# Replace with your actual ngrok domain
openclaw config set gateway.controlUi.allowedOrigins '["https://your-domain.ngrok-free.app"]'
```

Then restart the gateway in **Terminal 1** (Ctrl+C to stop, then re-run):

```bash
openclaw gateway --port 18789 --verbose
```

---

### Step 6: Approve Device Connections

When client devices (phones, tablets, other computers) try to connect, manage them in **Terminal 3**:

```bash
# List all pending connection requests
openclaw devices list

# Output example:
# Pending Requests:
# - UUID: abc-123-def-456
#   Name: iPhone 15
#   Requested: 2 minutes ago

# Approve specific device
openclaw devices approve abc-123-def-456

# View all approved devices
openclaw devices list --approved
```

---

## 🎓 Detailed Tutorial

### Using the Jupyter Notebook

The project includes an interactive tutorial `main.ipynb` covering:

1. ✅ Environment setup and NVM installation
2. ✅ Detailed OpenClaw configuration steps
3. ✅ ERNIE 5.0 API integration walkthrough
4. ✅ ngrok public tunneling configuration
5. ✅ Device management and troubleshooting
6. ✅ FAQ and common issues

**Run on Baidu AI Studio**:

1. Upload `main.ipynb` to your project
2. Execute cells step-by-step
3. Fill in your API keys as prompted

**Run Online**: Visit [our AI Studio project](https://aistudio.baidu.com/projectdetail/10043604) for a pre-configured environment!

---

## 🔧 Troubleshooting

### Issue 1: "Command not found: nvm"

**Cause**: NVM not properly loaded into current shell

**Solution**:

```bash
# Reload shell configuration
source ~/.bashrc
# If using zsh:
source ~/.zshrc

# Verify NVM is available
command -v nvm
# Should output: nvm

# If still fails, manually add to config
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc
source ~/.bashrc
```

---

### Issue 2: Port 18789 Already in Use

**Cause**: Another program is using the port

**Solution**:

```bash
# Check what's using the port
netstat -tulpn | grep 18789
# Or use lsof
lsof -i :18789

# Option 1: Kill the process (if safe)
kill -9 <PROCESS_ID>

# Option 2: Use a different port
openclaw gateway --port 18790 --verbose
# Remember to update ngrok config
```

---

### Issue 3: LLM API Authentication Failed

**Symptoms**: Gateway starts but errors when calling the model

**Troubleshooting Steps**:

1. **Verify API Key**:

```bash
# View current config
openclaw config get models.providers.ernie-profile

# Confirm apiKey is correct
```

2. **Test API Connectivity**:

```bash
# Direct test using curl (ERNIE example)
curl -X POST "https://aistudio.baidu.com/llm/lmapi/v3/chat/completions" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "ernie-5.0-thinking-preview",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

3. **Common Error Codes**:
   - `401 Unauthorized`: API key incorrect or expired
   - `403 Forbidden`: Account doesn't have access to this model
   - `429 Too Many Requests`: Rate limit exceeded
   - `500 Server Error`: API service issue

---

### Issue 4: ngrok Connection Dropped

**Cause**: Free ngrok has 2-hour session limit

**Solution**:

```bash
# Restart tunnel
./ngrok http 18789

# If domain changed, update CORS
openclaw config set gateway.controlUi.allowedOrigins '["https://new-domain.ngrok-free.app"]'
```

**Long-term Solutions**:
- Upgrade to ngrok paid plan (fixed domain)
- Use self-hosted frp
- Use Cloudflare Tunnel

---

### Issue 5: Device Connected but Can't Use

**Checklist**:

```bash
# 1. Confirm device is approved
openclaw devices list --approved

# 2. Check gateway logs (Terminal 1)
# Should see device connection logs

# 3. Verify model configuration
openclaw config get agents.defaults.model.primary

# 4. Test model availability (send message in OpenClaw UI manually)
```

---

## 🌍 Tested Platforms

| Platform | Status | Notes |
|----------|--------|-------|
| Baidu AI Studio | ✅ Fully Tested | Primary tutorial environment, stable |
| Google Colab | ✅ Compatible | Session time limits, need periodic re-run |
| Kaggle Notebooks | ✅ Compatible | Network access restricted |
| Gitpod | ✅ Compatible | Auto port forwarding, no ngrok needed |
| GitHub Codespaces | ✅ Compatible | Built-in port forwarding, best experience |
| AWS Cloud9 | ✅ Compatible | Use IAM roles for credentials |
| University Servers | ✅ Compatible | Perfect for restricted student accounts |
| Local Docker | ✅ Compatible | Need volume mounts for persistence |

---

## 🧪 Supported LLM Providers

### ✅ Verified Models

| Provider | Model | Status | Test Environment |
|----------|-------|--------|-----------------|
| Baidu | ERNIE 5.0 Thinking Preview | ✅ Fully Verified | AI Studio |

### 🔬 Theoretically Compatible (Awaiting Community Verification)

The following providers claim OpenAI-compatible API support and **should theoretically work**, but we haven't actually tested them yet.

#### Chinese Commercial Models

| Provider | Example Models | OpenAI Compatible Claim | Notes |
|----------|---------------|------------------------|-------|
| Alibaba | Qwen3-Max | ✅ Official docs | Need to verify actual compatibility |
| Zhipu | GLM-5 | ✅ Official docs | API format may differ |
| DeepSeek | DeepSeek-V3.2 | ✅ Official docs | Strong coding capabilities |
| Moonshot | Kimi-k2.5 | ✅ Official docs | Long context |

#### Self-Hosted Solutions

| Tool | OpenAI Compatible | Difficulty | Notes |
|------|------------------|-----------|-------|
| Ollama | ✅ Official support | ⭐ Easy | Need version confirmation |
| vLLM | ✅ Official support | ⭐⭐ Medium | High-performance inference |
| LocalAI | ✅ Native design | ⭐⭐ Medium | Multiple formats |
| LM Studio | ✅ Built-in | ⭐ Easy | GUI tool |
| text-generation-webui | ⚠️ Needs plugin | ⭐⭐⭐ Complex | Feature-rich |

#### International Cloud Services

Known services with OpenAI-compatible APIs:
- Together.ai (✅ Official docs confirmed)
- Groq (✅ Official docs confirmed)
- Fireworks AI (✅ Official docs confirmed)
- Replicate (⚠️ Needs confirmation)
- Perplexity (⚠️ Needs confirmation)

### 🤝 Help Us Verify

**We need community help!** If you successfully configure any of the above models:

1. **Submit Verification Report** - Share in Issues:
   - Model and provider used
   - Complete configuration commands
   - Whether parameters needed adjustment
   - Problems encountered and solutions

2. **Contribute Example Configs** - Create a PR:
   - Add to "Verified" list
   - Document special configuration notes
   - Provide test screenshots

3. **Report Incompatibility** - If a model doesn't work:
   - Describe specific errors
   - Help us update documentation

---

## 📚 Learning Resources

### OpenClaw Related

- **Official Docs**: https://docs.openclaw.ai
- **GitHub Repo**: https://github.com/openclaw/openclaw
- **Community**: https://discord.gg/openclaw

### LLM API Documentation

- **Baidu Qianfan**: https://cloud.baidu.com/doc/WENXINWORKSHOP
- **Alibaba DashScope**: https://help.aliyun.com/zh/dashscope
- **Zhipu AI**: https://open.bigmodel.cn/dev/api
- **DeepSeek**: https://platform.deepseek.com/api-docs
- **OpenAI API Reference**: https://platform.openai.com/docs/api-reference

### Tool Documentation

- **NVM**: https://github.com/nvm-sh/nvm
- **ngrok**: https://ngrok.com/docs
- **frp**: https://github.com/fatedier/frp

### Original Tutorial & Online Experience

- **This Project on AI Studio**: [Run Online](https://aistudio.baidu.com/projectdetail/10043604)
  - Complete interactive Jupyter Notebook tutorial
  - No local setup required, try online instantly
  - Pre-configured AI Studio environment
- **Original Inspiration**: [Baidu AI Studio Original Tutorial](https://aistudio.baidu.com/projectdetail/10013178)
  - Thanks to the original author for the ERNIE integration approach
  - This project extends it into a universal solution

---

## 🤝 Contributing

We welcome community contributions! You can participate by:

### What You Can Contribute

- 📝 **Documentation**: Fix errors, add details, translations
- 🐛 **Bug Reports**: Submit issues you encounter
- ✨ **New Features**: Add support for new LLM providers
- 💡 **Best Practices**: Share your deployment experiences
- 🎨 **Examples**: Provide use-case specific examples

### Contribution Process

1. **Fork this repository**

2. **Create feature branch**:
   ```bash
   git checkout -b feature/support-xyz-model
   ```

3. **Make changes and test**

4. **Commit changes**:
   ```bash
   git commit -m "Add support for XYZ model"
   ```

5. **Push to your fork**:
   ```bash
   git push origin feature/support-xyz-model
   ```

6. **Create Pull Request**

### Issue Submission Tips

Please include:

- OS and platform (AI Studio/Colab, etc.)
- Node.js and OpenClaw versions
- Complete error messages
- Steps to reproduce

---

## 📝 License

This project is licensed under the **MIT License**.

This means you can:
- ✅ Use this code and documentation freely
- ✅ Modify and distribute
- ✅ Use for commercial purposes

Only requirement:
- 📌 Retain original license notice
- 📌 State modifications you made

See [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

### Core Dependencies

- **[OpenClaw](https://github.com/openclaw/openclaw)** - Powerful AI agent framework
- **[NVM](https://github.com/nvm-sh/nvm)** - Node.js version manager
- **[ngrok](https://ngrok.com)** - Tunneling tool

### Inspiration & Resources

- **[This Project on AI Studio](https://aistudio.baidu.com/projectdetail/10043604)** - Run the complete tutorial online
- **[Original Baidu AI Studio Tutorial](https://aistudio.baidu.com/projectdetail/10013178)** - The starting point, thanks to the original author's pioneering work
- **Baidu AI Studio** - Providing free cloud compute resources

### Community Support

- All contributors who submitted issues and PRs
- Users who tested and provided feedback across platforms
- Community members sharing their experiences

---

## ⭐ Support This Project

If this project helps you, please consider:

- ⭐ **Star this repo** - Help more people discover it
- 🐛 **Report issues** - Help us improve
- 📖 **Share experiences** - Blog/social media posts about your usage
- 🤝 **Contribute code** - Add features or documentation
- 💬 **Join discussions** - Share ideas in Issues

---

## 💡 FAQ (Frequently Asked Questions)

### Q1: Must I use Baidu AI Studio?

**A**: No. This tutorial uses AI Studio as an example, but the method works on any Linux environment. You can use Colab, Kaggle, your own server, etc.

### Q2: Can I configure multiple LLMs simultaneously?

**A**: Yes! Just create different profiles for each LLM:

```bash
openclaw config set models.providers.ernie-profile '{...}'
openclaw config set models.providers.qwen-profile '{...}'
openclaw config set models.providers.glm-profile '{...}'
```

Then choose the model you need when using.

### Q3: Is free ngrok sufficient?

**A**: For learning and testing, yes. But it has limitations:
- Sessions disconnect after 2 hours
- Domain changes on each restart
- Request limits

For stable long-term service, consider:
- Upgrade to ngrok paid plan
- Use self-hosted frp
- Use Cloudflare Tunnel

### Q4: What if my LLM API isn't OpenAI-compatible?

**A**: You can:
1. Use adapter/proxy services (like LiteLLM) to convert formats
2. Write your own simple conversion service
3. Contact the LLM provider about OpenAI-compatible mode

### Q5: What about performance and latency?

**A**: Latency mainly depends on:
- LLM API response speed
- Network connection quality
- Tunnel latency (if using)

Local Ollama usually has lowest latency, cloud APIs depend on geography.

### Q6: Is it secure?

**A**: 
- Commercial APIs (ERNIE/Qwen) send data to provider servers
- Local Ollama processes all data locally
- OpenClaw itself doesn't collect your data
- Recommend enabling auth (`gateway.auth.mode`) for production

### Q7: What can I do with this?

**A**: OpenClaw supports:
- 💬 Chat conversations
- 🖥️ Computer control (with authorization)
- 📁 File operations
- 🌐 Web browsing
- 🔧 Tool usage
- 📱 Multi-device collaboration

Specific features depend on your connected LLM's capabilities.

---

## 📧 Contact

- **GitHub Issues**: [Submit Issue](https://github.com/Shallowdreamss/openclaw-anyllm/issues)
- **GitHub Discussions**: [Join Discussion](https://github.com/Shallowdreamss/openclaw-anyllm/discussions)
- **Email**: 2702220119@ahstu.edu.cn

---

## 🎉 Quick Reference

### One-Click Installation Commands

```bash
# Complete installation flow (execute in Linux terminal)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash && \
source ~/.bashrc && \
nvm install 22 && \
nvm use 22 && \
npm install -g openclaw@latest && \
openclaw onboard --install-daemon && \
openclaw setup && \
openclaw config set gateway.mode local && \
openclaw config set gateway.auth.mode "none"

# Then configure your LLM (replace with actual values):
openclaw config set models.providers.my-llm '{
  "baseUrl": "YOUR_API_ENDPOINT",
  "apiKey": "YOUR_API_KEY",
  "api": "openai-completions",
  "models": [{"id": "model-id", "name": "Display Name"}]
}'

openclaw config set agents.defaults.model.primary "my-llm/model-id"

# Start gateway
openclaw gateway --port 18789 --verbose
```

---

**🌟 Happy deploying! Feel free to ask questions in Issues.**

---

<div align="center">

**Built with ❤️ for the OpenClaw community and developers in restricted environments**

*Adapted from [Baidu AI Studio Original Tutorial](https://aistudio.baidu.com/projectdetail/10013178) | [Try Online](https://aistudio.baidu.com/projectdetail/10043604)*

</div>
