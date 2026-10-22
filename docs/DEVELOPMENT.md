# 校招日程管家 · 项目文档

> 单文件、纯本地的校招投递追踪工具。把「投了哪些公司、走到哪一步、什么时候要做什么」三件事装进一个 HTML 页面，数据全部存在你自己的 HTML 文件里。

---

## 一、项目概述

| 项 | 说明 |
| --- | --- |
| 形态 | 单文件 HTML（内嵌 CSS / JS / JSON 数据），双击即可用，无需安装、无需服务器 |
| 存储 | 数据内嵌在文件自身的 `<script type="application/json" id="APP_DATA">` 块中；保存时直接覆盖原文件 |
| 依赖 | 无。不用任何 CDN、框架、第三方库 |
| 浏览器 | Chrome / Edge 86+（覆盖保存依赖 File System Access API）。Firefox 会退化为「下载副本」 |
| 主文件 | `campus-recruit-planner.html` —— 日常使用的正式版（放你自己的投递数据） |
| 演示文件 | `campus-recruit-planner-demo.html` —— 24 家公司 / 14 条日程 / 9 阶段 / 12 标签的虚构演示数据，带开场说明与署名（作者 Bilibili -Estrella-__），**存储命名空间独立、不含任何个人信息，可单独分发** |
| 快照目录 | `snapshots/` —— 每次大改前的备份 |

### 核心作用

1. **投递追踪** —— 每家公司一张卡片，记录公司、岗位、城市、投递日期、截止日期、备注、投递记录链接。
2. **流程可视化** —— 9 个流程阶段（已投递/初筛 → 笔试 → 一面 → 二面 → 三面/HR面 → 谈薪 → offer发放 → 流入人才库 → 已拒绝），用彩色宝石徽章镶嵌在卡片上，一眼看出进度。
3. **日程安排** —— 月历视图 + 时间线视图，面试、笔试、宣讲会都能排进去，到期前有相对时间提示（「3 天后」「今天」「已过期」）。
4. **筛选与统计** —— 侧栏按流程标签、属性标签、城市、快捷条件筛选；顶部统计卡显示总数 / 进行中 / Offer / 人才库 / 已拒绝；漏斗条显示各阶段占比。
5. **导出报表** —— 一键把当前看板生成 A4 版式 PDF，含概览、漏斗、明细表和日程汇总。
6. **智能识别** —— 导入 JSON 时自动按公司名、岗位、备注识别标签，省去手工打标。
7. **快速预览** —— 点卡片直接看完整详情（长备注全文、相关日程、链接），不用进编辑弹窗。
8. **公司页与评分** —— 顶栏「公司」页把同一家公司的所有投递聚合起来，按评分或薪资排名；评分 = 评分标签分值之和 + 个人倾向加减分。
9. **评分标签** —— 第三类标签（既不是流程也不是属性），带正负分值（双休 +5 / 网络风评差 −8），名称、颜色、分值都可自定义增删。
10. **卡片外观自定义** —— 卡片宽度、内边距、圆角、边框粗细与颜色、公司名字号、背景色、阴影、悬停效果都能调，实时预览，随数据保存。
11. **手机可用** —— 抽屉式侧栏、单列卡片、日历保留文字；保存一键下载、导出 PDF 自动走 iframe 打印。

---

## 二、快速上手

### 打开

双击 `campus-recruit-planner.html`，用 Chrome 或 Edge 打开即可。第一次打开会看到内置的示例数据。

### 首次连接文件（重要）

保存**永远不会弹文件选择框**。要让改动写回文件，需要先连接一次：

1. 打开文件资源管理器，找到 `campus-recruit-planner.html` 自己。
2. 把它**拖进浏览器页面**，页面出现全屏虚线高亮时松手。
3. 浏览器顶部可能弹一条「允许写入」的小权限条，点「允许」即可。
4. 之后所有改动都会自动覆盖写回这个文件，无需再操作。

