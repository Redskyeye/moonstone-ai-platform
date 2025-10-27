# 月光石AI职业咨询平台 - 系统架构设计

## 项目概述

月光石AI职业咨询平台（www.ygsjw.com）是一个基于人工智能的职业发展服务平台，旨在为用户提供专业的简历优化、职业规划、面试准备等一站式职业咨询服务。

### 核心功能
- 简历智能优化与评估
- 职业规划建议
- 模拟面试训练
- 求职技巧指导
- 行业薪资分析

## 技术架构总览

### 架构图
```
┌─────────────────────────────────────────────────────────────┐
│                    用户界面层 (Frontend)                      │
│  React 18 + Next.js 14 + TypeScript + Tailwind CSS           │
├─────────────────────────────────────────────────────────────┤
│                    业务逻辑层 (Business)                       │
│              Zustand 状态管理 + 自定义 Hooks                  │
├─────────────────────────────────────────────────────────────┤
│                    数据服务层 (Services)                       │
│              API Client + 数据处理 + 缓存管理                  │
├─────────────────────────────────────────────────────────────┤
│                    后端服务层 (Backend)                        │
│                     N8N 工作流引擎                            │
├─────────────────────────────────────────────────────────────┤
│                    部署基础设施层 (Infrastructure)               │
│                  Vercel 部署 + CDN + 监控                     │
└─────────────────────────────────────────────────────────────┘
```

## 前端技术栈详细设计

### 1. 核心框架层
```typescript
// Next.js 14 App Router 配置
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    appDir: true,
    serverComponentsExternalPackages: ['@prisma/client']
  },
  images: {
    domains: ['ygsjw.com', 'cdn.ygsjw.com'],
    formats: ['image/webp', 'image/avif']
  },
  compiler: {
    removeConsole: process.env.NODE_ENV === 'production'
  }
}

module.exports = nextConfig
```

### 2. React 18 并发特性应用
```typescript
// Suspense + Lazy Loading 实现
import { lazy, Suspense } from 'react'
import { motion, AnimatePresence } from 'framer-motion'

// 懒加载组件
const ResumeOptimizer = lazy(() => import('@/components/ResumeOptimizer'))
const InterviewSimulator = lazy(() => import('@/components/InterviewSimulator'))
const CareerPlanner = lazy(() => import('@/components/CareerPlanner'))

// 加载状态组件
const LoadingFallback = () => (
  <motion.div
    initial={{ opacity: 0 }}
    animate={{ opacity: 1 }}
    className="flex items-center justify-center min-h-[400px]"
  >
    <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-600" />
  </motion.div>
)

// 应用路由组件
function AppRoutes() {
  return (
    <AnimatePresence mode="wait">
      <Routes>
        <Route path="/resume" element={
          <Suspense fallback={<LoadingFallback />}>
            <ResumeOptimizer />
          </Suspense>
        } />
        <Route path="/interview" element={
          <Suspense fallback={<LoadingFallback />}>
            <InterviewSimulator />
          </Suspense>
        } />
        <Route path="/career" element={
          <Suspense fallback={<LoadingFallback />}>
            <CareerPlanner />
          </Suspense>
        } />
      </Routes>
    </AnimatePresence>
  )
}
```

### 3. TypeScript 严格模式配置
```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "ES2022"],
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"],
      "@/components/*": ["./components/*"],
      "@/lib/*": ["./lib/*"],
      "@/types/*": ["./types/*"],
      "@/hooks/*": ["./hooks/*"],
      "@/utils/*": ["./utils/*"]
    },
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "exactOptionalPropertyTypes": true
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

## 状态管理架构

### 1. Zustand 状态管理设计
```typescript
// stores/types.ts
export interface User {
  id: string
  email: string
  name: string
  avatar?: string
  subscription: 'free' | 'premium' | 'enterprise'
  preferences: UserPreferences
}

export interface UserPreferences {
  theme: 'light' | 'dark' | 'system'
  language: 'zh-CN' | 'en-US'
  notifications: {
    email: boolean
    push: boolean
    career: boolean
  }
}

export interface AppState {
  // 用户状态
  user: User | null
  isAuthenticated: boolean

