# AutoDelta

[English](README.en.md)

Private task: Delta Force multi-client workflow control and visual automation.

AutoDelta 是面向《三角洲行动》的多端自动化工作流控制套件。项目通过 Gradio 构建控制面板，并用 FastAPI 在服务端与客户端之间同步任务、定时器、匹配结果和中控命令；自动化流程依赖屏幕截图、模板匹配、窗口激活、键鼠模拟和路径回放。

## 功能

- 服务端/客户端双模式 Gradio 控制台。
- FastAPI 接口用于客户端注册、定时任务、状态同步和中控命令轮询。
- `workflow1` 负责服务端物品扫描与信息上报。
- `workflow2` 负责客户端任务比对、条件执行、寻路和等待态中控。
- 视觉资源位于 `assets/` 和 `img/`，用于物品、导航模板、任务按钮和路径识别。
- 日志写入本地 `.logs/` 或 `logs/`，不进入 Git。

## 工作流与控制流程

AutoDelta 的核心入口是 `main.py`。启动后进入一个 Gradio 主界面，可选择“服务端模式”或“客户端模式”。

### 服务端模式

服务端会启动内置 FastAPI 服务，默认监听 `0.0.0.0:8000`，同时显示本机 IP、客户端注册状态、定时器和中控按钮。服务端承担三类职责：

1. **定时调度**：在页面中填写目标时间，或使用 10/30/60 秒快捷按钮。到达时间前 2 秒会尝试激活游戏窗口，到点后按模式触发本机 `workflow1`、本机 `workflow2_new`，或仅作为分组调度服务。
2. **数据同步**：`workflow1` 会把服务端扫描到的右侧物品列表和撤离方向发送到 `/information`；客户端旧逻辑通过轮询 `/information` 获取这份数据。
3. **匹配与中控**：新逻辑下客户端向 `/match/submit` 提交自己的右侧物品签名，服务端按 `min_valid_match_connections` 聚合同局客户端。匹配成功后，客户端进入中控等待态，服务端按钮会把命令写入对应客户端的 `pending_command`。

服务端主要接口包括：

| 接口 | 用途 |
| --- | --- |
| `POST /register` | 客户端注册，返回 `client_id` |
| `GET /start_timer` | 客户端轮询定时触发时间 |
| `POST/GET/DELETE /information` | 写入、读取或清空旧版 `workflow1` 同步数据 |
| `GET/POST /matching_mode` | 读取或切换 old/new 匹配模式 |
| `POST /match/submit` | 新版客户端提交物品签名 |
| `GET /match/result/{client_id}` | 新版客户端轮询匹配结果 |
| `POST /match/expire` | 清理指定客户端匹配缓存 |
| `POST /client/{client_id}/status` | 客户端上报状态 |
| `GET /client/{client_id}/command` | 客户端领取中控命令 |

### `workflow1`：服务端采集与发布数据

`workflow1` 用于旧匹配链路中的“车头/服务端”采集流程：

1. 清空服务端旧的 `/information` 数据，避免客户端读到上一局残留。
2. 通过窗口标题 `configs.GAME_TITLE` 找到游戏窗口，并按 `configs.mode` 创建键鼠模拟器。
3. 点击预设坐标进入任务/背包视图，等待 `img/task.png` 出现。
4. 在屏幕区域内用 `assets/item/` 模板扫描右侧物品，得到物品名称列表。
5. 对左右两个撤离点区域匹配 `img/exit_point.png`，比较置信度得出撤离方向“左/右/错误”。
6. 将 `[右侧物品列表, 撤离方向]` 提交到本机 FastAPI 的 `/information`。
7. 等待血条接近满值后执行一段固定动作序列，用于进入后续对局状态。

### `workflow2_old`：旧版客户端比对与寻路

旧逻辑中，客户端到点后执行 `workflow2_old(server_ip)`：

1. 向服务端 `/register` 注册并拿到 `client_id`。
2. 找到游戏窗口，等待并点击 `img/task.png`。
3. 扫描本机右侧物品列表，并通过 `img/start.png` 定位当前降落点/小地图起点。
4. 轮询服务端 `/information`，读取 `workflow1` 发布的右侧物品列表和撤离方向。
5. 若本机右侧物品与服务端物品完全一致，则根据最近路径点索引和撤离方向生成路线 JSON 文件名，调用 `run_replay_workflow()` 执行路径回放。
6. 若物品不一致、数据超时或路线无效，则执行无寻路兜底动作序列。
7. 路径完成后进入中控等待循环，持续轮询 `/client/{id}/command`。

