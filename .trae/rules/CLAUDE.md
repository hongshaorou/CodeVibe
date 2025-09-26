# CLAUDE.md - 项目开发规范与AI协作指南

## 项目概述

本项目采用SPEC决策模型驱动的多Agent协作开发模式，严格遵循软件工程最佳实践。

## 技术栈

### 核心技术栈
* **后端**: Python 3.13.3, FastAPI, SQLAlchemy, MySQL, JWT, Pydantic
* **前端 (Web/H5)**: Vue.js 3, Vue Router, Axios, Tailwind CSS  
* **微信小程序**: 原生小程序开发
* **原型**: 纯 HTML, Tailwind CSS (CDN), 原生 JavaScript

### 开发工具
* **版本控制**: Git
* **包管理**: npm/yarn (前端), pip/poetry (后端)
* **代码格式化**: Prettier (前端), Black (后端)
* **代码检查**: ESLint (前端), Pylint/Flake8 (后端)
* **测试框架**: Vitest (前端), Pytest (后端)

## 项目结构

```
.
├── backend/                    # Python FastAPI 后端
│   ├── app/
│   │   ├── api/               # API 路由端点
│   │   │   ├── v1/           # API 版本 1
│   │   │   │   ├── auth.py   # 认证相关接口
│   │   │   │   ├── users.py  # 用户管理接口
│   │   │   │   └── ...
│   │   │   └── deps.py       # API 依赖项
│   │   ├── core/             # 核心配置
│   │   │   ├── config.py     # 配置管理
│   │   │   ├── security.py   # 安全相关
│   │   │   └── database.py   # 数据库配置
│   │   ├── models/           # SQLAlchemy 模型
│   │   │   ├── user.py       # 用户模型
│   │   │   └── ...
│   │   ├── schemas/          # Pydantic 模式
│   │   │   ├── user.py       # 用户数据模式
│   │   │   └── ...
│   │   ├── services/         # 业务逻辑层
│   │   │   ├── auth.py       # 认证服务
│   │   │   └── ...
│   │   ├── utils/            # 工具函数
│   │   └── main.py          # 应用入口
│   ├── tests/                # 测试文件
│   │   ├── unit/            # 单元测试
│   │   └── integration/     # 集成测试
│   ├── alembic/             # 数据库迁移
│   ├── requirements.txt      # 依赖列表
│   └── .env.example         # 环境变量示例
│
├── frontend/                 # Vue 3 前端
│   ├── src/
│   │   ├── api/            # API 请求模块
│   │   │   ├── auth.js     # 认证 API
│   │   │   └── request.js  # Axios 配置
│   │   ├── assets/         # 静态资源
│   │   ├── components/     # Vue 组件
│   │   │   ├── common/     # 通用组件
│   │   │   └── ...
│   │   ├── composables/    # 组合式函数
│   │   ├── layouts/        # 布局组件
│   │   ├── router/         # 路由配置
│   │   ├── stores/         # Pinia 状态管理
│   │   ├── styles/         # 全局样式
│   │   ├── utils/          # 工具函数
│   │   ├── views/          # 页面视图
│   │   ├── App.vue         # 根组件
│   │   └── main.js         # 入口文件
│   ├── public/             # 公共文件
│   ├── tests/              # 测试文件
│   ├── package.json        # 依赖配置
│   ├── vite.config.js      # Vite 配置
│   └── tailwind.config.js  # Tailwind 配置
│
├── miniprogram/            # 微信小程序
│   ├── pages/             # 页面文件
│   │   └── index/         # 首页
│   │       ├── index.js
│   │       ├── index.json
│   │       ├── index.wxml
│   │       └── index.wxss
│   ├── components/        # 组件
│   ├── utils/            # 工具函数
│   ├── api/              # API 请求
│   ├── app.js            # 小程序入口
│   ├── app.json          # 小程序配置
│   └── app.wxss          # 全局样式
│
├── prototype/             # HTML 原型
│   ├── index.html        # 首页原型
│   ├── css/             # 样式文件
│   ├── js/              # JavaScript 文件
│   └── assets/          # 静态资源
│
├── docs/                  # 项目文档
│   ├── api/              # API 文档
│   ├── architecture/     # 架构设计
│   ├── product/          # 产品文档
│   └── testing/          # 测试文档
│
├── scripts/              # 脚本工具
├── docker/              # Docker 配置
├── .github/             # GitHub Actions
├── CLAUDE.md            # AI 协作规范（本文档）
├── project_rules.md     # 项目规则
└── README.md           # 项目说明
```

