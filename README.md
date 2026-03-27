# 个人作品集网站

> 一个 vibe coding 项目，由claude code AI 辅助设计与开发的全栈个人作品集，集成 AI 数字人对话系统  
> **线上地址：** [clubyhy.top](https://clubyhy.top)  ps.因为vercel服务器挂载在海外，可能需要挂vpn

---

## 项目概览

本项目是一个面向 AI 产品经理岗位的个人作品集网站，黑橙色视觉风格（主色 `#FF4400`），借鉴影视飓风的视觉冲击力设计语言。核心亮点在于集成了基于 **DeepSeek API** 的 AI 数字人对话模块，访客可以直接与 AI 版本的博主进行自然语言交流，了解其经历、技术方向与合作意向。

---

## 技术栈

| 层级 | 技术方案 |
|------|----------|
| 前端 | 纯 HTML / CSS / JavaScript（无框架） |
| 字体 | Bebas Neue（展示字体）· Noto Sans SC · Space Mono |
| AI 对话 | DeepSeek API（`deepseek-chat` 模型）|
| 部署 | Vercel（自动 CI/CD）+ 阿里云域名解析 |
| 域名 | `clubyhy.top`（阿里云，A 记录指向 Vercel） |

---

## 页面结构

```
index.html
│
├── Hero          — 全屏人像背景 + Ken Burns 动画 + 逐词淡入标题
├── Ticker        — 跑马灯（产品运营 · AI产品经理 · 内容策划…）
├── About         — 个人介绍 + 数据统计卡片（6+年/5+项目/70%提效/1.2KW粉丝）
├── Works         — 代表项目（2×2 等大图片网格，16:9 比例，悬停橙色顶线动画）
├── Experience    — 三栏布局（标题/时间线/个人照片），时间线带彩色 Badge
├── Skills        — 三列技能条，滚动触发动画
├── Avatar        — AI 数字人对话区（ChatGPT 风格聊天界面）
├── Contact       — LET'S / MAKE IT / POSSIBLE 逐词弹出动画（POSSIBLE 橙色发光）
└── Footer        — 版权信息
```

---

## 核心功能说明

### 1. AI 数字人对话（Avatar）

核心功能模块，访客在网站内直接与 AI 版本的「Harold」对话。

**交互流程：**
1. 初始状态：居中展示头像占位 + 快捷提问卡片（自我介绍 / 华为项目 / 求职方向 / 联系方式）
2. 发送第一条消息后：界面切换为 ChatGPT 风格聊天视图
3. AI 基于内置简历 System Prompt 以第一人称回答
4. 消息历史自动滚动，超长出现滚动条，输入框固定在底部

**技术实现：**
```javascript
// 调用 DeepSeek API
const res = await fetch('https://api.deepseek.com/v1/chat/completions', {
  method: 'POST',
  headers: { 'Authorization': 'Bearer YOUR_KEY' },
  body: JSON.stringify({
    model: 'deepseek-chat',
    max_tokens: 200,
    messages: [{ role: 'system', content: AV_PROMPT }, ...history]
  })
});
```

**System Prompt 设计原则：**
- 角色定义：第一人称、口语化、控制在 100 字以内（适配后续数字人朗读）
- 知识结构：华为经历 → 阅文实习 → 学术背景 → 技能方向 → 求职目标
- 行为规则：不知道的问题不编造，薪资问题引导加微信，拒绝无关问题

---

### 2. 成长轨迹（Experience）

**布局：** `220px（标题粘性）/ 1fr（时间线）/ 1fr（个人照片）`

**特效：**
- 时间线竖线从橙色渐变到透明
- 每个节点悬停时橙色发光 + 右移 4px
- 照片区左侧双层渐变遮罩，与时间线内容自然融合：
  ```css
  .exp-photo-wrap::before {
    background: linear-gradient(to right,
      var(--bg) 0%, rgba(20,20,20,.85) 15%,
      transparent 80%
    );
  }
  ```

---

### 3. 联系页动效（Contact）

三行文字 `LET'S / MAKE IT / POSSIBLE` 逐词弹出：
- `LET'S`：0ms 延迟，标准上弹
- `MAKE / IT`：180ms / 340ms，带轻微 skewY 切变
- `POSSIBLE`：540ms，弹性过冲曲线 `cubic-bezier(0.34, 1.56, 0.64, 1)` + 橙色发光

```css
.word-possible.in .word-inner {
  text-shadow: 0 0 40px rgba(255,68,0,.35), 0 0 80px rgba(255,68,0,.15);
}
```

---

### 4. 其他细节

| 功能 | 实现方式 |
|------|----------|
| 自定义光标 | 橙色点 + 跟踪圆环（JS requestAnimationFrame 惰性跟随）|
| 浮动回到顶部 | SVG 进度环，滚动超过 400px 显示 |
| 滚动触发动画 | IntersectionObserver，threshold 0.07 |
| 技能条动画 | `--level` CSS 变量 + 进入视口后 transition |
| 跑马灯 | CSS `animation: ticker 20s linear infinite` |

---

## 项目图片规格

| 位置 | 推荐比例 | 推荐尺寸 | 说明 |
|------|----------|----------|------|
| Hero 背景 | 任意 | 1920×1080+ | 正面人像，留上方空间 |
| 代表项目卡片 | **16:9** | 1280×720+ | 放入 `<img src="">` |
| 成长轨迹右侧 | 任意 | 800×1200+ | object-fit:cover 自动裁切 |
| 个人头像（竖版）| **4:5** | 800×1000+ | 左侧被渐变遮住约 30% |

---

## 本地使用

```bash
# 克隆仓库
git clone https://github.com/your-username/personal-website.git

# 直接用浏览器打开（无需构建）
open index.html
```

**填入 DeepSeek API Key：**

打开 `index.html`，找到：
```javascript
const AV_KEY = 'YOUR_DEEPSEEK_KEY_HERE';
```
替换为你的 Key，保存即可。

---

## 部署流程

### Vercel 部署（推荐）

```
1. 将代码推送到 GitHub 仓库
2. 登录 vercel.com → New Project → 选择仓库
3. 无需配置，直接 Deploy
4. 之后每次 git push 自动更新
```

### 自定义域名绑定

```
Vercel：Settings → Domains → 添加 clubyhy.top

阿里云 DNS 解析：
  类型：A 记录
  主机：@
  记录值：216.198.79.1

  类型：CNAME
  主机：www
  记录值：cname.vercel-dns.com
```

---

## 开发迭代记录

| 阶段 | 主要内容 |
|------|----------|
| **v1 基础结构** | Hero + About + 项目卡片 + 技能矩阵 + Contact 基础版 |
| **v2 Experience 升级** | 三栏布局，右侧图片渐变融合，时间线 Badge |
| **v3 Contact 动效** | 逐词弹出，POSSIBLE 橙色发光，弹性过冲曲线 |
| **v4 项目区改版** | 去掉视频播放，改为 2×2 等大图片网格，16:9 比例 |
| **v5 Avatar 集成** | 接入 DeepSeek API，ChatGPT 风格聊天界面 |
| **v6 Avatar 交互优化** | 快捷提问弹层，输入框固定底部，打字动画，历史记录 |
| **v7 部署上线** | Vercel 部署 + clubyhy.top 域名绑定 |

---

## 文件结构

```
personal-website/
├── index.html          # 主文件（全部代码集中在单文件）
├── self_vertical.png   # 成长轨迹右侧个人照片
├── project1.jpg        # 代表项目图片（自行添加）#后续可能采用seedance2.0重新编排，改为视频介绍
├── project2.jpg
├── project3.jpg
├── project4.jpg
└── README.md
```

---


---

## License

MIT License · 欢迎 fork 修改，保留原作者信息即可
