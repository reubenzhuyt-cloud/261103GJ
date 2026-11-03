# 版本控制约定

> last-verified: (未取 sha) @ 2026-11-03 —— 本任务禁止运行 git，下列规则对照 `.gitignore` / `.gitattributes` 本地文件核实。
> `.yy` 合并风险来自官方手册（主题：版本控制 / 源码管理）+ 调研报告。

## 职责
哪些文件进仓库、`.yy` 合并纪律、`.gitignore`/`.gitattributes` 的作用。
**不**覆盖：具体 git 命令执行（由 Chief/用户操作）。

## 必须提交
- `*.yyp`、`*.yy`、`*.gml`（`.gitignore:4-5` 注释明确其为源码）
- 图片/音频**源**素材、`options/`、`datafiles/`、`.gitignore`、`.gitattributes`、`opencode.json`
  （`.gitignore:50` 注释明确根 `opencode.json` 是项目配置、要提交）

## 禁止提交（`.gitignore` 对照行号）
- 构建产物：`*.yyz`(:11)、`*.win`(:12)、`*.exe`(:13)、`Build/`(:19)、`Builds/`(:20)、`output/`(:21)
- IDE 排序：`*.resource_order`(:29)；备份：`*.bak`(:30)、`*.autosave`(:31)、`*.old`(:33)
- 本机密钥/设置：`local_settings.json`(:38)、`*.user`(:39)
- Agent 工具目录：`.gamemaker-mcp/`(:46)、`.opencode/`(:52)

## `.yy` 合并纪律（手册：源码管理）
- `.yy` 是 JSON：字段顺序敏感、尾逗号容错；三方合并极易整体损坏
- 冲突必须**整体取一侧**（ours/theirs 二选一），**禁止手工拼接 JSON 字段**
- 未在 `.yyp resources[]` 注册的资源等于不存在 —— 合并后核对 `.yyp` 与磁盘一致（见 `project-format.md`）

## `.gitattributes`（`.gitattributes:2-8`）
- `*.yy linguist-generated=true` —— 不计入语言统计
- 强制 `eol=lf`：`*.gml`、`*.yy`、`*.yyp`、`*.json` —— 保证跨机器合并简单

## 未验证项
- `261103GJ.resource_order` 是否已被 git 跟踪（需 `git ls-files` 核验，本任务未运行 git）
- 远端仓库 `main` 分支状态与本地同步情况（同上）

## Links
- Parent: [INDEX.md](INDEX.md)
- 相关：[checklist.md](checklist.md)、[project-format.md](project-format.md)