> 没连接文件时也能正常用，改动存在浏览器的恢复缓冲里，不会丢；只是没写进磁盘文件。状态栏会一直提示「把本 HTML 拖到页面上即可连接」。
>
> 拖进来的必须是本工具自己的 HTML（里面要有 `id="APP_DATA"` 数据块）。拖错文件会提示「这个文件里没有数据块」。

### 日常使用

- **加投递** —— 右上角「+ 投递」，填公司名（必填）、岗位、城市、流程标签（必填）、投递/截止日期、属性标签、链接按钮。
- **改投递** —— 卡片上「编辑」。
- **推进流程** —— 编辑卡片，换一个流程标签，或直接点卡片里的宝石槽。
- **排日程** —— 进「日历」视图，把左下角拖拽池里的公司卡片拖到某一天，弹窗里写「一面」「二面」这类事项即可。
- **删日程** —— 日历上把鼠标移到日程上，右侧出现 `×`；或点日程打开编辑弹窗，底部有红色删除按钮。
- **撤销 / 重做** —— `Ctrl+Z` / `Ctrl+Shift+Z`（或 `Ctrl+Y`），最多 80 步。
- **强制保存** —— `Ctrl+S`。

---

## 三、功能清单

### 1. 四视图

| 视图 | 内容 |
| --- | --- |
| 岗位看板 | 投递卡片网格，可按流程 / 投递日期 / 截止日期 / 公司名 / 薪资 / 评分 / 星标排序，可切「舒适 / 紧凑」密度 |
| 时间线 | 按时间排列的投递 + 日程混合流，过期条目置灰，带「· 今天」「· 明天」相对标记 |
| 日历 | 月历网格，每天显示当日日程色条，`‹ ›` 翻月，「今天」回到当月 |
| 公司 | 按公司名聚合的排名卡片，可按评分 / 薪资 / 公司名排序，点卡片打开该公司第一条投递的预览 |

### 2. 标签体系（三层）

- **流程标签**（stage）—— 单选，代表当前进度。宝石形状（`clip-path` 切角），带渐变底色。
- **属性标签**（tag）—— 多选，代表公司特征（机械 / 电气 / 家电 / 汽车 / 新能源 / 半导体 / 医疗器械 / 材料 / 软件 / 大厂 / 国企 / 外企 / 快消 / 机器人）。圆形色点 + 名称，镶在卡片的凹陷槽里。
- **评分标签**（scoreTag）—— 多选，带正负分值（上市公司 +4 / 双休 +5 / 网络风评差 −8 / 裁员传闻 −6…）。既不是流程也不是属性，专门用来算分。

三套标签都能在「管理」弹窗里增删改名称和颜色（评分标签还能改分值）。颜色用 `<input type="color">` 取色，底色由 `gemBg()` 自动算渐变。

### 2.1 评分系统

- 每条投递的评分 = `Σ(所选评分标签的分值) + 个人倾向加减分`
- 字段：`scores: []`（评分标签 id 数组）、`bias: 0`（个人倾向，可正可负）、`salary: 0`（月薪 k 数，用于薪资排名）
- 编辑弹窗里有「评分标签」点击选择区、薪资输入框、个人倾向加减分输入框
- 卡片上公司名右侧显示评分徽章（正分绿底、负分红底、0 灰底）
- 预览弹窗 meta 里显示「薪资」「评分」，并有「评分标签」区块列出各标签及其分值

**公司页聚合规则**（`companyGroups()`）：按 `company` 字段精确分组，公司评分取该公司所有投递评分的**平均值**（保留 1 位小数），薪资取所有投递的**最大值**，评分标签取并集，公司备注取第一条非空的 `coNote`。

### 3. 侧栏筛选

- 流程标签（点选，多选 = 或）
- 属性标签（可切「或 / 且」）
- 城市（自动从数据里汇总）
- 快捷：★ 星标 / 7 天内截止 / 无链接
- 搜索框（`/` 聚焦），匹配公司 / 岗位 / 标签 / 备注

### 4. 拖拽排日程

