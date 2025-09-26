# CodeVibe 系统架构设计文档 v2.0

## 文档信息
- **版本**: v2.0
- **创建日期**: 2024年
- **项目**: CodeVibe (VibeCoding) 技术社区平台
- **架构原则**: KISS + SOLID + DRY
- **基于需求**: CodeVibe需求 v2.0.md

---

## 1. SPEC架构设计流程

### 1.1 Setting（场景分析）

#### 业务需求分析
```yaml
functional_requirements:
  core_features:
    - 社区首页: "卡片式信息流，CSS Scroll Snap滚动吸附"
    - 内容详情: "Markdown渲染，代码高亮，完整评论系统"
    - 发布内容: "Vditor编辑器，多媒体支持，标签系统"
    - 收藏管理: "搜索筛选，标签分类，快速访问"
    - 用户中心: "登录状态管理，个人资料，内容管理"
  
  interaction_features:
    - 评论回复: "Toast UI Editor，图片上传，Base64预览"
    - 点赞收藏: "实时交互，状态同步"
    - 内容分享: "多平台分享支持"
    - 搜索筛选: "标签筛选，内容搜索"

non_functional_requirements:
  performance:
    - response_time: "<200ms API响应时间"
    - concurrent_users: "支持1000+并发用户"
    - page_load: "<2s页面加载时间"
  
  user_experience:
    - mobile_first: "移动端优先设计"
    - smooth_animation: "平滑动效和过渡"
    - immersive_browsing: "沉浸式浏览体验"
  
  reliability:
    - availability: "99.9%系统可用性"
    - data_consistency: "强一致性数据保证"
    - error_handling: "优雅的错误处理"
```

#### 技术约束分析
```yaml
technical_constraints:
  architecture_principles:
    - KISS: "保持简单，避免过度设计"
    - SOLID: "面向对象设计原则"
    - DRY: "避免重复代码"
  
  technology_restrictions:
    - no_microservices: "不使用微服务架构"
    - no_redis: "不使用Redis缓存"
    - monolithic_approach: "采用模块化单体架构"
  
  platform_requirements:
    - wechat_miniprogram: "微信小程序原生开发"
    - web_h5: "Vue.js 3 + Tailwind CSS"
    - backend: "Python FastAPI + SQLAlchemy + MySQL"
```

### 1.2 Problem（问题定义）

#### 核心架构挑战
```yaml
core_challenges:
  scalability:
    problem: "如何在单体架构下支持高并发访问"
    target: "支持1000+并发用户，响应时间<200ms"
  
  performance:
    problem: "如何在不使用Redis的情况下保证性能"
    target: "页面加载<2s，API响应<200ms"
  
  maintainability:
    problem: "如何保持代码的可维护性和扩展性"
    target: "模块化设计，清晰的代码结构"
  
  user_experience:
    problem: "如何实现流畅的移动端交互体验"
    target: "平滑动效，响应式设计，沉浸式浏览"

success_criteria:
  performance_metrics:
    - "API响应时间 < 200ms"
    - "页面加载时间 < 2s"
    - "支持1000+并发用户"
  
  quality_metrics:
    - "代码覆盖率 > 80%"
    - "系统可用性 > 99.9%"
    - "用户满意度 > 4.5/5"
```

### 1.3 Evaluation（方案评估）

#### 架构方案对比
```yaml
option_1:
  name: "传统MVC单体架构"
  description: "经典的三层架构模式"
  pros:
    - "开发简单，学习成本低"
    - "部署简单，运维成本低"
    - "事务一致性容易保证"
  cons:
    - "模块耦合度较高"
    - "扩展性受限"
    - "代码组织不够清晰"
  risk_score: 4/10
  feasibility_score: 9/10
  maintainability_score: 6/10

option_2:
  name: "模块化单体架构"
  description: "基于领域驱动的模块化设计"
  pros:
    - "模块边界清晰"
    - "代码组织良好"
    - "便于团队协作"
    - "支持渐进式重构"
  cons:
    - "初期设计复杂度较高"
    - "需要良好的架构规范"
  risk_score: 5/10
  feasibility_score: 8/10
  maintainability_score: 9/10

option_3:
  name: "分层架构 + 领域模型"
  description: "DDD + 分层架构的混合模式"
  pros:
    - "业务逻辑清晰"
    - "领域模型丰富"
    - "扩展性好"
  cons:
    - "学习成本高"
    - "过度设计风险"
    - "开发周期长"
  risk_score: 7/10
  feasibility_score: 6/10
  maintainability_score: 8/10

evaluation_matrix:
  criteria: ["Performance", "Scalability", "Complexity", "Maintainability"]
  weights: [0.3, 0.2, 0.3, 0.2]
  scores:
    option_1: [7, 6, 9, 6]
    option_2: [8, 8, 7, 9]
    option_3: [9, 9, 5, 8]
  
  weighted_scores:
    option_1: 7.0
    option_2: 8.0
    option_3: 7.6
```

### 1.4 Conclusion（架构决策）

