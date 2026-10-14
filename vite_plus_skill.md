---
name: vite-plus
description: Use Vite+ (vite-plus and the vp CLI), the unified web toolchain combining Vite 8, Rolldown, Vitest 5, tsdown, Oxlint, Oxfmt, and Vite Task. Trigger when the user asks to create, migrate, configure, lint, format, type-check, test, build, pack, run tasks, manage Node.js or package managers, or set up CI/Docker with Vite+ (vp create, vp migrate, vp check, vp lint, vp fmt, vp test, vp dev, vp build, vp pack, vp run, vpr, vpx, vp env, vp hooks, vp staged, vite.config.ts).
---

# vite-plus

基于 [Vite+ 官方指南](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2F) 与 [Vite+ 配置参考](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fconfig%2F) 的统一 Web 工具链技能指南，涵盖 `vp` 全局与本地 CLI 操作、项目脚手架与迁移、依赖与运行时管理、代码检查、测试、应用与库构建、任务缓存编排、Monorepo 及 CI/Docker 最佳实践。

## When to Use

- 使用 `vp create` 创建新应用、库、Monorepo 或基于 Bingo 的自定义代码生成器。
- 使用 `vp migrate` 将现有 Vite / Vitest / ESLint / Prettier / tsdown / tsup / lint-staged 项目迁移或升级至 Vite+。
- 在 `vite.config.ts` 中统一配置 `server`、`build`、`preview`、`create`、`run`、`fmt`、`lint`、`check`、`test`、`pack`、`staged` 及 `defaultPackage`。
- 使用 `vp check`、`vp lint`、`vp fmt`、`vp test`、`vp dev`、`vp build`、`vp pack` 执行日常研发与构建任务。
- 使用 `vp run` (`vpr`) 与 Vite Task 配置跨工作区任务编排、依赖拓扑调度及自动文件追踪缓存。
- 使用 `vp env` 管理 Node.js 运行时与包管理器 (`pnpm`、`npm`、`yarn`、`bun`) 版本，或通过 `vp install` / `vp add` / `vpx` / `vp exec` / `vp dlx` 管理依赖与执行二进制命令。
- 配置 Git 提交钩子 (`vp hooks`、`vp config`、`vp staged`)、编辑器集成 (VS Code、Zed、JetBrains)、CI (`voidzero-dev/setup-vp`) 或官方 Docker 镜像 (`ghcr.io/voidzero-dev/vite-plus`)。

## Step 1: 安装 CLI 与管理运行时环境

参考 [Getting Started](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2F)、[Environment](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fenv)、[Upgrading Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fupgrade) 与 [Removing Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fimplode)。

### 1.1 安装与升级全局 `vp` CLI

```bash
# macOS / Linux 安装
curl -fsSL https://vite.plus | bash

# Windows (PowerShell) 安装
irm https://vite.plus/ps1 | iex

# 安装指定版本或 PR 预览构建
curl -fsSL https://vite.plus | VP_VERSION=1.1.0 bash
curl -fsSL https://vite.plus | VP_PR_VERSION=<pr-or-sha> bash

# 检查工具链版本、升级与回滚 (保留最近 3 个版本)
vp toolchain
vp toolchain --global
vp upgrade
vp upgrade --check
vp upgrade --rollback

# 完全卸载全局 vp 及托管运行时数据 (需输入 boom 确认，或加 --yes)
vp implode --yes
```

- **Alpine Linux (musl) 依赖**: 使用托管 Node.js 运行时前需执行 `apk add libstdc++`。
- **Homebrew 安装**: 若通过 Homebrew 安装，使用 `brew upgrade vite-plus` 升级；卸载时先运行 `vp implode` 清理托管数据，再运行 `brew uninstall vite-plus`。
- **仅使用项目本地 CLI (不装全局 `vp`)**: 可通过包管理器调用，如 `pnpm dlx --package=vite-plus@1.1.0 vp create`、`npx --package=vite-plus@1.1.0 vp migrate --no-interactive`，或在安装依赖后使用 `pnpm exec vp check` / `npm exec -- vp check`。

### 1.2 使用 `vp env` 管理 Node.js 与包管理器

