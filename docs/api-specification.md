# 月光石AI职业咨询平台 - API接口规范

## 概述

月光石AI职业咨询平台的API接口规范，定义了前后端数据交互的标准格式和协议。

### 基础信息
- **Base URL**: `https://api.ygsjw.com`
- **API版本**: `v1`
- **认证方式**: JWT Token
- **数据格式**: JSON
- **字符编码**: UTF-8

### 通用响应格式
```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: {
    code: string
    message: string
    details?: any
  }
  meta?: {
    timestamp: string
    requestId: string
    pagination?: {
      page: number
      limit: number
      total: number
      totalPages: number
    }
  }
}
```

### 错误码定义
```typescript
enum ErrorCode {
  // 通用错误 (1000-1999)
  INVALID_REQUEST = 'INVALID_REQUEST',
  UNAUTHORIZED = 'UNAUTHORIZED',
  FORBIDDEN = 'FORBIDDEN',
  NOT_FOUND = 'NOT_FOUND',
  INTERNAL_ERROR = 'INTERNAL_ERROR',
  RATE_LIMIT_EXCEEDED = 'RATE_LIMIT_EXCEEDED',

  // 认证错误 (2000-2999)
  INVALID_TOKEN = 'INVALID_TOKEN',
  TOKEN_EXPIRED = 'TOKEN_EXPIRED',
  INVALID_CREDENTIALS = 'INVALID_CREDENTIALS',

  // 文件上传错误 (3000-3999)
  FILE_TOO_LARGE = 'FILE_TOO_LARGE',
  INVALID_FILE_TYPE = 'INVALID_FILE_TYPE',
  UPLOAD_FAILED = 'UPLOAD_FAILED',

  // 业务逻辑错误 (4000-4999)
  RESUME_PARSE_FAILED = 'RESUME_PARSE_FAILED',
  OPTIMIZATION_FAILED = 'OPTIMIZATION_FAILED',
  INTERVIEW_NOT_STARTED = 'INTERVIEW_NOT_STARTED',
  INSUFFICIENT_CREDITS = 'INSUFFICIENT_CREDITS'
}
```

## 认证接口

### 用户注册
```http
POST /api/v1/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securePassword123",
  "name": "张三",
  "phone": "13800138000"
}
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "usr_1234567890",
      "email": "user@example.com",
      "name": "张三",
      "avatar": null,
      "subscription": "free",
      "createdAt": "2024-01-01T00:00:00Z"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "requestId": "req_1234567890"
  }
}
```

### 用户登录
```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

### 刷新Token
```http
POST /api/v1/auth/refresh
Authorization: Bearer <refresh_token>
```

### 用户登出
```http
POST /api/v1/auth/logout
Authorization: Bearer <access_token>
```

## 用户管理接口

### 获取用户信息
```http
GET /api/v1/users/me
Authorization: Bearer <access_token>
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "id": "usr_1234567890",
    "email": "user@example.com",
    "name": "张三",
    "avatar": "https://cdn.ygsjw.com/avatars/usr_1234567890.jpg",
    "subscription": "premium",
    "preferences": {
      "theme": "light",
      "language": "zh-CN",
      "notifications": {
        "email": true,
        "push": true,
        "career": false
      }
    },
    "usage": {
      "resumeOptimizations": 15,
      "interviewSimulations": 8,
      "remainingCredits": 25
    },
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-15T00:00:00Z"
  }
}
```

### 更新用户信息
```http
PUT /api/v1/users/me
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "name": "李四",
  "phone": "13900139000",
  "preferences": {
    "theme": "dark",
    "notifications": {
      "email": true,
      "push": false,
      "career": true
    }
  }
}
```

## 简历优化接口

### 文件上传
```http
POST /api/v1/resume/upload
Authorization: Bearer <access_token>
Content-Type: multipart/form-data