#### 最终架构选择
```yaml
selected_approach: "模块化单体架构 + 分层设计"
rationale:
  - "平衡了复杂性和可维护性"
  - "符合KISS原则，避免过度设计"
  - "支持团队协作和代码复用"
  - "为未来扩展预留空间"

implementation_strategy:
  phase_1: "核心模块设计和基础架构搭建"
  phase_2: "业务功能模块开发"
  phase_3: "性能优化和用户体验提升"
  phase_4: "监控运维和持续改进"
```

---

## 2. 系统整体架构

### 2.1 架构概览

```
┌─────────────────────────────────────────────────────────────┐
│                        客户端层                              │
├─────────────────────┬───────────────────┬───────────────────┤
│   微信小程序         │     Web H5        │    管理后台        │
│   (原生开发)         │   (Vue.js 3)      │   (Vue.js 3)      │
└─────────────────────┴───────────────────┴───────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      API网关层                               │
│                   (FastAPI Router)                         │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      业务逻辑层                              │
├─────────────┬─────────────┬─────────────┬─────────────────┤
│  用户模块    │  内容模块    │  互动模块    │    系统模块      │
│ (User)      │ (Content)   │(Interaction)│   (System)      │
└─────────────┴─────────────┴─────────────┴─────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      数据访问层                              │
│                 (SQLAlchemy ORM)                           │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      数据存储层                              │
│                    (MySQL 8.0)                             │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 模块划分

#### 核心业务模块
```python
# 用户模块 (User Module)
user/
├── models/          # 用户数据模型
├── services/        # 用户业务逻辑
├── controllers/     # 用户API控制器
└── schemas/         # 用户数据验证

# 内容模块 (Content Module)  
content/
├── models/          # 内容数据模型
├── services/        # 内容业务逻辑
├── controllers/     # 内容API控制器
└── schemas/         # 内容数据验证

# 互动模块 (Interaction Module)
interaction/
├── models/          # 互动数据模型
├── services/        # 互动业务逻辑
├── controllers/     # 互动API控制器
└── schemas/         # 互动数据验证

# 系统模块 (System Module)
system/
├── auth/           # 认证授权
├── cache/          # 缓存管理
├── storage/        # 文件存储
└── utils/          # 工具函数
```

### 2.3 技术栈选择

#### 后端技术栈
```yaml
framework: "FastAPI 0.100+"
orm: "SQLAlchemy 2.0"
database: "MySQL 8.0"
validation: "Pydantic v2"
authentication: "JWT + OAuth2"
file_storage: "本地文件系统 + 云存储"
testing: "pytest + httpx"
```

#### 前端技术栈
```yaml
web_framework: "Vue.js 3 + Composition API"
ui_framework: "Tailwind CSS"
state_management: "Pinia"
http_client: "Axios"
editor: "Vditor + Toast UI Editor"
build_tool: "Vite"
```

#### 微信小程序
```yaml
development: "原生小程序开发"
ui_components: "WeUI + 自定义组件"
state_management: "小程序原生状态管理"
http_client: "wx.request"
```

---

## 3. 数据库设计

### 3.1 核心数据模型

#### 用户表 (users)
```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    openid VARCHAR(64) UNIQUE NOT NULL COMMENT '微信openid',
    nickname VARCHAR(50) NOT NULL COMMENT '用户昵称',
    avatar_url VARCHAR(255) COMMENT '头像URL',
    phone VARCHAR(20) COMMENT '手机号',
    email VARCHAR(100) COMMENT '邮箱',
    bio TEXT COMMENT '个人简介',
    status TINYINT DEFAULT 1 COMMENT '状态：1-正常，0-禁用',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_openid (openid),
    INDEX idx_status (status),
    INDEX idx_created_at (created_at)
);
```

#### 内容表 (posts)
```sql
CREATE TABLE posts (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL COMMENT '作者ID',
    title VARCHAR(200) NOT NULL COMMENT '标题',
    content LONGTEXT NOT NULL COMMENT 'Markdown内容',
    content_html LONGTEXT COMMENT '渲染后的HTML',
    summary VARCHAR(500) COMMENT '摘要',
    cover_image VARCHAR(255) COMMENT '封面图片',
    post_type ENUM('text', 'image', 'video') DEFAULT 'text' COMMENT '内容类型',
    status ENUM('draft', 'published', 'deleted') DEFAULT 'published' COMMENT '状态',
    view_count INT DEFAULT 0 COMMENT '浏览数',
    like_count INT DEFAULT 0 COMMENT '点赞数',
    comment_count INT DEFAULT 0 COMMENT '评论数',
    favorite_count INT DEFAULT 0 COMMENT '收藏数',
    is_featured BOOLEAN DEFAULT FALSE COMMENT '是否精华',
    published_at TIMESTAMP NULL COMMENT '发布时间',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id),
    INDEX idx_user_id (user_id),
    INDEX idx_status (status),
    INDEX idx_published_at (published_at),
    INDEX idx_is_featured (is_featured),
    FULLTEXT idx_title_content (title, content)
);
```

#### 标签表 (tags)
```sql
CREATE TABLE tags (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) UNIQUE NOT NULL COMMENT '标签名',
    description VARCHAR(200) COMMENT '标签描述',
    color VARCHAR(7) DEFAULT '#4285F4' COMMENT '标签颜色',
    post_count INT DEFAULT 0 COMMENT '使用次数',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_name (name),
    INDEX idx_post_count (post_count)
);
```

#### 内容标签关联表 (post_tags)
```sql
CREATE TABLE post_tags (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    post_id BIGINT NOT NULL,
    tag_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE,
    FOREIGN KEY (tag_id) REFERENCES tags(id) ON DELETE CASCADE,
    UNIQUE KEY uk_post_tag (post_id, tag_id),
    INDEX idx_post_id (post_id),
    INDEX idx_tag_id (tag_id)
);
```

#### 评论表 (comments)
```sql
CREATE TABLE comments (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    post_id BIGINT NOT NULL COMMENT '内容ID',
    user_id BIGINT NOT NULL COMMENT '评论者ID',
    parent_id BIGINT NULL COMMENT '父评论ID',
    content TEXT NOT NULL COMMENT '评论内容',
    content_html TEXT COMMENT '渲染后的HTML',
    like_count INT DEFAULT 0 COMMENT '点赞数',
    reply_count INT DEFAULT 0 COMMENT '回复数',
    status ENUM('normal', 'deleted') DEFAULT 'normal' COMMENT '状态',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (parent_id) REFERENCES comments(id) ON DELETE CASCADE,
    INDEX idx_post_id (post_id),
    INDEX idx_user_id (user_id),
    INDEX idx_parent_id (parent_id),
    INDEX idx_created_at (created_at)
);
```

#### 互动表 (interactions)
```sql
CREATE TABLE interactions (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL COMMENT '用户ID',
    target_type ENUM('post', 'comment') NOT NULL COMMENT '目标类型',
    target_id BIGINT NOT NULL COMMENT '目标ID',
    action_type ENUM('like', 'favorite', 'share') NOT NULL COMMENT '操作类型',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id),
    UNIQUE KEY uk_user_target_action (user_id, target_type, target_id, action_type),
    INDEX idx_user_id (user_id),
    INDEX idx_target (target_type, target_id),
    INDEX idx_action_type (action_type)
);
```

### 3.2 数据库优化策略

#### 索引优化
```sql
-- 复合索引优化查询性能
CREATE INDEX idx_posts_user_status_time ON posts(user_id, status, published_at);
CREATE INDEX idx_comments_post_status_time ON comments(post_id, status, created_at);
CREATE INDEX idx_interactions_user_action ON interactions(user_id, action_type, created_at);

