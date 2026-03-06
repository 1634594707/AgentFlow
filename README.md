# AgentFlow

AgentFlow 是一个基于 Spring Boot + React 的 AI Agent 项目，提供 Agent 管理、会话管理、知识库与文档管理、SSE 流式对话能力。

## 技术栈

- 后端：Spring Boot 3.5、MyBatis、PostgreSQL（pgvector）
- 前端：React 19、Vite、TypeScript、Ant Design
- 数据库：PostgreSQL 18（容器镜像 `pgvector/pgvector:pg18`）

## 项目结构

```text
AgentFlow/
├─ jchatmind/              # Spring Boot 后端
├─ ui/                     # React 前端
├─ docker/postgres/init/   # PostgreSQL 初始化脚本（扩展）
├─ docker-compose.db.yml   # 数据库容器编排
└─ README.md
```

## 环境要求

- JDK 17+
- Maven 3.9+（或使用 `jchatmind/mvnw.cmd`）
- Node.js 20+（建议 LTS）
- Docker Desktop（用于本地数据库）

## 快速启动（推荐）

### 1. 启动 PostgreSQL

在项目根目录执行：

```powershell
docker compose -f docker-compose.db.yml up -d
```

数据库默认配置：

- 地址：`localhost:5433`
- 数据库：`jchatmind`
- 用户名：`postgres`
- 密码：`1209`

初始化脚本会自动启用：

- `pgcrypto`
- `vector`

### 2. 启动后端

打开终端 A：

```powershell
cd jchatmind
.\mvnw.cmd spring-boot:run
```

后端地址：

- `http://localhost:8080`
- 健康检查：`http://localhost:8080/health`

### 3. 启动前端

打开终端 B：

```powershell
cd ui
npm install
npm run dev
```

如果 PowerShell 执行策略拦截 `npm`，可改为：

```powershell
npm.cmd run dev
```

前端地址：

- `http://127.0.0.1:5173`

## 配置说明

### 数据库配置（后端）

后端默认读取以下环境变量（未设置时使用默认值）：

- `SPRING_DATASOURCE_URL`（默认 `jdbc:postgresql://localhost:5433/jchatmind`）
- `SPRING_DATASOURCE_USERNAME`（默认 `postgres`）
- `SPRING_DATASOURCE_PASSWORD`（默认 `1209`）

### 模型配置（后端）

建议通过环境变量覆盖 AI 平台密钥，不要在仓库中提交真实密钥：

- `SPRING_AI_DEEPSEEK_API_KEY`
- `SPRING_AI_ZHIPUAI_API_KEY`

PowerShell 示例：

```powershell
$env:SPRING_AI_DEEPSEEK_API_KEY="your-deepseek-key"
$env:SPRING_AI_ZHIPUAI_API_KEY="your-zhipu-key"
cd jchatmind
.\mvnw.cmd spring-boot:run
```

### 前端后端地址

当前前端默认请求：

- REST：`ui/src/api/http.ts` 中的 `BASE_URL`
- SSE：`ui/src/components/views/AgentChatView.tsx` 中的 SSE 连接地址

如果后端端口不是 `8080`，请同步修改上述两个位置。

## 停止服务

- 停止前后端：在各自终端按 `Ctrl + C`
- 停止数据库：

```powershell
docker compose -f docker-compose.db.yml down
```

## 常见问题

- 后端启动失败且提示 8080 端口被占用：先释放端口，或改端口启动。
- 前端无法请求后端：确认后端已启动且地址与前端配置一致。
- 数据库连接失败：确认容器在运行，且 `SPRING_DATASOURCE_*` 配置正确。

