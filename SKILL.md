---
name: colab-agent-deploy
description: 让 Agent 在 Google Colab 部署 GGUF 模型，通过 SSH 接入本机聊天客户端，完成真实问答和工具调用验收。适用于云端推理、本机 Agent 协作及已有 Colab 会话的恢复。
---

# Colab Agent Deploy

把模型推理放在 Colab，把聊天界面和工具保留在用户电脑。交付可重新启动的笔记本、可直接打开的聊天入口，以及真实调用结果。

## 先读取现场

1. 确认用户选择的模型、Agent 后端、聊天界面和算力预算。读取现有 Colab 会话、GPU、模型文件、SSH 转发及客户端配置，优先复用。
2. 用同一 Google 账号打开 [Colab](https://colab.research.google.com/)，核对已到账点数与当前 GPU 费率。登录、验证码、账号授权和付款页面交给用户完成，再继续执行。
3. 保留用户选定的 Agent 后端，图形应用作为界面层。新增配置使用独立 profile，并保存原配置备份。
4. 需要一套具体参数时，读取 [已验证配置示例](references/example.md)。把示例中的模型、端口和上下文容量作为可调整参数。

## 部署模型

1. 阅读模型卡、GGUF 文件列表和当前 [llama.cpp](https://github.com/ggml-org/llama.cpp) 文档。根据实测 GPU 显存、模型大小和上下文开销选择量化与参数；用当前二进制的 `--help` 核对启动选项。
2. 将模型从原仓库下载到云端。按发布方提供的校验值验证文件；同时保存本地计算的 SHA-256，供后续恢复时比对。
3. 准备可重复运行的 `.ipynb`：环境与文件检查、CUDA 构建、模型启动、接口验证、状态保存各占独立单元格。记录实际使用的代码版本、模型文件和参数。
4. 服务绑定云端回环地址。通过现有 SSH 转发接到本机回环地址；首次连接时核对 SSH 主机公钥。需要调整端口时同时更新隧道和客户端。
5. 读取 `/v1/models`，再向 `/v1/chat/completions` 发起真实 POST 请求，核对模型 ID 与返回文字。接口返回后再进入客户端接入。

## 连接本机 Agent

1. 根据用户选定的后端和客户端读取其当前官方文档，填写实际协议、基址与模型 ID。OpenAI 兼容入口通常带 `/v1`；Anthropic 兼容入口以服务实际支持的端点为准。
2. 用户需要图形界面时，准备可直接打开的聊天应用或网页快捷入口，并在真实聊天窗口中完成问答。保留现有客户端、供应商配置和工具。
3. 在独立演示目录开放本任务需要的文件工具。让模型创建一个文件、写入指定短句，再通过读取工具回读；随后由部署 Agent 独立读取文件，核对内容。
4. 用户选择联网搜索时，再接入搜索组件。可将 [DuckDuckGo MCP](https://github.com/nickclyde/duckduckgo-mcp-server) 放在 Colab CPU 上运行，并复用现有 SSH。实际执行 `search` 与 `fetch_content` 后记录搜索和正文读取结果。

## 保存与交付

- 保存笔记本、模型来源、SHA-256、启动命令、隧道配置和独立客户端 profile。凭据保存在用户环境的密钥服务；配置说明与日志只保留脱敏值。
- 提供一条恢复路径：检查会话与模型文件 → 恢复服务与隧道 → 验证 API → 打开聊天入口。
- 根据用户当次指令处理会话。用户明确指定并授权释放的会话，释放后再次读取会话列表核对；共享会话按其既有用途保留。
- 报告应用入口、模型和 GPU、实际通过项、尚待执行项，以及用户下一步操作。将真实测试输出与配置说明分开呈现。

完成标准：真实 API 回复、真实聊天窗口回复、模型发起的文件写入与回读、部署 Agent 的独立文件核对均有证据。搜索属于用户选择的附加验收项。

## 官方入口

- [Google 注册](https://accounts.google.com/signup)
- [Hugging Face 注册](https://huggingface.co/join) · [Read 令牌](https://huggingface.co/settings/tokens)：公开文件先直接下载，需要认证时采用所需最小权限。
- [Colab FAQ](https://research.google.com/colaboratory/faq.html) · [Google AI 套餐权益](https://one.google.com/about/google-ai-plans/)
- [Google Colab CLI](https://github.com/googlecolab/google-colab-cli)：先阅读当前连接与凭据存储说明，再选择与用户环境匹配的接入方式。
