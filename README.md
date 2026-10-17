# chemleap

**ChemLeap · 3D 化学跳一跳**，制作人：派蒙。版本 **0.1.1**。

从 H₂ 出发，选择能反应的物质平台。成功后变成生成物并加分，选错则结束。包含15条平衡反应、每轮3～6个平台且保证1～2个有效选项、点击/数字键/蓄力跳跃、反应效果、图鉴、暂停、重开和SDK存档。

![实际游戏画面，0.1.0历史截图，未冒充0.1.1截图](media/gameplay.png)

## 下载和商店

[下载 ChemLeap-0.1.1.autumn](https://github.com/paimeng5201314/chemleap/releases/download/v0.1.1/ChemLeap-0.1.1.autumn)（28871字节）。[版本说明](https://github.com/paimeng5201314/chemleap/releases/tag/v0.1.1)。这是AutumnOS内部应用包，源码ZIP不能代替安装包。

兼容AutumnOS商店可搜索ChemLeap，或在“打开GitHub仓库”输入paimeng5201314/chemleap，选择0.1.1下载。保留既有仓库标签；分类由客户端识别规则决定，不代表安全认证或额外权限。

包SHA-256：e00ecf58d1c4a5d1190163cd02a13199135809ad0a782d5b830274e9642da0a2。

## 宿主兼容与游玩

发布元数据最低内部宿主版本0.5.1-local.8，SDK0.3.0。旧宿主普通社区启动仍受安全门禁限制，可通过设置启用开发者模式→开发者工具→选择并检查.autumn→内部预览并确认。

新增宿主隔离实现在独立的AutumnOS 0.5.2-local.1本地候选中。该候选对普通安装包先做每实例原生隔离自检，通过后才加载游戏；它不随本游戏包分发，本次没有发布启动器。新路径的普通跳跃及SDK存档读回尚待桌面条件允许后验证，不能仅凭0.1.1下载成功声称任意旧版宿主已可普通运行。

进入游戏后点击物质按钮或按数字键1～6跳跃；也可按住空格蓄力、松开起跳，左右键选目标。只申请saves权限；保存、读取均由宿主单独授权。玩法和存档格式1保持。

## 0.1.1变更与测试

统一应用清单及界面版本为0.1.1，按实际版本生成包名、元数据和校验值，更新兼容与验证说明。玩法代码和SDK保持。本版本实际重跑模型50项及三层材料校验、8个ZIP资源逐项hash读回；完整新版原生游玩为not_run。0.1.0浏览器14/14、开发预览16/16是旧版历史证据，不能直接算作新版整包成绩。[测试范围](docs/TESTING.md)。

## 本地开发

游戏无npm运行依赖、无CDN、无运行时外网请求。使用WebGL三维渲染，WebGL不可用时明确显示Canvas2D降级。模型测试：

~~~powershell
node --test tests/chemleap-model-tests.cjs
~~~

浏览器预览可在仓库根执行以下命令，访问http://127.0.0.1:8080/。浏览器localStorage与AutumnOS宿主SDK存档分开。

~~~powershell
python -m http.server 8080 --bind 127.0.0.1 --directory samples/chemleap
~~~

源码在samples/chemleap/，完整方案、结构、反应表和跳跃规则在[制作文档](docs/chemleap-game.md)，接入边界在[兼容说明](docs/chemleap-integration.md)。source-manifest.json记录本版本8个运行文件的真实摘要。旧版0.1.0 Release及其历史材料保留。

反应按图鉴注明的教学条件抽象处理。仓库目前没有声明独立开源许可证。
