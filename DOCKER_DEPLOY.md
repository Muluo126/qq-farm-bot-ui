# Docker Hub 部署指南

## 自动构建与推送

本项目已配置 GitHub Actions 工作流，可自动构建 Docker 镜像并推送到 Docker Hub。

## 配置步骤

### 1. 配置 Docker Hub 凭据

在 GitHub 仓库设置中添加以下 Secrets：

| Secret 名称 | 说明 |
|------------|------|
| `DOCKERHUB_USERNAME` | 你的 Docker Hub 用户名 |
| `DOCKERHUB_TOKEN` | Docker Hub 访问令牌（在 Docker Hub 的 Account Settings → Security 中生成） |

### 2. 触发构建

工作流会在以下情况自动触发：
- 推送到 `main` 或 `master` 分支
- 创建版本标签（如 `v1.0.0`）
- 发布 Release
- 手动触发（通过 Actions 页面）

### 3. 使用 Docker Hub 镜像

#### 使用 Docker Compose（推荐）

```bash
# 复制配置文件
cp docker-compose.dockerhub.yml docker-compose.yml

# 设置环境变量
export DOCKERHUB_USERNAME=你的用户名
export ADMIN_PASSWORD=你的强密码

# 启动服务
docker compose up -d
```

#### 使用 Docker 命令

```bash
# 拉取镜像
docker pull 你的用户名/qq-farm-bot-ui:latest

# 运行容器
docker run -d \
  --name qq-farm-bot \
  -p 3000:3000 \
  -e ADMIN_PASSWORD=你的强密码 \
  -e TZ=Asia/Shanghai \
  -v $(pwd)/data:/app/core/data \
  --restart unless-stopped \
  你的用户名/qq-farm-bot-ui:latest
```

## 镜像标签说明

| 标签 | 说明 |
|-----|------|
| `latest` | 最新稳定版（main/master 分支） |
| `v1.0.0` | 特定版本 |
| `v1.0` | 次要版本 |
| `v1` | 主要版本 |
| `sha-xxxx` | 提交哈希 |

## 工作流文件说明

### `.github/workflows/docker-build-push.yml`
- 多平台构建（linux/amd64, linux/arm64）
- 自动标签生成
- 构建缓存优化
- PR 时仅构建不推送

### `.github/workflows/docker-release.yml`
- Release 发布时触发
- 生成部署摘要
- 支持手动触发

## 手动构建并推送

如需本地构建并推送：

```bash
# 登录 Docker Hub
docker login

# 构建镜像
docker build -t 你的用户名/qq-farm-bot-ui:latest -f core/Dockerfile .

# 推送镜像
docker push 你的用户名/qq-farm-bot-ui:latest
```