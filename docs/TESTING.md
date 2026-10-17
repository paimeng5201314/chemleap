# ChemLeap 测试记录

制作人：派蒙。2026-10-17，版本 `0.1.0`。

## 实际完成

| 项目 | 结果 | 范围 |
| --- | --- | --- |
| 模型测试 | 50/50 passed | 平衡方程式、实际生成物、48,000 组有解且唯一平台、500 次连续跳跃、唯一结算、难度、存档有效性 |
| 浏览器游戏测试 | 14/14 passed | 真实软件 WebGL、20 次 UI 连跳到 200 分、错误结束、蓄力/键盘、暂停、localStorage 重载及坏档保护、离线、390/768/1440 宽度、减少动画、WebGL 上下文丢失恢复 |
| AutumnOS 内部开发预览 | 16/16 passed | 官方工具校验/打包、用户确认预览、真实 H₂ + Cl₂ → HCl 得 10 分、SDK 权限授权、实际存档落盘、结束后同包新实例读取 |
| 发布材料校验 | passed | 三层清单一致、8 个 ZIP 资源逐项字节哈希与源码匹配 |

游戏运行源快照：`sha256:6a909c33279cdb6b4f309ad4d8929dbf5ca4ce003793f5b9e6b4a60747d00f08`。各文件字节摘要见根 `source-manifest.json`。

原生实测包与本仓库 `dist/ChemLeap-0.1.0.autumn` 相同：28,872 字节，SHA-256 `3a9f2f4e977e7adafec424df3223e19e3ddb5a7f14ce1c0005c88212552e8598`。上传前已在独立仓库目录重新运行模型测试（50/50）和官方 CLI 三层发布材料校验（passed）；上传后的远端完整性核对结果另存本地交付报告。

## 复现与边界

本仓库保留无依赖模型测试，运行 `node --test tests/chemleap-model-tests.cjs`。浏览器与原生驱动在宿主开发工作区运行，涉及固定测试工具链及原生确认流程；本仓库没有把这些不可独立运行的脚本宣传成开箱可用测试。原始机器报告含本机路径，公开文档只提供脱敏摘要。

浏览器测试使用 Windows 的 Chromium Headless 和 ANGLE SwiftShader，Chromium sandbox 保持开启；这不是硬件 GPU、物理触屏或完整系统矩阵验收。宿主验证版本为内部 SemVer `0.5.1-local.8`，SDK `0.3.0`。

普通社区启动、完整第三方网络隔离验收仍未完成；商店内完整安装/普通启动流程为 `not_run`，真实商店发现、版本资格与公开附件下载的本轮验证见下文。干净系统/Windows 10、硬件 GPU、物理触屏、人工屏幕阅读器及长期稳定性为 `not_run`。这些结果不替代 AutumnOS 完整产品验收。

## 2026-10-17 GitHub 商店发布验证

公开 Release `v0.1.0`（ID `415671030`）已发布，非 prerelease；三个独立附件均已匿名 HTTP 200 下载，大小和 SHA-256 与本地材料相同。实际下载材料再次通过官方 CLI 三层校验。仓库 Topics 为 `autumnos-app`、`autumn-app-sq`，根展示清单有效。

- GitHub 实际 Topic 查询返回目标仓库：`total_count=1`、`incomplete_results=false`。
- AutumnOS 真实生产适配器执行 `--probe-store`：`passed`，发现 1 个仓库，目标为 ChemLeap，分类 `Sq`。
- 同一适配器执行 `--probe-store-repository paimeng5201314/chemleap`：`passed`，`installable_release_count=1`，`0.1.0` 的 `can_install=true`，`warnings=[]`。

以上是实际远端发现、版本资格和附件下载验证，未把仓库源码 ZIP 当安装包，也未使用商店假数据。它们不表示完整商店 UI 安装/普通社区运行已通过，宿主既有安全门禁仍保留；游戏实际可玩证据继续对应同一字节包的内部开发者预览。