  // 应用状态
  theme: 'light' | 'dark'
  isLoading: boolean
  error: string | null

  // 功能状态
  resumeData: ResumeData | null
  interviewHistory: InterviewSession[]
  careerPlan: CareerPlan | null
}

// stores/user-store.ts
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'

interface UserStore {
  user: User | null
  isAuthenticated: boolean
  updateUser: (user: Partial<User>) => void
  login: (email: string, password: string) => Promise<void>
  logout: () => void
}

export const useUserStore = create<UserStore>()(
  devtools(
    persist(
      immer((set, get) => ({
        user: null,
        isAuthenticated: false,

        updateUser: (userData) =>
          set((state) => {
            if (state.user) {
              Object.assign(state.user, userData)
            }
          }),

        login: async (email, password) => {
          try {
            set((state) => { state.isLoading = true })
            const user = await authService.login(email, password)
            set((state) => {
              state.user = user
              state.isAuthenticated = true
              state.isLoading = false
            })
          } catch (error) {
            set((state) => {
              state.error = error.message
              state.isLoading = false
            })
          }
        },

        logout: () =>
          set((state) => {
            state.user = null
            state.isAuthenticated = false
          })
      })),
      { name: 'user-store' }
    ),
    { name: 'user-store' }
  )
)
```

### 2. 全局状态管理
```typescript
// stores/app-store.ts
interface AppStore {
  // UI 状态
  sidebarOpen: boolean
  currentPath: string
  notifications: Notification[]

  // 业务状态
  resumeOptimization: {
    currentStep: 1 | 2 | 3
    uploadData: UploadData
    configData: ConfigData
    resultData: ResultData
  }

  // Actions
  setSidebarOpen: (open: boolean) => void
  setCurrentPath: (path: string) => void
  addNotification: (notification: Notification) => void
  removeNotification: (id: string) => void
  updateResumeStep: (step: 1 | 2 | 3) => void
}

export const useAppStore = create<AppStore>()(
  devtools(
    immer((set) => ({
      sidebarOpen: false,
      currentPath: '/',
      notifications: [],

      resumeOptimization: {
        currentStep: 1,
        uploadData: {},
        configData: {},
        resultData: {}
      },

      setSidebarOpen: (open) =>
        set((state) => { state.sidebarOpen = open }),

      setCurrentPath: (path) =>
        set((state) => { state.currentPath = path }),

      addNotification: (notification) =>
        set((state) => {
          state.notifications.push(notification)
        }),

      removeNotification: (id) =>
        set((state) => {
          state.notifications = state.notifications.filter(n => n.id !== id)
        }),

      updateResumeStep: (step) =>
        set((state) => {
          state.resumeOptimization.currentStep = step
        })
    }))
  )
)
```

## 组件架构设计

### 1. 设计系统组件
```typescript
// components/ui/Button.tsx
import { motion, MotionProps } from 'framer-motion'
import { cn } from '@/lib/utils'

export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'ghost' | 'danger'
  size?: 'sm' | 'md' | 'lg'
  loading?: boolean
  leftIcon?: React.ReactNode
  rightIcon?: React.ReactNode
  fullWidth?: boolean
}

const buttonVariants = {
  primary: 'bg-blue-600 hover:bg-blue-700 text-white shadow-sm',
  secondary: 'bg-gray-100 hover:bg-gray-200 text-gray-900',
  ghost: 'hover:bg-gray-100 text-gray-700',
  danger: 'bg-red-600 hover:bg-red-700 text-white'
}

