# CryptoTGBot

简体中文 | [English](README.en.md)

CryptoTGBot 是带 Gradio 后台的 Telegram 交易监控机器人。它通过链上接口检查配置地址的交易，按模板生成通知，并发送到已记录的 Telegram 群组。

## 功能

- Gradio 后台管理 Telegram Bot Token、Etherscan API Key、监控地址、消息模板和发送频控。
- 通过 Telegram Bot API 轮询识别机器人所在群组。
- 支持交易通知、自定义定时消息和可选代理配置，代理默认关闭。
- 将 token、配置、群组 ID、交易状态和日志隔离在本地。

## 快速开始

```powershell
uv sync
uv run python main.py
```

启动后打开控制台输出的 Gradio 地址，完成 Bot Token、API Key、监控地址和后台密码配置。

## 安装

### uv

```powershell
uv sync
uv run python main.py
```

### 原生 venv

```powershell
python -m venv .venv
.\.venv\Scripts\activate
python -m pip install -r requirements.txt
python main.py
```

### Conda

```bash
conda env create -f environment.yml
conda activate crypto-tg-bot
python main.py
```

## 使用

| 命令 | 用途 |
| --- | --- |
| `uv run python main.py` | 默认 Gradio 后台和机器人流程 |

## 配置

- `config.json`：后台保存的运行配置，包含敏感信息，已忽略。
- `PROXY_ENABLED` / `PROXY_URL`：保存在 `config.json` 中，默认关闭；开启后 Telegram 与链上 API 请求会使用该代理。
- `.env`：可选本地环境变量文件，已忽略。
- `chat_ids.json`：机器人识别到的群组 ID，已忽略。
- `processed_transactions.json`、`last_processed_block_*.txt`、`last_update_id.txt`：运行状态，已忽略。
- `bot.log`：运行日志，已忽略。

> [!CAUTION]
> 不要提交真实 Telegram Token、Etherscan API Key、群组 ID、交易状态或日志。

## 项目结构

| 路径 | 说明 |
| --- | --- |
| `main.py` | 机器人、Gradio 后台与可选代理配置入口 |
| `release/` | 历史发布快照，默认忽略 |
| `requirements.txt` / `environment.yml` / `pyproject.toml` / `uv.lock` | 三种依赖入口 |

## 开发与验证

```powershell
uv lock --check -p 3.11
python -B -m py_compile main.py
```

## 许可

本项目使用 MIT License，详见 `LICENSE`。
