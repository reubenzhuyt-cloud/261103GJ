# 操作检查清单

> last-verified: (未取 sha) @ 2026-11-03
> 依据：`opencode.json`、`GamemakerMaster.md:61-86,147-179`、`project-format.md`、`vcs.md`。
> 工具命令为**规范引用**，本任务未实际执行（禁止编译/禁止 git）。

## 职责
把其他模块的规则收敛为可直接照做的步骤。每步给出动作与通过标准。

## 开工前
- [ ] `gm_project_info` 确认 `mode=workspace-write`（默认 read-only 会拒绝一切写入）
- [ ] 确认 `GAMEMAKER_PROJECT` 指向 `E:\GMprojects\261103GJ`（`opencode.json:9`），避免改错工程
- [ ] `gm_snapshot_create`（带标签）建立还原点；必要时再 `gm_snapshot_list` 确认
- [ ] 读 `docs/architecture/INDEX.md`，只打开本次需要的模块文档

## 改资源时
- [ ] 新增资源：写 `.yy`（字段齐全、路径符合 `objects|sprites|rooms|scripts/<Name>/<Name>.yy`）
- [ ] **同时**登记进 `.yyp` 的 `resources[]`，否则资源被完全忽略
- [ ] 房间新增实例：写入 `layers[].instances[]` **且**登记 `instanceCreationOrder`
- [ ] 命名全局唯一，避开内置名（`score`/`lives`/`speed`/`hspeed`/`vspeed`/`gravity`/`x`/`y`…）
- [ ] 更新已有文件前先 `gm_file_read` 取最新 SHA-256，写入时带上（防脏写）
- [ ] Sprite：每帧两张 PNG（`<frameUUID>.png` + `layers/<frame>/<layer>.png`）都在
- [ ] **不用** `gm_room_instance_add`（缺字段必炸）；**警惕** `gm_sprite_create` 空壳（能编译但不可见）

## 改代码时
- [ ] 手动资源（`ds_*`/`surface_*`/`buffer_*`/`vertex_buffer_*`）释放写在 **Clean Up** 事件（换房不触发 Destroy）
- [ ] `with` 遍历里 `instance_destroy()` 后，用 `continue`/`break` 跳过该实例
- [ ] 每帧遍历用 `with (obj)`，不用 `for + instance_number + instance_find`（O(N²)）
- [ ] Create 事件不读同房其他实例的变量（初始化顺序：对象变量 → Create → 创建代码 → … → Room Start）
- [ ] Draw 事件一旦写代码就不再自动画精灵，需要时显式 `draw_self()`
- [ ] 字符串拼数字用 `string()` 或 `$"..."`；浮点比较用容差/`math_set_epsilon`
- [ ] 未在 `gm_object_event_upsert` 支持列表的事件（如 Collision）→ Step + `place_meeting`

## 收尾 / 提交前
- [ ] `gm_project_validate` → 须 `{"ok":true,"errors":0}`
- [ ] `gm_project_compile_errors` → 须 `ok=true, errors=[]`（真实编译，非“文件写成功”）
- [ ] 需要跑游戏：`Runner.exe -game "<project>\.gamemaker-mcp\temp\261103GJ.win"`
- [ ] `git status`：确认**无** `.gamemaker-mcp/`、`*.win`、`*.exe`、`Build*/` 等产物进入暂存区
- [ ] 确认无 `local_settings.json`、`*.resource_order` 被跟踪（`.gitignore:29,38`）
- [ ] 若 `.yy` 出现冲突：整体取一侧，禁止手工拼 JSON（`vcs.md`）

## Links
- Parent: [INDEX.md](INDEX.md)
- 依据：[toolchain.md](toolchain.md)、[project-format.md](project-format.md)、[gml-runtime.md](gml-runtime.md)、[vcs.md](vcs.md)