-- 全文索引支持内容搜索
ALTER TABLE posts ADD FULLTEXT(title, content);
ALTER TABLE comments ADD FULLTEXT(content);
```

#### 分区策略
```sql
-- 按时间分区优化大表查询
ALTER TABLE posts PARTITION BY RANGE (YEAR(created_at)) (
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p2025 VALUES LESS THAN (2026),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
```

---

## 4. 缓存策略设计

### 4.1 应用内存缓存实现

由于不使用Redis，采用应用内存缓存 + 数据库优化的策略：

#### 缓存层设计
```python
# cache/memory_cache.py
from typing import Any, Optional, Dict
from datetime import datetime, timedelta
import threading
from dataclasses import dataclass

@dataclass
class CacheItem:
    value: Any
    expire_time: datetime
    hit_count: int = 0

class MemoryCache:
    def __init__(self, max_size: int = 1000, default_ttl: int = 300):
        self._cache: Dict[str, CacheItem] = {}
        self._max_size = max_size
        self._default_ttl = default_ttl
        self._lock = threading.RLock()
    
    def get(self, key: str) -> Optional[Any]:
        with self._lock:
            if key not in self._cache:
                return None
            
            item = self._cache[key]
            if datetime.now() > item.expire_time:
                del self._cache[key]
                return None
            
            item.hit_count += 1
            return item.value
    
    def set(self, key: str, value: Any, ttl: Optional[int] = None) -> None:
        with self._lock:
            if len(self._cache) >= self._max_size:
                self._evict_lru()
            
            expire_time = datetime.now() + timedelta(seconds=ttl or self._default_ttl)
            self._cache[key] = CacheItem(value, expire_time)
    
    def _evict_lru(self) -> None:
        # 移除最少使用的缓存项
        if not self._cache:
            return
        
        lru_key = min(self._cache.keys(), 
                     key=lambda k: self._cache[k].hit_count)
        del self._cache[lru_key]

# 全局缓存实例
cache = MemoryCache(max_size=2000, default_ttl=600)
```

#### 缓存使用策略
```python
# services/cache_service.py
from typing import List, Optional
from cache.memory_cache import cache
from models.post import Post

class CacheService:
    @staticmethod
    def get_hot_posts() -> Optional[List[Post]]:
        """获取热门内容缓存"""
        return cache.get("hot_posts")
    
    @staticmethod
    def set_hot_posts(posts: List[Post], ttl: int = 300) -> None:
        """设置热门内容缓存"""
        cache.set("hot_posts", posts, ttl)
    
    @staticmethod
    def get_user_profile(user_id: int) -> Optional[dict]:
        """获取用户资料缓存"""
        return cache.get(f"user_profile:{user_id}")
    
    @staticmethod
    def set_user_profile(user_id: int, profile: dict, ttl: int = 600) -> None:
        """设置用户资料缓存"""
        cache.set(f"user_profile:{user_id}", profile, ttl)
    
    @staticmethod
    def invalidate_user_cache(user_id: int) -> None:
        """清除用户相关缓存"""
        cache.delete(f"user_profile:{user_id}")
        cache.delete(f"user_posts:{user_id}")
```

### 4.2 数据库查询优化

#### 查询优化策略
```python
# services/post_service.py
from sqlalchemy.orm import Session, selectinload
from sqlalchemy import select, func, desc
from models.post import Post
from models.user import User
from models.tag import Tag

class PostService:
    @staticmethod
    def get_hot_posts(db: Session, limit: int = 20) -> List[Post]:
        """获取热门内容，优化查询"""
        # 检查缓存
        cached_posts = CacheService.get_hot_posts()
        if cached_posts:
            return cached_posts
        
        # 数据库查询，使用预加载优化N+1问题
        stmt = (
            select(Post)
            .options(
                selectinload(Post.author),
                selectinload(Post.tags),
                selectinload(Post.comments).selectinload(Comment.author)
            )
            .where(Post.status == 'published')
            .order_by(desc(Post.like_count + Post.comment_count * 2))
            .limit(limit)
        )
        
        posts = db.execute(stmt).scalars().all()
        
        # 设置缓存
        CacheService.set_hot_posts(posts, ttl=300)
        return posts
```

---

## 5. API设计规范

### 5.1 RESTful API设计

#### API路由结构
```python
# routers/api_v1.py
from fastapi import APIRouter
from routers import auth, users, posts, comments, interactions

api_router = APIRouter(prefix="/api/v1")

# 认证相关
api_router.include_router(auth.router, prefix="/auth", tags=["认证"])

# 用户相关
api_router.include_router(users.router, prefix="/users", tags=["用户"])

# 内容相关  
api_router.include_router(posts.router, prefix="/posts", tags=["内容"])

# 评论相关
api_router.include_router(comments.router, prefix="/comments", tags=["评论"])

# 互动相关
api_router.include_router(interactions.router, prefix="/interactions", tags=["互动"])
```

#### 核心API端点
```python
# routers/posts.py
from fastapi import APIRouter, Depends, Query, Path
from typing import List, Optional
from schemas.post import PostCreate, PostUpdate, PostResponse, PostListResponse
from services.post_service import PostService
from dependencies.auth import get_current_user

router = APIRouter()

@router.get("/", response_model=PostListResponse)
async def get_posts(
    page: int = Query(1, ge=1, description="页码"),
    size: int = Query(20, ge=1, le=100, description="每页数量"),
    tag: Optional[str] = Query(None, description="标签筛选"),
    user_id: Optional[int] = Query(None, description="用户筛选"),
    featured: Optional[bool] = Query(None, description="是否精华"),
    db: Session = Depends(get_db)
):
    """获取内容列表"""
    return await PostService.get_posts(
        db=db, page=page, size=size, 
        tag=tag, user_id=user_id, featured=featured
    )

@router.get("/{post_id}", response_model=PostResponse)
async def get_post(
    post_id: int = Path(..., description="内容ID"),
    db: Session = Depends(get_db)
):
    """获取内容详情"""
    return await PostService.get_post_by_id(db=db, post_id=post_id)

@router.post("/", response_model=PostResponse)
async def create_post(
    post_data: PostCreate,
    current_user: User = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """创建内容"""
    return await PostService.create_post(
        db=db, post_data=post_data, user_id=current_user.id
    )

@router.put("/{post_id}", response_model=PostResponse)
async def update_post(
    post_id: int = Path(..., description="内容ID"),
    post_data: PostUpdate,
    current_user: User = Depends(get_current_user),
    db: Session = Depends(get_db)
):
    """更新内容"""
    return await PostService.update_post(
        db=db, post_id=post_id, post_data=post_data, user_id=current_user.id
    )
```

### 5.2 统一响应格式

#### 响应数据结构
```python
# schemas/base.py
from pydantic import BaseModel
from typing import Generic, TypeVar, Optional, Any

T = TypeVar('T')

class ApiResponse(BaseModel, Generic[T]):
    """统一API响应格式"""
    code: int = 200
    message: str = "success"
    data: Optional[T] = None
    timestamp: int
    request_id: str

class PaginationMeta(BaseModel):
    """分页元数据"""
    page: int
    size: int
    total: int
    pages: int
    has_next: bool
    has_prev: bool

class PaginatedResponse(BaseModel, Generic[T]):
    """分页响应格式"""
    items: List[T]
    meta: PaginationMeta
```

#### 错误处理
```python
# exceptions/handlers.py
from fastapi import Request, HTTPException
from fastapi.responses import JSONResponse
from fastapi.exceptions import RequestValidationError
import logging

logger = logging.getLogger(__name__)

async def http_exception_handler(request: Request, exc: HTTPException):
    """HTTP异常处理"""
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "code": exc.status_code,
            "message": exc.detail,
            "data": None,
            "timestamp": int(time.time()),
            "request_id": request.headers.get("X-Request-ID", "")
        }
    )