const buttonSizes = {
  sm: 'px-3 py-1.5 text-sm',
  md: 'px-4 py-2 text-base',
  lg: 'px-6 py-3 text-lg'
}

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({
    className,
    variant = 'primary',
    size = 'md',
    loading = false,
    leftIcon,
    rightIcon,
    fullWidth = false,
    disabled,
    children,
    ...props
  }, ref) => {
    return (
      <motion.button
        ref={ref}
        className={cn(
          'inline-flex items-center justify-center rounded-md font-medium transition-colors',
          'focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2',
          'disabled:opacity-50 disabled:cursor-not-allowed',
          buttonVariants[variant],
          buttonSizes[size],
          fullWidth && 'w-full',
          className
        )}
        whileHover={{ scale: 1.02 }}
        whileTap={{ scale: 0.98 }}
        disabled={disabled || loading}
        {...props}
      >
        {loading && (
          <div className="animate-spin rounded-full h-4 w-4 border-b-2 border-current mr-2" />
        )}
        {leftIcon && <span className="mr-2">{leftIcon}</span>}
        {children}
        {rightIcon && <span className="ml-2">{rightIcon}</span>}
      </motion.button>
    )
  }
)
```

### 2. 业务组件架构
```typescript
// components/ResumeOptimizer/ResumeOptimizer.tsx
import { useResumeOptimizerStore } from '@/stores/resume-optimizer-store'
import { UploadStep } from './UploadStep'
import { ConfigStep } from './ConfigStep'
import { ResultStep } from './ResultStep'
import { StepIndicator } from './StepIndicator'