`vp env` 属于全局 CLI 功能，通过垫片 (`node`、`npm`、`pnpm`、`yarn`、`bun`、`pn`、`pnx`) 独立解析并管理项目的 Node.js 运行时和包管理器版本。

- **Node.js 版本解析优先级** (从当前目录向上查找): `.node-version` -> `package.json#devEngines.runtime` -> `package.json#engines.node` -> `.nvmrc` -> `vp env default` -> 最新 LTS。
- **包管理器解析优先级** (从工作区根目录识别): `VP_PACKAGE_MANAGER` 环境变量 -> `package.json#packageManager` -> `package.json#devEngines.packageManager` -> `pnpm-workspace.yaml` -> `pnpm-lock.yaml` -> `yarn.lock`/`.yarnrc.yml` -> `package-lock.json` -> `bun.lock`/`bun.lockb` -> `.pnpmfile.cjs` -> `bunfig.toml` -> `yarn.config.cjs` -> 默认回退至 `pnpm`。

```bash
# 固定项目版本 (写入 .node-version / packageManager / devEngines)
vp env pin lts pnpm@10
vp env pin 24.11.0 --target node-version
vp env unpin

# 设置全局默认版本与当前 Shell 临时覆盖
vp env default lts
vp env default pnpm@10
vp env use 22 pnpm@10
vp env use --unset

# 切换托管模式 (Managed) 与系统优先模式 (System-first)
vp env on
vp env off
vp env off pm

# 诊断、查询与在解析环境中执行脚本
vp env current --json
vp env doctor
vp env which node
vp env list-remote --lts
vp node script.js
vp env exec --node lts --package-manager pnpm@10 pnpm install
```

- **企业镜像与校验**: 设置 `VP_NODE_DIST_MIRROR=https://mirror.example.com/nodejs/dist` 自定义 Node.js 下载源；设置 `VP_NODE_SKIP_SIGNATURE_VERIFY=1` 可在证书异常时跳过 PGP 签名校验 (仍保留 SHA-256 校验)。
- **pnpm 运行时冲突**: 若使用 pnpm 11+ 且由 Vite+ 托管 Node.js，可执行 `pnpm config set --global runtimeOnFail ignore` 避免 pnpm 重复下载运行时。

## Step 2: 创建新项目与迁移现有项目

参考 [Creating a Project](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcreate)、[Migrate to Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fmigrate) 与 [Migration Rules](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fmigrate-rules)。

### 2.1 使用 `vp create` 脚手架创建项目

```bash
# 交互式创建或使用内置模板
vp create
vp create vite:monorepo
vp create vite:application
vp create vite:library
vp create vite:generator

# 社区简写、完整包名、远程 GitHub 仓库或组织级模板 (@org/create)
vp create vite -- --template react-ts
vp create @tanstack/start
vp create github:user/template-repo
vp create @your-org:web --directory my-web --package-manager pnpm --no-interactive
```

- **常用参数**: `--directory <dir>`、`--package-manager <pnpm|npm|yarn|bun>`、`--agent <name>` / `--no-agent`、`--editor <name>` / `--no-editor`、`--git` / `--no-git`、`--hooks` / `--no-hooks`、`--approve-builds` (自动批准并构建含原生构建脚本的依赖)、`--no-interactive`、`--list`。模板自身的参数需放在 `--` 之后。
- **Monorepo 代码生成器 (`vite:generator`)**: 基于 Bingo 与 Zod 编写 `src/template.ts`，自动注册到根 `vite.config.ts` 的 `create.templates` 数组中，之后可直接运行 `vp create <generator-name> -- --name @org/pkg`。
- **组织模板 (`@org/create`)**: 在 `@org/create` 的 `package.json` 中定义 `createConfig.templates` 清单，并在项目根 `vite.config.ts` 中配置 `create: { defaultTemplate: '@your-org' }`。

### 2.2 使用 `vp migrate` 迁移或升级现有项目

执行迁移前请遵循以下标准流程：

1. **前置升级检查**: 对于尚未接入 Vite+ 的项目，必须先将上游依赖升级至 **Vite 8+** 与 **Vitest 4.1+** 并验证通过，且 Node.js 需满足 `^22.18.0 || ^24.11.0 || >=26.0.0`。
2. **保留原始依赖状态**: 运行 `vp migrate` 前**切勿**手动安装 `vite-plus` 或先行升级 `vitest`，保留原始 `package.json`、lockfile 和 `node_modules` 以便迁移器识别原 Vitest 版本并注入兼容配置。
3. **从工作区根目录执行迁移**:

```bash
# 使用全局 CLI 迁移
vp migrate --no-interactive

# 无全局 CLI 时通过包管理器拉取目标版本迁移器
pnpm dlx --package=vite-plus@1.1.0 vp migrate --no-interactive
npx --package=vite-plus@1.1.0 vp migrate --no-interactive
```

4. **理解自动改写规则**:
   - **配置文件合并**: 自动将 `vitest.config.ts`、`tsdown.config.ts`、`.oxlintrc.json`、`.oxfmtrc.json`、JSON 格式 `.lintstagedrc` 合并至 `vite.config.ts` 对应区块，并将 `.nvmrc` / Volta 转换为 `.node-version` (已有 Vite+ 的项目默认仅升级依赖与导入，需加 `--full` 才重跑环境配置步骤)。
   - **导入路径重写**: 仅在配置文件 (`vite.config.*`、`vitest.config.*`) 中将 `vite` 重写为 `vite-plus`；业务代码中的 `vite` 保持不变 (通过 `@voidzero-dev/vite-plus-core` 别名解析)。将 `vitest` 重写为 `vite-plus/test*`，`@vitest/browser*` 重写为 `vite-plus/test/browser*`，Oxlint 插件 API (`@oxlint/plugins`) 重写为 `vite-plus/lint/plugins`。
   - **保留不重写的特例**: `declare module 'vitest'` 与 `declare module '@vitest/browser*'` 类型扩展必须保留原模块名；依赖 `@nuxt/test-utils` 的包保留 `vitest` 导入；WebDriverIO 浏览器提供方使用社区包 `@vitest/browser-webdriverio`。
   - **脚本重写**: `package.json` 中的 `vite` -> `vp dev` (或对应子命令)、`vitest` -> `vp test`、`oxlint`/`eslint` -> `vp lint`、`oxfmt`/`prettier` -> `vp fmt`、`tsdown`/`tsup` -> `vp pack`、`lint-staged` -> `vp staged`。
   - **包管理器适配**: Yarn Plug'n'Play (PnP) 不受支持，会自动转换为 `nodeLinker: node-modules`；pnpm 10.6.2+ 会将根 `package.json#pnpm` 中的 `overrides` 等设置迁移至 `pnpm-workspace.yaml`。
5. **迁移后验证与兼容项清理**:
   - 依次运行 `vp install`、`vp check`、`vp test` 及 `vp build` (或 `vp pack`)。
   - 基线通过后，逐个尝试移除迁移器生成的 `Vitest v4 compatibility` 注释配置与 `tsdown <0.23 compatibility` 配置 (`deps.resolveDepSubpath: true`、`attw.profile: 'strict'`)，每次移除后重新运行测试与构建验证。

## Step 3: 依赖管理与二进制命令执行

参考 [Package Management](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Finstall) 与 [Running Binaries](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fvpx)。

| 命令类别 | 常用命令示例 | 核心说明 |
| :--- | :--- | :--- |
| 安装依赖 | `vp install` / `vp install --frozen-lockfile` / `vp install --filter web` / `vp install -w` | 自动调用项目检测到的包管理器 (`pnpm`/`npm`/`yarn`/`bun`) |
| 增删依赖 | `vp add react` / `vp add -D typescript` / `vp add -O fsevents` / `vp add --save-peer react` / `vp remove react` | 支持 `--ignore-scripts` 与 `--filter <pattern>` |
| 更新与整理 | `vp update` / `vp outdated` / `vp dedupe` | 更新版本、检查过期包、合并重复依赖 |
| 依赖审查 | `vp list` / `vp why <pkg>` / `vp info <pkg>` | 查看包管理器安装的依赖树 (内置编译器用 `vp toolchain` 查看) |
| 原生模块与构建审批 | `vp rebuild [pkg...]` / `vp pm approve-builds [<pkg...> \| --all]` | 重编译 C/C++ 原生模块；管理 npm v12+ / pnpm 安装脚本白名单 |
| 全局工具包 | `vp install -g <pkg>` / `vp update -g [--latest]` / `vp uninstall -g <pkg>` / `vp list -g` | 独立存储在 Vite+ 数据目录 `packages/` 下，跨 Node 版本共享 |
| 底层透传与分阶段发布 | `vp link` / `vp unlink` / `vp pm <cmd>` / `vp pm stage <publish\|list\|approve\|reject>` | 透传原生包管理器指令；支持无 2FA CI 暂存发布与终端审批 |
| 执行二进制 | `vpx <pkg[@ver]>` / `vp exec <cmd>` / `vp dlx <pkg>` | `vpx` 优先本地后远程；`vp exec` 仅限本地 `node_modules/.bin`；`vp dlx` 临时下载执行 |

