# 中医本草纲目知识库系统

## 📋 项目概述

基于Go微服务架构和UniApp前端框架开发的中医本草纲目知识库系统，包含网站和小程序双端应用。系统提供知识点管理、搜索查询、后台管理等核心功能，并预留AI大模型集成接口，为中医爱好者和专业人士提供便捷的知识查询平台。

## 🏗️ 系统架构

### 整体架构图
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   UniApp前端    │    │   管理后台      │    │   小程序/H5     │
│   (Vue3 + TS)   │    │   (Vue3 + TS)   │    │   (UniApp)      │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │   API网关       │
                    │   (Go + Gin)    │
                    └─────────────────┘
                                 │
        ┌────────────────────────┼────────────────────────┐
        │                       │                        │
┌─────────────┐    ┌─────────────┐    ┌─────────────┐   ┌─────────────┐
│ 用户服务    │    │ 知识服务    │    │ 搜索服务    │   │ 文件服务    │
│user-service │    │know-service │    │search-service│   │file-service │
└─────────────┘    └─────────────┘    └─────────────┘   └─────────────┘
        │                       │                        │               │
        └───────────────────────┼────────────────────────┼───────────────┘
                                │                        │
                    ┌─────────────────┐      ┌─────────────────┐
                    │   MongoDB       │      │   Redis缓存     │
                    │   (分片集群)     │      │   (集群)        │
                    └─────────────────┘      └─────────────────┘
```

### 微服务架构

#### 1. API网关服务 (gateway-service)
- **技术栈**: Go + Gin + JWT
- **功能**: 
  - 统一路由管理
  - 身份认证与授权
  - 请求限流与熔断
  - 跨域处理
  - 日志记录

#### 2. 用户服务 (user-service)
- **技术栈**: Go + GORM + MongoDB
- **功能**:
  - 用户注册/登录
  - 权限管理（管理员/普通用户）
  - 用户信息管理
  - JWT Token管理

#### 3. 知识管理服务 (knowledge-service)
- **技术栈**: Go + MongoDB + Elasticsearch
- **功能**:
  - 知识点CRUD操作
  - 分类标签管理
  - 知识点关联关系
  - 内容版本控制

#### 4. 搜索服务 (search-service)
- **技术栈**: Go + Elasticsearch + Redis
- **功能**:
  - 全文搜索
  - 智能推荐
  - 搜索历史记录
  - 热门搜索统计

#### 5. 文件服务 (file-service)
- **技术栈**: Go + MinIO/OSS
- **功能**:
  - 图片上传管理
  - 文件存储优化
  - CDN集成
  - 图片压缩处理

#### 6. AI服务 (ai-service) *预留*
- **技术栈**: Go + Python + AI模型API
- **功能**:
  - 智能问答
  - 知识推荐
  - 内容生成
  - 语义搜索

## 🎯 功能模块

### 管理后台功能
- **用户管理**: 管理员账号、权限分配
- **知识管理**: 
  - 知识点录入/编辑/删除
  - 批量导入/导出
  - 分类标签管理
  - 内容审核
- **系统管理**:
  - 系统配置
  - 日志查看
  - 数据统计
  - 备份恢复

### 用户端功能
- **知识浏览**:
  - 分类浏览
  - 详情查看
  - 相关推荐
  - 收藏功能
- **搜索功能**:
  - 关键词搜索
  - 高级筛选
  - 搜索历史
  - 热门搜索
- **个人中心**:
  - 个人信息
  - 收藏记录
  - 浏览历史
  - 意见反馈

## 🗄️ 数据库设计

### MongoDB集合设计

#### 用户集合 (users)
```json
{
  "_id": "ObjectId",
  "username": "用户名",
  "email": "邮箱",
  "password": "加密密码",
  "role": "用户角色(admin/user)",
  "avatar": "头像URL",
  "created_at": "创建时间",
  "updated_at": "更新时间",
  "last_login": "最后登录时间",
  "status": "状态(active/inactive)"
}
```

#### 知识点集合 (knowledge_items)
```json
{
  "_id": "ObjectId",
  "title": "标题",
  "content": "详细内容",
  "summary": "摘要",
  "category_id": "分类ID",
  "tags": ["标签数组"],
  "images": ["图片URL数组"],
  "properties": {
    "nature": "性味",
    "meridian": "归经",
    "function": "功效",
    "usage": "用法用量",
    "contraindication": "禁忌"
  },
  "relations": ["关联知识点ID数组"],
  "source": "来源页码",
  "author_id": "录入人ID",
  "status": "状态(draft/published)",
  "view_count": "查看次数",
  "created_at": "创建时间",
  "updated_at": "更新时间"
}
```

#### 分类集合 (categories)
```json
{
  "_id": "ObjectId",
  "name": "分类名称",
  "description": "分类描述",
  "parent_id": "父分类ID",
  "sort_order": "排序",
  "icon": "图标",
  "created_at": "创建时间"
}
```

#### 标签集合 (tags)
```json
{
  "_id": "ObjectId",
  "name": "标签名称",
  "color": "标签颜色",
  "usage_count": "使用次数",
  "created_at": "创建时间"
}
```

#### 搜索历史集合 (search_history)
```json
{
  "_id": "ObjectId",
  "user_id": "用户ID",
  "keyword": "搜索关键词",
  "result_count": "结果数量",
  "created_at": "搜索时间"
}
```

#### 收藏集合 (favorites)
```json
{
  "_id": "ObjectId",
  "user_id": "用户ID",
  "knowledge_id": "知识点ID",
  "created_at": "收藏时间"
}
```

## 💻 技术栈

### 后端技术栈
- **语言**: Go 1.21+
- **框架**: Gin、Fiber
- **数据库**: MongoDB 6.0+
- **缓存**: Redis 7.0+
- **搜索**: Elasticsearch 8.0+
- **消息队列**: RabbitMQ
- **配置管理**: Viper
- **日志**: Logrus
- **容器**: Docker + Docker Compose
- **监控**: Prometheus + Grafana

### 前端技术栈
- **框架**: UniApp (Vue3 + TypeScript)
- **UI组件**: UniUI、ColorUI
- **状态管理**: Pinia
- **网络请求**: uni-app原生API
- **图表**: uCharts
- **富文本**: editor组件

### 管理后台技术栈
- **框架**: Vue3 + TypeScript
- **UI框架**: Element Plus
- **构建工具**: Vite
- **路由**: Vue Router 4
- **状态管理**: Pinia
- **富文本编辑器**: Quill.js

## 📱 部署架构

### 开发环境
```yaml
version: '3.8'
services:
  # API网关
  gateway:
    build: ./services/gateway
    ports:
      - "8080:8080"
    
  # 微服务
  user-service:
    build: ./services/user
    
  knowledge-service:
    build: ./services/knowledge
    
  search-service:
    build: ./services/search
    
  file-service:
    build: ./services/file
    
  # 数据库
  mongodb:
    image: mongo:6.0
    ports:
      - "27017:27017"
    
  redis:
    image: redis:7.0
    ports:
      - "6379:6379"
    
  elasticsearch:
    image: elasticsearch:8.0.0
    ports:
      - "9200:9200"
