# Memory（中文版）

`Memory` 是 Memmy 的本地优先（local-first）记忆服务。默认使用 SQLite 存储，
并通过 HTTP 服务与 `memmy-memory` CLI 暴露记忆操作。

## 环境要求

- Node.js 20 或更高版本
- npm

## 开发

在仓库根目录运行主要工作流：

```bash
npm run memory:serve:dev
npm run memory:test
npm run memory:lint
npm run memory:build
```

开发服务器的入口是 `Memory/src/server/index.ts`。构建后，服务器入口为
`Memory/dist/src/server/index.js`，可以这样启动：

```bash
npm run memory:serve
```

服务默认监听 `http://127.0.0.1:18960`。可以在 `--` 之后覆盖其设置：

```bash
npm run memory:serve:dev -- \
  --host 127.0.0.1 \
  --port 18960 \
  --db ~/.memmy/memory-service/memory.sqlite \
  --config ~/.memmy/config.yaml
```

内置的 Memory 面板可在 `/` 与 `/viewer` 访问。

## 配置

除非提供 `--config`，服务会按以下顺序检查配置位置：

```text
MEMMY_CONFIG
~/.memmy/config.yaml
```

最小化的本地配置如下：

```yaml
memmyMemory:
  version: 1
  activeProfile: byok
  storage:
    mode: local
    backend: sqlite
    sqlitePath: ~/.memmy/memory-service/memory.sqlite
    endpoint: http://127.0.0.1:18960
    token: local-token
  profiles:
    byok:
      embedding:
        provider: local
```

`MEMMY_MEMORY_HOST`、`MEMMY_MEMORY_PORT` 与 `MEMMY_MEMORY_DB` 环境变量
会覆盖对应的服务器设置。也接受 `MEMORY_SERVICE_*` 别名。

当设置了 `storage.token`、`MEMMY_MEMORY_TOKEN` 或 `MEMORY_SERVICE_TOKEN` 时，
除 `GET /api/v1/health` 之外的所有 HTTP 路由都要求将该 token 作为
Bearer token 或 `x-api-key` 提供。

## CLI

在 `Memory/` 目录内直接从源码运行 CLI：

```bash
npx tsx src/cli/index.ts health --url http://127.0.0.1:18960
```

构建后，使用编译产物入口：

```bash
node dist/src/cli/index.js health --url http://127.0.0.1:18960
```

可用命令：

```text
memmy-memory init
memmy-memory install
memmy-memory serve
memmy-memory health
memmy-memory reload-config
memmy-memory session open
memmy-memory session close <sessionId>
memmy-memory turn start
memmy-memory turn complete <turnId>
memmy-memory search <query>
memmy-memory add <content>
memmy-memory get <id>
memmy-memory get <id> --verbose
memmy-memory delete <id>
memmy-memory raw <method> <path>
```

使用 `--url`、`--token`、`--user-id`、`--source` 或 `--config` 为单条命令
选择目标服务与命名空间。

`memmy-memory get` 默认打印紧凑的、面向 agent 可读的内容。加 `--verbose`
可查看完整的 JSON 详细响应。

`memmy-memory serve` 不会启动本地 HTTP 服务。它只报告如何把这个独立 CLI
连接到外部 Memory 服务。

npm 包安装与 agent-skill 配置见
[`src/cli/npm/README.md`](src/cli/npm/README.md)。集成测试布局见
[`tests/service/README.md`](tests/service/README.md)。

---

> 译注（2026-09-05，xiaozhengspace 项目）：本文件是 `readme.md` 的中文翻译，
> 基于上游 v1.1.2（同步于 2026-09-05，fast-forward 95 提交）。英文原版为
> 权威版本；两者不一致时以英文原版为准。
