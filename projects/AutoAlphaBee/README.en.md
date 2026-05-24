# AutoAlphaBee

[简体中文](README.md) | English

Automation script for Convertible Bond conversion in AlphaBee (Kingdom Securities Trading Software). It reads trade and order CSV files, then uses window focus, image-template matching, and scripted flows to assist convertible-bond conversion operations.

## Features

- Reads trade and order CSV files with UTF-8, GBK, GB2312, and related fallbacks.
- Controls the target trading window with `pyautogui`, OpenCV, and Win32 APIs.
- Splits the workflow into `process1.py`, `process2.py`, and `process3.py` for targeted debugging.
- Provides coordinate setup and processed-state reset helpers.
- Keeps sample data, outputs, historical snapshots, and logs outside the Git commit surface.

## Quick Start

```powershell
uv sync
uv run python main.py
```

Helper commands:

```powershell
uv run python setup_coords.py
uv run python reset_processed_ids.py
```

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
conda activate autoalphabee
python main.py
```

## Usage

| Command | Purpose |
| --- | --- |
| `uv run python main.py` | Run the main automation flow |
| `uv run python setup_coords.py` | Capture or update coordinate configuration |
| `uv run python reset_processed_ids.py` | Clear processed state |
| `uv run python paddletest.py` | Run a local OCR recognition test |

> [!IMPORTANT]
> Real execution requires a visible Windows desktop session, the target trading client window, valid template images, and local business data. Without a desktop session, only static checks are practical.

## Configuration

- `settings.txt`: lightweight runtime settings.
- `config.json`: local runtime state that may be generated or read by scripts; ignored by Git.
- `assets/`: image templates that should be maintained with source code.
- `data/`, `output/`, `logs/`, `archive/`: samples, outputs, logs, and historical snapshots; ignored by default.

## Project Structure

| Path | Description |
| --- | --- |
| `main.py` | Main workflow entrypoint |
| `automation_core.py` | Shared window control, screenshot, and template-matching utilities |
| `process1.py` / `process2.py` / `process3.py` | Business action flows |
| `setup_coords.py` | Coordinate setup |
| `reset_processed_ids.py` | State cleanup |
| `requirements.txt` / `environment.yml` / `pyproject.toml` / `uv.lock` | Three dependency surfaces |

## Development and Verification

```powershell
uv lock --check -p 3.11
python -B -m py_compile main.py automation_core.py process1.py process2.py process3.py setup_coords.py reset_processed_ids.py
```

## License

This project is licensed under the GNU Affero General Public License v3.0. See `LICENSE`.
