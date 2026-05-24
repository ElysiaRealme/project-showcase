# GoFreight SOP Automation

简体中文 | [English](README.en.md)

GoFreight SOP Automation 是面向 GoFreight 的 API 与 Excel 批处理自动化项目。它从工作簿读取任务行，构建 shipment / invoice 请求，并记录逐行执行结果。

## 功能

- `batch_excel_runner.py` 从 Excel 读取批量任务并生成结构化输入。
- `pipeline_workflow.py` 执行 GoFreight 多步骤 API 流程。
- 支持 AI/OI 类型、关税、清关费、代理代码和超时参数。
- 支持 dry-run、断点状态文件和批处理报告目录。
- HAR、Excel、凭据、报告和运行状态都放入被忽略的数据区。

## 快速开始

查看批处理参数：

```powershell
uv sync
uv run python batch_excel_runner.py --help
```

真实运行需要传入工作簿路径和有效认证值。

## 安装

### uv

```powershell
uv sync
uv run python batch_excel_runner.py --help
```

### 原生 venv

```powershell
python -m venv .venv
.\.venv\Scripts\activate
python -m pip install -r requirements.txt
python batch_excel_runner.py --help
```

### Conda

```bash
conda env create -f environment.yml
conda activate gofreight-sop-automation
python batch_excel_runner.py --help
```

## 使用

```powershell
uv run python batch_excel_runner.py --files data\merged_all_companies.xlsx --csrf-token <csrf> --session-id <session> --gf-token <token> --dry-run
```

单票流程可查看：

```powershell
uv run python pipeline_workflow.py --help
```

## 配置与数据

- `data/`：HAR、Excel、报告、历史压缩包和本地运行数据，已忽略。
- `CSRF_TOKEN`、`SESSION_ID`、`GF_TOKEN`：可通过环境变量或 CLI 参数传入。
- `gofreight_config.template.json`：安全配置模板；复制为已忽略的 `gofreight_config.local.json` 后填写真实租户、认证和客户映射。
- `SENSITIVE_FIELDS.md`：记录本项目中不应提交的敏感字段类型。
- `processed_mbl_entry_index.json`、`pipeline_errors.jsonl`：默认运行状态/错误日志，已忽略。

> [!CAUTION]
> 不要提交 HAR、cookie、CSRF、session、GF token、客户表格或生成报告。

## 项目结构

| 路径 | 说明 |
| --- | --- |
| `batch_excel_runner.py` | Excel 批处理入口 |
| `pipeline_workflow.py` | GoFreight API 流程 |
| `gofreight_config.py` / `gofreight_config.template.json` | 本地敏感配置加载器与安全模板 |
| `SENSITIVE_FIELDS.md` | 敏感字段清单 |
| `step1.py` / `step2.py` / `step3.py` | 分步实验和参考脚本 |
| `requirements.txt` / `environment.yml` / `pyproject.toml` / `uv.lock` | 三种依赖入口 |

## 开发与验证

```powershell
uv lock --check -p 3.11
python -B -m py_compile gofreight_config.py batch_excel_runner.py pipeline_workflow.py step1.py step2.py step3.py
```

## 许可

本项目根目录包含 `LICENSE`。发布前请确认业务数据和凭据未被提交。