日历视图左下角有「拖到日历建日程」拖拽池，列出当前筛选结果的公司小卡片。把卡片拖到日历某一天 → 弹出快速创建弹窗 → 填事项（默认取流程标签名，可改）和时间 → 生成日程。

### 5. 导出 PDF

点「导出 PDF」→ 弹窗选：
- **范围** —— 当前筛选 / 全部投递
- **内容** —— ☑ 附日程安排表 ☑ 附职位备注 ☑ 附公司备注 ☑ 附评分 ☑ 附评分标签（五个开关互相独立）
- **分组** —— 按流程分组 / 不分组（一张表）

点「生成 PDF」后走浏览器打印，在打印对话框里选「另存为 PDF」。版式为 A4 竖版，含标题、导出时间、5 个统计卡、彩色漏斗条 + 图例、明细表格、日程汇总表、页脚。

明细表的列按开关动态增减：`公司 / 岗位 / 城市 / 投递 / 截止 / 属性标签` 是固定列，之后依次按需插入 `评分`、`评分标签`、`日程`。评分列居中显示（正分绿字带 `+`、负分红字），评分标签列每个标签带分值且正分绿底、负分红底。列宽按开启的列数动态分配（`prTable()` 里按 `unit` 比例算）。

**备注独立成行**：备注不再挤在公司单元格里（那样会把整行撑得很高），而是每条投递下面单独占一行、跨所有列合并（`<tr class="pr-noterow"><td colspan="N">`）。职位备注前缀蓝色「职位」标签，公司备注前缀「公司」标签，两者用竖线分隔。

### 6. 导入 + 自动识别标签

点「导入」选 JSON 备份 → 解析成功后会弹出「自动识别标签」清单：
- 摘要条显示「从 N 条投递里识别出 M 个标签（其中 K 个是新建的），应用后会有 X 条投递被打上标签」
- 清单每行 = 复选框 + 色点 + 标签名 +（新建时）琥珀色「新建」徽章 + 「N 条」
- 默认全选，可逐个取消，点「应用选中标签」或「跳过」

识别规则见 `TAG_RULES`（14 条），按公司名 / 岗位 / 城市 / 备注 / 流程名 / 已有标签名做子串匹配。已存在的同名标签会复用，不会重复创建。

### 7. 撤销 / 重做

基于快照栈，`commitHistory()` 在每次改动后压栈，420ms 内的连续改动会合并成一步，最多保留 80 步。`Ctrl+Z` 撤销，`Ctrl+Shift+Z` 或 `Ctrl+Y` 重做。

### 8. 实时保存

任何改动后 800ms 自动覆盖写入已连接的文件，**不弹任何对话框**。若尚未连接文件，则写入 localStorage 恢复缓冲（`crp_recover_buffer`），状态栏提示「把本 HTML 拖到页面上即可连接」。连接方式：把 HTML 文件从资源管理器拖进页面，松手即可。

### 9. 卡片预览

点卡片任意空白处（不是按钮）弹出「投递详情」：公司名、岗位、星标、流程标签 + 属性标签、投递/截止日期（过期标红、临近标橙）、城市、相关日程数、链接按钮、完整备注、相关日程列表。底部三个按钮：删除 / 关闭 / 编辑（编辑直接转到编辑弹窗）。移动端全宽。

卡片上的备注限制 3 行（手机 2 行）并加省略号，只有预览里才完整显示。

### 10. 移动端适配

| 断点 | 行为 |
| --- | --- |
| > 1080px | 完整桌面布局，侧栏 272px 固定 |
| 861–1080px | 侧栏收窄到 244px，统计卡 3 列 |
| 761–860px | 侧栏改抽屉（☰ 打开），顶栏单行紧凑，卡片自适应 2–3 列，日历保留文字（9.5px、最多 2 行） |
| ≤ 760px | 卡片单列，日历继续保留文字只压缩尺寸，弹窗全宽 |
| ≤ 400px | 进一步缩小字号和内边距 |