file: <resume_file.pdf>
targetRole: "前端开发工程师"
optimizationLevel: "advanced"
customInstructions: "希望突出React和TypeScript技能"
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "uploadId": "upload_1234567890",
    "fileInfo": {
      "originalName": "张三_前端开发_简历.pdf",
      "size": 1024000,
      "type": "application/pdf",
      "url": "https://cdn.ygsjw.com/uploads/upload_1234567890.pdf"
    },
    "parseResult": {
      "status": "success",
      "extractedText": "...",
      "structure": {
        "contact": {...},
        "education": [...],
        "experience": [...],
        "skills": [...]
      }
    }
  }
}
```

### 开始优化
```http
POST /api/v1/resume/optimize
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "uploadId": "upload_1234567890",
  "targetRole": "前端开发工程师",
  "optimizationLevel": "advanced",
  "focusAreas": ["技能匹配", "项目经验", "格式优化"],
  "customInstructions": "希望突出React和TypeScript技能"
}
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "optimizationId": "opt_1234567890",
    "status": "processing",
    "estimatedTime": 120,
    "webhookUrl": "https://api.ygsjw.com/webhook/n8n/resume-optimization"
  }
}
```

### 获取优化结果
```http
GET /api/v1/resume/optimization/{optimizationId}
Authorization: Bearer <access_token>
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "id": "opt_1234567890",
    "status": "completed",
    "originalResume": {
      "fileUrl": "https://cdn.ygsjw.com/resumes/original_1234567890.pdf",
      "parseResult": {...}
    },
    "optimizedResume": {
      "fileUrl": "https://cdn.ygsjw.com/resumes/optimized_1234567890.pdf",
      "previewUrl": "https://cdn.ygsjw.com/resumes/optimized_1234567890_preview.jpg"
    },
    "improvements": [
      {
        "type": "skill_match",
        "original": "熟悉React",
        "suggestion": "精通React，熟练使用Hooks、Context API",
        "reason": "目标职位要求React深度技能"
      },
      {
        "type": "experience_format",
        "original": "负责前端开发",
        "suggestion": "主导公司核心产品前端开发，使用React+TypeScript技术栈",
        "reason": "量化成果，突出技术栈"
      }
    ],
    "score": {
      "overall": 85,
      "skills": 90,
      "experience": 80,
      "format": 85,
      "keywords": 82
    },
    "metrics": {
      "processingTime": 108,
      "wordCount": 650,
      "readabilityScore": 75,
      "atsScore": 88
    },
    "createdAt": "2024-01-01T00:00:00Z",
    "completedAt": "2024-01-01T00:01:48Z"
  }
}
```

### 获取优化历史
```http
GET /api/v1/resume/history?page=1&limit=10
Authorization: Bearer <access_token>
```

## 模拟面试接口

### 开始面试
```http
POST /api/v1/interview/start
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "position": "前端开发工程师",
  "level": "mid",
  "companyType": "互联网",
  "focusAreas": ["技术能力", "项目经验", "沟通能力"],
  "duration": 30
}
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "sessionId": "interview_1234567890",
    "firstQuestion": {
      "id": "q_1",
      "type": "technical",
      "question": "请介绍一下你最熟悉的技术栈，并说明为什么选择它们？",
      "category": "技术基础",
      "difficulty": "easy",
      "estimatedAnswerTime": 120
    },
    "settings": {
      "totalDuration": 1800,
      "questionCount": 8,
      "allowSkip": true,
      "recordVideo": false
    }
  }
}
```

### 提交回答
```http
POST /api/v1/interview/{sessionId}/answer
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "questionId": "q_1",
  "answer": "我最熟悉的技术栈是React + TypeScript...",
  "answerType": "text",
  "duration": 95,
  "attachments": []
}
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "feedback": {
      "score": 85,
      "strengths": [
        "技术栈选择有明确理由",
        "体现了技术深度思考"
      ],
      "improvements": [
        "可以补充具体项目中的应用案例",
        "建议提及相关的生态系统工具"
      ],
      "nextQuestion": {
        "id": "q_2",
        "type": "project",
        "question": "请介绍一个你参与的最重要的项目，你在其中扮演什么角色？",
        "category": "项目经验"
      }
    },
    "sessionProgress": {
      "currentQuestion": 2,
      "totalQuestions": 8,
      "timeRemaining": 1620
    }
  }
}
```

### 获取面试报告
```http
GET /api/v1/interview/{sessionId}/report
Authorization: Bearer <access_token>
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "sessionId": "interview_1234567890",
    "overallScore": 82,
    "dimensionScores": {
      "technical": 85,
      "communication": 78,
      "problemSolving": 83,
      "experience": 82
    },
    "questionResults": [
      {
        "questionId": "q_1",
        "question": "请介绍一下你最熟悉的技术栈...",
        "answer": "我最熟悉的技术栈是React + TypeScript...",
        "score": 85,
        "feedback": {
          "strengths": [...],
          "improvements": [...]
        }
      }
    ],
    "recommendations": [
      "在技术深度方面表现优秀，建议继续保持",
      "在沟通表达方面可以更加简洁明了",
      "建议多准备项目相关的具体案例"
    ],
    "skillsAssessment": {
      "strongSkills": ["React", "TypeScript", "前端工程化"],
      "improvementAreas": ["项目沟通", "问题表达能力"],
      "recommendedLearning": ["GraphQL", "Node.js", "团队协作"]
    },
    "completedAt": "2024-01-01T00:30:00Z"
  }
}
```

## 职业规划接口

### 技能评估
```http
POST /api/v1/career/skills-assessment
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "targetPosition": "前端开发工程师",
  "currentSkills": [
    {"name": "JavaScript", "level": "advanced"},
    {"name": "React", "level": "intermediate"},
    {"name": "CSS", "level": "advanced"}
  ],
  "experienceYears": 3,
  "industry": "互联网"
}
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "assessmentId": "assessment_1234567890",
    "skillGap": {
      "missingSkills": [
        {"name": "TypeScript", "importance": "high", "currentLevel": "none"},
        {"name": "Next.js", "importance": "medium", "currentLevel": "none"}
      ],
      "strengtheningSkills": [
        {"name": "React", "currentLevel": "intermediate", "targetLevel": "advanced"}
      ]
    },
    "careerPath": {
      "currentLevel": "中级前端开发",
      "nextLevel": "高级前端开发",
      "estimatedTime": "12-18个月",
      "requirements": [
        "掌握TypeScript",
        "熟悉前端工程化",
        "具备架构设计能力"
      ]
    },
    "recommendations": [
      {
        "type": "learning",
        "title": "TypeScript深入学习",
        "description": "建议系统学习TypeScript，包括类型系统、泛型、装饰器等",
        "priority": "high",
        "estimatedDuration": "2-3个月"
      }
    ]
  }
}
```

### 获取职业路径建议
```http
GET /api/v1/career/path-suggestions?position=前端开发工程师&years=3
Authorization: Bearer <access_token>
```

### 薪资分析
```http
GET /api/v1/career/salary-analysis?position=前端开发工程师&city=北京&experience=3
Authorization: Bearer <access_token>
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "position": "前端开发工程师",
    "location": "北京",
    "experience": "3-5年",
    "salaryRange": {
      "min": 18000,
      "max": 35000,
      "median": 25000,
      "average": 24500
    },
    "marketData": {
      "sampleSize": 1250,
      "lastUpdated": "2024-01-01",
      "trend": "up",
      "growthRate": 0.08
    },
    "factors": [
      {
        "factor": "技术栈",
        "impact": "positive",
        "description": "React/Vue/TypeScript技能薪资溢价15-20%"
      },
      {
        "factor": "公司规模",
        "impact": "positive",
        "description": "大厂薪资普遍高于中小公司20-30%"
      }
    ],
    "recommendations": [
      "建议重点关注React生态和TypeScript技能",
      "可以考虑大厂或独角兽公司机会"
    ]
  }
}
```

## Webhook接口

### N8N工作流回调
```http
POST /api/webhook/n8n/resume-optimization
Content-Type: application/json
X-N8N-Signature: <signature>