```

### 生产环境
- **容器编排**: Kubernetes
- **负载均衡**: Nginx + Ingress
- **服务发现**: Consul
- **配置中心**: Nacos
- **监控告警**: Prometheus + Grafana + AlertManager
- **日志收集**: ELK Stack
- **CI/CD**: GitLab CI/CD

## 🚀 项目结构

```
tcm-knowledge-base/
├── services/                    # 微服务目录
│   ├── gateway/                # API网关
│   ├── user/                   # 用户服务
│   ├── knowledge/              # 知识服务
│   ├── search/                 # 搜索服务
│   ├── file/                   # 文件服务
│   └── ai/                     # AI服务(预留)
├── web/
│   ├── admin/                  # 管理后台
│   └── user-app/               # UniApp前端
├── docs/                       # 文档目录
├── scripts/                    # 脚本目录
├── docker/                     # Docker配置
├── k8s/                        # Kubernetes配置
└── README.md
```

## 📋 开发计划

### 第一阶段 (4-6周)
- [x] 项目架构设计
- [ ] 基础微服务框架搭建
- [ ] 用户服务开发
- [ ] 知识管理服务开发
- [ ] 管理后台开发

### 第二阶段 (3-4周)
- [ ] 搜索服务开发
- [ ] UniApp前端开发
- [ ] 文件服务开发
- [ ] 系统集成测试

### 第三阶段 (2-3周)
- [ ] 性能优化
- [ ] 安全加固
- [ ] 部署上线
- [ ] 监控完善

### 第四阶段 (预留)
- [ ] AI服务集成
- [ ] 智能问答功能
- [ ] 推荐算法优化
- [ ] 移动端优化

## ⚙️ 本地开发环境搭建

### 环境要求
- Go 1.21+
- Node.js 18+
- MongoDB 6.0+
- Redis 7.0+
- Docker & Docker Compose

### 快速启动
```bash
# 1. 克隆项目
git clone https://github.com/your-org/tcm-knowledge-base.git
cd tcm-knowledge-base

# 2. 启动基础服务
docker-compose up -d mongodb redis elasticsearch

# 3. 启动后端服务
cd services
go mod tidy
make run-all

# 4. 启动管理后台
cd web/admin
npm install
npm run dev

# 5. 启动UniApp
cd web/user-app
npm install
npm run dev:h5
```

## 📊 性能指标

### 目标性能指标
- **响应时间**: API响应时间 < 200ms
- **并发量**: 支持1000+ QPS
- **可用性**: 99.9%
- **搜索性能**: 搜索响应时间 < 100ms

### 优化策略
- **缓存策略**: Redis多级缓存
- **数据库优化**: MongoDB索引优化
- **CDN加速**: 静态资源CDN
- **负载均衡**: 微服务负载均衡

## 🔒 安全设计

### 认证授权
- JWT Token认证
- RBAC权限控制
- OAuth2.0接入(预留)

### 数据安全
- 敏感信息加密存储
- API请求签名验证
- SQL注入防护
- XSS攻击防护

### 运维安全
- 容器安全扫描
- 依赖漏洞检测
- 访问日志审计
- 异常行为监控

## 📝 API文档

API文档采用Swagger/OpenAPI 3.0规范，提供交互式API文档。

访问地址: `http://localhost:8080/swagger/index.html`

## 🤝 贡献指南

1. Fork项目
2. 创建功能分支
3. 提交变更
4. 推送到分支
5. 创建Pull Request

## 📄 许可证

本项目采用MIT许可证，详见[LICENSE](LICENSE)文件。

## 📞 联系方式

- 项目负责人: [您的姓名]
- 邮箱: [您的邮箱]
- 项目地址: [GitHub地址]

---

**备注**: 本项目为中医知识普及和学习交流平台，不提供医疗诊断建议，如有健康问题请咨询专业医师。
