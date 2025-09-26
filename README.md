# CodeVibe - 现代化技术社区平台

<div align="center">

![CodeVibe Logo](product-doc/logo.jpg)

**为开发者打造的高质量技术分享社区**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.13.3-blue.svg)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-green.svg)](https://fastapi.tiangolo.com)
[![Vue.js](https://img.shields.io/badge/Vue.js-3.0+-green.svg)](https://vuejs.org)

</div>

## 📋 项目概述

**CodeVibe** (VibeCoding) 是一个现代化的技术问答与分享社区平台，专注为开发者提供高质量、高效率、沉浸式的技术交流和知识分享环境。项目采用微信小程序 + Web H5 的多端覆盖策略，致力于成为开发者首选的技术分享平台。

### 🎯 核心特色

- **🎨 极致体验**: 卡片式设计 + 平滑动效 + 沉浸式浏览
- **📝 高质量内容**: 完整 Markdown 支持 + 代码高亮 + 富文本编辑
- **💬 高效互动**: 强大的评论回复系统 + 实时交互反馈
- **📱 移动优先**: 基于 420x850px 手机模型的一致性设计
- **🔍 智能筛选**: 实时搜索 + 动态标签筛选

## 🚀 快速开始

### 环境要求

- Python 3.13.3+
- Node.js 16+
- MySQL 8.0+
- 微信开发者工具

### 安装部署

```bash
# 克隆项目
git clone https://github.com/your-username/CodeVibe_Qo.git
cd CodeVibe_Qo

# 后端环境
cd backend
pip install -r requirements.txt

# 前端环境
cd ../frontend
npm install

# 数据库初始化
mysql -u root -p < database/init.sql

# 启动服务
# 后端
cd backend && uvicorn main:app --reload

# 前端
cd frontend && npm run dev