{
  "workflowId": "wf_resume_optimization",
  "executionId": "exec_1234567890",
  "status": "completed",
  "data": {
    "optimizationId": "opt_1234567890",
    "result": {
      "optimizedResume": "...",
      "improvements": [...],
      "score": 85
    },
    "processingTime": 108
  },
  "timestamp": "2024-01-01T00:01:48Z"
}
```

### Webhook签名验证
```typescript
import crypto from 'crypto'

export function verifyWebhookSignature(payload: string, signature: string, secret: string): boolean {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex')

  return crypto.timingSafeEqual(
    Buffer.from(`sha256=${expectedSignature}`),
    Buffer.from(signature)
  )
}
```

## 系统接口

### 健康检查
```http
GET /api/health
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2024-01-01T00:00:00Z",
    "version": "1.0.0",
    "services": {
      "database": "healthy",
      "redis": "healthy",
      "n8n": "healthy",
      "storage": "healthy"
    },
    "metrics": {
      "uptime": 86400,
      "memoryUsage": 0.65,
      "cpuUsage": 0.25,
      "activeConnections": 125
    }
  }
}
```

### 系统统计
```http
GET /api/v1/system/stats
Authorization: Bearer <admin_token>
```

### 版本信息
```http
GET /api/v1/version
```

**响应示例:**
```json
{
  "success": true,
  "data": {
    "apiVersion": "1.0.0",
    "frontendVersion": "1.2.3",
    "buildNumber": "20240101-001",
    "deployedAt": "2024-01-01T00:00:00Z",
    "changelog": "修复简历优化bug，新增面试模拟功能"
  }
}
```

## 错误处理

### 错误响应格式
```json
{
  "success": false,
  "error": {
    "code": "RESUME_PARSE_FAILED",
    "message": "简历解析失败，请检查文件格式是否正确",
    "details": {
      "reason": "Unsupported file format",
      "supportedFormats": ["pdf", "doc", "docx"],
      "fileInfo": {
        "name": "resume.txt",
        "size": 1024,
        "type": "text/plain"
      }
    }
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "requestId": "req_1234567890"
  }
}
```

### 常见错误场景

#### 文件上传错误
```json
{
  "success": false,
  "error": {
    "code": "FILE_TOO_LARGE",
    "message": "文件大小超过限制",
    "details": {
      "maxSize": "5MB",
      "actualSize": "8MB"
    }
  }
}
```

#### 认证错误
```json
{
  "success": false,
  "error": {
    "code": "TOKEN_EXPIRED",
    "message": "Token已过期，请重新登录",
    "details": {
      "expiredAt": "2024-01-01T00:00:00Z"
    }
  }
}
```

#### 配额限制
```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "请求频率超过限制",
    "details": {
      "limit": 100,
      "window": "1小时",
      "retryAfter": 3600
    }
  }
}
```

## 请求限制

### 频率限制
- **免费用户**: 100 requests/hour
- **高级用户**: 1000 requests/hour
- **企业用户**: 10000 requests/hour

### 文件上传限制
- **文件大小**: 最大 5MB
- **支持格式**: PDF, DOC, DOCX
- **并发上传**: 每用户最多 3 个

### 简历优化限制
- **免费用户**: 5 次/月
- **高级用户**: 50 次/月
- **企业用户**: 无限制

### 面试模拟限制
- **免费用户**: 3 次/月
- **高级用户**: 30 次/月
- **企业用户**: 300 次/月

## SDK和客户端库

### JavaScript/TypeScript SDK
```typescript
import { YGSJWClient } from '@ygsjw/sdk'