移动端顶栏为 sticky，永远贴顶；`⋯` 按钮收纳「导出 / 导入 / 导出 PDF / 外观」等次要操作。

**移动端的两处行为差异**（`isMobile()` 判定：`matchMedia("(max-width:860px)")` 或 UA 命中 Android/iPhone/iPad/iPod/Mobile/HarmonyOS）：
- **保存改一键下载** —— 移动端不要求拖放连接文件句柄，点 💾 直接下载一份新的 HTML 副本（`download(currentFileName(), buildSnapshot())`），状态栏显示「已下载副本」。
- **导出 PDF 自动下载** —— 移动端不走 `window.print()`，而是 `printViaIframe()` 把完整报表写进隐藏 iframe 调 `contentWindow.print()`；若返回失败则降级为下载一份独立的可打印 HTML（内嵌自动 `window.print()`）。

### 11. 卡片外观自定义

`⋯` 菜单里的「外观」打开「卡片外观」弹窗，7 行控件全部实时生效并随数据一起保存（存在 `DB.look`）：

| 控件 | 范围 | CSS 变量 |
| --- | --- | --- |
| 卡片宽度 | 210–460px | `--card-min` |
| 内边距 | 8–26px | `--card-pad` |
| 圆角 | 0–26px | `--card-radius` |
| 边框粗细 | 0–4px | `--card-bd` |
| 公司名字号 | 11–22px | `--card-co-size` |
| 卡片背景 | 任意色 + 「默认」 | `--card-bg` |
| 边框颜色 | 任意色 + 「默认」 | `--card-bd-color` |
| 显示投影 | 勾选 | `--card-shadow` |
| 悬停加深边框 | 勾选 | `body.no-hover-bd` |

底部有实时预览卡与「全部恢复默认」。拖动滑杆时静默实时生效（`oninput`），松手时才提交撤销点（`onchange`），所以一次拖动只占一步撤销。

`LOOK_DEF = {min:292, pad:14, radius:14, bd:1, co:14.5, bg:"#ffffff", bdColor:"#e6e9f2", shadow:true, hover:true}`；`normalizeLook(x)` 负责范围钳制与色值校验。

---

## 四、数据结构

```json
{
  "v": 4,
  "stages": [{ "id": "st1", "name": "已投递/初筛", "color": "#3d5afe" }],
  "tags":   [{ "id": "tg1", "name": "机械", "color": "#0e9f6e" }],
  "scoreTags": [{ "id": "sq01", "name": "双休", "color": "#0d9488", "score": 5 }],
  "apps": [{
    "id": "ap01",
    "company": "示例公司",
    "position": "机械助理工程师",
    "city": "北京",
    "stage": "st1",
    "tags": ["tg1"],
    "scores": ["sq01"],
    "salary": 13,
    "bias": 4,
    "applied": "2026-09-20",
    "deadline": "2026-10-30",
    "star": false,
    "note": "职位备注（进度、联系人、面试反馈）",
    "coNote": "公司备注（风评、通勤、福利）",
    "links": [{ "name": "投递记录", "url": "https://..." }]
  }],
  "schs": [{
    "id": "sc01",
    "app": "ap01",
    "date": "2026-10-28",
    "time": "14:00",
    "title": "一面",
    "place": "线上腾讯会议"
  }]
}
```

`normalize()` 在加载时补齐缺失字段，保证老数据能兼容（缺 `scoreTags` 时补空数组，缺 `scores`/`salary`/`bias`/`coNote` 时补 `[]`/`0`/`0`/`""`）。

**注意字段名**：
- 日程关联字段是 **`app`**（不是 `appId`）
- 收藏字段是 **`fav`**（不是 `star`）
- 两条备注分开存：`note` = 职位备注，`coNote` = 公司备注

---

## 五、关键技术点与踩坑记录

### 覆盖保存（File System Access API）

**结论**：点 💾 绝不弹文件选择器。`file://` 下 `showOpenFilePicker` 只在拖放连接路径用；保存路径完全静默。