export const ResumeOptimizer: React.FC = () => {
  const { currentStep, totalSteps, nextStep, previousStep } = useResumeOptimizer()

  return (
    <div className="max-w-4xl mx-auto p-6">
      <div className="mb-8">
        <h1 className="text-3xl font-bold text-gray-900 mb-4">
          AI简历优化专家
        </h1>
        <StepIndicator
          currentStep={currentStep}
          totalSteps={totalSteps}
        />
      </div>

      <AnimatePresence mode="wait">
        {currentStep === 1 && (
          <motion.div
            key="upload"
            initial={{ opacity: 0, x: 20 }}
            animate={{ opacity: 1, x: 0 }}
            exit={{ opacity: 0, x: -20 }}
          >
            <UploadStep onNext={nextStep} />
          </motion.div>
        )}

        {currentStep === 2 && (
          <motion.div
            key="config"
            initial={{ opacity: 0, x: 20 }}
            animate={{ opacity: 1, x: 0 }}
            exit={{ opacity: 0, x: -20 }}
          >
            <ConfigStep
              onNext={nextStep}
              onPrevious={previousStep}
            />
          </motion.div>
        )}

        {currentStep === 3 && (
          <motion.div
            key="result"
            initial={{ opacity: 0, x: 20 }}
            animate={{ opacity: 1, x: 0 }}
            exit={{ opacity: 0, x: -20 }}
          >
            <ResultStep onPrevious={previousStep} />
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  )
}
```

## 文件结构设计

```
月光石AI平台/
├── src/
│   ├── app/                          # Next.js 14 App Router
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── (dashboard)/              # 路由组
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx
│   │   │   ├── resume/
│   │   │   │   ├── page.tsx
│   │   │   │   └── optimize/
│   │   │   │       └── page.tsx
│   │   │   ├── interview/
│   │   │   │   ├── page.tsx
│   │   │   │   └── simulate/
│   │   │   │       └── page.tsx
│   │   │   └── career/
│   │   │       ├── page.tsx
│   │   │       └── plan/
│   │   │           └── page.tsx
│   │   ├── api/                      # API 路由
│   │   │   ├── auth/
│   │   │   │   ├── login/route.ts
│   │   │   │   └── register/route.ts
│   │   │   ├── resume/
│   │   │   │   ├── upload/route.ts
│   │   │   │   ├── optimize/route.ts
│   │   │   │   └── export/route.ts
│   │   │   ├── interview/
│   │   │   │   ├── start/route.ts
│   │   │   │   └── evaluate/route.ts
│   │   │   └── webhook/
│   │   │       └── n8n/route.ts
│   │   └── loading.tsx
│   ├── components/                   # 组件库
│   │   ├── ui/                      # 基础UI组件
│   │   │   ├── Button/
│   │   │   │   ├── Button.tsx
│   │   │   │   ├── Button.stories.tsx
│   │   │   │   ├── Button.test.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Input/
│   │   │   ├── Card/
│   │   │   ├── Modal/
│   │   │   ├── Dropdown/
│   │   │   ├── Badge/
│   │   │   ├── Avatar/
│   │   │   ├── Skeleton/
│   │   │   └── index.ts
│   │   ├── layout/                  # 布局组件
│   │   │   ├── Header/
│   │   │   ├── Sidebar/
│   │   │   ├── Footer/
│   │   │   ├── Navigation/
│   │   │   └── index.ts
│   │   ├── features/                # 功能组件
│   │   │   ├── ResumeOptimizer/
│   │   │   │   ├── ResumeOptimizer.tsx
│   │   │   │   ├── UploadStep.tsx
│   │   │   │   ├── ConfigStep.tsx
│   │   │   │   ├── ResultStep.tsx
│   │   │   │   ├── StepIndicator.tsx
│   │   │   │   ├── ResumePreview.tsx
│   │   │   │   └── index.ts
│   │   │   ├── InterviewSimulator/
│   │   │   │   ├── InterviewSimulator.tsx
│   │   │   │   ├── QuestionDisplay.tsx
│   │   │   │   ├── AnswerRecorder.tsx
│   │   │   │   ├── FeedbackPanel.tsx
│   │   │   │   └── index.ts
│   │   │   └── CareerPlanner/
│   │   │       ├── CareerPlanner.tsx
│   │   │       ├── SkillAssessment.tsx
│   │   │       ├── PathRecommendation.tsx
│   │   │       └── index.ts
│   │   └── common/                  # 通用组件
│   │       ├── Loading/
│   │       ├── ErrorBoundary/
│   │       ├── EmptyState/
│   │       ├── NotificationCenter/
│   │       └── index.ts
│   ├── hooks/                       # 自定义 Hooks
│   │   ├── useAuth.ts
│   │   ├── useResumeOptimizer.ts
│   │   ├── useInterview.ts
│   │   ├── useLocalStorage.ts
│   │   ├── useDebounce.ts
│   │   ├── useInfiniteScroll.ts
│   │   └── index.ts
│   ├── lib/                         # 工具库
│   │   ├── utils.ts
│   │   ├── api.ts
│   │   ├── auth.ts
│   │   ├── storage.ts
│   │   ├── validation.ts
│   │   ├── constants.ts
│   │   ├── helpers.ts
│   │   └── index.ts
│   ├── stores/                      # Zustand 状态管理
│   │   ├── user-store.ts
│   │   ├── app-store.ts
│   │   ├── resume-optimizer-store.ts
│   │   ├── interview-store.ts
│   │   ├── career-store.ts
│   │   ├── types.ts
│   │   └── index.ts
│   ├── types/                       # TypeScript 类型定义
│   │   ├── user.ts
│   │   ├── resume.ts
│   │   ├── interview.ts
│   │   ├── career.ts
│   │   ├── api.ts
│   │   ├── common.ts
│   │   └── index.ts
│   ├── styles/                      # 样式文件
│   │   ├── globals.css
│   │   ├── components.css
│   │   ├── utilities.css
│   │   └── themes/
│   │       ├── light.css
│   │       └── dark.css
│   └── public/                      # 静态资源
│       ├── images/
│       ├── icons/
│       ├── fonts/
│       └── favicon/
├── docs/                            # 项目文档
│   ├── architecture.md              # 系统架构文档
│   ├── api-specification.md         # API接口规范
│   ├── components.md                # 组件文档
│   ├── deployment.md                # 部署文档
│   └── contributing.md              # 贡献指南
├── tests/                           # 测试文件
│   ├── __mocks__/
│   ├── components/
│   ├── hooks/
│   ├── utils/
│   └── e2e/
├── .env.example                     # 环境变量示例
├── .env.local                       # 本地环境变量
├── next.config.js                   # Next.js 配置
├── tailwind.config.js               # Tailwind CSS 配置
├── tsconfig.json                    # TypeScript 配置
├── package.json                     # 项目依赖
├── package-lock.json
├── README.md                        # 项目说明
└── vercel.json                      # Vercel 部署配置
```

## 性能优化策略

### 1. 代码分割与懒加载
```typescript
// 动态导入实现代码分割
const HeavyChart = lazy(() => import('@/components/Charts/HeavyChart'))
const PDFViewer = lazy(() => import('@/components/PDFViewer'))

// Suspense 配置
const LazyComponent = ({ component: Component, ...props }) => (
  <Suspense
    fallback={
      <div className="flex items-center justify-center p-8">
        <div className="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600" />
      </div>
    }
  >
    <Component {...props} />
  </Suspense>
)
```

### 2. 图片优化
```typescript
// Next.js Image 组件优化配置
import Image from 'next/image'

const OptimizedImage = ({ src, alt, ...props }) => (
  <Image
    src={src}
    alt={alt}
    placeholder="blur"
    blurDataURL="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcUFhYaHSUfGhsjHBYWICwgIyYnKSopGR8tMC0oMCUoKSj/2wBDAQcHBwoIChMKChMoGhYaKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCj/wAARCAABAAEDASIAAhEBAxEB/8QAFQABAQAAAAAAAAAAAAAAAAAAAAv/xAAUEAEAAAAAAAAAAAAAAAAAAAAA/8QAFQEBAQAAAAAAAAAAAAAAAAAAAAX/xAAUEQEAAAAAAAAAAAAAAAAAAAAA/9oADAMBAAIRAxEAPwCdABmX/9k="
    sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
    quality={85}
    {...props}
  />
)
```

### 3. 缓存策略
```typescript
// API 缓存配置
export const apiClient = {
  get: async (url: string, options?: RequestInit) => {
    const response = await fetch(url, {
      next: { revalidate: 3600 }, // 1小时缓存
      ...options
    })
    return response.json()
  },

  post: async (url: string, data: any, options?: RequestInit) => {
    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers
      },
      body: JSON.stringify(data),
      cache: 'no-store', // POST 请求不缓存
      ...options
    })
    return response.json()
  }
}
```

## 安全性设计

### 1. 输入验证与清理
```typescript
// lib/validation.ts
import { z } from 'zod'

