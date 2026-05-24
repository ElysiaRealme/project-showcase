# CryptoTGBot

[简体中文](README.md) | English

CryptoTGBot is a Telegram transaction-monitoring bot with a Gradio admin UI. It checks configured blockchain addresses through upstream APIs, formats matching transactions, and sends notifications to recorded Telegram groups.

## Features

- Gradio admin UI for Telegram Bot Token, Etherscan API Key, monitored addresses, message templates, and send-rate controls.
- Telegram group discovery through Bot API polling.
- Transaction notifications, scheduled custom messages, and optional proxy configuration that is disabled by default.
- Keeps tokens, configuration, group IDs, transaction state, and logs local.

## Quick Start

```powershell
uv sync
uv run python main.py
```

After startup, open the Gradio URL printed in the console and configure the bot token, API key, monitored addresses, and admin password.

## Installation

### uv

```powershell
uv sync
uv run python main.py
```

### Native venv

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

## Usage

| Command | Purpose |
| --- | --- |
| `uv run python main.py` | Default Gradio admin and bot flow |

## Configuration

- `config.json`: admin UI runtime configuration; contains sensitive values and is ignored.
- `PROXY_ENABLED` / `PROXY_URL`: stored in `config.json`, disabled by default; when enabled, Telegram and upstream chain API requests use that proxy.
- `.env`: optional local environment variable file; ignored.
- `chat_ids.json`: Telegram group IDs discovered by the bot; ignored.
- `processed_transactions.json`, `last_processed_block_*.txt`, `last_update_id.txt`: runtime state; ignored.
- `bot.log`: runtime log; ignored.

> [!CAUTION]
> Do not commit real Telegram tokens, Etherscan API keys, group IDs, transaction state, or logs.

## Project Structure

| Path | Description |
| --- | --- |
| `main.py` | Bot, Gradio admin UI, and optional proxy configuration entrypoint |
| `release/` | Historical release snapshot; ignored by default |
| `requirements.txt` / `environment.yml` / `pyproject.toml` / `uv.lock` | Three dependency surfaces |

## Development and Verification

```powershell
uv lock --check -p 3.11
python -B -m py_compile main.py
```

## License

This project is licensed under the MIT License. See `LICENSE`.