**坑 1（手势消耗）**：`await` 会消耗 user activation。实测 `await h.queryPermission()` 之后再 `h.createWritable()` 必报 `SecurityError: User activation is required to request permissions.`。所以有句柄时必须**同步**发 `requestPermission({mode:"readwrite"})`，不能先 await 任何东西。

**坑 2（旧实现弹另存为）**：早期版本无句柄时会 `showOpenFilePicker()`，用户看到的就是「另存为」。

**现在的行为**：
- 有句柄且已授权 → 直接 `writeHtml()` 覆盖，无任何弹窗
- 有句柄未授权 → 同步 `requestPermission()`（浏览器顶部一条小权限条，不是文件对话框）
- 无句柄 → 只写 localStorage 恢复缓冲 + 状态栏提示「未连接文件 · 把本 HTML 拖到页面上即可连接」

**连接文件的方式**：把本 HTML 文件从资源管理器拖到页面任意位置，出现全屏虚线高亮时松开，即完成连接（`initDropConnect()`，用 `getAsFileSystemHandle()`）。

**坑 3（headless 测不了）**：headless 下真实拖放拿到的句柄调 `createWritable` 会无限挂起（权限条无法显示），测试必须用 mock 句柄替换 `requestPermission`/`createWritable`。

### 顶栏 sticky 失效

**坑**：`html,body{height:100%}` 把 body 锁成视口高度，`document.scrollingElement` 是 html，sticky 的约束框只有 800px 高，滚过一屏后顶栏就跑了。

**解法**：改成 `html{height:100%}` + `body{min-height:100%}`。

### 侧栏抽屉在列方向下宽度塌陷

**坑**：`.shell{flex-direction:column}` 后 `align-items:flex-start` 仍然生效，`main` 被收缩到内容宽度（768px 视口下只有 324px），导致卡片只能单列。

**解法**：媒体查询里改成 `.shell{flex-direction:column;align-items:stretch}`。

### `$$("nav button")` 误选抽屉按钮

**坑**：移动端在 `<nav>` 里插了 `<button class="sidebtn" id="btnSide">☰</button>`，它也被 `$$("nav button")` 选中，`onclick` 被 `setView(b.dataset.view)` 覆盖，点 ☰ 没反应。

**解法**：选择器改为 `$$("nav button[data-view]")`。

### 拖 textarea 手柄松在遮罩上会误关弹窗

**坑**：拖右下角调大小，`mousedown` 落在 textarea 上、`mouseup` 落在遮罩上，浏览器合成的 `click` 事件 `target` 就是遮罩 → 原来的 `m.onclick = e => { if(e.target===m) close }` 直接把弹窗关了。

**解法**：改成 mousedown / mouseup 配对判断，只有两者都落在遮罩上才关：

```js
$$(".mask").forEach(m => {
  let downOnMask = false;
  m.addEventListener("mousedown", e => { downOnMask = (e.target === m); });
  m.addEventListener("mouseup", e => {
    if(downOnMask && e.target === m){ m.classList.add("hidden"); editingApp = editingSch = null; }
    downOnMask = false;
  });
});
```

### 卡片点击与按钮冲突

卡片整体绑定 `onclick = openPreview` 后，里面的星标 / 编辑 / 加日程 / 删除按钮点一下会冒泡触发预览。解法：四个 `[data-*]` 的 handler 都加 `e.stopPropagation()`，同时预览绑定里再用 `if(e.target.closest("button, a, input, select, textarea")) return;` 兜底。

### 内嵌 JSON 转义

`<script type="application/json">` 里的数据如果含 `</`，会提前结束 script 标签。写入时必须把 `</` 转成 `<\/`，读取时再 `replace(/<\\\//g, "</")` 还原。

### 评分口径与公司聚合

**坑**：`renderStats()` 里「流程进行中」用阶段下标判断（`stageIdx(a) > 0 && stageIdx(a) < n-2`），全部处于「已投递」时显示 0，「谈薪」「offer发放」被误算成进行中。PDF 里的同名统计已改成按阶段名正则判断，看板统计卡尚未统一（遗留问题）。

