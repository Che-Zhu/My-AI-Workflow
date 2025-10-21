# Claude Code 项目文档初始化指南

## 使用说明

本文档是**一次性指导文件**。当你第一次接触项目时阅读，并立即执行初始化。

**阅读完本文档后，立即执行以下流程：**
1. 探索项目（判断项目阶段、发现技术栈、识别现有规范）
2. 生成文档体系（根据项目状态采取不同策略）
3. 总结并提示用户补充关键信息

**本文档在生成后无需再读**，后续参照生成的 `CLAUDE.md` 即可。

---

## 开发流程中的临时文档

在实际开发过程中，必须维护一个**开发计划文档** `implement-temp.md`（位于项目根目录）。

### 使用流程

1. **开发前**：Claude 必须先和开发者就功能的开发计划达成一致，并将计划写入 `implement-temp.md`
2. **开发中**：每完成一个步骤，立即更新文档中的状态
3. **开发完成后**（按顺序执行）：
   - **独立思考总结**：基于 `implement-temp.md` 中的内容，总结功能变更
   - **生成 changelog**：在 `structured-instructions/changelogs/` 目录下创建或更新版本 changelog
   - **删除临时文档**：删除 `implement-temp.md`（changelog 已保存所有重要信息）

### 文档格式

```markdown
# 功能开发计划：{功能名称}

**开始时间**: {YYYY-MM-DD HH:mm}
**预计完成时间**: {YYYY-MM-DD HH:mm}

## 功能概述

{简要描述功能目标和验收标准}

## 实现计划

- [ ] 步骤 1：{描述} - 预计 {时间}
  - 涉及文件：{文件列表}
  - 说明：{详细说明}

- [ ] 步骤 2：{描述} - 预计 {时间}
  - 涉及文件：{文件列表}
  - 说明：{详细说明}

- [x] 步骤 3：{描述} - ✅ 已完成 {实际耗时}
  - 涉及文件：{文件列表}
  - 说明：{详细说明}
  - 完成时间：{YYYY-MM-DD HH:mm}

## 技术决策

- **决策 1**: {描述技术选型或实现方案}
  - 理由：{为什么这样做}
  - 备选方案：{其他可能的方案}

## 已知问题

- {描述开发过程中发现的问题}
- {描述需要后续解决的技术债}

## 测试计划

- [ ] 单元测试：{描述测试范围}
- [ ] 集成测试：{描述测试场景}
- [ ] 手动测试：{描述测试步骤}

## 进度追踪

| 时间 | 完成内容 | 备注 |
|------|---------|------|
| {HH:mm} | {步骤 X} | {说明} |
| {HH:mm} | {步骤 Y} | {说明} |
```

### 重要原则

- **开发前必须同步**：在开始编写代码前，必须先将计划写入文档并获得开发者确认
- **实时更新**：每完成一个步骤立即更新，不要累积更新
- **透明化**：遇到问题或需要调整计划时，在文档中记录并与开发者沟通
- **单一功能**：每次只维护一个功能的开发计划，避免混乱
- **完成后总结**：功能开发完成后，必须基于此文档生成 changelog，再删除此文档

### Changelog 生成规范

功能完成后，Claude 应基于 `implement-temp.md` 独立思考并生成 changelog，包含：

1. **功能变更总结**：
   - 从"实现计划"中提取完成的功能点
   - 用简洁的语言描述用户可见的变化
   - 按类型分类：新增功能(feat)、修复(fix)、优化(refactor)等

2. **技术细节**（可选）：
   - 从"技术决策"中提取重要的技术选型
   - 列出主要修改的文件或模块

3. **已知问题**（如有）：
   - 从"已知问题"部分提取需要后续解决的问题

4. **破坏性更改**（如有）：
   - 标注 API 变更、配置变更等影响现有功能的改动

**Changelog 格式示例**：
```markdown
# v1.2.0

**发布日期**: 2025-01-21

## 新增功能

- 实现用户登录功能，支持邮箱和手机号登录
- 添加 JWT 认证中间件
- 新增用户个人资料页面

## 技术改进

- Go 后端使用 gin-jwt 替代自定义 JWT 实现（提升安全性）
- Next.js 使用 Server Components 渲染用户页面（提升性能）

## Bug 修复

- 修复登录时的并发问题

## 破坏性更改

- API 认证方式从 Session 改为 JWT，需要更新客户端代码

## 已知问题

- 登录页面在移动端显示需要优化（计划 v1.2.1 修复）

---

**生成自**: implement-temp.md (功能开发计划：用户认证系统)
```

---

## 项目初始化流程

### 步骤 1：探索项目（自动执行，无需询问用户）

#### 1.1 判断项目阶段

**新项目特征：**
- git log 无提交或只有 init commit
- 源代码目录基本为空或只有脚手架文件
- 无业务逻辑实现代码

**已有项目特征：**
- 有多个 git commits
- 存在实现代码（非脚手架）
- 可能已有测试、文档

**探索方法：**
```bash
# 检查提交数量
git log --oneline 2>/dev/null | wc -l

# 检查源代码文件数（根据项目调整路径）
find src -type f -name "*.ts" -o -name "*.js" -o -name "*.py" | wc -l
```

#### 1.2 发现技术栈

**自动检测配置文件：**

| 技术栈 | 检测文件 | 需提取信息 |
|--------|---------|-----------|
| Node.js | `package.json` | dependencies, devDependencies, scripts |
| Next.js | `package.json`, `next.config.js`/`next.config.mjs` | next 版本, app/pages router 类型 |
| Go | `go.mod`, `go.work` | module 路径, dependencies, workspace |
| Kubernetes | `k8s/`, `kubernetes/`, `*.yaml` 配置 | Deployment, Service, Ingress 配置 |
| Python | `pyproject.toml` / `requirements.txt` | dependencies, [tool.*] 配置 |
| Rust | `Cargo.toml` | dependencies, workspace 结构 |
| Java | `pom.xml` / `build.gradle` | dependencies, plugins |

**框架识别：**
- Next.js: package.json 中有 `next` 依赖，或存在 `next.config.*`
  - App Router: 存在 `app/` 目录
  - Pages Router: 存在 `pages/` 目录