export const resumeUploadSchema = z.object({
  file: z.instanceof(File).refine(
    (file) => file.size <= 5 * 1024 * 1024, // 5MB
    '文件大小不能超过 5MB'
  ).refine(
    (file) => ['application/pdf', 'application/msword', 'application/vnd.openxmlformats-officedocument.wordprocessingml.document'].includes(file.type),
    '仅支持 PDF、DOC、DOCX 格式'
  ),
  targetRole: z.string().min(1, '目标职位不能为空').max(100, '目标职位不能超过100个字符'),
  optimizationLevel: z.enum(['basic', 'advanced', 'expert']),
  customInstructions: z.string().max(500, '自定义说明不能超过500个字符').optional()
})

export type ResumeUploadData = z.infer<typeof resumeUploadSchema>
```

### 2. API 安全
```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // CORS 配置
  if (request.nextUrl.pathname.startsWith('/api/')) {
    const response = NextResponse.next()

    response.headers.set('Access-Control-Allow-Origin', process.env.ALLOWED_ORIGINS || '*')
    response.headers.set('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS')
    response.headers.set('Access-Control-Allow-Headers', 'Content-Type, Authorization')
    response.headers.set('Access-Control-Allow-Credentials', 'true')

    return response
  }

  // 认证中间件
  if (request.nextUrl.pathname.startsWith('/api/protected')) {
    const token = request.headers.get('authorization')?.replace('Bearer ', '')

    if (!token || !isValidToken(token)) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      )
    }
  }

  return NextResponse.next()
}
```

## SEO 优化策略

### 1. Meta 标签优化
```typescript
// app/layout.tsx
import { Metadata } from 'next'