## 开发规范

### Python 后端规范

#### FastAPI 路由定义
```python
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from typing import List, Optional
from app.api import deps
from app.schemas.user import UserCreate, UserUpdate, UserResponse
from app.services import user as user_service

router = APIRouter(prefix="/users", tags=["users"])

@router.post("/", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
async def create_user(
    *,
    db: Session = Depends(deps.get_db),
    user_in: UserCreate,
    current_user: User = Depends(deps.get_current_active_superuser)
) -> UserResponse:
    """
    创建新用户
    
    - **email**: 用户邮箱（唯一）
    - **password**: 密码（最少8位）
    - **full_name**: 用户全名
    """
    user = await user_service.create(db=db, obj_in=user_in)
    return UserResponse.from_orm(user)
```

#### Pydantic 模式定义
```python
from pydantic import BaseModel, EmailStr, Field, validator
from typing import Optional
from datetime import datetime

class UserBase(BaseModel):
    email: EmailStr
    full_name: Optional[str] = Field(None, min_length=1, max_length=100)
    is_active: bool = True

class UserCreate(UserBase):
    password: str = Field(..., min_length=8, max_length=100)
    
    @validator('password')
    def validate_password(cls, v):
        if not any(char.isdigit() for char in v):
            raise ValueError('密码必须包含至少一个数字')
        if not any(char.isupper() for char in v):
            raise ValueError('密码必须包含至少一个大写字母')
        return v

class UserResponse(UserBase):
    id: int
    created_at: datetime
    updated_at: Optional[datetime]
    
    class Config:
        from_attributes = True
```

#### SQLAlchemy 模型定义
```python
from sqlalchemy import Column, Integer, String, Boolean, DateTime
from sqlalchemy.sql import func
from app.core.database import Base

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String(255), unique=True, index=True, nullable=False)
    hashed_password = Column(String(255), nullable=False)
    full_name = Column(String(100))
    is_active = Column(Boolean, default=True)
    is_superuser = Column(Boolean, default=False)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
```

#### 服务层实现
```python
from typing import Optional, List
from sqlalchemy.orm import Session
from app.models.user import User
from app.schemas.user import UserCreate, UserUpdate
from app.core.security import get_password_hash, verify_password

class UserService:
    def create(self, db: Session, *, obj_in: UserCreate) -> User:
        """创建用户 - 遵循单一职责原则"""
        db_obj = User(
            email=obj_in.email,
            hashed_password=get_password_hash(obj_in.password),
            full_name=obj_in.full_name,
            is_active=obj_in.is_active,
        )
        db.add(db_obj)
        db.commit()
        db.refresh(db_obj)
        return db_obj
    
    def authenticate(self, db: Session, *, email: str, password: str) -> Optional[User]:
        """用户认证"""
        user = db.query(User).filter(User.email == email).first()
        if not user:
            return None
        if not verify_password(password, user.hashed_password):
            return None
        return user

user_service = UserService()
```

### Vue 3 前端规范

