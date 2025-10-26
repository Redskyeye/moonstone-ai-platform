---
name: new
status: backlog
created: 2025-10-26T15:45:30Z
updated: 2025-10-26T16:07:52Z
progress: 0%
prd: .claude/prds/new.md
github: [Will be updated when synced to GitHub]
---

# Epic: 月光石AI赋能职业规划师平台

## 概述

打造**功能完善、界面高端、使用便捷**的AI赋能职业规划师平台。基于React 18+和TypeScript，通过已部署的N8N工作流集成AI服务，专注于前端模块开发和极致用户体验。平台提供简历优化、面试辅导、效率工具三大核心功能，采用现代化设计语言和流畅交互，为职业规划师提供专业高效的数字化工具。

### 核心价值
- ✨ **功能完善**: 覆盖职业规划全流程的AI工具套件
- 🎨 **界面高端**: 现代化设计语言，对标行业顶尖产品
- ⚡ **使用便捷**: 一键操作，智能识别，实时反馈

### N8N集成说明
- N8N工作流已部署完成，包含简历解析和优化两个核心Webhook
- 我们专注于前端模块开发，通过API与N8N进行数据交互
- Webhook端点已确定：解析端点和优化端点可直接调用

## 架构决策

### 技术栈选择

#### 前端技术栈
- **核心框架**: React 18 + TypeScript + Vite
  - 强类型支持，并发特性，闪电般的构建速度

- **UI框架**: Tailwind CSS + Headless UI
  - 快速构建现代化界面，原子化CSS设计

- **动画库**: Framer Motion
  - 流畅的页面过渡和微交互动画

- **组件库**: Radix UI + 自研高端组件
  - 无障碍访问支持，完全可定制

- **图标系统**: Lucide React
  - 现代化图标库，300+精美图标

- **图表组件**: Recharts
  - 优雅的数据可视化方案

- **通知系统**: React Hot Toast
  - 轻量级、美观的消息提示

- **主题系统**: next-themes
  - 完美的暗色/亮色模式切换

- **状态管理**: Zustand + React Query
  - 轻量级状态管理 + 服务端状态同步

#### 后端技术栈
- **API服务**: 简化的Node.js + Express（仅必要API）
  - 用户认证、支付回调、文件上传

- **AI集成**: N8N Webhook对接
  - 已部署的N8N工作流，通过HTTP调用

### 设计原则
- **组件优先**: 一切皆组件，高度可复用
- **移动优先**: 响应式设计，完美适配所有设备
- **渐进增强**: 核心功能优先，优雅降级
- **微交互**: 每个操作都有反馈和动画
- **无障碍**: WCAG 2.1 AA标准，键盘导航支持

### 设计模式
- **组合模式**: 灵活的组件组合
- **观察者模式**: 实时状态更新
- **策略模式**: 多种AI服务切换
- **单例模式**: 全局状态管理

## 技术方案

### 页面架构设计