export const metadata: Metadata = {
  title: {
    default: '月光石AI职业咨询平台 - 专业的AI简历优化和职业规划服务',
    template: '%s | 月光石AI'
  },
  description: '月光石AI提供专业的AI简历优化、职业规划、模拟面试等服务，帮助求职者提升竞争力，找到理想工作。',
  keywords: 'AI简历优化,职业规划,模拟面试,求职技巧,月光石AI',
  authors: [{ name: '月光石AI团队' }],
  creator: '月光石AI',
  publisher: '月光石AI',
  formatDetection: {
    email: false,
    address: false,
    telephone: false,
  },
  metadataBase: new URL('https://www.ygsjw.com'),
  alternates: {
    canonical: '/',
  },
  openGraph: {
    type: 'website',
    locale: 'zh_CN',
    url: 'https://www.ygsjw.com',
    title: '月光石AI职业咨询平台',
    description: '专业的AI简历优化和职业规划服务',
    siteName: '月光石AI',
    images: [
      {
        url: '/og-image.jpg',
        width: 1200,
        height: 630,
        alt: '月光石AI职业咨询平台',
      },
    ],
  },
  twitter: {
    card: 'summary_large_image',
    title: '月光石AI职业咨询平台',
    description: '专业的AI简历优化和职业规划服务',
    images: ['/twitter-image.jpg'],
  },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
    },
  },
}
```

### 2. 结构化数据
```typescript
// components/SEO/StructuredData.tsx
export const StructuredData = () => {
  const structuredData = {
    '@context': 'https://schema.org',
    '@type': 'WebApplication',
    name: '月光石AI职业咨询平台',
    description: '专业的AI简历优化和职业规划服务',
    url: 'https://www.ygsjw.com',
    applicationCategory: 'BusinessApplication',
    operatingSystem: 'Any',
    offers: {
      '@type': 'Offer',
      price: '0',
      priceCurrency: 'CNY',
      availability: 'https://schema.org/InStock'
    },
    creator: {
      '@type': 'Organization',
      name: '月光石AI团队',
      url: 'https://www.ygsjw.com'
    }
  }

  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{ __html: JSON.stringify(structuredData) }}
    />
  )
}
```

## 监控与分析

### 1. 性能监控
```typescript
// lib/analytics.ts
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals'

function sendToAnalytics(metric: any) {
  // 发送到分析服务
  if (process.env.NODE_ENV === 'production') {
    gtag('event', metric.name, {
      value: Math.round(metric.value),
      metric_id: metric.id,
      metric_value: metric.value,
      metric_delta: metric.delta,
    })
  }
}

// Web Vitals 监控
getCLS(sendToAnalytics)
getFID(sendToAnalytics)
getFCP(sendToAnalytics)
getLCP(sendToAnalytics)
getTTFB(sendToAnalytics)

// 错误监控
window.addEventListener('error', (event) => {
  console.error('Global error:', event.error)
  // 发送错误报告到监控服务
})

