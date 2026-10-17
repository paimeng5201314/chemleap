# ChemLeap · 3D 化学跳一跳

制作人：派蒙。游戏版本0.1.0，应用ID `cn.labchronicles.chemleap`，AutumnOS SDK0.3.0，存档格式1。

## 实现方案与项目结构

这是可打包为 `.autumn` 的独立离线游戏。AutumnOS仍是.NET/WinUI原生客户端；游戏在内部WebView2呈现。WebGL负责真实三维网格、透视相机、深度测试、法线光照、分子角色、平台、阴影及反应粒子；没有外部引擎、CDN、远程字体或运行时下载。WebGL不可用时明确显示Canvas2D投影降级。实现依据可参阅[MDN WebGL教程](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Tutorial)。

```
samples/chemleap/
  manifest.json    应用身份、入口、saves权限和格式版本
  index.html       游戏、计分、平台、图鉴、存档的可访问界面
  game.css         桌面/平板/窄屏布局与减少动态效果
  chemistry.js     物质、15条平衡反应、条件、真实生成物和索引
  model.js         纯逻辑状态机、平台生成、跳跃结算和存档验证
  renderer.js      无依赖WebGL三维渲染、选取、上下文恢复
  game.js          输入、动画计时、UI、生命周期与SDK存档
  autumn-sdk.js    固定SDK 0.3.0副本
tests/
  chemleap-model-tests.cjs
```

`chemistry.js`与`model.js`同时支持浏览器global与Node CommonJS，便于在无界面的自动测试中使用同一份生产逻辑。没有把测试替身编入游戏。渲染、输入和反应模型分别维护；RAF由控制器统一管理，渲染器不另开定时循环。

## 核心状态与反应判定

`ChemLeapModel.create(seed)`创建初始状态：`current='H2'`、`score=0`、`best=0`、`round=1`、`phase='ready'`及首组平台。状态只有`ready → jumping → ready/over`。`beginJump`把所选目标和当前物质固定在私有WeakMap；`land`是唯一结算入口，重复点击或重复结算不会多次加分。

反应以两个物质ASCII ID排序组成键，任意顺序得到同一条规则：

```javascript
const reaction = ChemLeapChemistry.findReaction(state.current, platform.substance);
// reaction === null：此游戏反应表不支持该组合。
// reaction.products：全部生成物；reaction.next：下一轮控制的真实生成物。
const jump = ChemLeapModel.beginJump(state, index, power);
// 700ms跳跃完成后调用一次：
const result = ChemLeapModel.land(state);
```

成功增加10分、更新最高分与当前物质，记录本轮方程式，然后生成新平台。失败保持已有分数，进入`over`，显示本轮物质组合并提供重开。重开从H₂和0分开始，保留本会话最高分；主动保存后最高分也随档持久化。

多产物并不意味着把式子简化掉。例如中和反应完整显示`HCl + NaOH → NaCl + H₂O`，角色选择H₂O继续，NaCl作为副产物保留在方程式中。每条`next`必须属于`products`，且可达的八种角色物质均有至少两种后续试剂，避免生成物本身成为死路。

## 初始反应表

| 平衡方程式 | 下一物质 |
| --- | --- |
| 2H₂ + O₂ → 2H₂O | H₂O |
| H₂ + Cl₂ → 2HCl | HCl |
| H₂ + CuO → Cu + H₂O | H₂O |
| 2H₂O + 2Na → 2NaOH + H₂ | NaOH |
| H₂O + CaO → Ca(OH)₂ | Ca(OH)₂ |
| HCl + NaOH → NaCl + H₂O | H₂O |
| 2HCl + Ca(OH)₂ → CaCl₂ + 2H₂O | H₂O |
| CaCO₃ + 2HCl → CaCl₂ + CO₂ + H₂O | CO₂ |
| CaCO₃ + 2HNO₃ → Ca(NO₃)₂ + CO₂ + H₂O | CO₂ |
| 2NaOH + CO₂ → Na₂CO₃ + H₂O | Na₂CO₃ |
| Ca(OH)₂ + CO₂ → CaCO₃ + H₂O | CaCO₃ |
| Na₂CO₃ + 2HCl → 2NaCl + CO₂ + H₂O | CO₂ |
| Na₂CO₃ + 2HNO₃ → 2NaNO₃ + CO₂ + H₂O | CO₂ |
| Na₂CO₃ + Ca(OH)₂ → CaCO₃ + 2NaOH | CaCO₃ |
| NaOH + HNO₃ → NaNO₃ + H₂O | H₂O |