- Go 服务: 存在 `go.mod` 和 `main.go` 或 `cmd/` 目录
- K8s 部署: 存在 `k8s/`, `kubernetes/`, `helm/`, `kustomize/` 目录
- React: package.json 中有 `react`
- Vue: 有 `vue` 依赖
- Django: pyproject.toml 中有 `django`
- Flask: 有 `flask` 依赖

**项目架构检测（Next.js + Go + K8s）：**
```bash
# 检测 Next.js 路由类型
[ -d "app" ] && echo "Next.js App Router" || [ -d "pages" ] && echo "Next.js Pages Router"

# 检测 Go 服务
find . -name "go.mod" -o -name "main.go" | grep -v node_modules

# 检测 K8s 配置
find . -name "*.yaml" -path "*/k8s/*" -o -path "*/kubernetes/*"
```

#### 1.3 发现现有规范（已有项目）

**A. 命令规范**
- Node.js: 读取 `package.json` 的 `scripts` 字段
- Makefile: 检查 `Makefile` 并提取 targets（build, docker-build, release, deploy 等）
- 脚本: 检查 `scripts/` 目录（build.sh, docker-build.sh, deploy.sh, release.sh 等）
- 通用: 检查 `justfile`, `Taskfile.yml`
- CI/CD: 读取 `.github/workflows/*.yml` 中的命令
- 构建方式识别: Makefile > scripts/ > 直接命令（不使用 docker-compose 或 Helm）

**B. 代码风格**
- **Next.js/TypeScript 配置文件：**
  - `.eslintrc*`, `.prettierrc*`
  - `tsconfig.json` (strict mode, path aliases)
  - `next.config.js` (experimental features, rewrites)
- **Go 配置文件：**
  - `.golangci.yml` (linter 配置)
  - 检查是否使用 `gofmt` 或 `goimports`
- **代码分析（采样 3-5 个主要源文件）：**
  - **Next.js/TypeScript:**
    - 模块系统：`import/export` vs `require()`
    - 导入风格：`import { foo }` vs `import foo`
    - 命名规范：camelCase vs PascalCase
    - 文件命名：kebab-case vs PascalCase (组件)
    - 缩进：tabs vs spaces
    - 服务端组件 vs 客户端组件标记 (`'use client'`)
  - **Go:**
    - 包组织：单包 vs 多包结构
    - 导入分组：标准库 / 第三方 / 内部包
    - 命名规范：mixedCaps (Go 标准)
    - 错误处理：是否使用 `pkg/errors` 等库

**C. 工作流程**
- Git hooks: 检查 `.husky/`, `.git/hooks/`, `.pre-commit-config.yaml`
- CI 配置: 读取 `.github/workflows/` 推断测试流程
- 测试框架: 从依赖识别（jest, pytest, cargo test）

**D. 环境变量**
- **Next.js 环境变量约定：**
  - `.env.local`, `.env.development`, `.env.production`
  - `.env.example` (模板文件)
  - 客户端变量：`NEXT_PUBLIC_*` 前缀
  - 服务端变量：无前缀限制
- **Go 环境变量：**
  - 通常从系统环境或配置文件加载
  - 检查是否使用 `viper`, `godotenv` 等库
- **K8s 环境变量：**
  - ConfigMap: `k8s/*/configmap.yaml`
  - Secret: `k8s/*/secret.yaml`
  - Deployment 中的 `env` 字段
- **通用检测：**
  - 查找 `.env.example`, `.env.template`, `.env.sample`
  - 查找 `docker-compose.yml` 中的 `environment` 字段
  - 扫描代码中的 `process.env.*` (Next.js) 或 `os.Getenv()` (Go) 调用

---

### 步骤 2：生成 claude.md

#### 场景 A：新项目

生成**基础模板**，包含技术栈的最佳实践：

```markdown
# Bash commands

{从 package.json scripts 提取，或使用技术栈标准命令}

## Next.js 前端
- npm run dev: Start Next.js development server
- npm run build: Build Next.js for production
- npm run start: Start production server
- npm run test: Run test suite
- npm run lint: Lint the codebase
- npm run typecheck: Run TypeScript type checking

## Go 后端
- go run ./cmd/server: Run Go server locally
- go build ./cmd/server: Build Go binary
- go test ./...: Run all Go tests
- go mod tidy: Clean up dependencies

## K8s 部署
- kubectl apply -f k8s/: Apply all K8s configurations
- kubectl get pods: List running pods
- kubectl logs <pod-name>: View pod logs
- kubectl describe pod <pod-name>: Debug pod issues

# Code style

{使用该技术栈的社区最佳实践}

## Next.js/TypeScript
- Use ES modules (import/export), not CommonJS
- Destructure imports when possible (eg. import { foo } from 'bar')
- Use TypeScript strict mode
- Prefer Server Components by default (App Router)
- Mark Client Components with 'use client' directive
- Use kebab-case for file names, PascalCase for component files
- Path aliases: use @/ for imports from root

## Go
- Follow standard Go formatting (gofmt)
- Group imports: stdlib, external, internal
- Use mixedCaps for naming (Go convention)
- Error handling: always check errors, wrap with context
- Package naming: short, lowercase, no underscores

# Workflow

{基础开发流程}

- Run typecheck when you're done making code changes (Next.js)
- Run go test ./... for Go changes
- Run tests before committing
- Run linter before committing
- For K8s changes: validate YAML before applying
- Test with expected output: ask developer for expected output, then verify your implementation produces matching results
```

#### 场景 B：已有项目

生成**贴合实际**的文档：

**Bash commands（从实际配置提取）：**
```bash
# 读取 package.json
jq -r '.scripts | to_entries[] | "- npm run \(.key): \(.value)"' package.json

# 提取 Makefile targets（如存在）
if [ -f Makefile ]; then
  echo "## 构建与部署（Makefile）"
  grep "^[a-zA-Z0-9_-]*:" Makefile | sed 's/:.*/ /' | while read target; do
    echo "- make $target: {从 Makefile 注释或命令推断描述}"
  done
fi

# 列出 scripts/ 目录脚本（如存在）
if [ -d scripts ]; then
  echo "## 构建与部署（脚本）"
  ls scripts/*.sh | while read script; do
    echo "- $script: {从脚本头部注释推断描述}"
  done
fi
```