#### 完整页面结构
```
src/
├── app/                 # App根目录
│   ├── layout.tsx       # 全局布局
│   ├── page.tsx         # 首页
│   ├── globals.css      # 全局样式
│   └── loading.tsx      # 全局加载
├── components/          # 组件库
│   ├── ui/             # 基础UI组件
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Card.tsx
│   │   └── index.ts    # 统一导出
│   ├── forms/          # 表单组件
│   │   ├── ResumeForm.tsx
│   │   ├── InterviewForm.tsx
│   │   └── PaymentForm.tsx
│   ├── layout/         # 布局组件
│   │   ├── Header.tsx
│   │   ├── Sidebar.tsx
│   │   ├── Footer.tsx
│   │   └── Navigation.tsx
│   └── features/       # 功能组件
│       ├── FileUpload/
│       ├── AIOptimizer/
│       └── ProgressTracker/
├── pages/              # 页面路由
│   ├── Landing/        # 着陆页
│   │   ├── page.tsx
│   │   └── components/
│   ├── Dashboard/      # 工作台
│   │   ├── page.tsx
│   │   └── components/
│   ├── ResumeOptimizer/ # 简历优化
│   │   ├── page.tsx
│   │   ├── upload.tsx  # 上传步骤
│   │   ├── config.tsx  # 配置步骤
│   │   ├── result.tsx  # 结果页面
│   │   └── components/
│   ├── InterviewCoach/  # 面试辅导
│   │   ├── page.tsx
│   │   └── components/
│   ├── Tools/          # 效率工具
│   │   ├── page.tsx
│   │   └── components/
│   ├── Subscription/    # 订阅管理
│   ├── Profile/        # 个人中心
│   └── Help/           # 帮助中心
├── hooks/              # 自定义Hooks
│   ├── useTheme.ts
│   ├── useResume.ts
│   ├── useUpload.ts
│   └── useN8N.ts      # N8N集成Hook
├── services/           # API服务
│   ├── api.ts         # API配置
│   ├── auth.ts        # 认证服务
│   ├── n8n.ts         # N8N Webhook服务
│   └── payment.ts     # 支付服务
├── stores/            # Zustand状态管理
│   ├── authStore.ts
│   ├── resumeStore.ts
│   └── uiStore.ts
├── lib/               # 工具库
│   ├── utils.ts
│   ├── validations.ts
│   └── constants.ts
└── styles/            # 样式文件
    └── components.css
```

#### N8N集成方案
```typescript
// N8N Webhook服务封装
export class N8NService {
  private analyzeEndpoint = 'https://lt.ngrok.io/webhook/resume-see'
  private optimizeEndpoint = 'https://lt.ngrok.io/webhook/520296f2-1ce9-46a9-911a-c54dae85ba00'

  // 简历解析
  async analyzeResume(file: File, communicationFiles?: File[]) {
    const formData = new FormData()
    formData.append('file', file)
    formData.append('action', 'analyzeResume')

    if (communicationFiles) {
      communicationFiles.forEach((file, index) => {
        formData.append(`interviewFiles[${index}]`, file)
      })
    }

    return fetch(this.analyzeEndpoint, {
      method: 'POST',
      body: formData
    })
  }

  // 简历优化
  async optimizeResume(data: ResumeOptimizationData) {
    return fetch(this.optimizeEndpoint, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    })
  }
}
```

#### 状态管理方案
```typescript
// Zustand Store设计
interface ResumeStore {
  // 数据状态
  resume: ResumeData | null
  isAnalyzing: boolean
  isOptimizing: boolean
  error: string | null

  // UI状态
  currentStep: 'upload' | 'config' | 'result'
  uploadProgress: number

  // 操作
  setResume: (resume: ResumeData) => void
  analyzeResume: (file: File) => Promise<void>
  optimizeResume: (params: OptimizationParams) => Promise<void>
  reset: () => void
}

// React Query集成
export const useResumeQuery = () => {
  return useQuery({
    queryKey: ['resume'],
    queryFn: fetchResumeData,
    staleTime: 5 * 60 * 1000
  })
}
```

### 高端UI设计方案

#### 设计系统
```typescript
// 主题配置
export const theme = {
  colors: {
    primary: {
      50: '#f0f9ff',
      500: '#3b82f6',
      900: '#1e3a8a'
    },
    glass: {
      bg: 'rgba(255, 255, 255, 0.1)',
      border: 'rgba(255, 255, 255, 0.2)',
      backdrop: 'blur(10px)'
    }
  },
  animations: {
    spring: { type: 'spring', damping: 25, stiffness: 300 },
    slide: { type: 'tween', duration: 0.3 }
  }
}
```

#### 核心功能页面设计

1. **着陆页 (Landing)**
   - 全屏视频背景
   - 渐变玻璃态导航栏
   - 动态数据展示（已优化简历数）
   - 平滑滚动锚点导航
   - CTA按钮悬浮效果