const client = new YGSJWClient({
  baseURL: 'https://api.ygsjw.com',
  apiKey: 'your-api-key'
})

// 用户认证
await client.auth.login({
  email: 'user@example.com',
  password: 'password'
})

// 简历优化
const optimization = await client.resume.optimize({
  uploadId: 'upload_123',
  targetRole: '前端开发工程师',
  optimizationLevel: 'advanced'
})
```

### Python SDK
```python
from ygsjw import YGSJWClient

client = YGSJWClient(
    base_url='https://api.ygsjw.com',
    api_key='your-api-key'
)

# 用户认证
client.auth.login(
    email='user@example.com',
    password='password'
)

# 职业规划
career_path = client.career.get_path_suggestions(
    position='前端开发工程师',
    years=3
)
```

## 测试环境

### 测试API端点
- **Base URL**: `https://api-test.ygsjw.com`
- **认证**: 使用测试账号或Mock Token

### 测试数据
```json
{
  "testUser": {
    "email": "test@ygsjw.com",
    "password": "test123456"
  },
  "mockResponses": {
    "resumeOptimization": {
      "score": 85,
      "improvements": [...],
      "processingTime": 30
    }
  }
}
```

## API变更日志

### v1.2.0 (2024-01-15)
- 新增职业规划技能评估接口
- 优化简历优化算法
- 修复面试报告生成bug

### v1.1.0 (2024-01-01)
- 新增模拟面试功能
- 支持多文件批量上传
- 增加Webhook签名验证

### v1.0.0 (2023-12-01)
- 初始版本发布
- 基础简历优化功能
- 用户认证系统

## 技术支持

- **API文档**: https://docs.ygsjw.com/api
- **开发者社区**: https://community.ygsjw.com
- **技术支持邮箱**: api-support@ygsjw.com
- **问题反馈**: https://github.com/ygsjw/api-issues

---

本API规范文档持续更新，最新版本请访问：https://docs.ygsjw.com/api-specification