**Code style（从配置文件和代码分析）：**
```markdown
# Code style

{从 .eslintrc 等配置文件读取}
{从代码采样推断}

示例：
- Use ES modules (import/export) - {从代码分析得出}
- Destructure imports: import { foo } from 'bar' - {从代码采样得出}
- TypeScript strict mode enabled - {从 tsconfig.json 得出}
- File naming: kebab-case - {从文件名分析得出}
```

**Workflow（从 hooks 和 CI 推断）：**
```markdown
# Workflow

{从 .husky/pre-commit 或 CI 配置推断}

示例：
- Run lint-staged before commit - {从 .husky/pre-commit 得出}
- Run typecheck after major changes - {从开发习惯推断}
- Prefer running single tests for performance - {最佳实践}
```

**生成后标注：**
```markdown
<!-- 以下内容由 Claude Code 自动生成，请确认准确性 -->
```

---

### 步骤 3：创建 structured-instructions/

创建目录并生成 4 个必需文档。

#### 3.1 project-overview.md

**场景 A：新项目**
```markdown
# {从 package.json name 或目录名获取}

## 项目愿景

{从 package.json description 或 README 前几行提取}
{如无，标注：TODO - 请补充项目愿景}

## 要解决的核心问题

TODO - 请补充

## 当前进度

- 项目初始化阶段
- 技术栈：{列出检测到的主要技术}

## 已知问题

暂无

## 未来计划

TODO - 请补充功能规划
```

**场景 B：已有项目**
```markdown
# {项目名称}

## 项目愿景

{从 README.md 提取前 1-2 段}
{如无 README，标注：TODO - 请补充（根据代码推断这是一个 {类型} 项目）}

## 要解决的核心问题

{从 README 或代码注释推断}
{标注：根据代码分析推断，请确认}

## 当前进度

{分析目录结构和代码，列出主要模块}

示例：
- 已实现模块：
  - 用户认证（auth/）
  - API 路由（routes/）
  - 数据库模型（models/）
- 开发中：{从 TODO 注释或 Issues 推断}
- 未开始：{标注 TODO}

技术栈：{列出检测到的所有主要技术}

## 已知问题

{从以下来源收集：}
- GitHub Issues（如果是 GitHub 项目）
- 代码中的 TODO/FIXME 注释
- 标注：TODO - 请开发者补充

## 未来计划

{从以下来源提取：}
- ROADMAP.md（如果存在）
- TODO 注释
- 未完成的功能模块
- 标注：根据代码分析推断，请确认
```

#### 3.2 use-cases/

**场景 A：新项目**

创建目录和说明文件：

```markdown
# Use Cases

本目录用于记录项目的重要功能用例。

## 如何编写 use case

每个用例应包含：
1. **操作步骤**：用户执行的每一步及其影响
2. **期望结果**：功能正常时的输出
3. **例外情况**：错误处理和边界情况
4. **流程图**：复杂流程建议添加 UML 图或时序图

## 目录组织

按功能模块组织，例如：
- `use-cases/auth/login.md`
- `use-cases/payment/checkout.md`
```

**场景 B：已有项目**

分析代码结构，生成初始用例框架：

```bash
# 分析目录结构
ls src/ | grep -v test | head -5

# 分析路由（Web 项目）
grep -r "router\|route\|@app.route" --include="*.ts" --include="*.js" --include="*.py"

# 分析 API endpoints
grep -r "POST\|GET\|PUT\|DELETE" --include="*routes*" --include="*api*"
```

为 2-3 个核心功能创建初始文档：

```markdown
# {功能名称}

> **注意**：本文档由 Claude Code 根据代码结构自动生成，请补充细节。

## 功能描述

{从代码推断功能作用}

## 操作步骤

1. {从代码流程推断}
2. TODO - 请补充详细步骤

## 期望结果

TODO - 请补充

## 例外情况

{从错误处理代码推断}
TODO - 请补充其他边界情况

## 技术实现

相关文件：
- {列出主要相关文件，含行号}

## 流程图

TODO - 如流程复杂，建议添加时序图或 UML 图
```

#### 3.3 changelogs/

**重要**：changelogs 由 Claude 在功能开发完成后，基于 `implement-temp.md` 自动生成。

**场景 A：新项目**

```markdown
# v0.1.0

**发布日期**: {当前日期}

## 新增功能

- 项目初始化
- 基础项目结构搭建

## 技术栈

{列出初始技术栈}

---

**注意**：后续版本的 changelog 将由 Claude 在完成功能开发后，基于 `implement-temp.md` 自动生成。
```

**场景 B：已有项目**

**如果有 git tags：**

```bash
# 获取最新的 tag
git describe --tags --abbrev=0

# 为每个 tag 生成 changelog
git log v1.0.0..v1.1.0 --pretty=format:"- %s" --no-merges
```

生成对应版本的文档：

```markdown
# v1.1.0

**发布日期**: {从 git tag 获取}

## 功能变更

{从 commit messages 提取，按类型分类：}
- feat: {新功能}
- fix: {bug 修复}
- refactor: {重构}

## API 变更

{标注：请补充 API 变更说明}

## 破坏性更改

{从 commit messages 中查找 BREAKING CHANGE}

---

**注意**：本文档由 Claude Code 从 git history 自动生成，请确认准确性。
```

**如果无 git tags：**

```markdown
# UNRELEASED

**未发布的变更**

## 功能变更

{从最近的 commits 提取}

---

**提示**：
- 建议使用语义化版本号（Semantic Versioning）和 conventional commits 规范
- **重要**：后续开发中，每次功能完成后，Claude 应基于 `implement-temp.md` 自动生成 changelog
```

**Changelog 生成流程**（已有项目）：

从现在开始，每次功能开发完成后：
1. Claude 阅读 `implement-temp.md` 中的所有内容
2. 独立思考总结功能变更、技术改进、已知问题等
3. 创建新版本 changelog（`v{版本号}.md`）或追加到 `UNRELEASED.md`
4. 删除 `implement-temp.md`

#### 3.4 common-commands.md

这是新增的第 4 个必需文档，记录完整的命令生命周期和环境变量。

**场景 A：新项目**