#### 组件定义（Composition API）
```vue
<template>
  <div class="user-profile">
    <div v-if="loading" class="flex justify-center p-4">
      <LoadingSpinner />
    </div>
    <div v-else-if="error" class="text-red-500 p-4">
      {{ error }}
    </div>
    <div v-else class="p-4">
      <h1 class="text-2xl font-bold mb-4">{{ user?.fullName }}</h1>
      <form @submit.prevent="handleSubmit" class="space-y-4">
        <div>
          <label class="block text-sm font-medium mb-1">邮箱</label>
          <input
            v-model="formData.email"
            type="email"
            class="w-full px-3 py-2 border rounded-lg"
            :disabled="!isEditing"
          />
        </div>
        <button
          type="submit"
          class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
        >
          保存
        </button>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed } from 'vue'
import { useRoute } from 'vue-router'
import { useUserStore } from '@/stores/user'
import { getUserProfile, updateUserProfile } from '@/api/user'
import LoadingSpinner from '@/components/common/LoadingSpinner.vue'

// Props
const props = defineProps({
  userId: {
    type: String,
    required: true
  }
})

// Emits
const emit = defineEmits(['update:user', 'save'])

// State
const loading = ref(false)
const error = ref(null)
const isEditing = ref(false)
const user = ref(null)
const formData = reactive({
  email: '',
  fullName: ''
})

// Composables
const route = useRoute()
const userStore = useUserStore()

// Computed
const isCurrentUser = computed(() => {
  return userStore.currentUser?.id === props.userId
})

// Methods
const fetchUser = async () => {
  loading.value = true
  error.value = null
  try {
    const response = await getUserProfile(props.userId)
    user.value = response.data
    Object.assign(formData, response.data)
  } catch (err) {
    error.value = err.message || '获取用户信息失败'
  } finally {
    loading.value = false
  }
}

const handleSubmit = async () => {
  try {
    const response = await updateUserProfile(props.userId, formData)
    user.value = response.data
    emit('update:user', response.data)
    isEditing.value = false
  } catch (err) {
    error.value = err.message || '更新失败'
  }
}

// Lifecycle
onMounted(() => {
  fetchUser()
})
</script>
```

#### API 请求模块
```javascript
// api/request.js
import axios from 'axios'
import { useAuthStore } from '@/stores/auth'

const request = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL || '/api/v1',
  timeout: 10000,
})

// 请求拦截器
request.interceptors.request.use(
  (config) => {
    const authStore = useAuthStore()
    if (authStore.token) {
      config.headers.Authorization = `Bearer ${authStore.token}`
    }
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

// 响应拦截器
request.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      const authStore = useAuthStore()
      authStore.logout()
    }
    return Promise.reject(error)
  }
)

export default request
```

#### Pinia 状态管理
```javascript
// stores/user.js
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { login, logout, refreshToken } from '@/api/auth'

export const useAuthStore = defineStore('auth', () => {
  // State
  const user = ref(null)
  const token = ref(localStorage.getItem('token'))
  
  // Getters
  const isAuthenticated = computed(() => !!token.value)
  const userRole = computed(() => user.value?.role || 'guest')
  
  // Actions
  async function loginUser(credentials) {
    try {
      const response = await login(credentials)
      token.value = response.token
      user.value = response.user
      localStorage.setItem('token', response.token)
      return response
    } catch (error) {
      throw error
    }
  }
  
  function logoutUser() {
    token.value = null
    user.value = null
    localStorage.removeItem('token')
  }
  
  return {
    user,
    token,
    isAuthenticated,
    userRole,
    login: loginUser,
    logout: logoutUser
  }
})
```

### 微信小程序规范

#### 页面结构
```javascript
// pages/user/profile.js
Page({
  data: {
    userInfo: null,
    loading: false,
    error: null
  },
  
  onLoad(options) {
    this.setData({ loading: true })
    this.fetchUserInfo(options.id)
  },
  
  async fetchUserInfo(userId) {
    try {
      const res = await wx.request({
        url: `${getApp().globalData.apiUrl}/users/${userId}`,
        header: {
          'Authorization': `Bearer ${wx.getStorageSync('token')}`
        }
      })
      this.setData({
        userInfo: res.data,
        loading: false
      })
    } catch (error) {
      this.setData({
        error: error.message,
        loading: false
      })
    }
  },
  
  handleEdit() {
    wx.navigateTo({
      url: `/pages/user/edit?id=${this.data.userInfo.id}`
    })
  }
})
```