## Step 4: 统一配置 `vite.config.ts` 与核心研发命令

参考 [Configuring Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fconfig%2F)、[Check](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcheck)、[Lint](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Flint)、[Format](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Ffmt)、[Test](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Ftest)、[Dev](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fdev)、[Build](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fbuild) 与 [Pack](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fpack)。

### 4.1 统一 `vite.config.ts` 结构模板

将所有工具配置收敛在工作区根目录的 `vite.config.ts` 中，避免维护分散的 `.oxlintrc.json`、`.oxfmtrc.json`、`vitest.config.ts` 或 `tsdown.config.ts`：

```ts
import { defineConfig, lazyPlugins } from 'vite-plus';

export default defineConfig({
  // Monorepo 根目录下裸运行 vp dev/build/preview/pack 时的默认目标包 (必须为纯字符串字面量)
  defaultPackage: './apps/web',

  // 使用 lazyPlugins 延迟加载重型 Vite 插件，避免拖慢 vp lint / vp fmt / IDE 配置读取
  plugins: lazyPlugins(async () => {
    const { default: react } = await import('@vitejs/plugin-react');
    return [react()];
  }),

  // 1. 代码检查默认行为 (控制 vp check 组合命令)
  check: {
    fmt: true,
    lint: true,
  },

  // 2. Oxlint 静态检查与 TypeScript Go (tsgolint) 类型检查
  lint: {
    ignorePatterns: ['dist/**'],
    plugins: ['typescript'],
    jsPlugins: ['./lint/custom-plugin.js'],
    options: {
      typeAware: true,
      typeCheck: true,
    },
    rules: {
      'no-console': ['error', { allow: ['warn', 'error'] }],
    },
    overrides: [
      {
        files: ['apps/web/**', 'packages/ui/**'],
        plugins: ['react'],
        rules: { 'react/self-closing-comp': 'error' },
      },
    ],
  },

  // 3. Oxfmt 代码格式化 (兼容 Prettier)
  fmt: {
    singleQuote: true,
    semi: true,
    overrides: [
      {
        files: ['apps/api/**'],
        options: { printWidth: 120 },
      },
    ],
  },

  // 4. Vitest 5 测试配置
  test: {
    include: ['src/**/*.test.ts'],
  },

  // 5. tsdown 库打包与独立可执行文件配置
  pack: {
    entry: ['src/index.ts'],
    dts: true,
    format: ['esm', 'cjs'],
    sourcemap: true,
  },

  // 6. Git 暂存区检查 (替代 lint-staged)
  staged: {
    '*': 'vp check --fix',
  },

  // 7. Vite Task 任务编排与缓存
  run: {
    enablePrePostScripts: true,
    cache: { tasks: true, scripts: false },
    tasks: {
      build: {
        command: 'vp build',
        dependsOn: ['lint'],
      },
    },
  },
});
```

### 4.2 核心工具链命令速查

- **静态检查 (`vp check` / `vp lint` / `vp fmt`)**:
  - `vp check`: 单次遍历同时完成格式化 (Oxfmt)、代码检查 (Oxlint) 与类型检查 (`tsgolint`)。常用参数：`--fix` (自动修复与格式化)、`--quiet` (仅报错不报 warning)、`--no-fmt`、`--no-lint`。
  - `vp lint`: 单独运行 Oxlint (`--fix`、`--type-aware`、`-c <path>`)。自定义 JS 插件请从 `'vite-plus/lint/plugins'` 导入 `definePlugin` 与 `defineRule`，测试从 `'vite-plus/lint/plugins-dev'` 导入 `RuleTester`，切勿单独安装 `oxlint` 或 `@oxlint/plugins`。
  - `vp fmt`: 单独运行 Oxfmt (`--check`、`--write`、`-c <path>`)。
