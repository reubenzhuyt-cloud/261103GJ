# 工具链（Igor / Runner / gm_* MCP）

> last-verified: (未取 sha) @ 2026-11-03
> 路径已 glob 核实；工具清单与缺陷来自本机调研（`C:\Users\31087\.config\opencode\agents\GamemakerMaster.md:49-86,163-179`）
> 与两份调研报告；CLI 参数语法**未在本任务运行验证**（禁止编译）。

## 职责
编译/运行路径、MCP 模式闸门、`gm_*` 工具分类与已知缺陷。
**不**覆盖：git 操作（`vcs.md`）、格式字段（`project-format.md`）。

## 本机路径（glob 核实存在）
- Igor：`C:\ProgramData\GameMakerStudio2-LTS2026\Cache\runtimes\runtime-2026.0.0.23\bin\igor\windows\x64\Igor.exe`
- Runner：`C:\ProgramData\GameMakerStudio2-LTS2026\Cache\runtimes\runtime-2026.0.0.23\windows\x64\Runner.exe`
- 启动：`Runner.exe -game "<path>.win"`，可加 `-debugoutput <file>`（调研报告；未验证）
- Igor CLI（未验证）：`Igor.exe /project="<abs>.yyp" /rp="<runtime>" /lf="<license>" Windows Run`

## MCP 产物（本项目约定）
- 编译产物：`.gamemaker-mcp\temp\261103GJ.win`（`GamemakerMaster.md:56`，项目名代入）
- 备份/快照：`.gamemaker-mcp\backups`、`.gamemaker-mcp\snapshots`（已在 `.gitignore:46` 忽略）

## 模式闸门（`opencode.json:9-11` + `GamemakerMaster.md:47`）
- 默认 `read-only`；`GAMEMAKER_MCP_MODE=workspace-write` 才允许写 `.yyp/.yy/.gml`
- `GAMEMAKER_MCP_ALLOW_BUILD=1` 才允许调 Igor/Runner —— **本项目两者均已开启**
- `GAMEMAKER_PROJECT` 必须指向 `E:\GMprojects\261103GJ`，防止改错工程（`opencode.json:9`）

## `gm_*` 工具分类（调研报告）
- 只读：`gm_project_info`、`gm_project_statistics`、`gm_asset_list`、`gm_file_read`（带 SHA-256）
- 快照：`gm_snapshot_create` / `gm_snapshot_restore` / `gm_snapshot_list`
- 写入：`gm_object_create`、`gm_object_event_upsert`、`gm_gml_patch`
- 构建：`gm_project_validate`、`gm_project_compile_errors`
- 防脏写：更新已有文件必须带最近一次 `gm_file_read` 返回的 SHA-256；哈希过期是硬停，重新读取，
  不要强制写入（`GamemakerMaster.md:84`）

## 已知缺陷（重要）
| 工具 | 结论 | 后果 / 替代 |
|------|------|-------------|
| `gm_room_instance_add` | ❌ **不要用** | 生成的实例缺 `colour`/`frozen`/`hasCreationCode` 等必填字段且不写 `instanceCreationOrder` → Igor 报 `Field "colour": expected`（`GamemakerMaster.md:81,185`）；手工补全字段（模板见 `GamemakerMaster.md:116-125`） |
| `gm_sprite_create` | ⚠️ 空壳 | 只生成 `frames:[]`/`layers:[]`，能编译但**不可见**；PNG 与字段需手工（`GamemakerMaster.md:82,189`） |
| `gm_object_event_upsert` | ⚠️ 无 Collision | 只接受 create/destroy/step/begin_step/end_step/draw/cleanup/room_start/room_end；Collision 用 Step + `place_meeting`（`GamemakerMaster.md:86`） |

## 验证流程（`GamemakerMaster.md:63-70`）
1. `gm_project_validate` → 须 `{"ok":true,"errors":0}`
2. `gm_project_compile_errors` → 须 `ok=true, errors=[]`
3. `Runner.exe -game <.win>` 运行；截图用 `PrintWindow`（flag 2），不用 `CopyFromScreen`
- “文件写成功”不等于“编译通过”，必须以工具真实输出为准

## Links
- Parent: [INDEX.md](INDEX.md)
- 相关：[checklist.md](checklist.md)、[project-format.md](project-format.md)
