# CardHobby RPC

[中文](README.md)

Private task: CardHobby batch listing, authorization, and admin tooling.

This project is a CardHobby batch listing toolkit. It includes a client desktop UI, an administrator authorization panel, a FastAPI authorization service, and the CardHobby request client. The admin side logs in and issues temporary authorization codes; the client redeems a code, receives an encrypted session payload, then publishes listings from Excel rows and image folders.

## Features

- Client PyQt5 UI for authorization, Excel selection, image-folder selection, config saving, and batch publishing.
- Admin PyQt5 panel for account loading, CardHobby login, and temporary authorization-code generation.
- FastAPI authorization service with health and redeem endpoints.
- CardHobby HTTP/Playwright client for login, session persistence, item cloning, image upload, and publish requests.
- Batch input from `上架全部.xlsx` and `picture/` images named by Excel row number.

## Layout

```text
.
├─ cardhobby_ui.py                 # client desktop UI
├─ admin_panel.py                  # admin authorization panel
├─ admin_auth_service.py           # FastAPI authorization service
├─ cardhobby_batch_publish.py      # Excel/image batch publishing workflow
├─ cardhobby_site_client.py        # CardHobby HTTP/Playwright client
├─ auth_crypto.py                  # encrypted authorization payloads
├─ client_auth.py                  # client authorization cache
├─ session_runtime.py              # in-process session storage
├─ *.example.json / *.example.txt  # safe config templates
├─ docs/sensitive_fields.md        # sensitive-file policy
├─ data/                           # HAR, Excel, Word, images, run outputs; ignored
├─ script/                         # local build/package scripts; ignored
├─ requirements.txt
├─ environment.yml
└─ pyproject.toml
```

`admin_accounts.txt`, `admin_sessions/`, live configs, `data/`, `script/`, and historical packages are local data, sensitive files, or local build assets and are excluded by `.gitignore`.

## Quick Start

Copy templates first:

```powershell
Copy-Item admin_accounts.example.txt admin_accounts.txt
Copy-Item admin_config.example.json admin_config.json
Copy-Item cardhobby_console_config.example.json cardhobby_console_config.json
```

Fill `admin_accounts.txt`, then run the admin panel:

```powershell
uv sync
uv run python admin_panel.py
```

Run the client:

```powershell
uv run python cardhobby_ui.py
```

## Data Convention

- `excel_file` is read from `cardhobby_console_config.json`. Local examples and historical Excel files now live under `data/`.
- The default image directory is `picture/`; the current local image material has been moved to `data/picture/`, so either copy it back for local runs or point the config to the actual folder.
- Multi-image entries use `row_number (sort_number).JPG`, such as `10 (1).JPG` and `10 (2).JPG`.
- Run output is written to `cardhobby_publish_results.jsonl`.

## Dependency Options

```powershell
# venv / pip
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt

# conda
conda env create -f environment.yml
conda activate cardhobby-rpc

# uv
uv sync
```

For the Playwright login fallback:

```powershell
uv run playwright install
```

## Security

Before committing, check that live accounts, sessions, HAR captures, Excel files, images, and package outputs are ignored or removed from the Git index. See [docs/sensitive_fields.md](docs/sensitive_fields.md).

## License

MIT License. See [LICENSE](LICENSE).