### `workflow2_new`：新版同局分组、重扫与寻路

新版逻辑用于“多客户端自动分组”，不依赖服务端提前发布一台车头的物品数据：

1. 客户端注册并读取服务端匹配模式/session。
2. 等待 `img/task.png`，延迟后定位 `img/start.png`，计算最近路径点索引。
3. 初次扫描右侧物品，至少要求 `REQUIRED_MATCH_ITEM_COUNT = 4` 个物品。
4. 等待血条完成加载，停止并行监控，再按 `M` 打开界面做落地后二次扫描。
5. 二次扫描最多循环 3 次，使用 `img/task2.png` 定位任务区域；如果扫描不足 4 个物品，会回退到初次扫描结果。
6. 若初次和三次二扫均不足 4 个物品，则等待到落地后 40 秒，并执行无寻路兜底流程。
7. 若物品足够，则向 `/match/submit` 提交 `client_id`、`session_id`、右侧物品签名和最近路径点索引。
8. 客户端轮询 `/match/result/{client_id}`。同一签名达到服务端设置的最小有效匹配连接数后判定为同局匹配成功。
9. 匹配成功后根据最近路径点生成 `assets/location/{index}.json`，并在寻路前分析小地图颜色选择导航模板。
10. 调用 `run_replay_workflow()` 按路径 JSON 回放移动；寻路结束后进入中控等待循环。

### 中控命令

客户端完成路径后会把状态更新为 `waiting`。服务端中控面板只向 `waiting` 客户端广播命令：

| 命令 | 客户端动作 |
| --- | --- |
| `flow_safe_box_4` | 打开背包，从 4 格安全箱坐标逐格执行 Alt+D 丢出 |
| `flow_safe_box_6` | 打开背包，从 6 格安全箱坐标逐格执行 Alt+D 丢出 |
| `flow_safe_box_9` / `flow_safe_box` | 打开背包，从 9 格安全箱坐标逐格执行 Alt+D 丢出 |
| `flow_c` | 执行固定自雷退出动作序列 |
| `flow_d` | 退出中控等待模式，不执行游戏内操作 |

### 资源与配置

- `assets/item/`：物品模板，用于背包物品识别。
- `assets/location/`：路径 JSON，文件名由最近降落点索引生成。
- `img/task.png`、`img/task2.png`、`img/start.png`、`img/exit_point.png`：关键 UI/地图模板。
- `configs.py` 默认读取 `datas/setting.ini`，没有文件时使用窗口标题 `三角洲行动` 和模式 `2`。
- 日志和截图记录写入 `logs/`、`.logs/`，这些目录均被忽略。

## 目录

```text
.
├─ main.py                 # Gradio + FastAPI 主入口
├─ workflow.py             # 自动化工作流
├─ configs.py              # 本地配置读取与窗口参数
├─ main_hook.py            # 键鼠 Hook
├─ record_path.py          # 路径记录工具
├─ replay.py               # 路径回放工具
├─ json_convert.py         # 路线数据转换
├─ assets/                 # 运行所需模板资源
├─ img/                    # 运行所需截图模板
├─ utils/                  # 图像识别、截图、模拟输入、状态管理
├─ data/                   # 历史数据/资料/压缩包，本地忽略
├─ requirements.txt
├─ environment.yml
└─ pyproject.toml
```

原来的 `AutoDelta/AutoDelta` + `AutoDelta_Data` 双层结构已合并为单个项目根；历史资料收敛到 `data/` 并由 `.gitignore` 排除。

## 快速开始

```powershell
uv sync
uv run python main.py
```

默认 Gradio 控制台监听 `0.0.0.0:7860`，内部 FastAPI 服务默认监听 `0.0.0.0:8000`。

## 运行前准备

- Windows 10/11。
- 目标游戏窗口标题默认读取为 `三角洲行动`。
- 当前模板资源通常要求显示比例、分辨率和游戏 UI 与采集时保持一致。
- 如需覆盖本地设置，可创建 `datas/setting.ini`；该目录为本地状态目录，不提交。

## 依赖方式

```powershell
# venv / pip
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt

# conda
conda env create -f environment.yml
conda activate autodelta

# uv
uv sync
```

## 数据与 Git 边界

提交 GitHub 时保留源码、`assets/`、`img/`、依赖文件和 README。`data/`、历史压缩包、环境目录、日志、运行配置、截图缓存和本地测试图片全部忽略。

## License

See [LICENSE](LICENSE).
