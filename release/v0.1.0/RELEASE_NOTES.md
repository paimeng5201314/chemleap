# ChemLeap 0.1.0 · 3D 化学跳一跳

制作人：派蒙。

H₂ 起点、15 条平衡反应、每轮 3～6 个平台且保证有解；点击或空格蓄力跳跃，成功反应后变为生成物并加分，错误则结束。包含三维场景、反应效果、图鉴、暂停、重开、最高分与 AutumnOS SDK 存档。无游戏网络依赖，仅申请 `saves`。

兼容要求：AutumnOS 内部版本至少 `0.5.1-local.8`，SDK 至少 `0.3.0`。当前已验证路径为用户确认后的开发者预览；宿主普通社区运行仍有既有安全门禁，添加 Release 不会解除该限制。

模型 50/50、浏览器 14/14、原生开发预览 16/16 已通过，具体范围和未验证项见仓库 `docs/TESTING.md`。

`ChemLeap-0.1.0.autumn`：28,872 字节，SHA-256 `3a9f2f4e977e7adafec424df3223e19e3ddb5a7f14ce1c0005c88212552e8598`。

## 发布操作清单

本文件是待用说明，不代表 Release 已创建。当前 GitHub 连接没有 Release/Topics 写工具，因此只完成仓库文件上传。

1. 创建标签 `v0.1.0`，目标选择上传后的 `main`，标题使用上方标题。
2. 不勾选 prerelease；包版本 `0.1.0` 和 `channel=stable` 必须一致。
3. 将仓库 `dist/` 中的 `ChemLeap-0.1.0.autumn`、`autumn.release.json` 作为同一 Release 的独立附件上传，可同时附 `SHA256SUMS`。GitHub 自动生成的源码 ZIP 不代替 `.autumn` 包。
4. 发布前核对附件字节数与摘要。在仓库 About 中添加 Topics `autumnos-app` 和 `autumn-app-sq`。
5. 公开后重新核验 AutumnOS 实际发现、元数据、下载与校验。普通运行仍按宿主安全状态处理。