```markdown
# Common Commands

记录项目从开发到发布的完整生命周期命令及环境变量配置。

## 环境变量

### Next.js 前端环境变量

{从 .env.example 提取，或使用技术栈标准环境变量}

**.env.local (开发环境)**
```bash
# Next.js
NODE_ENV=development

# 客户端可访问变量（NEXT_PUBLIC_ 前缀）
NEXT_PUBLIC_API_URL=http://localhost:8080
NEXT_PUBLIC_APP_NAME=MyApp

# 服务端专用变量
DATABASE_URL=postgresql://localhost:5432/dev_db
API_SECRET_KEY=your-dev-secret-key
```

**.env.production (生产环境)**
```bash
NODE_ENV=production
NEXT_PUBLIC_API_URL=https://api.example.com
DATABASE_URL=${PROD_DB_URL}  # 从 K8s Secret 注入
API_SECRET_KEY=${API_SECRET}  # 从 K8s Secret 注入
```

### Go 后端环境变量

```bash
# 开发环境
GO_ENV=development
PORT=8080
DB_HOST=localhost
DB_PORT=5432
DB_NAME=dev_db
LOG_LEVEL=debug

# 生产环境（通常从 K8s ConfigMap/Secret 注入）
GO_ENV=production
PORT=8080
DB_HOST=${DB_HOST}
DB_NAME=${DB_NAME}
```

### K8s 配置

环境变量通过 ConfigMap 和 Secret 管理：
- `k8s/configmap.yaml`: 非敏感配置
- `k8s/secret.yaml`: 敏感信息（需加密）

TODO - 请补充实际的生产环境变量

---

## 开发阶段

### 初次设置

```bash
# 1. 安装 Next.js 依赖
npm install

# 2. 复制环境变量模板
cp .env.example .env.local

# 3. 安装 Go 依赖
cd backend && go mod download

# 4. 初始化数据库（如适用）
npm run db:migrate
# 或
go run ./cmd/migrate

# 5. 验证 K8s 配置（本地开发可选）
kubectl apply --dry-run=client -f k8s/
```

### 日常开发

```bash
# Next.js 开发服务器
npm run dev

# Go 后端服务器（另一终端）
cd backend && go run ./cmd/server

# 运行测试
npm run test          # Next.js 测试
go test ./...         # Go 测试

# 代码检查
npm run lint          # Next.js lint
cd backend && golangci-lint run  # Go lint
```

---

## 测试阶段

```bash
# Next.js 单元测试
npm run test:unit

# Next.js 集成测试
npm run test:integration

# Next.js E2E 测试
npm run test:e2e

# Go 单元测试
cd backend && go test ./...

# Go 集成测试
cd backend && go test -tags=integration ./...

# 测试覆盖率
npm run test:coverage
cd backend && go test -cover ./...
```

---

## 构建阶段

```bash
# Next.js 构建
npm run build

# Next.js 类型检查
npm run typecheck

# Go 后端构建
cd backend && go build -o bin/server ./cmd/server

# 使用 Makefile 构建（如项目使用 make）
make build              # 构建所有组件
make build-frontend     # 仅构建前端
make build-backend      # 仅构建后端

# 使用脚本构建（如项目使用 sh）
./scripts/build.sh              # 构建所有组件
./scripts/build-frontend.sh     # 仅构建前端
./scripts/build-backend.sh      # 仅构建后端

# 构建 Docker 镜像（每个版本打包成镜像）
# 使用 Makefile
make docker-build VERSION=v1.0.0
# 或使用脚本
./scripts/docker-build.sh v1.0.0
# 或直接构建
docker build -t <registry>/myapp-frontend:v1.0.0 -f Dockerfile.frontend .
docker build -t <registry>/myapp-backend:v1.0.0 -f Dockerfile.backend ./backend
```

---

## 部署阶段

### 本地 K8s 测试（Minikube/Kind）

```bash
# 启动本地集群
minikube start
# 或
kind create cluster

# 应用配置
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/secret.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml

# 查看状态
kubectl get pods -n <namespace>
kubectl get svc -n <namespace>
```

### 生产环境部署

**方式 1：使用 Makefile**
```bash
# 构建并推送镜像
make release VERSION=v1.0.0

# 部署到 K8s
make deploy ENV=production VERSION=v1.0.0

# 回滚
make rollback ENV=production
```

**方式 2：使用脚本**
```bash
# 构建并推送镜像
./scripts/release.sh v1.0.0

# 部署到 K8s
./scripts/deploy.sh production v1.0.0

# 回滚
./scripts/rollback.sh production
```

**方式 3：手动步骤**
```bash
# 1. 构建并推送镜像
docker build -t <registry>/myapp-frontend:v1.0.0 .
docker push <registry>/myapp-frontend:v1.0.0

docker build -t <registry>/myapp-backend:v1.0.0 ./backend
docker push <registry>/myapp-backend:v1.0.0

# 2. 更新 K8s 配置中的镜像版本
# 编辑 k8s/deployment.yaml 更新 image tag

# 3. 应用到集群
kubectl apply -f k8s/ -n production

# 4. 滚动更新
kubectl rollout status deployment/myapp-frontend -n production
kubectl rollout status deployment/myapp-backend -n production

# 5. 回滚（如需要）
kubectl rollout undo deployment/myapp-frontend -n production
```

TODO - 请补充实际的镜像仓库地址和部署流程（使用 make/sh/直接打包）

---

## 维护与监控

### K8s 运维命令

```bash
# 查看 Pod 日志
kubectl logs -f <pod-name> -n <namespace>
kubectl logs -f <pod-name> -c <container-name>  # 多容器 Pod

# 进入 Pod
kubectl exec -it <pod-name> -n <namespace> -- /bin/sh

# 查看资源使用
kubectl top pods -n <namespace>
kubectl top nodes

# 查看事件
kubectl get events -n <namespace> --sort-by='.lastTimestamp'