async def validation_exception_handler(request: Request, exc: RequestValidationError):
    """参数验证异常处理"""
    return JSONResponse(
        status_code=422,
        content={
            "code": 422,
            "message": "参数验证失败",
            "data": {"errors": exc.errors()},
            "timestamp": int(time.time()),
            "request_id": request.headers.get("X-Request-ID", "")
        }
    )
```

---

## 6. 安全架构设计

### 6.1 认证授权机制

#### JWT认证实现
```python
# auth/jwt_handler.py
from datetime import datetime, timedelta
from typing import Optional
import jwt
from passlib.context import CryptContext
from config.settings import settings

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

class JWTHandler:
    @staticmethod
    def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
        """创建访问令牌"""
        to_encode = data.copy()
        if expires_delta:
            expire = datetime.utcnow() + expires_delta
        else:
            expire = datetime.utcnow() + timedelta(minutes=settings.ACCESS_TOKEN_EXPIRE_MINUTES)
        
        to_encode.update({"exp": expire})
        encoded_jwt = jwt.encode(to_encode, settings.SECRET_KEY, algorithm=settings.ALGORITHM)
        return encoded_jwt
    
    @staticmethod
    def verify_token(token: str) -> Optional[dict]:
        """验证令牌"""
        try:
            payload = jwt.decode(token, settings.SECRET_KEY, algorithms=[settings.ALGORITHM])
            return payload
        except jwt.PyJWTError:
            return None
