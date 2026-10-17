# ChemLeap 与 AutumnOS 接入

制作人：派蒙。游戏 `0.1.0`，应用 ID `cn.labchronicles.chemleap`，SDK `0.3.0`，存档格式 `1`。

## 可用文件与试玩

`samples/chemleap/` 是完整独立游戏项目。AutumnOS 仍是 .NET/WinUI 原生客户端，游戏通过内部 WebView2 运行。`dist/ChemLeap-0.1.0.autumn` 是已经打包、校验和原生预览实测的同一安装包，不是 GitHub 的源码 ZIP。

在兼容的 AutumnOS 设置中开启开发者模式，打开开发者工具，使用“选择并检查 .autumn”，选择该包后进行内部预览并确认。也可以选择源码目录，依次校验、打包和预览。当前验证的宿主内部版本为 `0.5.1-local.8`。

开发预览使用独立来源标识，存档通过真实 SDK 申请 `saves` 权限，并按应用、来源和用户隔离。关闭后用同一个包预览可读回存档；重新打包导致字节改变时可能产生不同来源，不应假定会共用旧档。拒绝存档权限仍可继续游玩。浏览器单独预览使用明确标注的 localStorage，宿主请求失败不会切换到浏览器存储。

普通社区包运行仍受宿主既有安全门禁限制。本游戏没有增加认可名单、特殊豁免或额外权限；开发者预览测试不等于普通商店运行验收通过。

## 独立源码验证

在本仓库根运行：

```powershell
node --test tests/chemleap-model-tests.cjs
```

没有前端构建步骤。SDK 已作为固定字节副本包含在项目中。浏览器预览命令见根 README。

重新生成 `.autumn` 需要兼容 AutumnOS 交付的开发者工具或 Developer CLI。本独立仓库不包含宿主 .NET 解决方案，也不提供依赖缺失的假打包脚本。使用宿主工具校验项目、打包，再从实际包生成 `autumn.release.json`；内容变化后必须重新计算大小和 SHA-256，并重新验证三层清单。

## GitHub 商店上架条件

应用仓库为 [paimeng5201314/chemleap](https://github.com/paimeng5201314/chemleap)。根 `autumn.store.json` 提供展示信息，分类 `sq`，截图为本仓库实际游戏画面。

AutumnOS 商店发现与安装需要同时满足：

1. 公开仓库具有 `autumnos-app` 与 `autumn-app-sq` Topics，不同时添加 `autumn-app-pm`。
2. 默认分支根存在有效 `autumn.store.json`。
3. 公开 Release 标签为 `v0.1.0` 或 `0.1.0`，附件同时包含 `ChemLeap-0.1.0.autumn` 与文件名完全匹配的 `autumn.release.json`。
4. 本版本 `channel=stable`，GitHub Release 不标记为 prerelease。这里 stable 是应用版本渠道，不表示 AutumnOS 全部功能或安全验收完成。
5. 应用 ID、版本、入口、权限、存档格式、字节数和摘要与真实包一致，并满足宿主兼容要求。

2026-10-17 已公开发布 [v0.1.0](https://github.com/paimeng5201314/chemleap/releases/tag/v0.1.0)，Release ID `415671030`，非 prerelease；三个独立附件为 `ChemLeap-0.1.0.autumn`、`autumn.release.json`、`SHA256SUMS`。仓库 Topics 已设置为 `autumnos-app` 与 `autumn-app-sq`。匿名下载三个附件均返回 HTTP 200，字节与摘要匹配，下载后的三层材料通过官方 CLI 校验。更多实际商店验证见 `TESTING.md`。

在具备上述发布条件的宿主中，可以通过商店“打开 GitHub 仓库”或粘贴仓库链接查找真实详情。缺少条件时应显示缺项，不创建假安装任务。现有宿主门禁仍需另行完成，不能靠添加 Topics 或 Release 绕过。