**评分设计**：公司评分取该公司所有投递评分的**平均值**而不是求和 —— 否则投递条数多的公司会无脑排前面。薪资取最大值，因为同一家公司不同岗位薪资可能不同，取最高的更能反映这家公司的上限。

**坑（编辑弹窗）**：`drawScorePicker()` 必须在 `drawTagPicker()` 之后调用，且 `draftScores` 要和 `draftTags` 一样在 `openApp()` 里重置，否则会带着上次编辑的选中状态。

**坑（demo 移植）**：demo 的 `#appModal` 里备注字段后面直接跟 `<div class="foot">`，没有主文件那样的 `</div>` 闭合层级，所以锚点字符串必须去掉尾部 `</div>`，否则 `count=0` 匹配失败。

### 打印时清空

`afterprint` 事件会清空 `#printArea`。所以调试打印版式时不能走 `doExportPdf()` 后截图，要手动塞 `buildPrintHtml()` + `Emulation.setEmulatedMedia({media:'print'})`。

### `buildSnapshot()` 会把所有弹窗内容清空（潜伏已久的 bug）

`buildSnapshot()` 为了让快照里不带上弹窗的临时状态，会把所有 `.mask` 临时 `add("hidden")` 后存 `className`，再还原。原代码把还原信息存成两元组 `[el, className]`，还原时却按 `if(el.tagName === "DIV" && typeof v === "string") el.innerHTML = v;` 处理 —— `.mask` 本身也是 `DIV`，于是把 `"mask hidden"` 这个字符串当 HTML 写进了 `innerHTML`，**所有弹窗的内容被清成一行文字**。

因为快照很少恰好在弹窗刚渲染完时被调用（弹窗内容通常在打开时重绘），这个 bug 潜伏了很久，直到「外观弹窗里改滑杆 → `setLook()` 触发 `markDirty()` → 定时器里 `buildSnapshot()`」才暴露。

修复：三元素元组带类型标记。

```js
$$(".mask").forEach(m => { restore.push([m, m.className, "cls"]); m.classList.add("hidden"); });
const html = "<!DOCTYPE html>\n" + document.documentElement.outerHTML;
restore.forEach(([el, v, kind]) => {
  if(kind === "cls") el.className = v;
  else el.innerHTML = v;
});
```

### 演示版必须与正式版存储隔离

两个 HTML 都在 `file://` 下打开，**同源**（`file://` 被视为 opaque origin 但 Chromium 下 localStorage 按 `file://` 共享），所以默认会共用 `localStorage` 和 `IndexedDB`。后果很直接：正式版的恢复缓冲 `crp_recover_buffer` 里存着你真实的投递数据，打开演示版时会被读到并弹窗问「检测到上次未写回文件的改动，是否恢复？」—— **真实数据直接泄漏在演示版里**。

修复：演示版把三个存储标识全部换成 `crpd_` 前缀。

| 常量 | 正式版 | 演示版 |
| --- | --- | --- |
| `IDB_NAME` | `crp_fs` | `crpd_fs` |
| `RECOVER_KEY` | `crp_recover_buffer` | `crpd_recover_buffer` |
| `INTRO_KEY` | — | `crpd_intro_hidden` |

**验收方法**：打开演示版后断言 `Object.keys(localStorage)` 里没有任何 `crp_` 开头的键；再在演示版里改点东西、关掉重开，确认正式版数据不受影响。注意 `INTRO_KEY` 定义在 `init()` 的 IIFE 作用域里，全局取不到，只能通过实际操作（勾「下次不再自动显示」再关面板）验证写入的键名。

**署名与个人信息**：演示版不得出现真实姓名、学校、学院、模型名。当前署名只有一行「作者 Bilibili -Estrella-__」。发布前用 `grep -nE "<你的姓名>|<你的学校>" campus-recruit-planner-demo.html` 应无输出。

