# GitOps 演示仓库实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 创建 GitOps 演示仓库，包含 Nginx 应用、Helm Chart、ArgoCD 配置和 GitHub Actions CI

**Architecture:** 应用代码在 app/ 目录，Helm Chart 在 charts/demo-app/，ArgoCD Application 配置在 argocd/。CI 流水线在推送 main 时自动构建 Docker 镜像推送到 GHCR 并更新 Helm values。

**Tech Stack:** Nginx Alpine, Docker, Helm, ArgoCD, GitHub Actions, GHCR

---

### Task 1: 初始化 Git 仓库

- [ ] **Step 1: 初始化 git**

```bash
git init
git remote add origin git@github.com:Nivek-Z/CICD-TEST.git
```

### Task 2: 创建 .gitignore

- [ ] **Step 1: 创建 .gitignore 文件**

写入 `C:\Users\30475\Desktop\CICD-TEST\.gitignore`:

```
# OS
.DS_Store
Thumbs.db

# IDE
.idea/
.vscode/
*.swp
*.swo

# Docker
*.log

# Helm
charts/**/charts/
charts/**/*.tgz

# Temp
tmp/
*.tmp
```

- [ ] **Step 2: 暂存并提交**

```bash
git add .gitignore
git commit -m "chore: add .gitignore"
```

### Task 3: 创建应用源代码

- [ ] **Step 1: 创建 app/html/index.html**

写入 `C:\Users\30475\Desktop\CICD-TEST\app\html\index.html`:

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GitOps Demo</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
            color: #fff;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .card {
            background: rgba(255,255,255,0.08);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255,255,255,0.12);
            border-radius: 20px;
            padding: 48px 56px;
            max-width: 540px;
            text-align: center;
            box-shadow: 0 25px 50px rgba(0,0,0,0.4);
        }
        h1 { font-size: 28px; margin-bottom: 8px; letter-spacing: 1px; }
        .badge {
            display: inline-block;
            background: #60a5fa;
            color: #fff;
            font-size: 12px;
            padding: 4px 14px;
            border-radius: 20px;
            margin-bottom: 24px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }
        .info-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
            margin: 24px 0;
            text-align: left;
        }
        .info-item {
            background: rgba(255,255,255,0.06);
            border-radius: 10px;
            padding: 12px 16px;
        }
        .info-item .label { font-size: 11px; text-transform: uppercase; opacity: 0.5; letter-spacing: 0.5px; }
        .info-item .value { font-size: 16px; font-weight: 600; margin-top: 4px; }
        .status {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            margin-top: 20px;
            font-size: 14px;
            opacity: 0.7;
        }
        .status .dot {
            width: 8px; height: 8px; border-radius: 50%;
            background: #4ade80;
            animation: pulse 2s infinite;
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.4; }
        }
    </style>
</head>
<body>
    <div class="card">
        <div class="badge">GitOps Demo</div>
        <h1>Hello from ArgoCD + k3s!</h1>
        <p style="opacity:0.6; margin-bottom:8px;">Nginx static site deployed via GitOps</p>
        <div class="info-grid">
            <div class="info-item">
                <div class="label">Version</div>
                <div class="value" id="version">1.0.0</div>
            </div>
            <div class="info-item">
                <div class="label">Commit</div>
                <div class="value" id="commit"><code>{{COMMIT_SHA}}</code></div>
            </div>
            <div class="info-item">
                <div class="label">Deployed</div>
                <div class="value" id="deployed">{{DEPLOY_TIME}}</div>
            </div>
            <div class="info-item">
                <div class="label">Namespace</div>
                <div class="value" id="namespace">{{NAMESPACE}}</div>
            </div>
        </div>
        <div class="status">
            <span class="dot"></span>
            Running on k3s cluster &middot; Synchronized via ArgoCD
        </div>
    </div>
</body>
</html>
```

- [ ] **Step 2: 创建 app/Dockerfile**

写入 `C:\Users\30475\Desktop\CICD-TEST\app\Dockerfile`:

```dockerfile
FROM nginx:alpine
COPY html /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- [ ] **Step 3: 创建 app/nginx.conf**

写入 `C:\Users\30475\Desktop\CICD-TEST\app\nginx.conf`:

```nginx
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html;
    }
}
```

- [ ] **Step 4: 暂存并提交**

```bash
git add app/
git commit -m "feat: add nginx static app with Dockerfile"
```

### Task 4: 创建 Helm Chart

- [ ] **Step 1: 创建 charts/demo-app/Chart.yaml**

