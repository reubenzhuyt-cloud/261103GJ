# 项目总览（261103GJ）

> last-verified: (未取 sha) @ 2026-11-03 —— 本次任务约束：不运行 git 命令，故无 commit 戳。
> 事实来源：本地文件（标 `file:line`）或本机路径 glob 核实（标路径）。

## 职责
记录工程的版本基线、目录结构、当前资源清单与构建链，作为其他文档的入口快照。
不含语言/格式/工具链细节（见 `conventions/` 各模块）。

## 版本基线
- GameMaker IDE `2026.0.0.16` — `261103GJ.yyp:18`
- 工程格式：`$GMProject:"v1"`、`resourceType:"GMProject"`、`resourceVersion:"2.0"` — `261103GJ.yyp:2,24-25`
- Runtime `runtime-2026.0.0.23`，本机路径已核实存在：
  - `C:\ProgramData\GameMakerStudio2-LTS2026\Cache\runtimes\runtime-2026.0.0.23\bin\igor\windows\x64\Igor.exe`（glob 命中）
  - `...\runtime-2026.0.0.23\windows\x64\Runner.exe`（glob 命中）
- MCP 配置：`GAMEMAKER_PROJECT=E:\GMprojects\261103GJ`、`GAMEMAKER_MCP_MODE=workspace-write`、`GAMEMAKER_MCP_ALLOW_BUILD=1` — `opencode.json:9-11`
- 远端仓库：https://github.com/reubenzhuyt-cloud/261103GJ （任务简报提供，本次未运行 git 核验）

## 目录树（当前，glob 核实）
```
261103GJ/
├─ 261103GJ.yyp              # 工程清单（1085 B）
├─ 261103GJ.resource_order   # IDE 自定义排序，不提交（.gitignore:29）
├─ opencode.json             # MCP 服务配置（提交）
├─ .gitignore / .gitattributes
├─ options/
│  ├─ main/options_main.yy
│  ├─ windows/options_windows.yy
│  ├─ operagx/options_operagx.yy
│  └─ reddit/options_reddit.yy
├─ rooms/Room1/Room1.yy
├─ datafiles/                # 空（glob 无匹配文件）
└─ docs/architecture/        # 本套文档
```

## 资源清单
- 全工程仅 **1 个资源**：`Room1`，注册于 `261103GJ.yyp:21-23`，初始房间为其（`RoomOrderNodes` 第 0 项，`261103GJ.yyp:26-28`）
- 无 objects / sprites / scripts / sounds / shaders / fonts / tilesets
- `Room1`：1366×768（`rooms/Room1/Room1.yy:30,33`），`enableViews:false`（`Room1.yy:49`）
- 图层：`Instances`（空实例层，`Room1.yy:11`）+ `Background`（colour `4278190080` = 不透明黑，`Room1.yy:12`）；`instanceCreationOrder` 为空（`Room1.yy:8`）

## 构建链
```
IDE / gm_* MCP 工具
   → Igor.exe /project=<abs>.yyp /rp=<runtime> …（编译）
   → .gamemaker-mcp\temp\261103GJ.win（MCP 产物）
   → Runner.exe -game "<path>.win"（运行）
```
详见 [conventions/toolchain.md](conventions/toolchain.md)。

## 本项目当前注意点
- `option_windows_interpolate_pixels` 当前为 `true`（`options/windows/options_windows.yy:20`）；若做像素风须改 `false`，见 [conventions/project-format.md](conventions/project-format.md)
- `option_windows_executable_name` 仍为模板默认 `new_blank.exe`（`options_windows.yy:16`）

## Links
- Root: [INDEX.md](INDEX.md)
- 下一步：[conventions/INDEX.md](conventions/INDEX.md)
