# Claude Code 项目文档初始化指南

## 使用说明

本文档是**一次性指导文件**。当你第一次接触项目时阅读，并立即执行初始化。

**阅读完本文档后，立即执行以下流程：**
1. 探索项目（判断项目阶段、发现技术栈、识别现有规范）
2. 生成文档体系（根据项目状态采取不同策略）
3. 总结并提示用户补充关键信息

**本文档在生成后无需再读**，后续参照生成的 `claude.md` 即可。

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
| Python | `pyproject.toml` / `requirements.txt` | dependencies, [tool.*] 配置 |
| Rust | `Cargo.toml` | dependencies, workspace 结构 |
| Go | `go.mod` | module 路径, dependencies |
| Java | `pom.xml` / `build.gradle` | dependencies, plugins |

**框架识别：**
- React: package.json 中有 `react`
- Next.js: 有 `next` 依赖或 `next.config.js`
- Vue: 有 `vue` 依赖
- Django: pyproject.toml 中有 `django`
- Flask: 有 `flask` 依赖

#### 1.3 发现现有规范（已有项目）

**A. 命令规范**
- Node.js: 读取 `package.json` 的 `scripts` 字段
- 通用: 检查 `Makefile`, `justfile`, `Taskfile.yml`
- CI/CD: 读取 `.github/workflows/*.yml` 中的命令

**B. 代码风格**
- 配置文件：`.eslintrc*`, `.prettierrc*`, `pyproject.toml [tool.black]`, `rustfmt.toml`
- 代码分析（采样 3-5 个主要源文件）：
  - 模块系统：`import/export` vs `require()`
  - 导入风格：`import { foo }` vs `import foo`
  - 命名规范：camelCase vs snake_case
  - 文件命名：kebab-case vs PascalCase
  - 缩进：tabs vs spaces

**C. 工作流程**
- Git hooks: 检查 `.husky/`, `.git/hooks/`, `.pre-commit-config.yaml`
- CI 配置: 读取 `.github/workflows/` 推断测试流程
- 测试框架: 从依赖识别（jest, pytest, cargo test）

**D. 环境变量**
- 查找 `.env.example`, `.env.template`, `.env.sample`
- 查找 `docker-compose.yml` 中的 `environment` 字段
- 扫描代码中的 `process.env.*` 或 `os.getenv()` 调用

---

### 步骤 2：生成 claude.md

#### 场景 A：新项目

生成**基础模板**，包含技术栈的最佳实践：

```markdown
# Bash commands

{从 package.json scripts 提取，或使用技术栈标准命令}

示例（Node.js 项目）：
- npm run dev: Start development server
- npm run build: Build for production
- npm run test: Run test suite
- npm run lint: Lint the codebase

# Code style

{使用该技术栈的社区最佳实践}

示例（TypeScript + React）：
- Use ES modules (import/export)
- Destructure imports when possible (eg. import { foo } from 'bar')
- Use TypeScript strict mode
- Prefer functional components in React
- Use kebab-case for file names

# Workflow

{基础开发流程}

- Run typecheck when you're done making code changes
- Run tests before committing
- Run linter before committing
- Test with expected output: ask developer for expected output, then verify your implementation produces matching results
```

#### 场景 B：已有项目

生成**贴合实际**的文档：

**Bash commands（从实际配置提取）：**
```bash
# 读取 package.json
jq -r '.scripts | to_entries[] | "- npm run \(.key): \(.value)"' package.json
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

**场景 A：新项目**

```markdown
# v0.1.0

**发布日期**: {当前日期}

## 新增功能

- 项目初始化
- 基础项目结构搭建

## 技术栈

{列出初始技术栈}
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

**提示**：建议使用语义化版本号（Semantic Versioning）和 conventional commits 规范。
```

#### 3.4 common-commands.md

这是新增的第 4 个必需文档，记录完整的命令生命周期和环境变量。

**场景 A：新项目**

```markdown
# Common Commands

记录项目从开发到发布的完整生命周期命令及环境变量配置。

## 环境变量

### 开发环境

{从 .env.example 提取，或使用技术栈标准环境变量}

示例：
```bash
NODE_ENV=development
DATABASE_URL=postgresql://localhost:5432/dev_db
LOG_LEVEL=debug
```