# 端口转发（本地调试）
kubectl port-forward svc/myapp-frontend 3000:3000 -n <namespace>
kubectl port-forward svc/myapp-backend 8080:8080 -n <namespace>
```

---

## 故障排查

### Next.js 常见问题

**构建失败：**
```bash
# 清除缓存重新构建
rm -rf .next
npm run build
```

**端口被占用：**
```bash
# 查找占用端口的进程
lsof -i :3000
kill -9 <PID>
```

### Go 后端常见问题

**依赖问题：**
```bash
# 清理并重新下载依赖
go clean -modcache
go mod download
```

**编译问题：**
```bash
# 验证 Go 版本
go version

# 更新依赖
go get -u ./...
go mod tidy
```

### K8s 部署问题

**Pod 无法启动：**
```bash
# 查看详细信息
kubectl describe pod <pod-name> -n <namespace>

# 查看日志
kubectl logs <pod-name> -n <namespace> --previous  # 查看崩溃前的日志
```

**镜像拉取失败：**
```bash
# 检查 Secret
kubectl get secret -n <namespace>
kubectl describe secret <image-pull-secret>

# 验证镜像是否存在
docker pull <image-name>
```

**服务无法访问：**
```bash
# 检查 Service
kubectl get svc -n <namespace>
kubectl describe svc <service-name>

# 检查 Endpoints
kubectl get endpoints -n <namespace>

# 检查网络策略
kubectl get networkpolicies -n <namespace>
```

TODO - 随着项目发展补充实际遇到的问题和解决方案
```

**场景 B：已有项目**

```markdown
# Common Commands

## 环境变量

{从 .env.example / .env.template 完整提取}

### Next.js 前端环境变量

**.env.local (开发环境)**
```bash
{列出所有环境变量，带注释}
NODE_ENV=development
NEXT_PUBLIC_API_URL=http://localhost:8080  # Go 后端地址
NEXT_PUBLIC_APP_NAME=MyApp
DATABASE_URL=postgresql://localhost:5432/dev_db  # 本地数据库
LOG_LEVEL=debug
```

**.env.production (生产环境)**
```bash
{列出生产环境差异}
NODE_ENV=production
NEXT_PUBLIC_API_URL=https://api.example.com  # 生产 API 地址
DATABASE_URL=${PROD_DB_URL}  # 从 K8s Secret 注入
LOG_LEVEL=error
```

### Go 后端环境变量

```bash
# 开发环境
GO_ENV=development
PORT=8080
DB_HOST=localhost
DB_PORT=5432
DB_NAME=dev_db
LOG_LEVEL=debug

# 生产环境（从 K8s ConfigMap/Secret 注入）
GO_ENV=production
PORT=8080
DB_HOST=${DB_HOST}
DB_NAME=${DB_NAME}
```

---

## 开发阶段

### 初次设置

```bash
{从 README 或推断得出}
# 1. 安装 Next.js 依赖
npm install

# 2. 复制环境变量
cp .env.example .env.local

# 3. 安装 Go 依赖
cd backend && go mod download

# 4. 初始化数据库
{从 package.json scripts 或 Go 命令推断}
npm run db:migrate
# 或
go run ./cmd/migrate

# 5. 验证 K8s 配置
kubectl apply --dry-run=client -f k8s/
```

### 日常开发

{从 package.json scripts 完整提取所有开发相关命令}

```bash
# Next.js 开发服务器
npm run dev

# Go 后端服务器（另一终端）
cd backend && go run ./cmd/server

# 并发运行（如有配置）
npm run dev:all  # 同时启动前后端

# 运行测试
npm run test          # Next.js 测试
go test ./...         # Go 测试

# 代码检查
npm run lint                    # Next.js lint
cd backend && golangci-lint run # Go lint

# 代码格式化
npm run format               # Next.js format
cd backend && gofmt -w .     # Go format
```

---

## 测试阶段

{从 package.json scripts 和代码推断}

### Next.js 测试

```bash
# 单元测试
npm run test:unit

# 集成测试
npm run test:integration

# E2E 测试
npm run test:e2e

# 测试覆盖率
npm run test:coverage
```

### Go 测试

```bash
# 单元测试
cd backend && go test ./...

# 集成测试
cd backend && go test -tags=integration ./...

# 测试覆盖率
cd backend && go test -cover ./...
cd backend && go test -coverprofile=coverage.out ./... && go tool cover -html=coverage.out
```

---

## 构建阶段

```bash
# Next.js 构建
{从 package.json scripts 提取}
npm run build
npm run typecheck

# Go 后端构建
cd backend && go build -o bin/server ./cmd/server

# 使用 Makefile（如存在）
{检测 Makefile 并提取相关 targets}
make build
make build-frontend
make build-backend
make docker-build VERSION=v1.0.0

# 使用脚本（如存在）
{检测 scripts/ 目录}
./scripts/build.sh
./scripts/docker-build.sh v1.0.0

# Docker 镜像构建（每个版本打包成镜像）
{从 Makefile 或脚本推断镜像名称和 registry}
docker build -t <registry>/myapp-frontend:v1.0.0 -f Dockerfile.frontend .
docker build -t <registry>/myapp-backend:v1.0.0 -f Dockerfile.backend ./backend
```

---

## 部署阶段

{从 CI/CD 配置、Makefile 或脚本推断}

### 方式 1：使用 Makefile

```bash
{从 Makefile 提取 deploy 相关 targets}
# 构建并推送镜像
make release VERSION=v1.0.0

# 部署到 Staging
make deploy-staging VERSION=v1.0.0

# 部署到 Production
make deploy-production VERSION=v1.0.0
# 或
make deploy ENV=production VERSION=v1.0.0

# 回滚
make rollback ENV=production
```

### 方式 2：使用脚本

```bash
{检测 scripts/ 目录中的部署脚本}
# 构建并推送镜像
./scripts/release.sh v1.0.0

# 部署到 Staging
./scripts/deploy.sh staging v1.0.0

# 部署到 Production
./scripts/deploy.sh production v1.0.0

# 回滚
./scripts/rollback.sh production
```

### 方式 3：直接使用 kubectl

```bash
{从 .github/workflows/ 或 k8s/ 配置推断}
# 部署到 Staging
kubectl apply -f k8s/staging/ -n staging

# 部署到 Production
kubectl apply -f k8s/production/ -n production

# 查看部署状态
kubectl rollout status deployment/myapp-frontend -n production
kubectl rollout status deployment/myapp-backend -n production