2. **简历优化器 (ResumeOptimizer)**
   - 三步骤流程：上传→配置→结果
   - 拖拽上传区域，支持进度条
   - 实时表单验证和预览
   - AI参数调节滑块
   - 优化结果对比展示

3. **面试辅导 (InterviewCoach)**
   - 模板选择卡片
   - 公司信息智能填充
   - PPT预览功能
   - 一键下载

4. **工作台 (Dashboard)**
   - 使用统计图表
   - 快速操作入口
   - 最近任务列表
   - 订阅状态卡片

### 部署架构

#### 前端部署
- **主站**: Vercel (自动部署，全球CDN)
- **域名**: www.ygsjw.com
- **HTTPS**: 自动SSL证书
- **性能**: Lighthouse评分 > 95

#### 后端API (最小化)
- **认证服务**: Next.js API Routes
- **文件上传**: Vercel Blob Storage
- **支付回调**: Next.js Webhook Handler
- **无需数据库**: 使用Supabase或Firebase

## 实施策略

### 开发阶段

#### 第一阶段：核心体验（1个月）
**目标：打造极致的用户体验**

1. **设计系统搭建**
   - 完整的组件库开发
   - 主题系统（亮色/暗色模式）
   - 动画库集成
   - 响应式布局框架

2. **简历优化核心功能**
   - 拖拽上传组件（带进度条）
   - N8N Webhook集成封装
   - 三步骤流程设计
   - 实时表单验证
   - 优化结果展示

3. **着陆页开发**
   - 品牌展示页面
   - 功能介绍
   - 视频背景集成
   - CTA转化流程

#### 第二阶段：功能完善（1个月）
**目标：完整的功能闭环**

1. **面试辅导模块**
   - PPT模板选择器
   - 面试信息表单
   - AI内容生成流程
   - 预览和下载功能

2. **效率工具套件**
   - 7大服务类别入口
   - 批量文件上传
   - 分析报告展示
   - 结果导出功能

3. **用户系统**
   - 注册/登录（支持OAuth）
   - 个人中心
   - 订阅管理
   - 使用历史

#### 第三阶段：优化提升（1个月）
**目标：行业标杆级产品**

1. **微交互优化**
   - 页面过渡动画
   - 加载骨架屏
   - 操作反馈效果
   - 手势支持

2. **性能优化**
   - 图片懒加载
   - 代码分割
   - 缓存策略
   - SEO优化

3. **高级功能**
   - 实时通知系统
   - 快捷键支持
   - 批量操作
   - 数据统计仪表板

### 用户体验设计原则
- **零学习成本**: 直观的界面设计
- **即时反馈**: 每个操作都有视觉反馈
- **容错设计**: 友好的错误提示和恢复机制
- **性能优先**: 300ms内响应，流畅动画

### 测试方案
- **视觉回归测试**: Chromatic组件快照
- **E2E测试**: Playwright用户流程
- **性能测试**: Lighthouse CI集成
- **可访问性测试**: axe-core自动化

## 任务创建列表

### 第一阶段任务（核心体验）
- [ ] **001.md - 设计系统开发** (parallel: true) - 8小时
- [ ] **002.md - 项目初始化** (parallel: true) - 6小时
- [ ] **003.md - 主题系统实现** (parallel: true) - 5小时
- [ ] **004.md - 路由和布局** (parallel: true) - 7小时

### 第二阶段任务（功能完善）
- [ ] **005.md - 着陆页开发** (parallel: true) - 12小时
- [ ] **006.md - N8N Webhook集成** (parallel: true) - 10小时
- [ ] **007.md - 文件上传组件** (parallel: true) - 8小时
- [ ] **008.md - 简历优化三步流程** (parallel: false, depends_on: [006, 007]) - 14小时
- [ ] **009.md - 面试辅导模块** (parallel: true) - 16小时
- [ ] **010.md - 效率工具套件** (parallel: true) - 14小时
- [ ] **011.md - 用户系统** (parallel: true) - 12小时