```

#### 微信登录集成
```python
# auth/wechat_auth.py
import httpx
from typing import Optional
from config.settings import settings

class WeChatAuth:
    @staticmethod
    async def get_user_info(code: str) -> Optional[dict]:
        """通过code获取用户信息"""
        # 获取access_token
        token_url = "https://api.weixin.qq.com/sns/oauth2/access_token"
        token_params = {
            "appid": settings.WECHAT_APP_ID,
            "secret": settings.WECHAT_APP_SECRET,
            "code": code,
            "grant_type": "authorization_code"
        }
        
        async with httpx.AsyncClient() as client:
            token_response = await client.get(token_url, params=token_params)
            token_data = token_response.json()
            
            if "access_token" not in token_data:
                return None
            
            # 获取用户信息
            user_url = "https://api.weixin.qq.com/sns/userinfo"
            user_params = {
                "access_token": token_data["access_token"],
                "openid": token_data["openid"]
            }
            
            user_response = await client.get(user_url, params=user_params)
            return user_response.json()
```

### 6.2 数据安全保护

#### 敏感数据加密
```python
# security/encryption.py
from cryptography.fernet import Fernet
from config.settings import settings

class DataEncryption:
    def __init__(self):
        self.cipher = Fernet(settings.ENCRYPTION_KEY.encode())
    
    def encrypt(self, data: str) -> str:
        """加密数据"""
        return self.cipher.encrypt(data.encode()).decode()
    
    def decrypt(self, encrypted_data: str) -> str:
        """解密数据"""
        return self.cipher.decrypt(encrypted_data.encode()).decode()

# 全局加密实例
encryption = DataEncryption()
```

#### SQL注入防护
```python
# 使用SQLAlchemy ORM和参数化查询防止SQL注入
from sqlalchemy import text

# 错误示例（容易SQL注入）
# query = f"SELECT * FROM users WHERE name = '{user_input}'"

# 正确示例（参数化查询）
stmt = text("SELECT * FROM users WHERE name = :name")
result = db.execute(stmt, {"name": user_input})
```

### 6.3 访问控制

#### 权限装饰器
```python
# auth/permissions.py
from functools import wraps
from fastapi import HTTPException, Depends
from dependencies.auth import get_current_user

def require_permission(permission: str):
    """权限检查装饰器"""
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            current_user = kwargs.get('current_user')
            if not current_user or not current_user.has_permission(permission):
                raise HTTPException(status_code=403, detail="权限不足")
            return await func(*args, **kwargs)
        return wrapper
    return decorator

# 使用示例
@router.delete("/{post_id}")
@require_permission("delete_post")
async def delete_post(
    post_id: int,
    current_user: User = Depends(get_current_user)
):
    """删除内容"""
    pass
```

---

## 7. 性能优化策略

### 7.1 数据库性能优化

#### 查询优化
```python
# 使用索引优化查询
class PostService:
    @staticmethod
    def get_posts_with_optimization(db: Session, filters: dict) -> List[Post]:
        """优化的内容查询"""
        query = db.query(Post)
        
        # 使用索引字段进行筛选
        if filters.get('user_id'):
            query = query.filter(Post.user_id == filters['user_id'])
        
        if filters.get('status'):
            query = query.filter(Post.status == filters['status'])
        
        # 使用复合索引排序
        query = query.order_by(Post.published_at.desc())
        
        # 预加载关联数据，避免N+1问题
        query = query.options(
            selectinload(Post.author),
            selectinload(Post.tags),
            selectinload(Post.comments).selectinload(Comment.author)
        )
        
        return query.limit(20).all()
```

#### 连接池优化
```python
# config/database.py
from sqlalchemy import create_engine
from sqlalchemy.pool import QueuePool

engine = create_engine(
    DATABASE_URL,
    poolclass=QueuePool,
    pool_size=20,          # 连接池大小
    max_overflow=30,       # 最大溢出连接数
    pool_pre_ping=True,    # 连接前检查
    pool_recycle=3600,     # 连接回收时间
    echo=False             # 生产环境关闭SQL日志
)
```

### 7.2 应用层性能优化

#### 异步处理
```python
# services/async_service.py
import asyncio
from typing import List
from concurrent.futures import ThreadPoolExecutor

