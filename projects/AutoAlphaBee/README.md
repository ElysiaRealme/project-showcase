# AutoAlphaBee

简体中文 | [English](README.en.md)

AutoAlphaBee 是面向 AlphaBee / 金证证券交易软件的 Windows 桌面自动化项目。它读取成交与委托 CSV，结合窗口聚焦、图像模板匹配和流程脚本，辅助完成可转债转股相关操作。

## 功能

- 读取成交、委托 CSV，兼容 UTF-8、GBK、GB2312 等常见编码。
- 使用 `pyautogui`、OpenCV 和 Win32 API 控制目标交易窗口。
- 将主流程拆成 `process1.py`、`process2.py`、`process3.py`，便于局部调试。
- 提供坐标初始化和已处理状态重置脚本。
- 将样本数据、输出、历史快照和日志隔离到 Git 忽略目录。

## 快速开始

```powershell
uv sync
uv run python main.py
```

辅助脚本：

```powershell
uv run python setup_coords.py
uv run python reset_processed_ids.py
```

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
conda activate autoalphabee
python main.py
```

## 使用

| 命令 | 用途 |
| --- | --- |
| `uv run python main.py` | 运行主自动化流程 |
| `uv run python setup_coords.py` | 采集/更新坐标配置 |
| `uv run python reset_processed_ids.py` | 清理已处理记录 |
| `uv run python paddletest.py` | 本地 OCR 识别测试 |

> [!IMPORTANT]
> 真实运行需要 Windows 可见桌面会话、目标交易客户端窗口、可用模板图片，以及必要的本地业务数据。无桌面会话时只能做静态检查。

## 配置

- `settings.txt`：轻量运行配置。
- `config.json`：脚本运行时可能生成或读取的本地状态文件，已忽略。
- `assets/`：图像模板目录，应随源码维护。
- `data/`、`output/`、`logs/`、`archive/`：样本、输出、日志和历史快照，默认不提交。

## 项目结构

| 路径 | 说明 |
| --- | --- |
| `main.py` | 主流程入口 |
| `automation_core.py` | 窗口控制、截图、模板匹配等通用能力 |
| `process1.py` / `process2.py` / `process3.py` | 业务动作流程 |
| `setup_coords.py` | 坐标初始化 |
| `reset_processed_ids.py` | 状态清理 |
| `requirements.txt` / `environment.yml` / `pyproject.toml` / `uv.lock` | 三种依赖入口 |

## 开发与验证

```powershell
uv lock --check -p 3.11
python -B -m py_compile main.py automation_core.py process1.py process2.py process3.py setup_coords.py reset_processed_ids.py
```

## 许可

本项目使用 GNU Affero General Public License v3.0，详见 `LICENSE`。