表中为教学条件下的抽象反应，条件文字保存在每条`condition`并显示于图鉴，例如水溶液中的中和、碱相对充足的CO₂反应，或需要相应能量的反应。游戏不模拟实验操作。配平与反应类别参考[OpenStax配平方程式](https://openstax.org/books/chemistry-2e/pages/4-1-writing-and-balancing-chemical-equations)和[UBC酸碱与产气反应](https://chem.libretexts.org/Courses/University_of_British_Columbia/CHEM_100:_Foundations_of_Chemistry/07:_Chemical_Reactions/7.08:_AcidBase_and_Gas_Evolution_Reactions)；逐项来源保存在`chemistry.sources`。测试还对每条显示方程式独立解析元素及括号，验证两侧原子守恒。

## 平台生成与难度

| 当前分数 | 平台总数 | 可反应平台 | 干扰平台比例 |
| --- | ---: | ---: | ---: |
| 0～40 | 3 | 2 | 1/3 |
| 50～90 | 4 | 1 | 3/4 |
| 100～190 | 5 | 1 | 4/5 |
| 200及以上 | 6 | 1 | 5/6 |

先从当前物质的有效试剂中无放回抽取安全项，再从He、Ne、Ar、NaCl、Cu、Kr中抽取不在该反应对索引的干扰项，最后洗牌。平台物质不重复，安全项数不依赖随机运气；候选不足明确抛错误，不生成无解关卡。所有随机使用可重现的uint32种子。

## 跳跃与蓄力

点击下方物质按钮或数字键1～6直接跳往对应平台；画面点选后再次点击同一平台也可起跳。左右方向键选择目标。按住空格或蓄力按钮时，蓄力值以2400ms周期在0～1间往返，`floor(power * platformCount)`决定高亮目标，松开即跳往该目标。力度同时改变弧线高度`2.2 + power * 1.1`，跳跃时长700ms。此次基础版以化学选择为失败条件，不附加随机落点失败。

动画用透视世界坐标中的起终点插值与正弦高度，只有动画抵达后结算。暂停、页面隐藏、AutumnOS后台或WebGL上下文丢失时停止RAF及蓄力；恢复后继续未结算的同一跳，不把后台时间补算。指针取消和焦点丢失会取消蓄力，不偷偷跳跃。

## 存档与权限

在AutumnOS中仅声明`saves`，第一次主动保存/读取时申请权限。通过真实SDK `saves.write/read`使用`game`槽，按宿主的来源和当前用户隔离；拒绝授权不影响游玩。没有身份或网络权限，不接收宿主登录凭据。普通浏览器试玩使用明确标注的真实localStorage，宿主请求失败不会转写浏览器存储。

存档包含物质、得分、最高分、轮次、种子、选择与最多32条历史。读取严格核对格式、值域、分数/轮次关系、历史反应和真实生成物；平台根据种子重建，不相信存档自报的平台。非法或未来格式不改变当前局面。跳跃期间UI禁用保存与读取，模型的序列化也不会提前结算。

## 开发与验证

游戏无 npm 运行依赖，无需前端构建。模型测试执行 `node --test tests/chemleap-model-tests.cjs`，浏览器预览命令见根 README。用兼容 AutumnOS 开发者工具选择项目目录或现有 `.autumn` 包进行校验和内部预览。

独立仓库不包含宿主解决方案与仅适用于宿主工作区的自动化脚本。实际完成的模型、浏览器和原生测试范围见 [测试记录](TESTING.md)，打包与商店约束见 [接入说明](chemleap-integration.md)。开发预览不等于普通商店运行验收通过。