### 生产环境

```bash
NODE_ENV=production
DATABASE_URL={生产数据库连接}
LOG_LEVEL=error
API_KEY=*** # 从密钥管理服务获取
```

TODO - 请补充实际的生产环境变量

---

## 开发阶段

### 初次设置

```bash
# 安装依赖
{根据技术栈：npm install / pip install -r requirements.txt / cargo build}

# 复制环境变量模板
cp .env.example .env.local

# 初始化数据库（如适用）
{根据项目：npm run db:migrate / python manage.py migrate}
```

### 日常开发

```bash
# 启动开发服务器
{从 package.json scripts 提取}

# 运行测试
{从 package.json scripts 提取}

# 代码检查
{从 package.json scripts 提取}
```

---

## 测试阶段

```bash
# 单元测试
{技术栈标准命令}

# 集成测试
{如适用}

# 端到端测试
{如适用}
```

---

## 构建阶段

```bash
# 本地构建
{从 package.json scripts 提取}

# 类型检查
{如适用}
```

---

## 部署阶段

TODO - 请补充部署命令和流程

---

## 故障排查

### 常见问题

TODO - 随着项目发展补充常见问题和解决方案
```

**场景 B：已有项目**

```markdown
# Common Commands

## 环境变量

{从 .env.example / .env.template 完整提取}

### 开发环境

```bash
{列出所有环境变量，带注释}
NODE_ENV=development
DATABASE_URL=postgresql://localhost:5432/dev_db  # 本地数据库
PORT=3000  # 开发服务器端口
LOG_LEVEL=debug
```

### 生产环境

```bash
{列出生产环境差异}
NODE_ENV=production
DATABASE_URL=${PROD_DB_URL}  # 从环境变量或密钥服务获取
LOG_LEVEL=error
```

---

## 开发阶段

### 初次设置

```bash
{从 README 或推断得出}
{列出所有必要的初始化步骤}
```

### 日常开发

{从 package.json scripts 完整提取所有开发相关命令}

```bash
# 启动开发服务器
npm run dev

# 启动开发服务器（带监听）
npm run dev:watch

# 运行测试（监听模式）
npm run test:watch

# 代码检查
npm run lint

# 代码格式化
npm run format
```

---

## 测试阶段

{从 package.json scripts 和代码推断}

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

---

## 构建阶段

```bash
{从 package.json scripts 提取}
npm run build

{如有类型检查}
npm run typecheck

{如有代码检查}
npm run lint
```

---

## 部署阶段

{从 CI/CD 配置推断}

```bash
# 部署到 Staging
{从 .github/workflows/ 推断}

# 部署到 Production
{从 .github/workflows/ 推断}
```

{如使用容器}
```bash
# 构建 Docker 镜像
docker build -t {镜像名} .

# 运行容器
docker run -p 3000:3000 {镜像名}
```

---

## 维护与调试

### 数据库操作

{如项目使用数据库，从 package.json scripts 提取}

```bash
# 创建迁移
{相关命令}

# 执行迁移
{相关命令}

# 回滚迁移
{相关命令}

# 数据库 seed
{如适用}
```

### 日志查看

```bash
# 本地日志
{根据项目日志配置}

# 生产日志
{根据部署平台}
```

---

## 故障排查

{从代码和经验推断常见问题}

### 端口被占用

```bash
# 查找占用端口的进程
lsof -i :{端口号}

# 或使用
netstat -vanp tcp | grep {端口号}

# 终止进程
kill -9 <PID>
```

### 数据库连接失败

- 检查 `DATABASE_URL` 环境变量是否正确
- 确认数据库服务已启动
- 检查网络连接和防火墙设置

{根据项目补充其他常见问题}

---

**注意**：本文档部分内容由 Claude Code 自动生成，请补充实际的部署流程和故障排查经验。
```

---

### 步骤 4：总结并提示用户

生成完成后，根据项目类型向用户展示不同的提示。

#### 新项目提示

```
✅ 已为你的项目生成文档体系

生成的文件：
- claude.md（基于 {技术栈} 的基础规范）
- structured-instructions/
  ├── project-overview.md（项目框架）
  ├── use-cases/（功能用例目录）
  ├── changelogs/（版本记录）
  └── common-commands.md（命令与环境变量）

