# GitOps 演示仓库设计

## 概述

为 ArgoCD + k3s 集群演示搭建的 GitOps 示例仓库。包含一个 Nginx 静态页面应用、Helm Chart、GitHub Actions CI 流水线和 ArgoCD 配置。

## 仓库结构

```
CICD-TEST/
├── .github/workflows/
│   └── ci.yaml              # CI: 构建镜像 → 推送到 GHCR → 更新 Helm values
├── app/                      # 应用源码
│   ├── Dockerfile            # Nginx 容器镜像
│   └── html/
│       └── index.html        # 演示页面
├── charts/demo-app/          # Helm Chart
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       ├── deployment.yaml
│       └── service.yaml
├── argocd/
│   └── application.yaml      # ArgoCD Application 定义
└── .gitignore
```

## 组件说明

### 1. 应用 (app/)

- Nginx 静态页面，展示应用名称、版本号、commit SHA 和部署时间
- Dockerfile 基于 nginx:alpine，体积小、启动快

### 2. Helm Chart (charts/demo-app/)

- `values.yaml`：可配置 replicaCount、image.repository、image.tag、service.type/port
- `deployment.yaml`：K8s Deployment，含资源限制、存活探针、滚动更新
- `service.yaml`：ClusterIP 类型 Service

### 3. CI 流水线 (.github/workflows/ci.yaml)

- 触发条件：push 到 main 分支
- 步骤：checkout → 登录 GHCR → 构建并推送镜像（tag 为 git commit SHA）→ 更新 values.yaml 中的 image.tag → 提交回仓库

### 4. ArgoCD 配置 (argocd/application.yaml)

- 定义 ArgoCD Application CR
- source：本仓库的 Helm Chart 路径
- destination：k3s 集群的 default 命名空间
- syncPolicy：自动同步

## 工作流

```
开发者推送代码 → GitHub Actions 构建新镜像 → 推送到 GHCR
                                      → 自动更新 values.yaml 中的 image.tag
                                           → ArgoCD 检测到 Git 变化
                                              → 自动同步到 k3s 集群
```

## 镜像仓库

使用 **GHCR**（GitHub Container Registry），免额外注册，Docker 镜像地址格式：
`ghcr.io/nivek-z/cicd-test:${GITHUB_SHA}`
