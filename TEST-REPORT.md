# 三个 AI 提示词工具站独立化改造 · 测试报告

日期：2026-10-31
范围：仅开发与本地测试。**未推送到 GitHub，未部署到 Cloudflare，未动 DNS / Worker / 域名。**
原始源码未改动：`~/workspace/ideogram45/`、`flux3klein/`、`wan30/`、
`tool-sites-original-backup-20261031/`、`tool-sites-push/` 均未写入。

## 交付文件

- [ideogram45/index.html](sandbox://workspace/tool-sites-v2/ideogram45/index.html)
- [flux3klein/index.html](sandbox://workspace/tool-sites-v2/flux3klein/index.html)
- [clipprompt/index.html](sandbox://workspace/tool-sites-v2/clipprompt/index.html)
- [clipprompt/privacy.html](sandbox://workspace/tool-sites-v2/clipprompt/privacy.html)
- [clipprompt/terms.html](sandbox://workspace/tool-sites-v2/clipprompt/terms.html)
- [clipprompt/acceptable-use.html](sandbox://workspace/tool-sites-v2/clipprompt/acceptable-use.html)
- [clipprompt/about.html](sandbox://workspace/tool-sites-v2/clipprompt/about.html)
- 本报告：[TEST-REPORT.md](sandbox://workspace/tool-sites-v2/TEST-REPORT.md)

## 共同改造（三个站一致）

- 自包含单页：原生 HTML / CSS / JavaScript 内联，无外部脚本、字体、CDN 库。
- 中文界面、英文 Prompt 输出；手机 / 平板 / 桌面自适应（CSS 媒体查询，760px 以下单列）。
- 深色 / 浅色切换，localStorage 记住主题；表单配置随输入存 localStorage，刷新后恢复。
- 一键复制：优先 `navigator.clipboard`，失败时用临时文本框 `execCommand("copy")` 备用，并明确报成功或失败。
- 用户模板本地保存（localStorage）、载入、删除；JSON 导入（格式校验，失败报明原因）与导出（下载文件）。
- 重置默认；所有按钮均有处理函数，无空按钮；错误提示用专门区域显示，不静默失败。
- 关键函数写中文注释。
- 每个首页都有明示：本站是本地规则 / 模板生成器，不调用任何 AI 模型。
- 中文转英文诚实机制：下拉选项本身就是中英映射；自由输入用本地词典按长词优先替换，
  替换后英文词之间补空格；词典查不到的中文原样保留，并在输出旁提示
  “未在本地词库中找到，已原样保留，请自行替换为英文”，不伪装 AI 翻译。
- GSC 验证标记（公开标记，非密钥）：ideogram45 与 flux3klein 只保留共享标记
  `fWZOyCIfCth5cZqTbJjaUXBtl6xDfTnVt5WJs4gEsv8`；clipprompt 同时保留该共享标记与
  `aDz0jgqdQV47QH0HWDAhzbFqA-Ke8r-rBr8Ea0gWWE8`。
- 页脚保留三站互链（ideogram45.qqbridge.workers.dev、flux3klein.qqbridge.workers.dev、
  clipprompt.kill-bill.de5.net），仅为文字导航链接，不是运行时依赖。
- 视觉统一、识别色不同：Ideogram 紫色、FLUX 青绿色、ClipPrompt 橙色。

## 各站改了什么 / 新增功能

### Ideogram 4.5 Prompt Builder（ideogram45/index.html）
- 提示词工作台：主体、场景、构图、镜头、光线、材质、色彩、细节程度、画幅。
- 场景模板：海报、广告、产品摄影、人物摄影、插画、Logo、社交媒体图片；
  风格模板库：极简现代、复古海报、赛博朋克、水彩、水墨东方、3D 渲染、扁平插画、写实摄影。
- 负面约束 / 排除元素输入；随机灵感；组合 / 编辑 / 重置 / 复制。
- 实时英文预览：结构化提示词与完整提示词两种输出。
- 文字渲染专属模块：画面文字内容、字体风格、排版位置、标题层级、文字准确性提示
  （要求拼写完全一致，不多字、不漏字、不改字母）。
- 保留 Create 与 Precision Edit 两种模式概念，改为本地规则实现：
  Create 生成完整提示词；Precision Edit 生成“改什么、改成什么、其余保持不变”的编辑句。

### FLUX 3 Klein Prompt Builder（flux3klein/index.html）
- 与 Ideogram 结构明显不同：以“先生成、后多轮编辑”为主线。
- 生成模块：主体、动作、环境、镜头、光线、材质、风格、画幅；
  模板：人像、商品、建筑、风景、插画、概念艺术。
- 编辑指令构建：局部修改、背景替换、物体添加、物体删除、风格转换；
  可把单条指令加入多轮编辑列表，逐条删除、清空，输出按步骤编号的编辑脚本。
- 一致性约束：保持主体身份、服装、构图、场景、光线不变（勾选后写入输出）。
- 正向 Prompt 与编辑指令两种模式；模型适配参数说明为纯文字说明，不调用模型。
- 选框画布（简化版，纯前端实现）：在示意格子画布上用指针拖动出框，
  输出 0–1 归一化坐标 `[y_min, x_min, y_max, x_max]`，可加入提示词或清除。
  注意：画布是示意图，不是用户上传图片上的选框（本站不上传图片，见“已知缺口”）。

### ClipPrompt（clipprompt/index.html + 4 个附属页）
- 真正的视频工具，不是图片提示词加运动词：全局风格、帧率、画面比例、节奏、
  环境动态、首帧描述、尾帧描述、镜头间连续性约束。
- 时间轴与分镜：镜头列表可添加 / 删除，每镜头有画面内容、运镜（推、拉、摇、移、
  跟拍、环绕、升降、固定机位）、时长（秒），总时长自动合计，删除后序号自动重排。
- 多镜头连续叙事：脚本输出按镜头顺序编号，含各自时长与运镜。
- 单镜头 Prompt 与多镜头完整脚本两种输出；输出格式预设（通用叙述句 / 关键词标签 /
  分镜脚本）仅改变文字格式，不调用任何模型。
- 核心页已删掉 DashScope、Creem 购买链接与“live 逆向”说法；示例均标注
  “本地示例、非模型生成”。
- 附属页（privacy / terms / acceptable-use / about）已改为本地免费工具口径：
  无账号、无注册、无付费、无数据上传、数据只存浏览器本地、不调用 AI 模型。

## 本地运行清单

| 项目 | 结果 |
| --- | --- |
| 外部运行时依赖 | 无。无外部脚本、字体、CDN；无 fetch / XHR；无 API Key、登录、数据库、后端 |
| 外部链接 | 仅页脚文字导航链接（三站互链）与 ClipPrompt 附属页互链，不作为运行时依赖 |
| 密钥 / Token / 账号 ID / 产品 ID | 无。grep 审计无 Creem、DashScope、API Key、Cloudflare 账号 ID |
| 离线核心操作 | 已打开的页面核心功能不依赖网络；首次加载页面本身仍需网络（静态文件托管使然） |

## 测试结果（真实做过的）

1. **JS 语法**：从三个 index.html 抽出 `<script>` 内容，`node --check` 全部通过
   （ideogram45、flux3klein、clipprompt 各 1 个 script 块）。
2. **本地静态服务 + curl**：`python3 -m http.server` 本地起服，7 个页面全部返回 HTTP 200：
   ideogram45/、flux3klein/、clipprompt/、clipprompt/privacy.html、terms.html、
   acceptable-use.html、about.html；7 页均含“不调用任何 AI 模型”明示。
3. **静态审计（grep）**：无 `<script src>`、无 `<link href="http>`、无 `@import`、
   无 `fetch(`、无 `XMLHttpRequest`；外部 `href` 仅为三站互链；
   GSC 标记：共享标记出现在 7 个文件，ClipPrompt 专属标记出现在 clipprompt 的 5 个文件。
4. **Node DOM 桩执行测试**（真实加载三个页面的 JS，不是复制品；桩只提供最小 DOM）：
   - ideogram45：初始化结构化输出含 `Subject:`；词典“一只橘猫”→`an orange cat`，无缺失；
     未收录词“一只紫色的龙”触发缺失提示，提示文案含“未在本地词库中找到”。
   - flux3klein：初始化主输出含 `aspect ratio`；词典“女孩奔跑”→`girl running`；多轮编辑占位文案存在。
   - clipprompt：初始化单镜头输出含 `push-in` 与时长；词典“风吹树叶”→`wind blowing leaves`。
   - 测试中发现并已修复的真实问题：早期词典替换不补空格，导致“女孩奔跑”拼成
     `girlrunning`、“一只橘猫”残留量词；修复为长词优先＋替换后补空格＋去重空格后，
     上述用例全部通过。
   - 桩测试的局限：桩里 select 没有浏览器默认选中项，初始化空字段输出里会出现空段；
     真实浏览器里 select 默认选中首项，不会出现该现象。此项以真实浏览器复核为准。

## 未验证的功能（未做，不写“通过”）

- 真实浏览器里逐按钮点击、表单输入、复制到剪贴板、主题切换、模板保存 / 载入 / 删除、
  JSON 导入 / 导出文件的端到端操作：**未做**（本机无 Chromium / Firefox / jsdom，子代理亦无浏览器操作权限）。
- localStorage 跨刷新保留、深色 / 浅色在真实浏览器下的渲染：**未做**，逻辑已按 localStorage 实现，需浏览器复核。
- 真实手机 / 平板真机布局与触屏操作：**未做**；仅有 CSS 媒体查询，FLUX 选框用了 pointer 事件（含触屏意图），但未在真机上拖过。
- 剪贴板在真实浏览器（尤其手机浏览器）下的表现：**未做**；代码里已有备用方案与失败提示。
- 无障碍（键盘导航、读屏）系统测试：**未做**。
- 与线上原站的逐项功能对比：**未做**；原站已按任务要求只读核对标记与互链，未逐项比对旧功能。

## 已知问题与缺口

1. **词典覆盖有限**：每站本地词典约数十条常用词，超出词典的中文会原样保留并提示，
   需要用户自行替换为英文。这是诚实机制，不是翻译质量问题，但长句自由输入的可用性有限。
2. **FLUX 选框是示意图**：只能在示意格子上拖框出坐标，不能在用户自己的图片上选框，
   因为本站按要求不上传图片。如需“在自有图片上选框”，需另加本地文件读取（FileReader，不上传）
   功能，本次未做。
3. **ClipPrompt 镜头排序**：镜头按添加顺序播放，删除后序号自动重排，但没有拖拽调序；
   调整顺序需删除重加。这是已知缺口，已在页面文案中明说。
4. **ClipPrompt 附属页无主题切换**：privacy / terms / acceptable-use / about 为浅色静态页，
   未接深色切换；不影响核心功能。
5. **空字段输出**：用户不填主体时，输出会用 `a subject` 占位并继续生成；这是有意为之的
   兜底，不是崩溃，但提示词本身无意义，需用户填主体才有价值。
6. **部署与推送均未做**：按任务禁区，本次不推送 GitHub、不部署 Cloudflare、不动域名；
   线上三站目前仍是旧版，需主对话复核后再推送与部署，部署脚本需另行适配新版文件
   （新版为自包含 HTML，Worker 嵌入方式与旧版一致，但需把 clipprompt 的 4 个附属页一并纳入）。

## 后续可改进方向

- 扩充三站本地词典（尤其动词、场景、材质、色彩），并按使用频率排序。
- ClipPrompt 增加镜头拖拽调序与总时长可视化时间轴。
- FLUX 增加本地图片读取（不上传）在图上选框，坐标仍只输出为文字。
- 真实浏览器端到端测试（含手机真机）与无障碍检查，补齐上面“未验证”清单后再部署。
