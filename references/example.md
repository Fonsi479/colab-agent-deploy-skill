# 已验证配置示例

以下参数来自一次实际部署，供 Agent 对照。使用时先核对当前模型卡、程序版本与现场资源，再按用户预算适配。

## 云端与接口

| 项目 | 示例值 |
| --- | --- |
| GPU | NVIDIA A100-SXM4-80GB，81920 MiB |
| 模型仓库 | [Blackfrost-AI/Qwen3.8-27B-ABLITERATED-GGUF](https://huggingface.co/Blackfrost-AI/Qwen3.8-27B-ABLITERATED-GGUF) |
| 模型文件 | `Qwen3.8-27B-ABLITERATED-Q4_K_M.gguf`，约 16.8 GB |
| 推理服务 | llama.cpp，CUDA 后端，模型内置 MTP |
| 上下文 | 262144 tokens；根据加载后的显存占用调整 |
| 模型别名 | `qwen38-27b` |
| 云端监听 | `127.0.0.1:8081` |
| 本地 SSH 转发入口 | `127.0.0.1:18080` |
| OpenAI 兼容基址 | `http://127.0.0.1:18080/v1` |
| Anthropic 兼容基址 | `http://127.0.0.1:18080`；先核验当前服务的 Messages 端点 |
| 模型列表 | `http://127.0.0.1:18080/v1/models` |
| 聊天调用 | `POST http://127.0.0.1:18080/v1/chat/completions` |

本例真实问答曾返回：“云端模型已连接，你好。”复用时发起新请求，记录当次返回。

## Pi 图形界面

示例使用 [Pi](https://pi.dev/) 作为后端、[Pi Web](https://github.com/agegr/pi-web) 作为图形界面。版本组合为 Pi Web `0.15.0` 与 Pi `1.5.0`，浏览器入口为 `http://127.0.0.1:30141`。

| 配置项 | 示例值 |
| --- | --- |
| provider | `qwen-colab` |
| baseUrl | `http://127.0.0.1:18080/v1` |
| api | `openai-completions` |
| apiKey | `no-key`，本地接口占位值 |
| model id | `qwen38-27b` |
| 显示名 | `Qwen 27B · Colab A100` |
| 思考级别 | `high` |
| 工具 | `read`、`write`、`edit`、`bash` |

在演示目录向 Pi 发出：

> 请创建 agent-demo.txt，写入“云端模型已连接”，再用读取工具回读这个文件，最后回复读到的文字。Git 和其他文件保持现状。

本例实际出现 `write` 与 `read` 两次工具调用，独立读取文件后内容一致。新部署也按这个流程验收。

## 其他客户端

已有 Claude Code 的用户可以通过 [CC Switch](https://github.com/farion1231/cc-switch/releases) 添加独立配置，使用上表的 Anthropic 兼容基址与模型别名。先验证服务端支持的接口，再完成客户端真实问答。

使用其他 Agent 时保留其后端，在该客户端的独立配置中接入同一云端模型。
