# CardHobby RPC

[English](README.en.md)

Private task: CardHobby batch listing, authorization, and admin tooling.

本项目是 CardHobby 批量多卡入仓工具，包含客户端批量发布界面、管理员授权面板、授权服务和站点请求封装。核心流程通过 PyQt5 提供桌面 UI，通过 FastAPI + 授权码把管理员登录态安全下发给客户端，再由 `cardhobby_batch_publish.py` 读取 Excel 与图片目录完成批量发布。

## 功能

- 客户端 PyQt5 界面：登录授权、选择 Excel、选择图片目录、保存配置、执行批量入仓。
- 管理员 PyQt5 面板：读取账号列表、登录 CardHobby、生成限时授权码。
- FastAPI 授权服务：提供健康检查与授权码兑换接口。
- CardHobby 请求封装：处理登录、会话保存、商品克隆、图片上传和发布请求。
- 批量输入：读取 `上架全部.xlsx` 和 `picture/` 中按行号命名的图片。

## 目录

```text
.
├─ cardhobby_ui.py                 # 客户端桌面界面
├─ admin_panel.py                  # 管理员授权面板
├─ admin_auth_service.py           # FastAPI 授权服务
├─ cardhobby_batch_publish.py      # Excel/图片批量发布流程
├─ cardhobby_site_client.py        # CardHobby HTTP/Playwright 客户端
├─ auth_crypto.py                  # 授权载荷加密与校验
├─ client_auth.py                  # 客户端授权缓存
├─ session_runtime.py              # 进程内 session 管理
├─ *.example.json / *.example.txt  # 安全配置模板
├─ docs/sensitive_fields.md        # 敏感字段与本地文件说明
├─ data/                           # HAR、Excel、Word、图片、运行结果，本地忽略
├─ script/                         # 构建/打包脚本，本地忽略
├─ requirements.txt
├─ environment.yml
└─ pyproject.toml
```

`admin_accounts.txt`、`admin_sessions/`、真实配置、`data/`、`script/` 和历史打包目录均属于本地数据/敏感信息或本地构建资产，已由 `.gitignore` 排除。

## 快速开始

首次使用先复制模板：

```powershell
Copy-Item admin_accounts.example.txt admin_accounts.txt
Copy-Item admin_config.example.json admin_config.json
Copy-Item cardhobby_console_config.example.json cardhobby_console_config.json
```

填写 `admin_accounts.txt` 后启动管理员端：

```powershell
uv sync
uv run python admin_panel.py
```

启动客户端：

```powershell
uv run python cardhobby_ui.py
```

## 数据约定

- Excel 默认路径来自 `cardhobby_console_config.json` 的 `excel_file`。本地样例和历史 Excel 已整理到 `data/`。
- 图片目录默认是 `picture/`；当前本地图片资料已整理到 `data/picture/`，使用时可复制或在配置中指向实际目录。
- 多图命名格式为 `行号 (序号).JPG`，例如 `10 (1).JPG`、`10 (2).JPG`。
- 运行结果写入 `cardhobby_publish_results.jsonl`。

## 依赖方式

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

如果需要 Playwright 浏览器登录兜底，可额外执行：

```powershell
uv run playwright install
```

## 安全说明

提交前确认 `git status --ignored` 中真实账号、会话、HAR、Excel、图片和打包产物均处于 ignored 或 removed-from-index 状态。字段清单见 [docs/sensitive_fields.md](docs/sensitive_fields.md)。

## License

MIT License. See [LICENSE](LICENSE).