# 回滚
kubectl rollout undo deployment/myapp-frontend -n production
```

---

## 维护与调试

### 数据库操作

{如项目使用数据库，从 package.json scripts 或 Go 代码提取}

```bash
# Next.js 数据库操作（如使用 Prisma）
npm run db:migrate       # 执行迁移
npm run db:studio        # 打开数据库管理界面
npm run db:seed          # 填充测试数据

# Go 数据库操作
go run ./cmd/migrate up      # 执行迁移
go run ./cmd/migrate down    # 回滚迁移
go run ./cmd/seed            # 填充测试数据
```

### K8s 运维

```bash
# 查看 Pod 状态
kubectl get pods -n <namespace>

# 查看 Pod 日志
kubectl logs -f <pod-name> -n <namespace>
kubectl logs -f deployment/myapp-frontend -n <namespace>

# 进入 Pod 调试
kubectl exec -it <pod-name> -n <namespace> -- /bin/sh

# 端口转发
kubectl port-forward svc/myapp-frontend 3000:3000 -n <namespace>
kubectl port-forward svc/myapp-backend 8080:8080 -n <namespace>

# 查看资源使用
kubectl top pods -n <namespace>
kubectl top nodes
```

### 日志查看

```bash
# 本地日志
{根据项目日志配置}
# Next.js 日志通常在控制台
# Go 日志根据配置可能在文件或 stdout

# K8s 生产日志
kubectl logs -f deployment/myapp-frontend -n production
kubectl logs -f deployment/myapp-backend -n production

# 使用日志聚合工具（如配置）
# 如 ELK, Loki, CloudWatch 等
{根据部署平台}
```

---

## 故障排查

{从代码和经验推断常见问题}

### Next.js 问题

**端口被占用：**
```bash
# 查找占用端口的进程
lsof -i :3000
kill -9 <PID>
```

**构建缓存问题：**
```bash
rm -rf .next
npm run build
```

### Go 后端问题

**端口被占用：**
```bash
lsof -i :8080
kill -9 <PID>
```

**依赖问题：**
```bash
go clean -modcache
go mod download
go mod tidy
```

### K8s 部署问题

**Pod 无法启动：**
```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace> --previous
```

**镜像拉取失败：**
```bash
kubectl get secret -n <namespace>
kubectl describe secret <image-pull-secret>
```

**服务无法访问：**
```bash
kubectl get svc -n <namespace>
kubectl describe svc <service-name>
kubectl get endpoints -n <namespace>
```

### 数据库连接失败

- 检查 `DATABASE_URL` 环境变量是否正确
- 确认数据库服务已启动（本地或 K8s）
- 检查网络连接和防火墙设置
- 验证数据库凭据是否正确

{根据项目补充其他常见问题}

---

**注意**：本文档部分内容由 Claude Code 自动生成，请补充实际的部署流程和故障排查经验。
```

---

### 步骤 4：总结并提示用户

生成完成后，根据项目类型向用户展示不同的提示。

#### 新项目提示

```
✅ 已为你的 Next.js + Go + K8s 项目生成文档体系

生成的文件：
- claude.md（基于 Next.js/TypeScript + Go 的基础规范）
- structured-instructions/
  ├── project-overview.md（项目框架）
  ├── use-cases/（功能用例目录）
  ├── changelogs/（版本记录）
  └── common-commands.md（命令与环境变量，包含 K8s 运维命令）

📝 建议补充：
- project-overview.md 中的"项目愿景"和"未来计划"
- common-commands.md 中的环境变量配置：
  * Next.js 的 .env.local (NEXT_PUBLIC_* 变量)
  * Go 后端的环境变量
  * K8s ConfigMap 和 Secret 配置
- K8s 部署配置（namespace, deployment, service, ingress）
- 随着功能开发，逐步添加 use-cases

💡 提示：
- **开发流程**：
  - 开发前：在 `implement-temp.md` 中记录开发计划
  - 开发中：实时更新进度
  - 完成后：基于 `implement-temp.md` 生成 changelog → 删除临时文档
- Next.js 客户端变量必须使用 NEXT_PUBLIC_ 前缀
- Go 后端建议使用 cmd/ 目录结构
- K8s 配置建议放在 k8s/ 或 kubernetes/ 目录
- 镜像构建使用 Makefile 或 scripts/ 目录中的脚本（不使用 docker-compose 或 Helm）
- 每个版本打包成镜像，使用语义化版本号
- **Changelog 自动生成**：功能完成后，Claude 独立思考总结并生成 changelog
- 下次对话时我会自动读取 claude.md，无需再读本文档
```

#### 已有项目提示

```
✅ 已为你的 Next.js + Go + K8s 项目生成文档体系

生成的文件：
- claude.md（从现有代码和配置分析生成）
- structured-instructions/
  ├── project-overview.md（基于代码结构推断）
  ├── use-cases/（已生成 {数量} 个初始用例框架）
  ├── changelogs/（{从 git history 生成 / 创建 UNRELEASED.md}）
  └── common-commands.md（从配置文件提取，包含 Next.js、Go、K8s 命令）

⚠️ 以下内容是自动推断的，请确认：
- claude.md 中的 Code style 部分（Next.js 和 Go）
- project-overview.md 中的"项目愿景"
- use-cases 中的功能描述
- Go 后端的目录结构（是否使用 cmd/, pkg/ 等）

📝 建议补充：
- use-cases 中标注 TODO 的用例细节
- project-overview.md 中的"已知问题"
- common-commands.md 中的：
  * K8s 部署流程和镜像仓库地址
  * Next.js 和 Go 的环境变量配置
  * 实际的故障排查经验
- K8s 配置文件（如尚未创建）

💡 提示：
- **开发流程**：
  - 开发前：在 `implement-temp.md` 中记录开发计划
  - 开发中：实时更新进度
  - 完成后：基于 `implement-temp.md` 生成 changelog → 删除临时文档
- Next.js App Router 和 Pages Router 的代码风格可能不同，已根据项目推断
- Go 后端的包组织方式已从代码结构推断
- 构建部署方式已从 Makefile/scripts/ 推断（不使用 docker-compose 或 Helm）
- 每个版本打包成镜像，版本号已从 git tags 或配置推断
- **Changelog 自动生成**：后续功能完成后，Claude 独立思考总结并生成 changelog
- K8s 相关命令假定集群已配置，请根据实际情况调整
- 文档中标注了 "TODO" 和 "请确认" 的部分需要你补充
- 下次对话时我会自动读取 claude.md，无需再读本文档
```