class AsyncService:
    def __init__(self):
        self.executor = ThreadPoolExecutor(max_workers=10)
    
    async def process_images_async(self, images: List[str]) -> List[str]:
        """异步处理图片"""
        loop = asyncio.get_event_loop()
        tasks = [
            loop.run_in_executor(self.executor, self._process_single_image, img)
            for img in images
        ]
        return await asyncio.gather(*tasks)
    
    def _process_single_image(self, image_path: str) -> str:
        """处理单个图片（CPU密集型任务）"""
        # 图片处理逻辑
        pass
```

#### 响应压缩
```python
# middleware/compression.py
from fastapi import FastAPI
from fastapi.middleware.gzip import GZipMiddleware

app = FastAPI()
app.add_middleware(GZipMiddleware, minimum_size=1000)
```

### 7.3 前端性能优化

#### 组件懒加载
```javascript
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: () => import('../views/Home.vue') // 懒加载
  },
  {
    path: '/post/:id',
    name: 'PostDetail',
    component: () => import('../views/PostDetail.vue')
  }
]
```

#### 图片懒加载
```vue
<!-- components/LazyImage.vue -->
<template>
  <div class="lazy-image-container">
    <img
      v-if="loaded"
      :src="src"
      :alt="alt"
      @load="onLoad"
      @error="onError"
    />
    <div v-else class="loading-placeholder">
      <div class="spinner"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps(['src', 'alt'])
const loaded = ref(false)

onMounted(() => {
  const observer = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting) {
      loaded.value = true
      observer.disconnect()
    }
  })
  
  observer.observe(document.querySelector('.lazy-image-container'))
})
</script>
```

---

## 8. 部署架构设计

### 8.1 容器化部署

#### Docker配置
```dockerfile
# Dockerfile
FROM python:3.13.3-slim

WORKDIR /app