- **单元测试与浏览器测试 (`vp test`)**:
  - 内置 Vitest 5.0.3。**注意**: `vp test` 默认执行单次测试并退出 (非 watch 模式)；进入监听模式需显式运行 `vp test watch`，覆盖率运行 `vp test run --coverage`。
  - 测试文件统一从 `'vite-plus/test'` 导入 `{ describe, expect, it, vi }`，Playwright 浏览器提供方从 `'vite-plus/test/browser-playwright'` 导入。
- **应用开发与生产构建 (`vp dev` / `vp build` / `vp preview`)**:
  - `vp dev`: 启动 Vite 8 开发服务器。
  - `vp build`: 使用 Vite 8 + Rolldown 构建生产 Web 应用 (支持 `--watch`、`--sourcemap`)，随后可用 `vp preview` 本地预览。
- **库打包与原生可执行文件 (`vp pack`)**:
  - 基于 `tsdown` 0.23+ 构建 npm 库 (自带类型声明 `dts`、CSS Modules 与 Lightning CSS 打包能力，无需额外安装 `@tsdown/css` 或 `lightningcss`)。
  - 构建免 Node 环境运行的独立可执行文件：在 `pack` 中设置 `exe: true` (底层使用 Node SEA，要求 Node.js 25.7.0+，可通过 `vp env use 26` 切换运行时)。

## Step 5: 任务编排 (`vp run`)、智能缓存与 Monorepo 工作流

参考 [Run](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Frun)、[Run Config](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fconfig%2Frun)、[Task Caching](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcache)、[Automatic Data Tracking](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fautomatic-data-tracking) 与 [Monorepo Guide](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fmonorepo)。

### 5.1 区分内置命令与项目脚本 (关键原则)

- `vp <cmd>` (`vp dev`、`vp build`、`vp preview`、`vp test`、`vp lint`、`vp fmt`、`vp check`、`vp pack`) **始终执行 Vite+ 内置工具**，绝不会被 `package.json#scripts` 同名脚本覆盖。
- `vp run <task>` (或简写 `vpr <task>`) **专门用于执行 `vite.config.ts#run.tasks` 任务或 `package.json#scripts` 脚本**。同一个任务名不能同时出现在 `vite.config.ts` 和 `package.json` 中。

### 5.2 Monorepo 目标选择与跨包执行

```bash
# 使用全局 -C 参数切换工作目录到指定子包执行内置命令 (推荐，优于位置参数 vp dev apps/web)
vp -C apps/web dev
vp -C packages/ui pack

# vp run / vpr 跨工作区任务调度
vp run @my/app#build                              # 指定包执行任务
vp run -r build                                   # 按 package.json 依赖拓扑顺序递归执行所有包
vp run -t @my/app#build                           # 传递执行目标包及其所有上游依赖包
vp run --filter "@my/*" --filter "!@my/utils" build # 按名称、目录或通配符过滤 (--fail-if-no-match 严格报错)
vp run -w build                                   # 在工作区根目录执行
vp run -r --concurrency-limit 8 build             # 控制并发数 (默认 4，或设 VP_RUN_CONCURRENCY_LIMIT)
vp run -r --parallel dev                          # 忽略依赖顺序全并发启动 (适合多服务 dev)
vp run -r -v build                                # 打印任务缓存命中率与耗时统计摘要 (或 vp run --last-details)
```

### 5.3 任务缓存与自动数据追踪 (Automatic Data Tracking)