window.addEventListener('unhandledrejection', (event) => {
  console.error('Unhandled promise rejection:', event.reason)
  // 发送错误报告到监控服务
})
```

### 2. 用户行为分析
```typescript
// hooks/useAnalytics.ts
export const useAnalytics = () => {
  const trackEvent = (eventName: string, properties?: Record<string, any>) => {
    if (typeof window !== 'undefined' && window.gtag) {
      window.gtag('event', eventName, properties)
    }
  }

  const trackPageView = (path: string) => {
    if (typeof window !== 'undefined' && window.gtag) {
      window.gtag('config', process.env.NEXT_PUBLIC_GA_ID, {
        page_path: path,
      })
    }
  }

  const trackUserInteraction = (action: string, element: string) => {
    trackEvent('user_interaction', {
      action,
      element,
      timestamp: new Date().toISOString()
    })
  }

  return {
    trackEvent,
    trackPageView,
    trackUserInteraction
  }
}
```

## 部署配置

### 1. Vercel 配置
```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": ".next",
  "installCommand": "npm install",
  "framework": "nextjs",
  "functions": {
    "api/**/*.ts": {
      "maxDuration": 30
    }
  },
  "headers": [
    {
      "source": "/static/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    },
    {
      "source": "/_next/static/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    }
  ],
  "redirects": [
    {
      "source": "/home",
      "destination": "/",
      "permanent": true
    },
    {
      "source": "/resume-optimizer",
      "destination": "/resume/optimize",
      "permanent": true
    }
  ],
  "env": {
    "NEXT_PUBLIC_API_URL": "@api-url",
    "NEXT_PUBLIC_GA_ID": "@ga-id"
  }
}
```

### 2. 环境变量配置
```bash
# .env.example
# 应用配置
NEXT_PUBLIC_APP_URL=https://www.ygsjw.com
NEXT_PUBLIC_API_URL=https://api.ygsjw.com
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX

# 认证配置
NEXTAUTH_URL=https://www.ygsjw.com
NEXTAUTH_SECRET=your-secret-key

# 第三方服务
N8N_WEBHOOK_URL=https://n8n.ygsjw.com/webhook
UPLOAD_SECRET_KEY=your-upload-secret

# 数据库配置（如果需要）
DATABASE_URL=postgresql://user:password@localhost:5432/ygsjw

# Redis 配置（缓存）
REDIS_URL=redis://localhost:6379

# 文件存储配置
AWS_ACCESS_KEY_ID=your-aws-access-key
AWS_SECRET_ACCESS_KEY=your-aws-secret-key
AWS_S3_BUCKET=your-s3-bucket
```

## 开发规范与最佳实践

### 1. 代码规范
```json
// .eslintrc.json
{
  "extends": [
    "next/core-web-vitals",
    "@typescript-eslint/recommended",
    "prettier"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/explicit-function-return-type": "warn",
    "prefer-const": "error",
    "no-var": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

### 2. Git 工作流
```bash
# 分支命名规范
feature/resume-optimizer-upload
feature/interview-simulator-evaluation
fix/authentication-bug
hotfix/critical-security-patch

# 提交信息规范
feat: 添加简历优化功能
fix: 修复登录验证问题
docs: 更新API文档
style: 优化按钮组件样式
refactor: 重构状态管理架构
test: 添加简历上传单元测试
chore: 更新依赖包版本
```

### 3. 组件开发规范
```typescript
// 组件模板
import React from 'react'
import { motion } from 'framer-motion'
import { cn } from '@/lib/utils'

interface ComponentNameProps {
  // 定义 Props 类型
  className?: string
  children?: React.ReactNode
}

export const ComponentName: React.FC<ComponentNameProps> = ({
  className,
  children
}) => {
  return (
    <motion.div
      className={cn('default-styles', className)}
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 0.3 }}
    >
      {children}
    </motion.div>
  )
}

ComponentName.displayName = 'ComponentName'
```

## 测试策略

### 1. 单元测试
```typescript
// components/Button/Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { Button } from './Button'

describe('Button Component', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument()
  })

  it('handles click events', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click me</Button>)

    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('shows loading state', () => {
    render(<Button loading>Click me</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
    expect(screen.getByTestId('loading-spinner')).toBeInTheDocument()
  })
})
```

### 2. 集成测试
```typescript
// components/ResumeOptimizer/ResumeOptimizer.test.tsx
import { render, screen, waitFor } from '@testing-library/react'
import { ResumeOptimizer } from './ResumeOptimizer'
import { useResumeOptimizerStore } from '@/stores/resume-optimizer-store'

// Mock store
jest.mock('@/stores/resume-optimizer-store')

describe('ResumeOptimizer Integration', () => {
  it('completes full workflow', async () => {
    const mockStore = {
      currentStep: 1,
      totalSteps: 3,
      nextStep: jest.fn(),
      previousStep: jest.fn()
    }

    useResumeOptimizerStore.mockReturnValue(mockStore)

    render(<ResumeOptimizer />)

    expect(screen.getByText('AI简历优化专家')).toBeInTheDocument()
    expect(screen.getByText('步骤 1 / 3')).toBeInTheDocument()
  })
})
```

## 总结

本架构设计基于现代化的技术栈和最佳实践，确保了：

1. **高性能**: 利用 Next.js 14 的 App Router、React 18 的并发特性、代码分割等优化技术
2. **类型安全**: TypeScript 严格模式配置确保代码质量
3. **用户体验**: Framer Motion 动画、响应式设计、加载状态等提升用户体验
4. **可维护性**: 清晰的文件结构、组件化设计、完善的文档
5. **可扩展性**: 模块化架构、状态管理、API 设计支持未来扩展
6. **安全性**: 输入验证、API 安全、环境变量管理等安全措施
7. **SEO 优化**: Meta 标签、结构化数据、性能监控等 SEO 策略

该架构为月光石AI职业咨询平台提供了坚实的技术基础，支持快速迭代和长期维护。