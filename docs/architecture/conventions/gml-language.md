# GML 语言约定与陷阱

> last-verified: (未取 sha) @ 2026-11-03
> 来源：官方手册（主题：GML 语言参考 / 变量作用域 / 数组 / 方法 / 数据结构）+ 本机调研报告（`C:\Users\31087\.config\opencode\agents\GamemakerMaster.md:134-145`）。
> 本项目尚无 `.gml` 文件，以下行为**未在本项目运行验证**，均为手册/调研结论。

## 职责
写 GML 代码时必须遵守的语言规则与陷阱。
**不**覆盖：事件顺序、资源释放时机、渲染/GUI → 见 `gml-runtime.md`。

## 语言特性
- 支持 `struct`、`function`、`method`、构造函数、`static`、`enum`、`try/catch/finally/throw`（手册：GML 语言基础）
- `instance_create` 已不存在；只用 `instance_create_layer` / `instance_create_depth`（手册：实例创建）
- `ds_*` 未硬废弃，但官方建议：数组替代 ds_list、struct 替代 ds_map；`json_encode` 等少数 API 仍要求 ds（手册：数据结构）

## 类型与数值
- 数值只有 double；另有 `int32()`/`int64()`，HTML5 不支持 int64（手册：数值类型）
- 浮点比较必须用容差或 `math_set_epsilon`（手册：浮点精度）
- 未初始化变量直接读取会崩溃（`Variable <name> not set before reading it`），不会自动变 undefined；
  struct 动态键缺失才返回 `undefined`，用 `is_undefined()` / `??` 判断（手册：变量）
- 字符串 + 数字隐式相加会抛类型错误，必须 `string()` 或 `$"..."` 模板字符串（手册：字符串）

## 作用域（四层）
- `var` 局部 → 实例变量 → `global.` → `static`（手册：变量作用域）
- `static`：只能写在函数内；首次调用时初始化；写在 `if` 内也会被提升到函数入口；
  构造函数内 static 为**所有实例共享**；函数从未执行时通过 `func.var` 读取会崩溃（手册：static 变量）

## 数组与 struct 是引用类型
- `arr2 = arr1` 只复制引用；拷贝用 `array_copy()`，比较用 `array_equals()`（手册：数组）
- 数组读越界崩溃；写越界自动扩容补 0；`array_length` 不接受非法参数（手册：数组）

## `with` 陷阱
- 遍历中 `instance_destroy()` 后实例仍在内存直到当前事件结束；销毁后访问已释放资源会抛 Unknown Resource；
  需要 `continue`/`break` 跳过（手册：`with`）

## 函数与方法
- 回调丢失 `self` 时用 `method(self, fn)` 绑定；把 script 函数存进变量后每帧调用会反复生成 method struct，
  用 `method(undefined, fn)` 固化；判断用 `is_method()` / `is_callable()`（手册：方法）
- struct 方法的 `self` 绑定到 struct 而非实例，需显式传 owner — `GamemakerMaster.md:139`（其他项目已验证）
- 内置名不可覆盖：`score`/`lives`/`speed`/`hspeed`/`vspeed`/`gravity`/`x`/`y` 等（`GamemakerMaster.md:138`）

## 调试与错误处理
- 无原生 `assert()`；用 `show_debug_message()` / `show_debug_message_ext()`（手册：调试）
- 全局崩溃拦截 `exception_unhandled_handler(handler)`，回调拿到 message/longMessage/script/line/stacktrace，
  但**无法让游戏继续运行**（手册：异常处理）

## Links
- Parent: [INDEX.md](INDEX.md)
- 相邻：[gml-runtime.md](gml-runtime.md)