📝 建议补充：
- project-overview.md 中的"项目愿景"和"未来计划"
- common-commands.md 中的环境变量配置
- 随着功能开发，逐步添加 use-cases

💡 提示：
下次对话时我会自动读取 claude.md，无需再读本文档。
```

#### 已有项目提示

```
✅ 已为你的项目生成文档体系

生成的文件：
- claude.md（从现有代码和配置分析生成）
- structured-instructions/
  ├── project-overview.md（基于代码结构推断）
  ├── use-cases/（已生成 {数量} 个初始用例框架）
  ├── changelogs/（{从 git history 生成 / 创建 UNRELEASED.md}）
  └── common-commands.md（从配置文件提取）

⚠️ 以下内容是自动推断的，请确认：
- claude.md 中的 Code style 部分
- project-overview.md 中的"项目愿景"
- use-cases 中的功能描述

📝 建议补充：
- use-cases 中标注 TODO 的用例细节
- project-overview.md 中的"已知问题"
- common-commands.md 中的部署流程和故障排查经验

💡 提示：
- 文档中标注了 "TODO" 和 "请确认" 的部分需要你补充
- 下次对话时我会自动读取 claude.md，无需再读本文档
```

---

## 生成参考：必须记录的内容

以下内容供生成 `claude.md` 时参考。

### 1. Bash commands（必需）

记录项目的常用命令，从以下来源提取：
- `package.json` 的 `scripts` 字段
- `Makefile` 的 targets
- `.github/workflows/` 中的常用命令
- 技术栈的标准命令

格式要求：
```markdown
# Bash commands

- {命令}: {简短描述}
```

示例：
```markdown
# Bash commands

- npm run dev: Start development server
- npm run build: Build the project
- npm run test: Run all tests
- npm run typecheck: Run the typechecker
- npm run lint: Lint the codebase
```

### 2. Code style（必需）

记录项目的关键代码风格约定，从以下来源提取：
- 配置文件：`.eslintrc*`, `.prettierrc*`, `tsconfig.json`, `pyproject.toml` 等
- 代码采样分析：导入风格、命名规范、文件组织方式

应当记录的内容：
- 模块系统（ES modules vs CommonJS）
- 导入风格（解构 vs 整体导入）
- 命名规范（camelCase vs snake_case）
- 文件命名规范（kebab-case vs PascalCase）
- 特定框架或库的使用约定
- 类型系统配置（TypeScript strict mode 等）

格式示例：
```markdown
# Code style

- Use ES modules (import/export) syntax, not CommonJS (require)
- Destructure imports when possible (eg. import { foo } from 'bar')
- Use TypeScript strict mode
- Prefer functional components over class components in React
- Use kebab-case for file names
- camelCase for variables and functions
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

格式示例：
```markdown
# Workflow

- Run typecheck when you're done making a series of code changes
- Prefer running single tests, not the whole test suite, for performance
- Always run linter before committing
- Write unit tests for new utilities and components
- Test with expected output: ask developer for expected output, then verify your implementation produces matching results
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

```
/your-project/
├── claude.md                    # Claude instruction 文档
├── structured-instructions/     # 技术文档目录
│   ├── project-overview.md      # 项目总览
│   ├── common-commands.md       # 命令与环境变量
│   ├── use-cases/               # 功能用例
│   │   ├── auth/
│   │   │   └── login.md
│   │   └── payment/
│   │       └── checkout.md
│   └── changelogs/              # 版本记录
│       ├── v1.0.0.md
│       └── v1.1.0.md
├── src/                         # 源代码
└── package.json                 # 项目配置
```

### 文档维护规则

**claude.md 更新时机：**
- 新增常用命令
- 建立新的代码规范
- 工作流程变化

**project-overview.md 更新时机：**
- 项目框架变动
- 进度变化
- 已知问题或未来计划调整

**use-cases/ 更新时机：**
- 重要功能完成
- 破坏性更改

**changelogs/ 更新时机：**
- 功能完成
- 准备发布新版本

**common-commands.md 更新时机：**
- 新增命令或脚本
- 环境变量变化
- 部署流程调整
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