写入 `C:\Users\30475\Desktop\CICD-TEST\charts\demo-app\Chart.yaml`:

```yaml
apiVersion: v2
name: demo-app
description: GitOps demo application Helm chart
type: application
version: 0.1.0
appVersion: "1.0.0"
```

- [ ] **Step 2: 创建 charts/demo-app/values.yaml**

写入 `C:\Users\30475\Desktop\CICD-TEST\charts\demo-app\values.yaml`:

```yaml
replicaCount: 2

image:
  repository: ghcr.io/nivek-z/cicd-test
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

resources:
  limits:
    cpu: 200m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 64Mi

autoscaling:
  enabled: false
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 80
```

- [ ] **Step 3: 创建 charts/demo-app/templates/deployment.yaml**

写入 `C:\Users\30475\Desktop\CICD-TEST\charts\demo-app\templates\deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "demo-app.fullname" . }}
  labels:
    app: {{ include "demo-app.name" . }}
    chart: {{ .Chart.Name }}-{{ .Chart.Version }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "demo-app.name" . }}
      release: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ include "demo-app.name" . }}
        release: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - containerPort: 80
              name: http
          livenessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 5
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /
              port: http
            initialDelaySeconds: 3
            periodSeconds: 5
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

- [ ] **Step 4: 创建 charts/demo-app/templates/service.yaml**

写入 `C:\Users\30475\Desktop\CICD-TEST\charts\demo-app\templates\service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "demo-app.fullname" . }}
  labels:
    app: {{ include "demo-app.name" . }}
    chart: {{ .Chart.Name }}-{{ .Chart.Version }}
    release: {{ .Release.Name }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app: {{ include "demo-app.name" . }}
    release: {{ .Release.Name }}
```

- [ ] **Step 5: 创建 charts/demo-app/templates/_helpers.tpl**

写入 `C:\Users\30475\Desktop\CICD-TEST\charts\demo-app\templates\_helpers.tpl`:

```yaml
{{- define "demo-app.name" -}}
{{- default .Chart.Name .Values.nameOverride | trunc 63 | trimSuffix "-" }}
{{- end }}

{{- define "demo-app.fullname" -}}
{{- if .Values.fullnameOverride }}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- $name := default .Chart.Name .Values.nameOverride }}
{{- if contains $name .Release.Name }}
{{- .Release.Name | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" }}
{{- end }}
{{- end }}
{{- end }}
```

- [ ] **Step 6: 暂存并提交**

```bash
git add charts/
git commit -m "feat: add Helm chart for demo-app"
```

### Task 5: 创建 ArgoCD Application

- [ ] **Step 1: 创建 argocd/application.yaml**

写入 `C:\Users\30475\Desktop\CICD-TEST\argocd\application.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: demo-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: git@github.com:Nivek-Z/CICD-TEST.git
    targetRevision: HEAD
    path: charts/demo-app
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

- [ ] **Step 2: 暂存并提交**

```bash
git add argocd/
git commit -m "feat: add ArgoCD Application manifest"
```

### Task 6: 创建 GitHub Actions CI 流水线

- [ ] **Step 1: 创建 .github/workflows/ci.yaml**

写入 `C:\Users\30475\Desktop\CICD-TEST\.github\workflows\ci.yaml`:

```yaml
name: CI

on:
  push:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      packages: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: ./app
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest

      - name: Update Helm values with new image tag
        run: |
          sed -i "s|tag: latest|tag: ${{ github.sha }}|" charts/demo-app/values.yaml

      - name: Commit and push updated values
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add charts/demo-app/values.yaml
          git commit -m "chore: update image tag to ${{ github.sha }} [skip ci]"
          git push
```

- [ ] **Step 2: 暂存并提交**

```bash
git add .github/
git commit -m "feat: add GitHub Actions CI workflow"
```

### Task 7: 推送到远程仓库

- [ ] **Step 1: 创建 main 分支并推送**

```bash
git branch -M main
git push -u origin main
```

### Task 8: 验证（后续手动验证）

- [ ] **验证 ArgoCD Application 创建：** 在 k3s 上运行 `kubectl apply -f argocd/application.yaml` 后，ArgoCD 会自动同步部署
- [ ] **验证 CI 流水线：** 推送代码到 main 后，在 GitHub Actions 页面查看 CI 运行状态
- [ ] **验证访问：** 通过 `kubectl port-forward svc/demo-app 8080:80` 或 k3s traefik ingress 访问应用