- **默认缓存策略**: `vite.config.ts` 中定义的 `run.tasks` 默认开启缓存 (`cache.tasks: true`)；`package.json` 中的 `scripts` 默认不缓存 (`cache.scripts: false`，可通过 `vp run --cache <script>` 临时开启，或用 `--no-cache` 强制禁用)。常驻进程 (如 `dev`/`watch`) 务必设置 `cache: false`。
- **复合命令与嵌套内联**: 使用 `&&` 连接的命令、字符串数组 `command: ['vp lint', 'vp build']` 以及脚本中嵌套的 `vp run` 会被自动拆分为独立子任务分别缓存；根目录 `"build": "vp run -r build"` 的自引用递归会被自动剪枝。
- **两层自动数据追踪**:
  1. **文件系统追踪**: 自动记录任务读取的文件、探测缺失的文件、扫描的目录及写入的产物文件，命中缓存时自动重放终端输出并恢复产物文件。
  2. **协作式追踪 (Cooperative Tracking)**: `vp build` 会自动向 Vite Task 报告 `VITE_*`、`NODE_ENV` 环境变量、`dist/**` 产物及需忽略的临时目录 (`node_modules/.vite-temp`)，因此标准 `vp build` 任务无需手写 `input`/`output`/`env`。
- **手动覆盖缓存指纹与清理**:
  - Tasks 默认在干净环境中运行 (仅透传 `PATH`、`HOME`、`CI`、`GITHUB_*` 等基础变量)。若任务结果依赖特定环境变量，需将其加入 `cache.env: ['NODE_ENV', 'VITE_*', '!VITE_SECRET']`；若仅需透传但不触发缓存失效，加入 `cache.untrackedEnv: ['GITHUB_ACTIONS']`。
  - 使用 `cache.input: [{ auto: true }, '!dist/**']` 在保留自动追踪的同时排除特定目录；跨包路径可使用 `{ pattern: 'shared/**', base: 'workspace' }`。
  - 缓存存储在根目录 `node_modules/.vite/task-cache`，执行 `vp cache clean` 可一键清空。

## Step 6: Git Hooks、IDE 集成、CI/CD 与 Docker 部署

参考 [Commit Hooks](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcommit-hooks)、[IDE Integration](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fide-integration)、[CI](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fci)、[GitHub Actions Cache](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fgithub-actions-cache) 与 [Docker](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fdocker)。

### 6.1 Git 提交钩子 (`vp hooks` / `vp config` / `vp staged`)

- 在 `vite.config.ts` 中配置 `staged: { '*': 'vp check --fix' }`，并在受版本控制的 `.vite-hooks/pre-commit` 中写入 `vp staged` (要求 Node.js `>=22.22.1` 或 `>=24.11.0`，且 Git `>=2.32.0`，不再支持 Node 20)。
- 使用 `vp hooks enable` (或 `vp config`) 安装生成的钩子分发器 (`.vite-hooks/_`) 并设置 `core.hooksPath`；使用 `vp hooks status` 查看状态；在本地克隆中关闭钩子运行 `vp hooks disable`；在 CI 或自动化提交中设置环境变量 `VP_GIT_HOOKS=0` (或 `HUSKY=0`) 跳过钩子。

### 6.2 编辑器集成 (IDE Integration)

- **VS Code**: 推荐安装扩展包 `VoidZero.vite-plus-extension-pack`。在 `.vscode/settings.json` 中将默认 formatter 设为 `oxc.oxc-vscode` (含 `[javascript]`、`[typescript]` 等语言块以覆盖全局 Prettier)，并设置 `"oxc.disableNestedConfig": true`、`"oxc.fmt.disableNestedConfig": true`、`"editor.formatOnSaveMode": "file"` (Oxfmt 不支持局部选区格式化) 以及 `"npm.scriptRunner": "vp"`。
- **Zed**: 安装 `oxc-zed` 扩展，并在 `.zed/settings.json` 中设置 `lsp.oxfmt.initialization_options.settings["fmt.configPath"] = "./vite.config.ts"`。
- **JetBrains (IntelliJ / WebStorm)**: 安装 Oxc 插件 (`com.github.oxc.project.oxcintellijplugin`)，并在 `.gitignore` 中添加 `!.idea/externalDependencies.xml`。

### 6.3 CI/CD 集成 (`voidzero-dev/setup-vp`)

- 在 GitHub Actions、GitLab CI/CD 或 Azure Pipelines 中使用 `voidzero-dev/setup-vp`，**务必锁定精确版本号或 Commit SHA**，切勿使用已冻结的 `@v1` 标签 (`vp migrate` 会自动将工作流中的 `@v1` 替换为精确版本)。
- `setup-vp` 默认自动安装 Node.js、包管理器并执行 `vp install` (除非设 `run-install: false`)。
- 若需跨 GitHub Actions 运行复用 Vite Task 缓存，必须在 `setup-vp` 完成依赖安装**之后**恢复 `node_modules/.vite/task-cache`，并使用带 `run_id` 的滚动主键与 OS/Arch 前缀：