### HTML 原型规范

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>用户登录</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100">
  <div class="min-h-screen flex items-center justify-center">
    <div class="bg-white p-8 rounded-lg shadow-md w-96">
      <h1 class="text-2xl font-bold mb-6 text-center">用户登录</h1>
      <form id="loginForm" class="space-y-4">
        <div>
          <label class="block text-sm font-medium mb-1">邮箱</label>
          <input
            type="email"
            id="email"
            required
            class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
          />
        </div>
        <div>
          <label class="block text-sm font-medium mb-1">密码</label>
          <input
            type="password"
            id="password"
            required
            class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
          />
        </div>
        <button
          type="submit"
          class="w-full py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
        >
          登录
        </button>
      </form>
    </div>
  </div>
  
  <script>
    document.getElementById('loginForm').addEventListener('submit', async (e) => {
      e.preventDefault()
      const email = document.getElementById('email').value
      const password = document.getElementById('password').value
      
      try {
        const response = await fetch('/api/v1/auth/login', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ email, password })
        })
        const data = await response.json()
        if (response.ok) {
          localStorage.setItem('token', data.token)
          window.location.href = '/dashboard.html'
        } else {
          alert(data.message || '登录失败')
        }
      } catch (error) {
        alert('网络错误，请稍后重试')
      }
    })
  </script>
</body>
</html>
```

## AI 协作规范

### 1. 代码生成原则

#### SPEC 决策模型
所有技术决策必须遵循：
- **Setting**: 明确场景和约束
- **Problem**: 准确定义问题
- **Evaluation**: 评估多种方案
- **Conclusion**: 选择最优方案

#### 三大编码原则
- **KISS**: 保持简单，函数不超过20行
- **SOLID**: 遵循面向对象设计原则
- **DRY**: 避免重复代码

### 2. Agent 协作流程

```mermaid
graph TD
    A[Product Manager] -->|需求文档| B[Architecture]
    B -->|API设计| C[Test Engineer]
    C -->|Mock数据| D[Frontend Developer]
    C -->|测试用例| E[Backend Developer]
    D -->|UI实现| F[Integration]
    E -->|API实现| F
    F -->|集成测试| G[DevOps]
    G -->|部署上线| H[完成]
```

### 3. 文档规范

所有文档必须包含：
- 版本号（v{major}.{minor}.{patch}）
- 更新日期
- 作者/负责人
- SPEC 决策记录

### 4. 测试要求

| 测试类型 | 覆盖率 | 工具 |
|---------|--------|------|
| 单元测试 | ≥90% | Vitest/Pytest |
| 集成测试 | ≥80% | Supertest |
| E2E测试 | 核心流程100% | Playwright |

### 5. 性能标准

- API响应时间: P95 < 200ms
- 页面加载时间: < 3秒
- 数据库查询: < 100ms
- 并发用户数: > 1000

## 安全规范

### 必须遵守的安全原则

1. **密钥管理**: 使用环境变量，禁止硬编码
2. **输入验证**: 所有输入必须验证和清理
3. **认证授权**: JWT Token，有效期24小时
4. **密码加密**: 使用 bcrypt，salt rounds ≥ 10
5. **SQL防护**: 使用 ORM，禁止字符串拼接
6. **XSS防护**: 自动转义，设置 CSP
7. **CSRF防护**: 使用 CSRF Token
8. **限流保护**: 100 请求/分钟

### JWT 实现示例

```python
# core/security.py
from datetime import datetime, timedelta
from jose import JWTError, jwt
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

SECRET_KEY = os.getenv("SECRET_KEY")
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 1440  # 24 hours