### 移动端打印

`window.print()` 在移动浏览器里经常静默失败或打印空白页。改用隐藏 iframe：把完整 HTML 文档（内联全部 `<style>` + 报表内容）写进 `position:fixed;width:0;height:0;opacity:0` 的 iframe，调 `fr.contentWindow.print()`。该调用返回 `Promise<boolean>`（用户取消时为 false），失败则降级下载一份内嵌 `window.print()` 的独立 HTML。

### 日历小字在移动端不渲染

原先 860 断点为了把日历压扁，写了 `.cal-cell .ev{font-size:0;height:5px}` + `.lbl,.evx{display:none}` —— 事件只剩一条纯色色条，日期信息全丢。改成保留文字、只压缩尺寸：事件 9.5px、`-webkit-line-clamp:2`、日期 10.5px、cell `min-height:64px`，只隐藏删除按钮 `.evx`（点事件仍能打开编辑弹窗）。

### PDF 统计口径

「流程进行中」不能按阶段下标判断（`i > 0 && i < n-2`），否则「谈薪」「offer发放」会被误算成进行中、全在「已投递」时显示 0。正确做法是按阶段名正则排除：

```js
!/(人才库|储备|拒|淘汰|未通过|offer|录用|入职|意向书)/i.test(stageName)
```

---

## 六、测试方法

项目用 **CDP（Chrome DevTools Protocol）驱动 headless Edge** 做自动化验证。

```bash
# 1. 启动 headless Edge
rm -rf /tmp/edgecdp
"C:/Program Files (x86)/Microsoft/Edge/Application/msedge.exe" \
  --headless=new --disable-gpu --remote-debugging-port=9335 \
  --user-data-dir=/tmp/edgecdp --no-first-run --disable-extensions about:blank &

# 2. 用 python + websocket 连
#    websocket.create_connection(url, timeout=40, suppress_origin=True)
#    必须 suppress_origin=True，否则 403 Forbidden
#    新建标签页用 requests.put("http://127.0.0.1:9335/json/new?about:blank")
```

**坑**：
- 同一 Edge 实例跑完一轮后第二轮会超时，需 `taskkill //F //IM msedge.exe` + 删 profile 重启。
- bash heredoc 写长 python 脚本会被截断，必须用 write 工具写文件。
- 设备模拟用 `Emulation.setDeviceMetricsOverride`；截图用 `Page.captureScreenshot`。

### 历史测试规模

| 批次 | 断言数 | 内容 |
| --- | --- | --- |
| v5 三项功能 | 76 | 实时保存 / 撤销重做 / 日历删除 |
| 覆盖保存 | 33 | 首次弹框、二次静默、权限过期、取消、选错文件、自动保存 |
| 演示版 | 57 | 数据完整性、开场说明、日期顺延、交互 |
| PDF + 识别 | 60 | 弹窗默认值、打印 HTML 结构、识别规则、导入不崩 |
| 移动端 | 59 + 49 | sticky、断点、抽屉、弹窗、无溢出、功能回归 |
| 静默保存 | 32 + 23 | 拖放连接、无 picker、写入内容、无句柄降级、跨会话 |
| 卡片预览 | 24 + 24 | 点击打开、长文本截断/完整、编辑回填、拖动不误关、删除撤销 |
| 公司页 + 评分 + PDF 备注 | 53 + 35 | 公司聚合/排名、评分计算、分值编辑、薪资排序、备注跨列整行、双开关 |
| PDF 评分列 | 19 + 9 | 评分/评分标签开关、动态表头与列宽、colspan 随开关变化（6↔7↔8↔9） |
| 移动端三修 + 外观 | 35 + 35 | 日历文字渲染、保存一键下载、PDF iframe 打印、外观 14 项（滑杆/颜色/阴影/撤销/恢复默认） |
| 演示版独立化 | 29 | 署名、个人信息零残留、存储隔离、24 家数据完好、正式版未受影响 |

---

## 七、版本演进

