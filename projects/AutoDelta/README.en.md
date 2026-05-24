# AutoDelta

[中文](README.md)

Private task: Delta Force multi-client workflow control and visual automation.

AutoDelta is a multi-client workflow control suite for Delta Force. It uses Gradio for the operator panels and FastAPI to synchronize tasks, timers, match results, and central commands between server and client nodes. The automation workflow relies on screenshots, template matching, window activation, keyboard/mouse simulation, and route replay.

## Features

- Server/client Gradio control panels.
- FastAPI endpoints for client registration, timers, status updates, and command polling.
- `workflow1` scans server-side items and publishes shared information.
- `workflow2` compares client-side tasks, executes conditional branches, performs route navigation, and enters central-control wait states.
- Runtime visual templates live in `assets/` and `img/`.
- Logs are written locally under `.logs/` or `logs/` and are excluded from Git.

## Workflow and Control Flow

The main entry point is `main.py`. It opens a Gradio UI where the operator chooses server mode or client mode.

### Server Mode

Server mode starts the embedded FastAPI service on `0.0.0.0:8000` and shows local IP, registered clients, timer controls, matching settings, and central-command buttons. It is responsible for:

1. **Timed dispatch**: set a target wall-clock time or use 10/30/60 second shortcuts. Two seconds before the target it tries to activate the game window. At the trigger time it runs local `workflow1`, local `workflow2_new`, or only acts as the grouping service depending on the selected mode.
2. **Data sync**: `workflow1` posts the server-side item list and exit direction to `/information`; old clients poll that endpoint.
3. **Matching and central control**: in new mode clients submit item signatures to `/match/submit`; the server groups clients by identical signatures and `min_valid_match_connections`. After path replay, waiting clients receive central commands through `/client/{id}/command`.

Key endpoints:

| Endpoint | Purpose |
| --- | --- |
| `POST /register` | register a client and return `client_id` |
| `GET /start_timer` | let clients poll the scheduled timestamp |
| `POST/GET/DELETE /information` | write, read, or clear old-mode `workflow1` data |
| `GET/POST /matching_mode` | read or switch old/new matching mode |
| `POST /match/submit` | submit a new-mode item signature |
| `GET /match/result/{client_id}` | poll new-mode matching result |
| `POST /match/expire` | clear a client's match cache |
| `POST /client/{client_id}/status` | update client status |
| `GET /client/{client_id}/command` | let clients claim central commands |

### `workflow1`: Server Collection

`workflow1` is the old-mode host collection flow:

1. Clear stale `/information` data.
2. Find the game window from `configs.GAME_TITLE` and create the configured input simulator.
3. Enter the task/backpack view and wait for `img/task.png`.
4. Scan right-side items with templates from `assets/item/`.
5. Compare left/right `img/exit_point.png` confidence to determine exit direction.
6. Submit `[right item names, exit direction]` to the local FastAPI `/information` endpoint.
7. Wait for the health bar to finish loading, then run the fixed action sequence for the next state.

### `workflow2_old`: Old Client Flow

Old clients run `workflow2_old(server_ip)`:

1. Register with `/register`.
2. Find the game window, wait for `img/task.png`, and scan local right-side items.
3. Locate the current start point via `img/start.png`.
4. Poll `/information` for `workflow1` data.
5. If local items match the host items, build the route JSON path from closest point index and exit direction, then run `run_replay_workflow()`.
6. If items differ, data times out, or the route is invalid, run the no-navigation fallback sequence.
7. After navigation, enter the central-command polling loop.

### `workflow2_new`: New Group Matching Flow

New mode groups clients without requiring one host to publish item data first:

1. Register and read server matching/session state.
2. Wait for `img/task.png`, locate `img/start.png`, and calculate the nearest route-point index.
3. Perform the first right-side item scan; at least `REQUIRED_MATCH_ITEM_COUNT = 4` items are expected.
4. Wait for health loading, stop parallel monitors, press `M`, and run post-landing rescans.
5. Rescan up to three times using `img/task2.png`; fall back to the first scan if all rescans are short.
6. If every scan has fewer than 4 items, wait until 40 seconds after landing and run the no-navigation fallback.
7. Submit `client_id`, `session_id`, item signature, and closest point index to `/match/submit`.
8. Poll `/match/result/{client_id}`. When enough clients share the same signature, matching succeeds.
9. Build `assets/location/{index}.json`, detect minimap color to select a navigation template, and run `run_replay_workflow()`.
10. Enter the central-command wait loop after route replay.

### Central Commands

After route replay, clients set status to `waiting`. The server broadcasts only to waiting clients:

| Command | Client action |
| --- | --- |
| `flow_safe_box_4` | open backpack and Alt+D each configured 4-cell safe-box slot |
| `flow_safe_box_6` | open backpack and Alt+D each configured 6-cell safe-box slot |
| `flow_safe_box_9` / `flow_safe_box` | open backpack and Alt+D each configured 9-cell safe-box slot |
| `flow_c` | run the fixed self-exit action sequence |
| `flow_d` | leave central-command wait mode without in-game action |

### Resources and Configuration

- `assets/item/`: item templates for inventory matching.
- `assets/location/`: route JSON files selected by closest point index.
- `img/task.png`, `img/task2.png`, `img/start.png`, `img/exit_point.png`: key UI/minimap templates.
- `configs.py` reads `datas/setting.ini`; without it, the default game title is `三角洲行动` and the default input mode is `2`.
- Logs and screenshot records are written under `logs/` and `.logs/`, both ignored.

## Layout

```text
.
├─ main.py                 # Gradio + FastAPI entry point
├─ workflow.py             # automation workflow
├─ configs.py              # local config and window parameters
├─ main_hook.py            # keyboard/mouse hook
├─ record_path.py          # route recording tool
├─ replay.py               # route replay tool
├─ json_convert.py         # route-data conversion
├─ assets/                 # required runtime templates
├─ img/                    # required screenshot templates
├─ utils/                  # vision, screenshot, input simulation, state management
├─ data/                   # historical data/materials/packages, ignored locally
├─ requirements.txt
├─ environment.yml
└─ pyproject.toml
```

The previous `AutoDelta/AutoDelta` + `AutoDelta_Data` two-level layout has been merged into one project root. Historical materials now live under `data/` and are excluded by `.gitignore`.

## Quick Start

```powershell
uv sync
uv run python main.py
```

The Gradio control panel listens on `0.0.0.0:7860` by default, and the internal FastAPI service listens on `0.0.0.0:8000`.

## Before Running

- Windows 10/11.
- The default target game-window title is `三角洲行动`.
- Template assets usually require the same display scale, resolution, and game UI layout used during capture.
- Create `datas/setting.ini` for local overrides if needed; `datas/` is local runtime state and is not committed.

## Dependency Options

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

## Data and Git Boundary

For GitHub, keep source code, `assets/`, `img/`, dependency files, and README files. `data/`, historical archives, environment folders, logs, runtime config, screenshot caches, and local test images are ignored.

## License

See [LICENSE](LICENSE).