### 第三阶段任务（优化提升）
- [ ] **012.md - 性能优化和部署** (parallel: false, depends_on: [008, 009, 010, 011]) - 20小时

### 任务统计
- **总任务数**: 12个
- **并行任务**: 9个
- **依赖任务**: 3个
- **预估总工时**: 132小时
- **预计完成时间**: 3个月（按每周40小时计算）

## 依赖关系

### 外部服务依赖
- **N8N工作流**: ✅ 已部署，Webhook端点就绪
- **AI LLM API**: OpenAI/文心一言（N8N内部集成）
- **支付网关**: Stripe/支付宝（后续集成）
- **文件存储**: Vercel Blob Storage
- **认证服务**: NextAuth.js（支持OAuth）

### 技术依赖
- **Next.js 14**: 全栈框架
- **React 18**: 前端框架（并发特性）
- **TypeScript 5**: 类型系统
- **Tailwind CSS 3**: 样式框架
- **Framer Motion 10**: 动画库
- **Zustand 4**: 状态管理
- **React Query 5**: 服务端状态

## 成功标准

### 用户体验指标
- **Lighthouse评分**: 全部 > 95分
- **首屏加载**: < 1.5秒
- **交互响应**: < 100ms
- **CLS分数**: < 0.1（视觉稳定性）

### 功能完整性
- **简历优化**: 端到端流程成功率 > 98%
- **用户转化**: 注册到付费转化率 > 15%
- **用户留存**: 7日留存率 > 60%
- **NPS评分**: > 50

### 技术质量
- **TypeScript覆盖**: 100%
- **组件测试覆盖**: > 90%
- **E2E测试覆盖**: 核心流程100%
- **可访问性**: WCAG 2.1 AA合规

## 预估工作量

### 时间线（3个月冲刺）
- **第一阶段**: 1个月（核心体验）
- **第二阶段**: 1个月（功能完善）
- **第三阶段**: 1个月（优化提升）

### 团队配置
- **全栈开发**: 2人（React/Next.js专家）
- **UI/UX**: 1人（Figma + 动效设计）
- **测试/QA**: 0.5人（自动化测试）

### 开发重点
1. **周1-2**: 设计系统和组件库
2. **周3-4**: 简历优化核心功能
3. **周5-6**: 面试辅导和效率工具
4. **周7-8**: 用户体验优化
5. **周9-10**: 性能优化和测试
6. **周11-12**: 部署和上线准备

### 里程碑
- **Week 4**: 核心功能演示
- **Week 8**: Beta版本发布
- **Week 12**: 正式版本上线

## 风险评估

### 技术风险及缓解
- **N8N依赖**: ✅ 已部署稳定，端点可用
  - 缓解：实现重试机制和降级策略

- **AI响应延迟**: 5-10分钟处理时间
  - 缓解：异步处理、进度提示、邮件通知

- **文件上传限制**: 10MB限制
  - 缓解：前端压缩、分片上传、清晰提示

### 用户体验风险
- **学习成本**: 新用户上手
  - 缓解：引导教程、 tooltips、视频演示

- **加载焦虑**: AI处理等待
  - 缓解：实时进度、预估时间、有趣动画

### 业务风险
- **付费转化**: 免费到付费转化
  - 缓解：免费额度、价值展示、平滑过渡

## 总结

本史诗专注于构建一个**功能完善、界面高端、使用便捷**的AI赋能职业规划师平台。通过现代化的技术栈、优秀的设计系统和流畅的用户体验，我们将在3个月内完成开发并上线。

### 核心优势
1. **N8N已就绪**: 省去AI集成复杂度，专注前端体验
2. **技术栈现代化**: React 18 + Next.js 14 + TypeScript
3. **设计驱动**: 玻璃态UI + 流畅动画 + 响应式设计
4. **快速迭代**: 3个月冲刺，每周里程碑

### 成功关键
- 极致的用户体验设计
- 完整的功能闭环
- 高性能的技术实现
- 持续的优化迭代