| 版本 | 主要变化 |
| --- | --- |
| v1 | 基础看板 / 时间线 / 日历，5 类状态徽章，JSON 导入导出 |
| v2 | 链接按钮（一键跳转），首次种子数据 |
| v3 | 侧栏筛选 + 标签体系，参照「成长星空」左坞设计 |
| v4 | 宝石镶嵌标签、拖拽池、流程标签 / 属性标签分离 |
| v5 | 实时保存、撤销重做、日历删除 |
| v5.1 | 覆盖保存（修复另存为根因） |
| v6 | 导出看板为 PDF、导入自动识别标签 |
| v6.1 | 移动端适配、顶栏 sticky 修复、抽屉式侧栏 |
| v6.2 | 卡片点击预览、长文本截断、备注框放大、拖动误关修复 |
| v6.3 | 保存完全静默（拖放连接文件句柄，永不弹选择器） |
| v7 | 公司页 + 评分系统（评分标签带分值、个人倾向、按评分/薪资排名）、PDF 备注独立成行 + 职位/公司双备注 |
| v7.1 | 移动端三修（日历文字保留、保存一键下载、PDF iframe 自动下载）、卡片外观自定义（宽度/内边距/圆角/边框/字号/背景/阴影） |
| demo | 演示版：虚构数据 + 开场说明 + 署名（已同步 v7.1 全部功能） |
| demo v2 | 演示版独立化：署名改为 Bilibili -Estrella-__、清除个人信息、localStorage/IndexedDB 命名空间隔离（`crpd_` 前缀） |

---

## 八、文件清单

（已隐藏）
```

---

## 九、GitHub 发布包

仓库根目录下的 `release/` 是可直接推上 GitHub 的独立发布包（1.2 MB），只含演示版、不含正式版与真实数据：

```
release/
├── README.md                          面向使用者的说明（带 8 张截图）
├── LICENSE                            MIT
├── .gitignore
├── campus-recruit-planner-demo.html   演示版（24 家虚构公司，开箱即用）
└── docs/
    ├── DEVELOPMENT.md                 本开发文档的副本
    └── *.jpg                          README 配图（1400px 宽，共 944 KB）
```

**配图来源**：`docs/*.jpg` 由 headless Edge 打开演示版后逐视图截图（`Page.captureScreenshot`，`deviceScaleFactor:2`），再用 Pillow 缩到 1400px 宽、JPEG q86 压缩。原 PNG 合计 5.5 MB，压完 944 KB。截图脚本一次跑完 8 张：看板 / 日历 / 公司页 / 卡片预览 / 编辑弹窗 / PDF 弹窗 / 标签管理 / 移动端。

**脱敏检查清单**（每次发布前跑）：

```bash
# 把尖括号里的内容换成你自己的姓名 / 学校 / 本机路径关键字
grep -rnE "<姓名>|<学校>|<学院>|<本机用户名>|D:\\\\" release/
```

应只在 `campus-recruit-planner-demo.html` 里命中两条无关内容（表单 placeholder 的「如：海尔集团」和 `TAG_RULES` 里的知名公司名关键词表），其余必须为空。

**README 写作要点**：面向使用者而非开发者，开头一句话说清「是什么 + 为什么用它」；功能部分每项配图；数据结构单独一节方便二次开发；踩坑只留 6 条最有价值的；最后给许可。

**发布包验证**：`_tr.py` 冒烟 14 项全过（页面加载、开场说明、24 家数据、署名、存储隔离、三视图、公司页、外观弹窗、无 JS 错误、4 项隐私关键词零命中）。

---

## 十、约定

1. **每次编写都创建快照** —— 改动前把当前文件复制到 `snapshots/`，命名 `{文件名}_{时间戳}_{阶段说明}.html`。
2. **每次编写都更新项目文档** —— 新功能、新踩坑、版本变化都记进本文档。
3. **不用 demo 做开发** —— 演示版只做功能同步，正式开发在 `campus-recruit-planner.html` 上做，改完再同步。
