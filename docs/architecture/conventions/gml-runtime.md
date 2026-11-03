# GML 运行时：事件、生命周期、渲染

> last-verified: (未取 sha) @ 2026-11-03
> 来源：官方手册（主题：事件 / 实例创建顺序 / 内存与垃圾回收 / 绘制与 GUI / 性能）。
> 本项目尚无对象与事件代码，以下行为**未在本项目运行验证**。

## 职责
事件执行顺序、进房初始化时机、手动资源释放与 GC、渲染/GUI 规则、性能反模式。
**不**覆盖：语法与类型 → 见 `gml-language.md`；字段格式 → 见 `project-format.md`。

## 每帧事件顺序（手册：事件）
- Begin Step → (Timelines / Time Sources / Alarms) → Step → 物理 / 移动 / 碰撞 → End Step →
  Draw 周期：Pre → Begin → Draw → End → Post → GUI Begin → GUI → GUI End
- Draw 事件里写了任何代码，就不再自动绘制自身精灵，需显式 `draw_self()`
- depth 越大越先画（即越靠底层）
- `event_perform` 同步嵌套执行，易无限递归；在非 Draw 流程调用 Draw 可能没有 Render Target

## 进房初始化顺序（手册：实例创建）
- 按房间 `instanceCreationOrder` 逐个执行：对象变量 → Create 事件 → 实例创建代码；
  全部实例完成后 → Game Start（仅首房）→ Room Creation Code → Room Start
- **Create 里不要读同房其他实例的变量**（此时其他实例可能尚未创建）
- 实例必须登记到 `instanceCreationOrder`，否则构建报错或 Create 不触发（见 `project-format.md`）

## 资源释放与垃圾回收（手册：内存管理）
- 必须手动释放：`ds_*`、`surface_*`、`buffer_*`、`vertex_buffer_*`
- 释放写在 **Clean Up 事件**：换房**不**触发 Destroy，只触发 Room End + Clean Up
- GC 只回收 struct / array / method（分代 + 增量）；`gc_target_frame_time()` 默认 100μs；
  `gc_collect()` 可强制回收

## 渲染与 GUI（手册：绘制 / GUI）
- `display_set_gui_size()` 锁定逻辑分辨率；读取用 `display_get_gui_width/height()`
- 自建 surface 可能丢失，绘制前必须 `surface_exists()` 判断
- 像素边缘发品红 → `option_windows_interpolate_pixels` 需为 `false`（见 `project-format.md`）

## 性能反模式（手册：性能）
- `for + instance_number + instance_find` 是 O(N²)，改用 `with (obj)` 遍历
- 每帧重复把 script 函数存变量再调用会反复生成 method struct（见 `gml-language.md`）

## Links
- Parent: [INDEX.md](INDEX.md)
- 相邻：[gml-language.md](gml-language.md)