---

## 生成参考：必须记录的内容

以下内容供生成 `claude.md` 时参考。

### 1. Bash commands（必需）

记录项目的常用命令，从以下来源提取：
- `package.json` 的 `scripts` 字段（Next.js）
- Go 的标准命令和项目自定义脚本
- `Makefile` 的 targets（优先检测）
- `scripts/` 目录中的 shell 脚本
- K8s 运维命令
- `.github/workflows/` 中的常用命令

**提取方法：**
```bash
# 提取 Makefile targets
grep "^[a-zA-Z0-9_-]*:" Makefile | sed 's/:.*//'

# 列出 scripts/ 目录
ls scripts/*.sh

# 从 CI/CD 推断部署命令
grep -r "make\|./scripts/" .github/workflows/
```

格式要求：
```markdown
# Bash commands

## {技术栈分类}
- {命令}: {简短描述}
```

示例（Next.js + Go + K8s）：
```markdown
# Bash commands

## Next.js 前端
- npm run dev: Start Next.js development server
- npm run build: Build Next.js for production
- npm run start: Start production server
- npm run test: Run test suite
- npm run typecheck: Run TypeScript type checking
- npm run lint: Lint the codebase

## Go 后端
- go run ./cmd/server: Run Go server locally
- go build ./cmd/server: Build Go binary
- go test ./...: Run all Go tests
- go mod tidy: Clean up dependencies
- golangci-lint run: Run Go linter

## 构建与部署（Makefile/脚本）
{检测 Makefile 或 scripts/ 目录，提取实际命令}

### 使用 Makefile
- make build: Build all components
- make docker-build VERSION=v1.0.0: Build Docker images
- make release VERSION=v1.0.0: Build and push images
- make deploy ENV=production VERSION=v1.0.0: Deploy to K8s
- make rollback ENV=production: Rollback deployment

### 使用脚本
- ./scripts/build.sh: Build all components
- ./scripts/docker-build.sh v1.0.0: Build Docker images
- ./scripts/release.sh v1.0.0: Build and push images
- ./scripts/deploy.sh production v1.0.0: Deploy to K8s
- ./scripts/rollback.sh production: Rollback deployment

## K8s 部署与运维
- kubectl apply -f k8s/: Apply all K8s configurations
- kubectl get pods -n <namespace>: List running pods
- kubectl logs -f <pod-name>: View pod logs (follow mode)
- kubectl describe pod <pod-name>: Debug pod issues
- kubectl rollout status deployment/<name>: Check deployment status
- kubectl rollout undo deployment/<name>: Rollback deployment
```

### 2. Code style（必需）

记录项目的关键代码风格约定，从以下来源提取：
- **Next.js/TypeScript 配置文件：** `.eslintrc*`, `.prettierrc*`, `tsconfig.json`
- **Go 配置文件：** `.golangci.yml`, `gofmt` 配置
- 代码采样分析：导入风格、命名规范、文件组织方式

应当记录的内容：
- **Next.js/TypeScript:**
  - 模块系统（ES modules vs CommonJS）
  - 导入风格（解构 vs 整体导入）
  - 命名规范（camelCase vs PascalCase）
  - 文件命名规范（kebab-case vs PascalCase）
  - 服务端组件 vs 客户端组件约定
  - 类型系统配置（TypeScript strict mode 等）
- **Go:**
  - 包组织方式（cmd/, pkg/, internal/）
  - 导入分组（标准库/第三方/内部）
  - 命名规范（mixedCaps）
  - 错误处理约定

格式示例（Next.js + Go）：
```markdown
# Code style

## Next.js/TypeScript
- Use ES modules (import/export), not CommonJS (require)
- Destructure imports when possible (eg. import { foo } from 'bar')
- Use TypeScript strict mode
- Prefer Server Components by default (App Router)
- Mark Client Components explicitly with 'use client' directive
- Use kebab-case for file names, PascalCase for React component files
- camelCase for variables and functions, PascalCase for types/interfaces
- Path aliases: use @/ for imports from project root (eg. import { foo } from '@/lib/utils')

## Go
- Follow standard Go formatting (gofmt/goimports)
- Group imports in order: standard library, third-party packages, internal packages
- Use mixedCaps for naming (Go convention), not snake_case
- Error handling: always check errors, wrap with context using fmt.Errorf or errors package
- Package naming: short, lowercase, no underscores
- Use cmd/ for main applications, pkg/ for library code, internal/ for private code
```

### 3. Workflow（必需）

记录项目的工作流程，从以下来源推断：
- Git hooks（`.husky/`, `.pre-commit-config.yaml`）
- CI 配置（`.github/workflows/`）
- 测试策略（从测试文件和配置推断）

应当记录的内容：
- 提交前必须执行的检查
- 测试策略（单测 vs 集成测试）
- 代码审查流程
- 测试方法（期望输出验证）
- K8s 配置变更流程

格式示例（Next.js + Go + K8s）：
```markdown
# Workflow

## 功能开发前
- **必须先创建开发计划**：在 `implement-temp.md` 中记录功能开发计划
- 与开发者确认计划后再开始编码
- 计划应包含：实现步骤、涉及文件、技术决策、测试计划

## 开发过程中
- **实时更新 implement-temp.md**：每完成一个步骤立即更新状态
- Run typecheck when you're done making a series of code changes (Next.js)
- Run go test ./... when you're done making Go code changes
- Prefer running single tests, not the whole test suite, for performance
- Write unit tests for new utilities and components
- Test with expected output: ask developer for expected output, then verify your implementation produces matching results

## 功能完成后（按顺序执行）
1. 确保所有测试通过
2. Always run linter before committing (both Next.js and Go)
3. **生成 changelog**：
   - 基于 `implement-temp.md` 独立思考总结功能变更
   - 在 `structured-instructions/changelogs/` 创建或更新 changelog
   - 格式：`v{版本号}.md` 或追加到 `UNRELEASED.md`
4. 更新相关文档（use-cases, project-overview 等，如需要）
5. **删除 `implement-temp.md`**（changelog 已保存所有重要信息）

## K8s 配置变更
- Validate YAML syntax before applying: kubectl apply --dry-run=client -f k8s/
- Test changes in staging environment first
- For production deployments, use kubectl rollout commands and monitor status
- Always verify ConfigMap and Secret changes don't break existing deployments

## 镜像构建与部署
- 每个版本打包成镜像（使用 make/sh 脚本或直接构建）
- 使用语义化版本号（v1.0.0）
- 确保镜像成功推送到 registry 后再部署

## 代码审查
- Ensure both Next.js and Go linters pass
- Check TypeScript types are correct (no any types unless necessary)
- Verify Go error handling is comprehensive
- Review K8s resource limits and requests
```