# 安装系统依赖
RUN apt-get update && apt-get install -y \
    gcc \
    default-libmysqlclient-dev \
    && rm -rf /var/lib/apt/lists/*

# 安装Python依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 复制应用代码
COPY . .

# 暴露端口
EXPOSE 8000

# 启动命令
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

#### Docker Compose配置
```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=mysql+pymysql://codevibe:password@db:3306/codevibe
      - SECRET_KEY=${SECRET_KEY}
    depends_on:
      - db
    volumes:
      - ./uploads:/app/uploads
    restart: unless-stopped

  db:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_DATABASE=codevibe
      - MYSQL_USER=codevibe
      - MYSQL_PASSWORD=password
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - app
    restart: unless-stopped

volumes:
  mysql_data:
```

### 8.2 Nginx配置

```nginx
# nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream app_servers {
        server app:8000;
    }

    server {
        listen 80;
        server_name codevibe.com;
        return 301 https://$server_name$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name codevibe.com;

        ssl_certificate /etc/nginx/ssl/cert.pem;
        ssl_certificate_key /etc/nginx/ssl/key.pem;

        # 静态文件
        location /static/ {
            alias /app/static/;
            expires 30d;
            add_header Cache-Control "public, immutable";
        }

        location /uploads/ {
            alias /app/uploads/;
            expires 7d;
        }

        # API代理
        location /api/ {
            proxy_pass http://app_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # 前端应用
        location / {
            try_files $uri $uri/ /index.html;
            root /app/dist;
        }
    }
}
```

### 8.3 部署脚本

```bash
#!/bin/bash
# deploy.sh

set -e

echo "开始部署 CodeVibe..."

# 拉取最新代码
git pull origin main

# 构建前端
echo "构建前端应用..."
cd frontend
npm install
npm run build
cd ..

# 构建并启动容器
echo "构建并启动容器..."
docker-compose down
docker-compose build --no-cache
docker-compose up -d

# 等待服务启动
echo "等待服务启动..."
sleep 30

# 运行数据库迁移
echo "运行数据库迁移..."
docker-compose exec app alembic upgrade head

# 健康检查
echo "进行健康检查..."
if curl -f http://localhost:8000/health; then
    echo "部署成功！"
else
    echo "部署失败，请检查日志"
    docker-compose logs
    exit 1
fi
```

---

## 9. 监控与运维

### 9.1 应用监控

#### 健康检查端点
```python
# routers/health.py
from fastapi import APIRouter, Depends
from sqlalchemy.orm import Session
from database.connection import get_db
from datetime import datetime

router = APIRouter()

@router.get("/health")
async def health_check(db: Session = Depends(get_db)):
    """健康检查"""
    try:
        # 检查数据库连接
        db.execute("SELECT 1")
        
        return {
            "status": "healthy",
            "timestamp": datetime.utcnow().isoformat(),
            "version": "2.0.0",
            "database": "connected"
        }
    except Exception as e:
        return {
            "status": "unhealthy",
            "timestamp": datetime.utcnow().isoformat(),
            "error": str(e)
        }
```

#### 性能监控
```python
# middleware/monitoring.py
import time
import logging
from fastapi import Request, Response
from starlette.middleware.base import BaseHTTPMiddleware

logger = logging.getLogger(__name__)

class MonitoringMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        start_time = time.time()
        
        response = await call_next(request)
        
        process_time = time.time() - start_time
        
        # 记录慢查询
        if process_time > 1.0:
            logger.warning(
                f"Slow request: {request.method} {request.url} "
                f"took {process_time:.2f}s"
            )
        
        # 添加响应头
        response.headers["X-Process-Time"] = str(process_time)
        
        return response
```

### 9.2 日志管理

#### 日志配置
```python
# config/logging.py
import logging
from logging.handlers import RotatingFileHandler
import os

def setup_logging():
    """配置日志"""
    # 创建日志目录
    os.makedirs("logs", exist_ok=True)
    
    # 配置根日志器
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
        handlers=[
            # 控制台输出
            logging.StreamHandler(),
            # 文件输出（自动轮转）
            RotatingFileHandler(
                "logs/app.log",
                maxBytes=10*1024*1024,  # 10MB
                backupCount=5
            )
        ]
    )
    
    # 设置第三方库日志级别
    logging.getLogger("uvicorn").setLevel(logging.WARNING)
    logging.getLogger("sqlalchemy.engine").setLevel(logging.WARNING)
```

### 9.3 数据库运维

#### 备份策略
```bash
#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_NAME="codevibe"

# 创建备份目录
mkdir -p $BACKUP_DIR

# 数据库备份
docker-compose exec db mysqldump -u root -prootpassword $DB_NAME > $BACKUP_DIR/db_backup_$DATE.sql

# 压缩备份文件
gzip $BACKUP_DIR/db_backup_$DATE.sql

# 删除7天前的备份
find $BACKUP_DIR -name "db_backup_*.sql.gz" -mtime +7 -delete

echo "数据库备份完成: db_backup_$DATE.sql.gz"
```

#### 性能监控SQL
```sql
-- 慢查询监控
SELECT 
    query_time,
    lock_time,
    rows_sent,
    rows_examined,
    sql_text
FROM mysql.slow_log 
WHERE start_time > DATE_SUB(NOW(), INTERVAL 1 HOUR)
ORDER BY query_time DESC;

-- 表大小监控
SELECT 
    table_name,
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)'
FROM information_schema.tables 
WHERE table_schema = 'codevibe'
ORDER BY (data_length + index_length) DESC;
```

---

## 10. 扩展性设计

### 10.1 水平扩展策略

#### 读写分离
```python
# database/read_write_split.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

class DatabaseManager:
    def __init__(self):
        # 主库（写）
        self.write_engine = create_engine(WRITE_DATABASE_URL)
        self.WriteSession = sessionmaker(bind=self.write_engine)
        
        # 从库（读）
        self.read_engine = create_engine(READ_DATABASE_URL)
        self.ReadSession = sessionmaker(bind=self.read_engine)
    
    def get_write_session(self):
        """获取写数据库会话"""
        return self.WriteSession()
    
    def get_read_session(self):
        """获取读数据库会话"""
        return self.ReadSession()

# 依赖注入
def get_write_db():
    db = db_manager.get_write_session()
    try:
        yield db
    finally:
        db.close()

def get_read_db():
    db = db_manager.get_read_session()
    try:
        yield db
    finally:
        db.close()
```

#### 负载均衡配置
```nginx
# nginx负载均衡
upstream app_servers {
    server app1:8000 weight=3;
    server app2:8000 weight=2;
    server app3:8000 weight=1;
    
    # 健康检查
    keepalive 32;
}

server {
    location /api/ {
        proxy_pass http://app_servers;
        proxy_next_upstream error timeout invalid_header http_500;
    }
}
```

### 10.2 垂直扩展策略

#### 数据库分表
```python
# models/sharding.py
from sqlalchemy import Column, Integer, String, Text, DateTime
from database.base import Base

class PostShard1(Base):
    __tablename__ = 'posts_shard_1'
    # 表结构同posts表

class PostShard2(Base):
    __tablename__ = 'posts_shard_2'
    # 表结构同posts表

class ShardingManager:
    @staticmethod
    def get_shard_table(user_id: int):
        """根据用户ID获取分片表"""
        shard_num = user_id % 2
        if shard_num == 0:
            return PostShard1
        else:
            return PostShard2
```

### 10.3 缓存扩展

#### 多级缓存策略
```python
# cache/multi_level_cache.py
from typing import Any, Optional
from cache.memory_cache import MemoryCache
from cache.file_cache import FileCache

class MultiLevelCache:
    def __init__(self):
        self.l1_cache = MemoryCache(max_size=1000, default_ttl=300)  # L1: 内存缓存
        self.l2_cache = FileCache(max_size=10000, default_ttl=3600)  # L2: 文件缓存
    
    def get(self, key: str) -> Optional[Any]:
        """多级缓存获取"""
        # 先查L1缓存
        value = self.l1_cache.get(key)
        if value is not None:
            return value
        
        # 再查L2缓存
        value = self.l2_cache.get(key)
        if value is not None:
            # 回写到L1缓存
            self.l1_cache.set(key, value)
            return value
        
        return None
    
    def set(self, key: str, value: Any, ttl: Optional[int] = None) -> None:
        """多级缓存设置"""
        self.l1_cache.set(key, value, ttl)
        self.l2_cache.set(key, value, ttl)
```

---

## 11. 风险评估与应对

### 11.1 技术风险

#### 风险识别与应对
```yaml
performance_risks:
  risk_1:
    description: "单体架构性能瓶颈"
    probability: "中等"
    impact: "高"
    mitigation:
      - "数据库查询优化"
      - "应用内存缓存"
      - "异步处理优化"
      - "预留水平扩展方案"
  
  risk_2:
    description: "数据库连接池耗尽"
    probability: "低"
    impact: "高"
    mitigation:
      - "连接池大小优化"
      - "连接泄漏监控"
      - "慢查询优化"
      - "读写分离"

security_risks:
  risk_1:
    description: "用户数据泄露"
    probability: "低"
    impact: "极高"
    mitigation:
      - "敏感数据加密"
      - "访问权限控制"
      - "安全审计日志"
      - "定期安全扫描"
  
  risk_2:
    description: "API接口滥用"
    probability: "中等"
    impact: "中等"
    mitigation:
      - "接口限流"
      - "用户认证"
      - "异常监控"
      - "IP黑名单"
```

### 11.2 业务风险

#### 业务连续性保障
```yaml
availability_risks:
  risk_1:
    description: "服务器宕机"
    probability: "低"
    impact: "高"
    mitigation:
      - "多实例部署"
      - "健康检查"
      - "自动重启"
      - "监控告警"
  
  risk_2:
    description: "数据库故障"
    probability: "低"
    impact: "极高"
    mitigation:
      - "主从复制"
      - "定期备份"
      - "故障转移"
      - "数据恢复演练"
```

### 11.3 应急预案

#### 故障处理流程
```python
# monitoring/alert_handler.py
import logging
from typing import Dict, Any
from datetime import datetime

class AlertHandler:
    def __init__(self):
        self.logger = logging.getLogger(__name__)
    
    async def handle_database_error(self, error: Exception) -> Dict[str, Any]:
        """数据库故障处理"""
        self.logger.error(f"Database error: {error}")
        
        # 1. 记录故障信息
        incident = {
            "type": "database_error",
            "timestamp": datetime.utcnow(),
            "error": str(error),
            "severity": "high"
        }
        
        # 2. 尝试自动恢复
        recovery_success = await self._attempt_database_recovery()
        
        # 3. 发送告警通知
        if not recovery_success:
            await self._send_alert_notification(incident)
        
        return incident
    
    async def _attempt_database_recovery(self) -> bool:
        """尝试数据库自动恢复"""
        try:
            # 重新建立连接
            # 检查数据库状态
            # 执行简单查询测试
            return True
        except Exception:
            return False
```

---

## 12. 总结

### 12.1 架构优势

#### 技术优势
- **简单可靠**: 遵循KISS原则，架构简洁明了，降低复杂性
- **高性能**: 通过数据库优化、内存缓存、异步处理保证性能
- **易维护**: 模块化设计，清晰的代码结构，便于团队协作
- **安全可靠**: 完善的认证授权、数据加密、访问控制机制

#### 业务优势
- **快速开发**: 成熟技术栈，开发效率高
- **成本可控**: 单体架构，部署运维成本低
- **用户体验**: 移动端优先，流畅的交互体验
- **扩展灵活**: 预留扩展接口，支持业务增长

### 12.2 技术亮点

#### 创新设计
- **CSS Scroll Snap**: 实现"一划一张"的沉浸式浏览体验
- **多级缓存**: 内存+文件的多级缓存策略，无Redis依赖
- **模块化架构**: 清晰的模块边界，支持独立开发和测试
- **异步优化**: 合理使用异步处理，提升并发性能

#### 工程实践
- **统一规范**: API设计、错误处理、日志记录的统一规范
- **自动化**: Docker容器化、自动部署、健康检查
- **监控完善**: 性能监控、日志管理、告警机制
- **安全保障**: 多层次的安全防护措施

### 12.3 扩展路径

#### 短期优化（3-6个月）
- 性能监控和优化
- 用户体验改进
- 功能模块完善
- 安全加固

#### 中期演进（6-12个月）
- 读写分离实施
- 缓存策略优化
- 搜索功能增强
- 移动端优化

#### 长期规划（1-2年）
- 微服务化评估
- 大数据分析
- AI功能集成
- 国际化支持

### 12.4 关键指标

#### 性能指标
- **响应时间**: API响应 < 200ms，页面加载 < 2s
- **并发能力**: 支持1000+并发用户
- **可用性**: 系统可用性 > 99.9%
- **扩展性**: 支持10倍数据量增长

#### 质量指标
- **代码质量**: 测试覆盖率 > 80%，代码规范100%
- **安全性**: 零安全漏洞，数据加密100%
- **维护性**: 模块化程度 > 90%，文档完整性 > 95%
- **用户满意度**: 用户评分 > 4.5/5，性能满意度 > 90%

---

**文档版本**: v2.0  
**最后更新**: 2024年  
**架构师**: CodeVibe架构团队  
**审核状态**: 已通过技术评审

本架构设计文档为CodeVibe技术社区平台提供了完整的技术蓝图，遵循KISS、SOLID、DRY原则，在保证系统简洁性的同时，确保了高性能、高可用性和良好的扩展性。