# HTML 模板

所有 HTML 文件: 单文件自包含 `.html`, 内联 CSS, 无外部依赖。

## 设计方向

深色高级 UI, 分层深度, 玻璃态, 微动画。

**禁止**: 蓝紫渐变, 通用 AI 配色。选择独特的强调色。

## CSS 变量

```css
:root {
  /* 背景 */
  --bg: #08080c;
  --surface: rgba(255,255,255,0.03);
  --surface-hover: rgba(255,255,255,0.08);
  --border: rgba(255,255,255,0.06);

  /* 文字 */
  --text: #ededef;
  --text-secondary: rgba(255,255,255,0.55);
  --text-tertiary: rgba(255,255,255,0.3);

  /* 强调色 — 选择协调的配色 */
  --accent-1: #10b981;
  --accent-2: #06b6d4;
  --accent-gradient: linear-gradient(135deg, var(--accent-1), var(--accent-2));

  /* 状态 */
  --green: #34d399;
  --amber: #fbbf24;
  --red: #f87171;

  /* 字体 */
  --font-mono: 'SF Mono', 'Fira Code', 'JetBrains Mono', monospace;
  --font-sans: -apple-system, 'Inter', system-ui, sans-serif;

  /* 圆角 */
  --radius: 12px;
}
```

### 强调色配色方案

| 主题氛围 | accent-1 | accent-2 |
|----------|----------|----------|
| Python / 技术 | `#3776ab` | `#ffd43b` |
| AI / 智能 | `#10b981` | `#06b6d4` |
| Agent / 能量 | `#f43f5e` | `#fb923c` |

## 模板: 学习路线图 (`roadmap.html`)

```
┌──────────────────────────────────────────┐
│ 标题: 渐变标题 + 元数据标签              │
│          + 圆形 SVG 进度环               │
│                                          │
│ 时间线 (垂直, 左侧连接线)                │
│  ├── 节点 (已掌握)  [✓ 绿色]             │
│  ├── 节点 (进行中) [脉动]                │
│  └── 节点 (未开始) [暗淡 0.45]           │
│                                          │
│ 底部: 总体进度条                         │
└──────────────────────────────────────────┘
```

**完整模板**:

```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>学习路线图 - {topic}</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    
    :root {
      --bg: #08080c;
      --surface: rgba(255,255,255,0.03);
      --surface-hover: rgba(255,255,255,0.08);
      --border: rgba(255,255,255,0.06);
      --text: #ededef;
      --text-secondary: rgba(255,255,255,0.55);
      --text-tertiary: rgba(255,255,255,0.3);
      --accent-1: #10b981;
      --accent-2: #06b6d4;
      --accent-gradient: linear-gradient(135deg, var(--accent-1), var(--accent-2));
      --green: #34d399;
      --amber: #fbbf24;
      --radius: 12px;
      --font-sans: -apple-system, 'Inter', system-ui, sans-serif;
    }
    
    body {
      font-family: var(--font-sans);
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      padding: 40px 20px;
    }
    
    .container {
      max-width: 720px;
      margin: 0 auto;
    }
    
    .header {
      text-align: center;
      margin-bottom: 48px;
    }
    
    .title {
      font-size: 2rem;
      font-weight: 700;
      background: var(--accent-gradient);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin-bottom: 16px;
    }
    
    .progress-ring {
      width: 120px;
      height: 120px;
      margin: 24px auto;
    }
    
    .progress-ring svg {
      transform: rotate(-90deg);
    }
    
    .progress-ring circle {
      fill: none;
      stroke-width: 8;
    }
    
    .progress-ring .bg {
      stroke: var(--surface);
    }
    
    .progress-ring .progress {
      stroke: url(#gradient);
      stroke-linecap: round;
      stroke-dasharray: 314;
      stroke-dashoffset: calc(314 - (314 * var(--progress, 0)) / 100);
      transition: stroke-dashoffset 0.5s ease;
    }
    
    .progress-text {
      font-size: 1.5rem;
      font-weight: 600;
      fill: var(--text);
    }
    
    .timeline {
      position: relative;
      padding-left: 32px;
    }
    
    .timeline::before {
      content: '';
      position: absolute;
      left: 8px;
      top: 0;
      bottom: 0;
      width: 2px;
      background: linear-gradient(to bottom, var(--accent-1) var(--mastered-pct, 0%), var(--border) var(--mastered-pct, 0%));
    }
    
    .node {
      position: relative;
      padding: 16px 20px;
      margin-bottom: 16px;
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      transition: all 0.2s ease;
    }
    
    .node:hover {
      background: var(--surface-hover);
    }
    
    .node::before {
      content: '';
      position: absolute;
      left: -28px;
      top: 50%;
      transform: translateY(-50%);
      width: 12px;
      height: 12px;
      border-radius: 50%;
      background: var(--border);
    }
    
    .node.mastered::before {
      background: var(--green);
      box-shadow: 0 0 12px var(--green);
    }
    
    .node.in-progress::before {
      background: var(--accent-1);
      animation: pulse 2s infinite;
    }
    
    @keyframes pulse {
      0%, 100% { box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.4); }
      50% { box-shadow: 0 0 0 8px rgba(16, 185, 129, 0); }
    }
    
    .node-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 8px;
    }
    
    .node-title {
      font-weight: 500;
    }
    
    .node-status {
      font-size: 0.75rem;
      padding: 4px 10px;
      border-radius: 999px;
      background: var(--surface-hover);
    }
    
    .node.mastered .node-status {
      background: rgba(52, 211, 153, 0.2);
      color: var(--green);
    }
    
    .node.in-progress .node-status {
      background: rgba(16, 185, 129, 0.2);
      color: var(--accent-1);
    }
    
    .node-score {
      font-size: 0.875rem;
      color: var(--text-secondary);
    }
    
    .footer {
      margin-top: 40px;
      padding-top: 24px;
      border-top: 1px solid var(--border);
    }
    
    .progress-bar {
      height: 8px;
      background: var(--surface);
      border-radius: 4px;
      overflow: hidden;
    }
    
    .progress-bar-fill {
      height: 100%;
      background: var(--accent-gradient);
      border-radius: 4px;
      transition: width 0.5s ease;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1 class="title">{topic}</h1>
      <div class="progress-ring">
        <svg viewBox="0 0 120 120">
          <defs>
            <linearGradient id="gradient" x1="0%" y1="0%" x2="100%" y2="100%">
              <stop offset="0%" stop-color="var(--accent-1)" />
              <stop offset="100%" stop-color="var(--accent-2)" />
            </linearGradient>
          </defs>
          <circle class="bg" cx="60" cy="60" r="50" />
          <circle class="progress" cx="60" cy="60" r="50" style="--progress: {overall-progress}" />
          <text class="progress-text" x="60" y="65" text-anchor="middle">{overall-progress}%</text>
        </svg>
      </div>
    </div>
    
    <div class="timeline" style="--mastered-pct: {mastered-percentage}">
      <!-- 节点列表 -->
      <div class="node mastered">
        <div class="node-header">
          <span class="node-title">1. 同步 vs 异步</span>
          <span class="node-status">已掌握</span>
        </div>
        <div class="node-score">掌握度: 90%</div>
      </div>
      
      <div class="node in-progress">
        <div class="node-header">
          <span class="node-title">2. 事件循环</span>
          <span class="node-status">学习中</span>
        </div>
        <div class="node-score">掌握度: 60%</div>
      </div>
      
      <div class="node">
        <div class="node-header">
          <span class="node-title">3. async/await</span>
          <span class="node-status">待学习</span>
        </div>
        <div class="node-score">掌握度: -</div>
      </div>
    </div>
    
    <div class="footer">
      <div class="progress-bar">
        <div class="progress-bar-fill" style="width: {overall-progress}%"></div>
      </div>
    </div>
  </div>
</body>
</html>
```

## 模板: 会话总结 (`summary.html`)

```
┌──────────────────────────────────────────┐
│ 标题: 渐变标题 + 完成徽章                │
│                                          │
│ 统计网格 (2x2): 掌握数、问题数、         │
│   掌握率、学习时长                       │
│                                          │
│ 概念分解: 水平条形图                     │
│                                          │
│ 关键洞察 + 下一步建议                    │
└──────────────────────────────────────────┘
```

**完整模板**:

