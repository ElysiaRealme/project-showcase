# GoFreight SOP Automation

[简体中文](README.md) | English

GoFreight SOP Automation is a GoFreight API and Excel batch-automation project. It reads task rows from workbooks, builds shipment/invoice requests, and records per-row execution results.

## Features

- `batch_excel_runner.py` reads Excel workbooks and builds structured task inputs.
- `pipeline_workflow.py` executes the multi-step GoFreight API flow.
- Supports AI/OI types, duty, customs-clearance fee, agent codes, and timeout parameters.
- Supports dry-run mode, checkpoint state files, and batch report directories.
- Keeps HAR captures, Excel workbooks, credentials, reports, and runtime state in ignored data areas.

## Quick Start

Inspect batch options:

```powershell
uv sync
uv run python batch_excel_runner.py --help
```

Real runs require workbook paths and valid authentication values.

## Installation

### uv

```powershell
uv sync
uv run python batch_excel_runner.py --help
```

### Native venv

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

## Usage

```powershell
uv run python batch_excel_runner.py --files data\merged_all_companies.xlsx --csrf-token <csrf> --session-id <session> --gf-token <token> --dry-run
```

For the single-shipment flow:

```powershell
uv run python pipeline_workflow.py --help
```

## Configuration and Data

- `data/`: HAR captures, Excel workbooks, reports, historical archives, and local run data; ignored.
- `CSRF_TOKEN`, `SESSION_ID`, `GF_TOKEN`: pass through environment variables or CLI arguments.
- `gofreight_config.template.json`: safe configuration template; copy it to ignored `gofreight_config.local.json` and fill real tenant, auth, and customer mapping values locally.
- `SENSITIVE_FIELDS.md`: inventory of sensitive field types that should not be committed.
- `processed_mbl_entry_index.json`, `pipeline_errors.jsonl`: default runtime state/error logs; ignored.

> [!CAUTION]
> Do not commit HAR files, cookies, CSRF values, sessions, GF tokens, customer spreadsheets, or generated reports.

## Project Structure

| Path | Description |
| --- | --- |
| `batch_excel_runner.py` | Excel batch entrypoint |
| `pipeline_workflow.py` | GoFreight API workflow |
| `gofreight_config.py` / `gofreight_config.template.json` | Local sensitive-config loader and safe template |
| `SENSITIVE_FIELDS.md` | Sensitive field inventory |
| `step1.py` / `step2.py` / `step3.py` | Step-level experiments and references |
| `requirements.txt` / `environment.yml` / `pyproject.toml` / `uv.lock` | Three dependency surfaces |

## Development and Verification

```powershell
uv lock --check -p 3.11
python -B -m py_compile gofreight_config.py batch_excel_runner.py pipeline_workflow.py step1.py step2.py step3.py
```

## License

This project root includes a `LICENSE` file. Before publishing, confirm business data and credentials are not committed.
