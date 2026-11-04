# 工程格式（.yyp / .yy / 房间 / options）

> last-verified: (未取 sha) @ 2026-11-03
> 本地事实标 `file:line`；格式规则来自官方手册（主题：项目结构 / 资源 .yy / 房间 / 项目选项）+ 本机调研（`GamemakerMaster.md:88-132`）。
> 手册类规则**未在本项目逐一编译验证**（工程当前仅 1 个房间）。

## 职责
`.yyp`/`.yy` 必填字段、物理目录与命名约定、对象事件文件名、房间结构、options 关键项。
**不**覆盖：GML 语法（`gml-language.md`）、git 操作（`vcs.md`）。

## `.yyp` 必填（对照 `261103GJ.yyp`）
- `$GMProject:"v1"`（:2）、`resourceType:"GMProject"`（:24）、`resourceVersion:"2.0"`（:25）
- `resources[]`：每项 `{"id":{"name":…,"path":…}}`（:21-23）
- `RoomOrderNodes[]`：第 0 项 = 初始房间（:26-28）
- `AudioGroups` 至少 `audiogroup_default`（:4-6）、`TextureGroups` 至少 `Default`（:30-32）
- `IncludedFiles` 空也要写 `[]`（:14）、`Folders`（:12）
- `defaultScriptType`（:11）分级结论：
  - `1` = **GML Code —— 已由项目所有者实机确认**（2026-11-03 23:28 在 IDE 创建 Object1 Create 事件时选择 "GML Code"，IDE 落盘 `0`→`1`，提交 `618436e`）
  - `0` = **未验证**（空白模板初始值为 `0`；可能为 GML Visual 或"每次询问"，**不可断言**，待实证）
  - 操作提示：若新建对象/脚本的事件不是 GML Code，先检查 `.yyp` 的 `defaultScriptType` 是否为 `1`，或在 IDE 中重设；
    **不要手改 `.yyp`** —— IDE 保存时会覆盖该字段
- 未在 `resources[]` 注册的 `.yy` 会被完全忽略；声明了不存在的路径会致命报错（手册：项目结构）

## 资源 `.yy` 通则
- 顶层：`$<GMType>:"v1|v2"`、`%Name`、`name`、`resourceType`、`resourceVersion`、`parent`（指向 `.yyp`，见 `rooms/Room1/Room1.yy:15-18`）
- 尾逗号容错，但**缺字段/字段乱序会整体加载失败**（`GamemakerMaster.md:90`）
- 物理路径硬约定（文件夹名必须与资源名一致）：
  `objects/<Name>/<Name>.yy`、`sprites/<Name>/<Name>.yy`、`rooms/<Name>/<Name>.yy`、`scripts/<Name>/<Name>.yy`
- 全局命名空间唯一：所有资源类型共享，且不得与内置变量/函数（`score`/`lives`/`speed`/`x`/`y` 等）重名（手册：命名）

## 对象事件文件命名（手册：事件文件）
- `Create_0.gml`、`Destroy_0.gml`、`CleanUp_0.gml`；`Step_0/1/2.gml`（普通/Begin/End）
- `Alarm_0..11.gml`；`Draw_0/64/72/73.gml`（Draw / GUI / Begin / End）
- `KeyPress_<code>.gml`、`Mouse_<btn>.gml`、`Other_<n>.gml`、`Collision_<obj>.gml`

## 资源附属文件
- Sprite：每帧 `<frameUUID>.png` + `layers/<frameUUID>/<layerUUID>.png`，缺任一 PNG 会异常；
  `.yy` 字段顺序与必填项见 `GamemakerMaster.md:94-112`
- Script：`.yy` + `.gml`；Shader：`.yy` + `.vsh` + `.fsh`

## 房间（对照 `rooms/Room1/Room1.yy`）
- `layers[]` 类型：`GMRInstanceLayer`（:11）/ `GMRBackgroundLayer`（:12）/ `GMRTileLayer` / `GMRAssetLayer`
- **实例必须同时登记到 `instanceCreationOrder`**（当前为空，:8），否则构建报错或 Create 不触发（手册：房间）
- `roomSettings` 宽高须为正整数（`Room1.yy:30,33`：1366×768）；`views[]` 固定 8 项（`Room1.yy:36-45`）
- `enableViews:false`（`Room1.yy:49`）—— 当前未启用视图

## options 关键项（本地 file:line）
- `options/windows`：`option_windows_interpolate_pixels` 当前 `true`（`options_windows.yy:20`）
  —— **像素风必须改 `false`**，否则精灵边缘品红（手册：Windows 选项；`GamemakerMaster.md:145`）
- `option_windows_executable_name`=`new_blank.exe`（:16）、`option_windows_display_name`=`261103GJ`（:14）、
  `option_windows_version`=`1.0.0.0`（:34）—— 影响产物名与版本
- `options/main`：`option_game_speed`=60（`options_main.yy:13`）、`option_remove_unused_assets`=true（:19）、
  `option_collision_compatibility`=false（:8）、`option_legacy_primitive_drawing`=false（:17）

## 提交边界相关
- 证书/密钥/本机 SDK 路径保存在 `local_settings.json`，绝不提交（`.gitignore:38`）
- `.resource_order` 只记录 IDE 自定义排序，应忽略不提交（`.gitignore:29`；文件本体存在于 `261103GJ.resource_order:1-4`，
  **是否已被 git 跟踪未验证**）

## Links
- Parent: [INDEX.md](INDEX.md)
- 相关：[toolchain.md](toolchain.md)、[vcs.md](vcs.md)