```yaml
- uses: voidzero-dev/setup-vp@<setup-vp-version>
  with:
    node-version: '24'
    cache: true
- name: Restore Vite Task cache
  id: vite-task-cache
  uses: actions/cache/restore@v6
  with:
    path: node_modules/.vite/task-cache
    key: vite-task-${{ runner.os }}-${{ runner.arch }}-${{ github.run_id }}-${{ github.run_attempt }}
    restore-keys: |
      vite-task-${{ runner.os }}-${{ runner.arch }}-
- run: vp run lint
- run: vp run build
- name: Save Vite Task cache
  if: success()
  uses: actions/cache/save@v6
  with:
    path: node_modules/.vite/task-cache
    key: ${{ steps.vite-task-cache.outputs.cache-primary-key }}
```

### 6.4 Docker 构建与容器化 (`ghcr.io/voidzero-dev/vite-plus`)

- 官方镜像 `ghcr.io/voidzero-dev/vite-plus` 适用于构建、CI 与 Devcontainer，**不应直接作为生产运行时基础镜像**。
- 镜像默认以非 root 用户 `vp` 运行 (具备免密 `sudo`)，在 Dockerfile 中复制文件时务必加上 `COPY --chown=vp:vp ...` 防止权限报错。
- **SSR 生产多阶段构建要点**:
  1. 在 `build` 阶段运行 `vp install --frozen-lockfile` 与 `vp build`，并通过 `cp "$(vp env which node | head -1)" /tmp/node` 导出与 `.node-version` 完全一致的 Node 二进制文件。
  2. 单独开启一个全新的 `deps` 阶段运行 `vp install --frozen-lockfile --prod` (在同一阶段追加 `--prod` 不会清理已安装的 `vite-plus` 等 `devDependencies`)。
  3. 在 `debian:bookworm-slim` 或 `gcr.io/distroless/cc` 运行时阶段仅复制 `/tmp/node`、`dist` 和 `deps` 阶段的 `node_modules`。
- **Docker 中运行 Playwright 测试**: 使用 `vp exec playwright install --with-deps chromium` (不要用 `vpx`，以确保安装与项目 lockfile 匹配的浏览器版本)。

## Gotchas (常见避坑指南)

- **内置命令与脚本混淆**: `vp dev` / `vp build` / `vp test` 永远调用内置 Vite / Vitest，若项目 `package.json` 定义了自定义脚本 (如 `"dev": "astro dev"`)，必须用 `vp run dev` (`vpr dev`) 执行。
- **`vp test` 默认非监听模式**: 直接运行 `vp test` 只执行一次测试；需要监听文件变化时请用 `vp test watch`。
- **子目录嵌套 Lint/Format 配置不生效**: `vp check` 始终读取工作区根 `vite.config.ts` 的 `lint` 与 `fmt` 块，子包的嵌套配置不会按文件覆盖。请统一在根 `vite.config.ts` 中使用 `lint.overrides` 和 `fmt.overrides` (可通过 JS 模块导入拆分维护)。
- **`tsconfig.json` 中的 `baseUrl` 导致类型检查跳过**: `tsgolint` 不支持 `compilerOptions.baseUrl`。若 `vp check` 未执行类型检查，先运行 `vp dlx @andrewbranch/ts5to6 --fixBaseUrl .` 移除 `baseUrl`，并确认 `lint.options.typeAware` 与 `lint.options.typeCheck` 均为 `true`。
- **`run.tasks` 中 `command` 数组的语义陷阱**: `command: ['vp lint', 'vp build']` 表示按顺序执行两条 Shell 命令 (等同于 `&&`)，绝不能把单条命令按参数拆成 `['vp', 'build']`。
- **顶层导入重型插件拖慢所有命令**: 若在 `vite.config.ts` 顶层直接初始化耗时 Vite 插件，连 `vp lint`、`vp fmt` 和 IDE 插件都会被迫加载它们。请使用 `lazyPlugins(() => [...])` 包装插件数组。
