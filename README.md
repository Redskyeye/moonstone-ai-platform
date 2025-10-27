# 月光石AI职业咨询平台

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Next.js](https://img.shields.io/badge/Next.js-14-black?logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-18-blue?logo=react)](https://reactjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-blue?logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3-38B2AC?logo=tailwind-css)](https://tailwindcss.com/)

> 🌟 **专业的AI驱动职业发展服务平台**，为求职者提供智能简历优化、职业规划建议、模拟面试训练等一站式解决方案。

**🌐 网站地址**: [www.ygsjw.com](https://www.ygsjw.com)

## ✨ 核心功能

### 📄 **AI简历优化**
- 智能解析简历内容，识别技能和经验
- 针对目标职位进行个性化优化建议
- 多维度评分体系（技能匹配、格式规范、关键词优化）
- 支持PDF、DOC、DOCX格式
- 实时预览优化效果

### 🎯 **模拟面试训练**
- AI驱动的智能面试官
- 多维度面试评估（技术能力、沟通表达、问题解决）
- 实时反馈和改进建议
- 覆盖多种职位和行业
- 面试报告生成

### 🗺️ **职业规划指导**
- 技能评估和差距分析
- 个性化职业发展路径建议
- 行业薪资数据分析
- 学习资源推荐
- 职业目标设定

### 📊 **求职市场洞察**
- 实时行业薪资数据
- 技能需求趋势分析
- 竞争力评估
- 就业市场动态

## 🏗️ 技术架构

### 前端技术栈
- **框架**: Next.js 14 (App Router)
- **UI库**: React 18 + TypeScript
- **样式**: Tailwind CSS + Framer Motion
- **状态管理**: Zustand
- **构建工具**: Turbopack
- **部署**: Vercel

### 后端服务
- **工作流引擎**: N8N
- **数据处理**: AI模型集成
- **文件存储**: 云存储服务
- **API**: RESTful API + Webhook

### 核心特性
- 🚀 **高性能**: Next.js 14 App Router + React 18 并发特性
- 🎨 **现代化UI**: Tailwind CSS + Framer Motion动画
- 🔒 **类型安全**: TypeScript 严格模式
- 📱 **响应式设计**: 完美适配桌面端和移动端
- 🌍 **SEO优化**: 服务端渲染 + 结构化数据
- ⚡ **性能优化**: 代码分割、懒加载、图片优化

## 🚀 快速开始

### 环境要求
- Node.js 18.17+
- npm 9+ 或 yarn 1.22+

### 安装依赖
```bash
# 克隆项目
git clone https://github.com/your-org/ygsjw-platform.git
cd ygsjw-platform

# 安装依赖
npm install

# 复制环境变量文件
cp .env.example .env.local
```

### 配置环境变量
```bash
# .env.local
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3001/api
NEXT_PUBLIC_GA_ID=your-google-analytics-id

# 认证配置
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-secret-key

# 第三方服务
N8N_WEBHOOK_URL=your-n8n-webhook-url
UPLOAD_SECRET_KEY=your-upload-secret
```

### 启动开发服务器
```bash
# 启动前端开发服务器
npm run dev

# 在另一个终端启动后端服务（如果需要）
npm run dev:api
```

访问 [http://localhost:3000](http://localhost:3000) 查看应用。

## 📁 项目结构

```
ygsjw-platform/
├── src/
│   ├── app/                          # Next.js 14 App Router
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── (dashboard)/              # 路由组
│   │   │   ├── layout.tsx
│   │   │   ├── resume/
│   │   │   ├── interview/
│   │   │   └── career/
│   │   └── api/                      # API 路由
│   ├── components/                   # 组件库
│   │   ├── ui/                      # 基础UI组件
│   │   ├── layout/                  # 布局组件
│   │   ├── features/                # 功能组件
│   │   └── common/                  # 通用组件
│   ├── hooks/                       # 自定义 Hooks
│   ├── lib/                         # 工具库
│   ├── stores/                      # Zustand 状态管理
│   ├── types/                       # TypeScript 类型定义
│   └── styles/                      # 样式文件
├── docs/                            # 项目文档
│   ├── architecture.md              # 系统架构文档
│   ├── api-specification.md         # API接口规范
│   └── deployment.md                # 部署文档
├── tests/                           # 测试文件
├── public/                          # 静态资源
└── ...配置文件
```

## 🛠️ 开发指南

### 代码规范
项目使用 ESLint + Prettier 进行代码格式化和规范检查：

```bash
# 检查代码规范
npm run lint

# 自动修复格式问题
npm run lint:fix

# 格式化代码
npm run format
```

### 测试
```bash
# 运行单元测试
npm run test

# 运行集成测试
npm run test:integration

# 运行端到端测试
npm run test:e2e

# 生成测试覆盖率报告
npm run test:coverage
```

### 构建
```bash
# 构建生产版本
npm run build

# 启动生产服务器
npm run start

# 分析打包大小
npm run analyze
```

## 📚 API文档

详细的API文档请参考：
- [API接口规范](./docs/api-specification.md)
- [系统架构设计](./docs/architecture.md)

### 主要API端点

#### 认证相关
```http
POST /api/v1/auth/register    # 用户注册
POST /api/v1/auth/login       # 用户登录
POST /api/v1/auth/refresh     # 刷新Token
```

#### 简历优化
```http
POST /api/v1/resume/upload    # 上传简历
POST /api/v1/resume/optimize  # 开始优化
GET  /api/v1/resume/result    # 获取结果
```

#### 模拟面试
```http
POST /api/v1/interview/start  # 开始面试
POST /api/v1/interview/answer # 提交回答
GET  /api/v1/interview/report # 获取报告
```

## 🚀 部署

### Vercel部署（推荐）
1. 将代码推送到GitHub
2. 在Vercel中导入项目
3. 配置环境变量
4. 自动部署完成

### 手动部署
```bash
# 构建项目
npm run build

# 部署到生产环境
npm run deploy
```

详细的部署指南请参考 [部署文档](./docs/deployment.md)。

## 🧪 测试策略

### 测试覆盖率目标
- 单元测试：> 80%
- 集成测试：> 70%
- E2E测试：关键用户流程100%

### 测试工具
- **单元测试**: Jest + React Testing Library
- **集成测试**: Jest + Supertest
- **E2E测试**: Playwright
- **视觉回归测试**: Chromatic

## 📊 性能指标

### Core Web Vitals目标
- **LCP** (Largest Contentful Paint): < 2.5秒
- **FID** (First Input Delay): < 100毫秒
- **CLS** (Cumulative Layout Shift): < 0.1
- **FCP** (First Contentful Paint): < 1.8秒

### 性能优化措施
- 代码分割和懒加载
- 图片优化（WebP、AVIF）
- 缓存策略
- CDN加速
- 服务端渲染

## 🔒 安全性

### 安全措施
- JWT Token认证
- 输入验证和清理
- XSS和CSRF防护
- 安全头部配置
- 文件上传安全检查

### 安全最佳实践
- 定期依赖更新
- 安全漏洞扫描
- 代码安全审查
- 最小权限原则

## 🤝 贡献指南

我们欢迎社区贡献！请遵循以下步骤：

1. Fork 项目
2. 创建功能分支 (`git checkout -b feature/amazing-feature`)
3. 提交更改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

### 提交信息规范
```
feat: 新功能
fix: 修复bug
docs: 文档更新
style: 代码格式调整
refactor: 代码重构
test: 测试相关
chore: 构建过程或辅助工具的变动
```

## 📄 许可证

本项目采用 [MIT 许可证](LICENSE)。

## 🙏 致谢

感谢以下开源项目：
- [Next.js](https://nextjs.org/) - React 框架
- [React](https://reactjs.org/) - UI 库
- [Tailwind CSS](https://tailwindcss.com/) - CSS 框架
- [Framer Motion](https://www.framer.com/motion/) - 动画库
- [Zustand](https://github.com/pmndrs/zustand) - 状态管理

## 📞 联系我们

- **网站**: [www.ygsjw.com](https://www.ygsjw.com)
- **邮箱**: contact@ygsjw.com
- **GitHub Issues**: [提交问题](https://github.com/your-org/ygsjw-platform/issues)
- **技术支持**: support@ygsjw.com

---

**⭐ 如果这个项目对你有帮助，请给我们一个 Star！**