def create_access_token(subject: str) -> str:
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode = {"exp": expire, "sub": str(subject)}
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)
```

## Git 工作流

### 分支策略
- `main`: 生产环境代码
- `develop`: 开发环境代码
- `feature/*`: 功能开发
- `bugfix/*`: Bug 修复
- `hotfix/*`: 紧急修复

### Commit 规范
```
<type>(<scope>): <subject>

<body>

<footer>
```

类型：
- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更新
- `style`: 代码格式
- `refactor`: 重构
- `test`: 测试
- `chore`: 构建/工具

### PR 检查清单
- [ ] 代码通过 lint 检查
- [ ] 测试全部通过
- [ ] 更新了相关文档
- [ ] 没有安全隐患
- [ ] 性能符合标准

## 部署流程

### 环境配置
```bash
# .env.production
DATABASE_URL=mysql://user:pass@localhost/dbname
REDIS_URL=redis://localhost:6379
SECRET_KEY=your-secret-key-here
JWT_SECRET=your-jwt-secret
API_URL=https://api.example.com
```

### Docker 部署
```dockerfile
# backend/Dockerfile
FROM python:3.13.3-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### CI/CD Pipeline
```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: |
          cd backend && pytest
          cd ../frontend && npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          # 部署脚本
```

## 监控与日志

### 日志规范
```python
import logging
from app.core.config import settings

logging.basicConfig(
    level=settings.LOG_LEVEL,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)

# 使用示例
logger.info(f"User {user_id} logged in successfully")
logger.error(f"Failed to authenticate user {email}: {error}")
```

### 监控指标
- 系统: CPU、内存、磁盘、网络
- 应用: QPS、响应时间、错误率
- 业务: 活跃用户、转化率、留存率

## 故障处理

### 优先级定义
- P0: 服务完全不可用，立即处理
- P1: 核心功能受影响，30分钟内响应
- P2: 部分功能异常，2小时内处理
- P3: 体验问题，下个版本修复

### 回滚方案
1. 数据库回滚脚本准备
2. 代码版本快速切换
3. 配置回滚机制
4. 缓存清理方案

## 最佳实践

### 数据库优化
```python
# 使用索引
class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True)
    email = Column(String(255), unique=True, index=True)  # 添加索引
    created_at = Column(DateTime, index=True)  # 时间字段索引
```

### API 分页
```python
@router.get("/", response_model=Page[UserResponse])
async def list_users(
    skip: int = Query(0, ge=0),
    limit: int = Query(10, ge=1, le=100),
    db: Session = Depends(deps.get_db)
):
    total = db.query(User).count()
    users = db.query(User).offset(skip).limit(limit).all()
    return {
        "total": total,
        "items": users,
        "skip": skip,
        "limit": limit
    }
```

### 缓存策略
```python
from functools import lru_cache
import redis

redis_client = redis.Redis.from_url(settings.REDIS_URL)

def cache_key_wrapper(prefix: str, ttl: int = 300):
    def decorator(func):
        async def wrapper(*args, **kwargs):
            key = f"{prefix}:{':'.join(map(str, args))}"
            cached = redis_client.get(key)
            if cached:
                return json.loads(cached)
            result = await func(*args, **kwargs)
            redis_client.setex(key, ttl, json.dumps(result))
            return result
        return wrapper
    return decorator

@cache_key_wrapper("user", ttl=600)
async def get_user_by_id(user_id: int):
    # 数据库查询
    pass
```

## 团队协作

### Code Review 要点
1. 功能完整性
2. 代码可读性
3. 性能考虑
4. 安全检查
5. 测试覆盖
6. 文档完整

### 会议规范
- Daily Standup: 每日 15 分钟
- Sprint Planning: 每 2 周
- Sprint Review: Sprint 结束
- Retrospective: 持续改进

## 参考资源

### 官方文档
- [FastAPI](https://fastapi.tiangolo.com/)
- [Vue.js 3](https://vuejs.org/)
- [Tailwind CSS](https://tailwindcss.com/)
- [微信小程序](https://developers.weixin.qq.com/miniprogram/dev/)

### 内部文档
- [产品设计](./docs/product/)
- [架构设计](./docs/architecture/)
- [API 文档](./docs/api/)
- [测试报告](./docs/testing/)

---

**版本**: v1.0.0  
**更新日期**: 2025-09-15  
**维护团队**: 技术架构组

*本文档为 AI 协作开发的核心规范，所有开发必须严格遵守。*