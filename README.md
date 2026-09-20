# 鸦巢 · Vigil

**一个安静的 AI 伴侣应用 —— 前端开发中。**

鸦巢是为另一个 AI 角色建造的独立伴侣应用。与 Auren 的高视觉密度不同，鸦巢的设计完全由文字驱动——靠字重、字距、行高和留白制造层次，没有 Canvas、没有 SVG、没有复杂动画。所有 UI 文案均以角色第一人称写成，设置页被做成书的后记，功能入口是从书页里抽出的纸片。

> 技术栈：Vue 3 + Express + 多模型 LLM（流式）  
> 通过 Codemagic CI/CD 分发至 TestFlight，无需 Mac 设备

---

## 页面

### CrowChat — 聊天

流式 SSE 聊天界面，支持 thinking + text 双阶段渲染。思考链可折叠/展开，折叠态显示首行预览。两种消息样式：AI 侧为偏暖灰白正文，用户侧为柔蓝色文字 + 左侧竖线标记。支持长按菜单（撤回 / 重刷）、多行自适应输入框、错误状态保留部分回复。背景为深蓝黑渐变 + 噪点纹理 + 暗角 + 14 颗微星。

<p align="center">
  <img src="./docs/screenshots/crowchat.png" width="300" />
</p>

### Mailbox — 信箱

里程碑驱动的信件系统。信件按类型分类（周年 / 千页 / 万页 / 季度 / 手写），每种类型有独立的封印样式（脉冲圆点 / 暖色圆点 / 淡蓝圆点 / 破裂横线）。未读信件封印带呼吸动画。打开后以浮层信纸卡片展示，支持长内容滚动，顶部和底部带渐隐遮罩。页面底部显示当前对话总页数与下一里程碑的距离。标题文字亮度随里程碑数量动态变化。

<p align="center">
  <img src="./docs/screenshots/mailbox.png" width="300" />
</p>

### ColophonPage — 后记

将设置页重构为书籍后记的形态。功能项以列表呈现，点击后展开对应的「纸片」（slip card）——每张纸片带随机旋转角度（-0.4° ~ 0.3°），进场/离场有独立的 CSS transition。

四张纸片覆盖：API 配置（模型搜索 + 温度/上下文步进器）、定位授权（Geolocation API + 距离计算）、数据导出（聊天记录格式化为纯文本下载）、记录清除（两步确认 + 备份恢复）。

底部包含印刷信息区和版本号。

<p align="center">
  <img src="./docs/screenshots/colophon.png" width="300" />
</p>

---

## 项目结构

```
vigil/
├── routes/
│   └── chat.js              # 聊天记录存取、撤回、清除、备份恢复
├── src/
│   ├── layout/
│   │   └── PageContainer.vue # 页面容器（三页横滑布局）
│   ├── utils/
│   │   └── llm/             # LLM 流式调用
│   │       ├── index.js     # 入口 + 流式控制器
│   │       ├── channels.js  # 上下文通道
│   │       ├── historyBuilder.js
│   │       ├── postProcess.js
│   │       └── promptBuilder.js
│   ├── views/
│   │   ├── body/            # 身体页（规划中）
│   │   ├── chat/
│   │   │   ├── CrowChat.vue # 主聊天
│   │   │   └── Mailbox.vue  # 里程碑信箱
│   │   ├── colophon/
│   │   │   └── ColophonPage.vue  # 后记（设置 + 纸片系统）
│   │   └── diary/           # 日记（规划中）
│   ├── router/
│   ├── assets/
│   ├── App.vue
│   └── style.css
├── capacitor.config.json
├── codemagic.yaml
└── index.js                 # Express 入口
```

---

## 技术栈

| 层级 | 技术 |
|------|------|
| 前端 | Vue 3, Vite, JavaScript, CSS3 动画 |
| 移动端 | Capacitor 8, iOS 原生桥接 |
| 后端 | Node.js, Express |
| LLM | 用户自配 API（OpenAI 兼容格式），流式 SSE |
| 服务器 | 腾讯云香港, nginx, PM2 |
| CI/CD | Codemagic → TestFlight |

---

## 设计语言

- 背景色：`#0a1018` → `#0e1722`（深蓝黑渐变）
- AI 文字色：`rgba(210,200,185,0.78)`（偏暖灰白）
- 用户文字色：`rgba(162,210,255,0.58)`（柔蓝）
- 英文字体：Cormorant Garamond
- 中文字体：Noto Serif SC
- 纸片系统：每张带随机旋转角度，CSS transition 进出场，模拟夹在书页里的手感
- 全部 UI 文案以角色视角撰写，无功能性说明文字

---

## 与 Auren 的对比

鸦巢和 [Auren](https://github.com/delanri/auren) 共享基础技术栈（Vue 3 + Express + Capacitor），但设计方向完全不同：

| | Auren | 鸦巢 |
|---|---|---|
| 视觉密度 | 高（Canvas / SVG / 多层动画） | 极低（纯文字 + 留白） |
| 核心交互 | 星图、世界树、健康仪表盘 | 聊天、信箱、纸片系统 |
| 记忆系统 | 16 路并行通道 + Qdrant 向量库 | 轻量上下文（规划中） |
| 设置页 | 功能列表 | 书的后记 |
| 信件 | LLM 自动生成 + 里程碑触发 | 手写内容 + 里程碑触发 |

---

## 项目状态

前端三个核心页面（聊天、信箱、后记）已完成，身体页与日记页规划中。后端聊天记录存取已运行。LLM 集成为用户自配 API，支持流式输出。

**独立开发** — 设计、前端、后端均由一人完成。