```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>学习总结 - {topic}</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    
    :root {
      --bg: #08080c;
      --surface: rgba(255,255,255,0.03);
      --surface-hover: rgba(255,255,255,0.08);
      --border: rgba(255,255,255,0.06);
      --text: #ededef;
      --text-secondary: rgba(255,255,255,0.55);
      --accent-1: #10b981;
      --accent-2: #06b6d4;
      --accent-gradient: linear-gradient(135deg, var(--accent-1), var(--accent-2));
      --green: #34d399;
      --radius: 12px;
      --font-sans: -apple-system, 'Inter', system-ui, sans-serif;
    }
    
    body {
      font-family: var(--font-sans);
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      padding: 40px 20px;
    }
    
    .container {
      max-width: 720px;
      margin: 0 auto;
    }
    
    .header {
      text-align: center;
      margin-bottom: 48px;
    }
    
    .title {
      font-size: 2rem;
      font-weight: 700;
      background: var(--accent-gradient);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin-bottom: 16px;
    }
    
    .badge {
      display: inline-block;
      padding: 8px 16px;
      background: rgba(16, 185, 129, 0.2);
      color: var(--green);
      border-radius: 999px;
      font-size: 0.875rem;
    }
    
    .stats-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 16px;
      margin-bottom: 32px;
    }
    
    .stat-card {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 20px;
      text-align: center;
    }
    
    .stat-value {
      font-size: 2rem;
      font-weight: 700;
      background: var(--accent-gradient);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
    
    .stat-label {
      font-size: 0.875rem;
      color: var(--text-secondary);
      margin-top: 4px;
    }
    
    .section {
      margin-bottom: 32px;
    }
    
    .section-title {
      font-size: 1.125rem;
      font-weight: 600;
      margin-bottom: 16px;
    }
    
    .concept-bar {
      display: flex;
      align-items: center;
      margin-bottom: 12px;
    }
    
    .concept-name {
      flex: 0 0 160px;
      font-size: 0.875rem;
      color: var(--text-secondary);
    }
    
    .concept-progress {
      flex: 1;
      height: 8px;
      background: var(--surface);
      border-radius: 4px;
      overflow: hidden;
      margin: 0 12px;
    }
    
    .concept-progress-fill {
      height: 100%;
      background: var(--accent-gradient);
      border-radius: 4px;
    }
    
    .concept-score {
      flex: 0 0 48px;
      text-align: right;
      font-size: 0.875rem;
      font-weight: 500;
    }
    
    .insights {
      background: var(--surface);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 20px;
    }
    
    .insight-item {
      display: flex;
      gap: 12px;
      margin-bottom: 12px;
    }
    
    .insight-item:last-child {
      margin-bottom: 0;
    }
    
    .insight-icon {
      flex-shrink: 0;
      width: 24px;
      height: 24px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 0.75rem;
    }
    
    .insight-icon.strength {
      background: rgba(16, 185, 129, 0.2);
      color: var(--green);
    }
    
    .insight-icon.next {
      background: rgba(6, 182, 212, 0.2);
      color: var(--accent-2);
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1 class="title">{topic} 学习总结</h1>
      <span class="badge">已完成 {mastered-count}/{total-count} 概念</span>
    </div>
    
    <div class="stats-grid">
      <div class="stat-card">
        <div class="stat-value">{mastered-count}</div>
        <div class="stat-label">已掌握概念</div>
      </div>
      <div class="stat-card">
        <div class="stat-value">{questions-answered}</div>
        <div class="stat-label">回答问题数</div>
      </div>
      <div class="stat-card">
        <div class="stat-value">{mastery-rate}%</div>
        <div class="stat-label">掌握率</div>
      </div>
      <div class="stat-card">
        <div class="stat-value">{duration}</div>
        <div class="stat-label">学习时长</div>
      </div>
    </div>
    
    <div class="section">
      <h2 class="section-title">概念掌握度</h2>
      
      <div class="concept-bar">
        <span class="concept-name">同步 vs 异步</span>
        <div class="concept-progress">
          <div class="concept-progress-fill" style="width: 90%"></div>
        </div>
        <span class="concept-score">90%</span>
      </div>
      
      <div class="concept-bar">
        <span class="concept-name">事件循环</span>
        <div class="concept-progress">
          <div class="concept-progress-fill" style="width: 85%"></div>
        </div>
        <span class="concept-score">85%</span>
      </div>
      
      <div class="concept-bar">
        <span class="concept-name">async/await</span>
        <div class="concept-progress">
          <div class="concept-progress-fill" style="width: 60%"></div>
        </div>
        <span class="concept-score">60%</span>
      </div>
    </div>
    
    <div class="section">
      <h2 class="section-title">学习洞察</h2>
      <div class="insights">
        <div class="insight-item">
          <span class="insight-icon strength">✓</span>
          <span>对事件循环机制理解深入，能清晰解释任务队列</span>
        </div>
        <div class="insight-item">
          <span class="insight-icon next">→</span>
          <span>下一步: 深入学习 asyncio.gather 和并发控制</span>
        </div>
      </div>
    </div>
  </div>
</body>
</html>
```

## 使用说明

1. 将 `{placeholder}` 替换为实际数据
2. 根据主题调整 `--accent-1` 和 `--accent-2` 颜色
3. 节点数量根据实际概念数量调整
4. 保存后用浏览器打开查看效果