---

## 生成参考：文档结构规范

### 目录组织原则

所有技术文档必须放在项目根目录下的 `structured-instructions/` 目录中。

**理由：**
1. 清晰区分多个项目的文档
2. 避免文档混淆
3. 便于集中管理

### 完整目录结构

示例（Next.js + Go + K8s 项目）：
```
/your-project/
├── claude.md                    # Claude instruction 文档
├── implement-temp.md            # 🔴 当前功能开发计划（开发中存在，完成后生成 changelog 并删除）
├── structured-instructions/     # 技术文档目录
│   ├── project-overview.md      # 项目总览
│   ├── common-commands.md       # 命令与环境变量
│   ├── use-cases/               # 功能用例
│   │   ├── auth/
│   │   │   └── login.md
│   │   └── api/
│   │       └── data-sync.md
│   └── changelogs/              # 版本记录（从 implement-temp.md 自动生成）
│       ├── v1.0.0.md
│       ├── v1.1.0.md
│       ├── v1.2.0.md
│       └── UNRELEASED.md        # 未发布的变更（可选）
├── app/                         # Next.js App Router (或 pages/ for Pages Router)
│   ├── layout.tsx
│   ├── page.tsx
│   └── api/                     # Next.js API routes
├── components/                  # React 组件
├── lib/                         # 共享工具函数
├── public/                      # 静态资源
├── scripts/                     # 构建和部署脚本（可选）
│   ├── build.sh
│   ├── docker-build.sh
│   ├── deploy.sh
│   └── release.sh
├── backend/                     # Go 后端（或其他名称）
│   ├── cmd/                     # Go main 应用
│   │   └── server/
│   │       └── main.go
│   ├── pkg/                     # 可复用的库代码
│   ├── internal/                # 私有代码
│   ├── go.mod
│   └── go.sum
├── k8s/                         # Kubernetes 配置
│   ├── namespace.yaml
│   ├── configmap.yaml
│   ├── secret.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
├── Makefile                     # 构建和部署命令（可选）
├── .env.example                 # Next.js 环境变量模板
├── .env.local                   # Next.js 本地环境变量（gitignore）
├── next.config.js               # Next.js 配置
├── tsconfig.json                # TypeScript 配置
└── package.json                 # Next.js 项目配置
```

### 文档维护规则

**implement-temp.md 使用时机：**
- **创建**：每次开始新功能开发前，必须先创建此文件
- **更新**：开发过程中，每完成一个步骤立即更新
- **完成后处理**（按顺序）：
  1. 基于此文档独立思考总结功能变更
  2. 生成 changelog 到 `structured-instructions/changelogs/`
  3. 删除 `implement-temp.md`
- **重要**：同一时间只维护一个功能的开发计划

**claude.md 更新时机：**
- 新增常用命令（Makefile、脚本）
- 建立新的代码规范
- 工作流程变化

**project-overview.md 更新时机：**
- 项目框架变动
- 进度变化
- 已知问题或未来计划调整

**use-cases/ 更新时机：**
- 重要功能完成（从 implement-temp.md 提取信息）
- 破坏性更改

**changelogs/ 更新时机：**
- **功能完成后自动生成**：Claude 基于 `implement-temp.md` 独立思考总结，生成版本 changelog
- 格式：`v{版本号}.md` 或 `UNRELEASED.md`（未发布的变更）
- 内容：功能变更、技术改进、Bug 修复、破坏性更改、已知问题
- 准备发布新版本时：将 `UNRELEASED.md` 重命名为版本号文件（镜像打包前）

**common-commands.md 更新时机：**
- 新增命令或脚本（Makefile targets、shell 脚本）
- 环境变量变化
- 部署流程调整（镜像构建、K8s 部署）
- 积累新的故障排查经验

---

## 生成参考：文档边界

### 不应该记录的内容

为避免文档过载，以下内容**不需要**记录在技术文档中：

- ❌ 显而易见的通用开发实践（如"编写清晰的代码"）
- ❌ 可以通过工具轻易发现的文件结构（用 `tree` 或 IDE 即可查看）
- ❌ 每个组件或文件的详细列表（应记录架构模式，而非枚举文件）
- ❌ 通用的软件工程原则（应记录项目特有的约定）

**原则**：只记录"大局观"的架构和不易发现的项目特定约定。

---

## 生成参考：工具使用约定

### 核心原则

**不造轮子**：优先使用项目已有工具、行业标准工具、官方推荐工具。

### 常用工具规范

**GitHub 项目：**
- 使用 `gh cli` 进行 PR、Issue、Repository 等操作
- 未安装时可通过 `brew install gh`（macOS）或参考官方文档安装

**npm 包管理：**
- shadcn 等组件库必须通过 npm 安装，不手动复制代码
- 示例：`npx shadcn-ui@latest add button`

**MCP 使用：**
- 前端开发建议使用 `chrome-devtools-mcp`
- 其他 MCP 需先向开发者说明用途，获得许可后使用

---

## 附录：编写原则

生成文档时遵循以下原则：

- **简洁**：只记录架构和项目特定约定，避免冗余
- **准确**：从配置文件和代码提取，不臆测
- **实用**：记录实际使用的内容，不编造占位符
- **标注推断**：自动推断的内容必须标注"请确认"或"TODO"

---

**文档结束**。执行完初始化流程后，本文档的使命即完成。后续维护参照生成的 `claude.md`。
