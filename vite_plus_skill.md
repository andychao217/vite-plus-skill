---
name: vite-plus
description: Use Vite+ (vite-plus and the vp CLI), the unified web toolchain combining Vite 8, Rolldown, Vitest 5, tsdown, Oxlint, Oxfmt, and Vite Task. Trigger when the user asks in English or Chinese to create, migrate, configure, lint, format, type-check, test, build, pack, run tasks, manage Node.js or package managers, or set up CI/Docker with Vite+ (使用 Vite+ 统一 Web 工具链与 vp CLI：创建项目 vp create、迁移升级 vp migrate、静态检查 vp check/lint/fmt、测试 vp test、开发构建 vp dev/build、库打包 vp pack、任务编排与缓存 vp run/vpr、环境与依赖管理 vp env/install/vpx、Git 钩子 vp hooks/staged 及 vite.config.ts 统一配置).
---

# vite-plus

Bilingual (English / 中文) skill guide for the [Vite+ Official Guide](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2F) and [Vite+ Config Reference](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fconfig%2F). Covers the unified web toolchain (`vite-plus` + `vp` CLI), project scaffolding, migration, runtime and dependency management, static checks, testing, app and library builds, cached task orchestration, monorepos, and CI/Docker workflows.
基于 [Vite+ 官方指南](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2F) 与 [Vite+ 配置参考](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fconfig%2F) 的中英双语技能指南，涵盖 `vp` 全局与本地 CLI、项目创建与迁移、运行时与依赖管理、代码检查、测试、应用与库构建、任务缓存编排、Monorepo 及 CI/Docker 最佳实践。

## When to Use (适用场景)

- **Scaffold Projects / 创建项目**: Create apps, libraries, monorepos, or Bingo code generators with `vp create` (`vite:monorepo`, `vite:application`, `vite:library`, `vite:generator`, `@org/create`).
- **Migrate or Upgrade / 迁移与升级**: Consolidate existing Vite, Vitest, ESLint, Prettier, tsdown, tsup, or lint-staged setups into Vite+ with `vp migrate`.
- **Unified Configuration / 统一配置**: Configure `server`, `build`, `preview`, `create`, `run`, `fmt`, `lint`, `check`, `test`, `pack`, `staged`, and `defaultPackage` in a single `vite.config.ts`.
- **Core Toolchain / 核心研发与构建**: Run static checks (`vp check`, `vp lint`, `vp fmt`), tests (`vp test`), dev server (`vp dev`), app builds (`vp build`), and library/executable builds (`vp pack`).
- **Task Orchestration & Caching / 任务编排与缓存**: Run scripts and cached workspace tasks across monorepos using `vp run` (`vpr`) and Vite Task.
- **Runtime & Package Management / 运行时与包管理**: Manage Node.js and `pnpm`/`npm`/`yarn`/`bun` versions via `vp env`, manage dependencies via `vp install`/`add`/`remove`, and execute binaries via `vpx`/`vp exec`/`vp dlx`.
- **Engineering Workflows / 工程化集成**: Configure Git hooks (`vp hooks`, `vp config`, `vp staged`), IDEs (VS Code, Zed, JetBrains), CI (`voidzero-dev/setup-vp`), and Docker (`ghcr.io/voidzero-dev/vite-plus`).

## Step 1: Install CLI & Manage Runtime Environment (安装 CLI 与管理运行时环境)

References / 参考文档: [Getting Started](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2F), [Environment](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fenv), [Upgrading Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fupgrade), [Removing Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fimplode).

### 1.1 Global & Local `vp` CLI Installation (安装与升级全局/本地 CLI)

```bash
# Install global vp on macOS / Linux (安装 macOS / Linux 全局 CLI)
curl -fsSL https://vite.plus | bash

# Install global vp on Windows PowerShell (安装 Windows 全局 CLI)
irm https://vite.plus/ps1 | iex

# Install specific version or PR preview build (安装指定版本或 PR 预览版)
curl -fsSL https://vite.plus | VP_VERSION=1.1.0 bash
curl -fsSL https://vite.plus | VP_PR_VERSION=<pr-or-sha> bash

# Inspect toolchain, upgrade, or rollback (查看工具链版本、升级与回滚，保留最近 3 个版本)
vp toolchain
vp toolchain --global
vp upgrade
vp upgrade --check
vp upgrade --rollback

# Remove global vp and all managed runtimes/caches (彻底卸载全局 vp 与托管数据，输入 boom 或加 --yes)
vp implode --yes
```

- **Alpine Linux (musl) Prerequisite / 环境依赖**: Run `apk add libstdc++` before using the managed Node.js runtime (使用托管 Node 运行时前需安装 `libstdc++`).
- **Homebrew Installations / Homebrew 管理**: Upgrade with `brew upgrade vite-plus`. To uninstall, run `vp implode` first to clean managed data, then `brew uninstall vite-plus` (先运行 `vp implode` 清理数据，再执行 `brew uninstall`).
- **Project-Local CLI Only / 仅使用项目本地 CLI**: Without global `vp`, invoke via package manager: `pnpm dlx --package=vite-plus@1.1.0 vp create`, `npx --package=vite-plus@1.1.0 vp migrate --no-interactive`, or after install via `pnpm exec vp check` / `npm exec -- vp check`.

### 1.2 Runtime & Package Manager Management with `vp env` (使用 `vp env` 管理环境)

`vp env` (global CLI only) independently manages Node.js and package managers (`npm`, `pnpm`, `yarn`, `bun`) via shell shims (`node`, `npm`, `npx`, `pnpm`, `pnpx`, `pn`, `pnx`, `yarn`, `bun`, `bunx`).
`vp env` (仅限全局 CLI) 通过垫片独立解析并管理项目的 Node.js 运行时和包管理器版本。

- **Node.js Resolution Order / Node.js 解析优先级** (walks up directories / 向上查找): `.node-version` -> `package.json#devEngines.runtime` -> `package.json#engines.node` -> `.nvmrc` -> `vp env default` -> latest LTS.
- **Package Manager Detection Order / 包管理器解析优先级** (from workspace root / 从工作区根目录识别): `VP_PACKAGE_MANAGER` -> `package.json#packageManager` -> `package.json#devEngines.packageManager` -> `pnpm-workspace.yaml` -> `pnpm-lock.yaml` -> `yarn.lock`/`.yarnrc.yml` -> `package-lock.json` -> `bun.lock`/`bun.lockb` -> `.pnpmfile.cjs` -> `bunfig.toml` -> `yarn.config.cjs` -> fallback to `pnpm`.

```bash
# Pin project versions (固定项目版本，写入 .node-version / packageManager / devEngines)
vp env pin lts pnpm@10
vp env pin 24.11.0 --target node-version
vp env unpin

# Set global defaults & current shell overrides (设置全局默认版本与当前 Shell 临时覆盖)
vp env default lts
vp env default pnpm@10
vp env use 22 pnpm@10
vp env use --unset

# Toggle managed mode vs system-first mode (切换托管模式与系统优先模式)
vp env on
vp env off
vp env off pm

# Inspect environment & execute commands (环境诊断、查询与在解析环境中执行脚本)
vp env current --json
vp env doctor
vp env which node
vp env list-remote --lts
vp node script.js
vp env exec --node lts --package-manager pnpm@10 pnpm install
```

- **Custom Mirror & PGP Verification / 企业镜像与签名校验**: Set `VP_NODE_DIST_MIRROR=https://mirror.example.com/nodejs/dist` for custom Node mirrors; set `VP_NODE_SKIP_SIGNATURE_VERIFY=1` to bypass PGP verification if certificates fail (SHA-256 is still verified / 仍校验 SHA-256).
- **Avoid pnpm Runtime Conflict / 避免 pnpm 运行时管理冲突**: On pnpm 11+ with Vite+ managing Node.js, run `pnpm config set --global runtimeOnFail ignore` so pnpm does not download Node separately.

## Step 2: Create or Migrate Projects (创建新项目与迁移现有项目)

References / 参考文档: [Creating a Project](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcreate), [Migrate to Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fmigrate), [Migration Rules](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fmigrate-rules).

### 2.1 Scaffold Projects with `vp create` (使用 `vp create` 创建项目)

```bash
# Interactive mode or built-in templates (交互式创建或使用内置模板)
vp create
vp create vite:monorepo
vp create vite:application
vp create vite:library
vp create vite:generator

# Shorthand, npm create-*, remote GitHub, or org templates (社区模板、GitHub 模板或组织模板)
vp create vite -- --template react-ts
vp create @tanstack/start
vp create github:user/template-repo
vp create @your-org:web --directory my-web --package-manager pnpm --no-interactive
```

- **Key Flags / 常用参数**: `--directory <dir>`, `--package-manager <pnpm|npm|yarn|bun>`, `--agent <name>` / `--no-agent`, `--editor <name>` / `--no-editor`, `--git` / `--no-git`, `--hooks` / `--no-hooks`, `--approve-builds` (auto-approve native dependency build scripts / 自动批准原生依赖构建脚本), `--no-interactive`, `--list`. Pass template flags after `--`.
- **Monorepo Code Generators (`vite:generator`) / 代码生成器**: Powered by Bingo and Zod in `src/template.ts`, auto-registered under `create.templates` in root `vite.config.ts`. Run via `vp create <generator-name> -- --name @org/pkg`.
- **Organization Templates (`@org/create`) / 组织模板**: Define `createConfig.templates` in `@org/create/package.json` and set `create: { defaultTemplate: '@your-org' }` in root `vite.config.ts`.

### 2.2 Migrate or Upgrade Projects with `vp migrate` (使用 `vp migrate` 迁移或升级)

Follow this workflow when migrating existing projects (迁移现有项目请遵循以下标准流程):

1. **Prerequisites / 前置升级要求**: For non-Vite+ projects, upgrade to **Vite 8+** and **Vitest 4.1+** and validate before migrating. Requires Node.js `^22.18.0 || ^24.11.0 || >=26.0.0`.
2. **Preserve Original Lockfile & Dependencies / 保留原始依赖状态**: Do **NOT** install `vite-plus` or upgrade `vitest` manually before running `vp migrate`, so the migrator can detect the original Vitest version and inject compatibility settings (迁移前切勿手动安装 `vite-plus` 或升级 `vitest`).
3. **Run from Workspace Root / 从工作区根目录执行**:

```bash
# With global CLI (使用全局 CLI 迁移)
vp migrate --no-interactive

# Without global CLI (无全局 CLI 时通过包管理器拉取目标版本迁移器)
pnpm dlx --package=vite-plus@1.1.0 vp migrate --no-interactive
npx --package=vite-plus@1.1.0 vp migrate --no-interactive
```

4. **Automatic Rewrite Rules / 自动改写规则**:
   - **Config Consolidation / 配置合并**: Merges `vitest.config.ts`, `tsdown.config.ts`, `.oxlintrc.json`, `.oxfmtrc.json`, and JSON `.lintstagedrc` into `vite.config.ts`, and converts `.nvmrc`/Volta to `.node-version` (existing Vite+ projects run upgrade-only by default; pass `--full` to re-run setup actions / 已接入项目默认仅升级，加 `--full` 重跑环境初始化).
   - **Import Rewrites / 导入重写**: Rewrites `vite` to `vite-plus` **only** in config files (`vite.config.*`, `vitest.config.*`); source files keep `vite` imports (resolved via `@voidzero-dev/vite-plus-core` alias). Rewrites `vitest` to `vite-plus/test*`, `@vitest/browser*` to `vite-plus/test/browser*`, and `@oxlint/plugins` to `vite-plus/lint/plugins`.
   - **What Is Never Rewritten / 不重写的特例**: Keep `declare module 'vitest'` and `declare module '@vitest/browser*'` on upstream module names for type augmentation merging; packages with `@nuxt/test-utils` preserve `vitest` specifiers; WebDriverIO uses `@vitest/browser-webdriverio`.
   - **Script Rewrites / 脚本重写**: `vite` -> `vp dev` (or subcommand), `vitest` -> `vp test`, `oxlint`/`eslint` -> `vp lint`, `oxfmt`/`prettier` -> `vp fmt`, `tsdown`/`tsup` -> `vp pack`, `lint-staged` -> `vp staged`.
   - **Package Manager Rules / 包管理器适配**: Converts Yarn PnP to `nodeLinker: node-modules` (PnP is unsupported); moves pnpm 10.6.2+ root `package.json#pnpm` settings (`overrides`, `peerDependencyRules`) to `pnpm-workspace.yaml`.
5. **Post-Migration Validation & Cleanup / 迁移后验证与兼容清理**:
   - Run `vp install`, `vp check`, `vp test`, and `vp build` (or `vp pack`).
   - Once green, iteratively test removing generated `Vitest v4 compatibility` comments/settings and `tsdown <0.23 compatibility` settings (`deps.resolveDepSubpath: true`, `attw.profile: 'strict'`), validating after each removal.

## Step 3: Package Management & Binary Execution (依赖管理与二进制命令执行)

References / 参考文档: [Package Management](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Finstall), [Running Binaries](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fvpx).

| Category (类别) | Commands (常用命令) | Behavior & Notes (核心说明) |
| :--- | :--- | :--- |
| Install (安装依赖) | `vp install` / `--frozen-lockfile` / `--filter web` / `-w` | Auto-detects and runs workspace package manager (`pnpm`/`npm`/`yarn`/`bun` 自动识别包管理器) |
| Add & Remove (增删依赖) | `vp add react` / `vp add -D typescript` / `vp add -O` / `vp add --save-peer` / `vp remove react` | Supports `--ignore-scripts` and `--filter <pattern>` (支持过滤子包与跳过脚本) |
| Maintain (更新与整理) | `vp update` / `vp outdated` / `vp dedupe` | Updates packages, checks outdated versions, deduplicates lockfile (更新、检查过期、去重) |
| Inspect (依赖审查) | `vp list` / `vp why <pkg>` / `vp info <pkg>` | Inspects PM dependency graph; use `vp toolchain` for bundled engines (审查依赖树；内置引擎用 `vp toolchain`) |
| Native & Build Scripts (原生模块与审批) | `vp rebuild [pkg...]` / `vp pm approve-builds [<pkg...> \| --all]` | Recompiles native addons; manages npm v12+ / pnpm script allowlists (重编译原生模块；管理构建脚本白名单) |
| Global Packages (全局包) | `vp install -g <pkg>` / `vp update -g [--latest]` / `vp uninstall -g` / `vp list -g` | Stored in Vite+ `packages/` data dir across Node versions (独立存储于 Vite+ 目录，跨 Node 版本共享) |
| Passthrough & Staging (底层透传与暂存发布) | `vp link` / `vp unlink` / `vp pm <cmd>` / `vp pm stage <publish\|list\|approve\|reject>` | Forwards raw PM commands; supports 2FA-free CI staged publishing (透传原生命令；支持 CI 分阶段发布与 2FA 审批) |
| Run Binaries (执行二进制) | `vpx <pkg[@ver]>` / `vp exec <cmd>` / `vp dlx <pkg>` | `vpx`: local first then remote; `vp exec`: local `node_modules/.bin` only; `vp dlx`: remote one-off execution |

## Step 4: Unified `vite.config.ts` & Core Toolchain Commands (统一配置与核心研发命令)

References / 参考文档: [Configuring Vite+](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fconfig%2F), [Check](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcheck), [Lint](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Flint), [Format](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Ffmt), [Test](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Ftest), [Dev](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fdev), [Build](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fbuild), [Pack](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fpack).

### 4.1 Unified `vite.config.ts` Template (统一配置文件模板)

Consolidate all tool configs into the workspace root `vite.config.ts` instead of separate `.oxlintrc.json`, `.oxfmtrc.json`, `vitest.config.ts`, or `tsdown.config.ts` files (将所有工具配置收敛至根目录 `vite.config.ts`):

```ts
import { defineConfig, lazyPlugins } from 'vite-plus';

export default defineConfig({
  // Default target for bare vp dev/build/preview/pack at monorepo root (plain string literal only)
  // Monorepo 根目录下裸运行应用命令时的默认目标目录 (必须为纯字符串字面量)
  defaultPackage: './apps/web',

  // Lazy-load heavy Vite plugins so vp lint / vp fmt / IDE config reads stay fast
  // 使用 lazyPlugins 延迟加载重型插件，避免拖慢 vp lint / vp fmt 及编辑器读取
  plugins: lazyPlugins(async () => {
    const { default: react } = await import('@vitejs/plugin-react');
    return [react()];
  }),

  // 1. Composite check defaults (vp check 组合检查默认开关)
  check: {
    fmt: true,
    lint: true,
  },

  // 2. Oxlint + TypeScript Go (tsgolint) static analysis (代码检查与类型感知检查)
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

  // 3. Oxfmt Prettier-compatible formatting (代码格式化配置)
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

  // 4. Vitest 5 test configuration (单元测试与浏览器测试配置)
  test: {
    include: ['src/**/*.test.ts'],
  },

  // 5. tsdown library & standalone executable packaging (库打包与独立可执行文件配置)
  pack: {
    entry: ['src/index.ts'],
    dts: true,
    format: ['esm', 'cjs'],
    sourcemap: true,
  },

  // 6. Git staged file checks replacing lint-staged (Git 暂存区检查)
  staged: {
    '*': 'vp check --fix',
  },

  // 7. Vite Task runner & caching (任务编排与缓存)
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

### 4.2 Core Toolchain Commands Reference (核心工具链命令详解)

- **Static Checks (`vp check` / `vp lint` / `vp fmt`) / 静态检查与格式化**:
  - `vp check`: Runs Oxfmt + Oxlint + `tsgolint` type-checking in a single pass (单次遍历同时完成格式化、Lint 与类型检查). Flags: `--fix`, `--quiet`, `--no-fmt`, `--no-lint`.
  - `vp lint`: Runs Oxlint (`--fix`, `--type-aware`, `-c <path>`). Author custom JS rules by importing `definePlugin` / `defineRule` from `'vite-plus/lint/plugins'` and `RuleTester` from `'vite-plus/lint/plugins-dev'` (do not install standalone `oxlint` or `@oxlint/plugins` / 切勿单独安装 `oxlint`).
  - `vp fmt`: Runs Oxfmt (`--check`, `--write`, `-c <path>`).
- **Testing (`vp test`) / 单元测试与浏览器测试**:
  - Powered by Vitest 5.0.3. **Important / 注意**: `vp test` runs once and exits by default (默认单次运行并退出); use `vp test watch` for interactive watch mode (监听模式) and `vp test run --coverage` for coverage.
  - Import test APIs from `'vite-plus/test'` (`{ describe, expect, it, vi }`) and Playwright browser provider from `'vite-plus/test/browser-playwright'`.
- **App Dev & Build (`vp dev` / `vp build` / `vp preview`) / 应用开发与构建**:
  - `vp dev`: Starts the Vite 8 dev server.
  - `vp build`: Runs production build with Vite 8 + Rolldown (`--watch`, `--sourcemap`). Serve locally with `vp preview`.
- **Library & Executable Packaging (`vp pack`) / 库打包与原生可执行文件**:
  - Powered by `tsdown` 0.23+ with built-in `.d.ts` generation (`dts: true`) and CSS / Lightning CSS bundling out of the box (内置类型声明与 CSS 打包，无需额外安装 `@tsdown/css`).
  - Build standalone Node executables with `exe: true` in `pack` config (uses Node SEA, requires Node.js 25.7.0+, switch via `vp env use 26` / 需 Node 25.7.0+，可用 `vp env use 26` 切换).

## Step 5: Task Runner (`vp run`), Caching & Monorepos (任务编排、智能缓存与 Monorepo)

References / 参考文档: [Run](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Frun), [Run Config](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fconfig%2Frun), [Task Caching](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcache), [Automatic Data Tracking](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fautomatic-data-tracking), [Monorepo Guide](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fmonorepo).

### 5.1 Built-in Commands vs. Scripts/Tasks (区分内置命令与项目脚本)

- **Built-in commands cannot be overwritten / 内置命令不可被覆盖**: `vp dev`, `vp build`, `vp preview`, `vp test`, `vp lint`, `vp fmt`, `vp check`, and `vp pack` **always** invoke the built-in Vite+ tools, ignoring same-named `package.json` scripts.
- **Run scripts/tasks with `vp run` (`vpr`) / 使用 `vp run` 执行脚本或任务**: Use `vp run <name>` or `vpr <name>` to execute `vite.config.ts#run.tasks` or `package.json#scripts`. A task name cannot exist in both files simultaneously (同名任务不可在两处重复定义).

### 5.2 Monorepo Package Targeting & Workspace Execution (Monorepo 跨包调度)

```bash
# Target a specific package with global -C flag (推荐用 -C 切换工作目录执行内置命令)
vp -C apps/web dev
vp -C packages/ui pack

# Workspace-aware task execution with vp run / vpr (跨工作区任务编排)
vp run @my/app#build                              # Run in specific package (指定包执行)
vp run -r build                                   # Recursive in topological dependency order (按依赖拓扑顺序递归执行)
vp run -t @my/app#build                           # Transitive: target package + its dependencies (执行目标包及其上游依赖)
vp run --filter "@my/*" --filter "!@my/utils" build # Filter by name/dir/glob (--fail-if-no-match 严格匹配报错)
vp run -w build                                   # Run in workspace root (在工作区根目录执行)
vp run -r --concurrency-limit 8 build             # Set max concurrent tasks (设置并发上限，默认 4)
vp run -r --parallel dev                          # Ignore dependency order, unlimited concurrency (忽略依赖全并发启动)
vp run -r -v build                                # Print execution & cache summary (打印缓存命中与耗时摘要，或用 --last-details)
```

### 5.3 Task Caching & Automatic Data Tracking (任务缓存与自动数据追踪)

- **Default Cache Policy / 默认缓存策略**: Tasks in `vite.config.ts#run.tasks` are cached by default (`cache.tasks: true`); `package.json` scripts are NOT cached by default (`cache.scripts: false`, enable per-run via `vp run --cache <script>` or disable all via `--no-cache`). Always set `cache: false` on long-running dev/watch tasks (常驻进程务必设 `cache: false`).
- **Compound & Nested Commands / 复合与嵌套命令**: Commands joined with `&&`, string arrays `command: ['vp lint', 'vp build']`, and nested `vp run` calls are split into independently cached sub-tasks. Self-referential root scripts (`"build": "vp run -r build"`) are automatically pruned to prevent infinite recursion.
- **Two-Tier Automatic Data Tracking / 两层自动数据追踪**:
  1. **File System Tracking (文件系统追踪)**: Automatically fingerprints read files, missing-file probes, directory listings, and written outputs; replays terminal logs and restores output files on cache hit.
  2. **Cooperative Tracking (协作式追踪)**: `vp build` automatically reports `VITE_*`, `NODE_ENV`, `dist/**`, and temporary exclusions (`node_modules/.vite-temp`) to Vite Task, requiring zero manual cache config.
- **Manual Cache Controls & Cleanup / 手动覆盖缓存指纹与清理**:
  - Tasks run in a clean environment by default (passing only system/CI vars like `PATH`, `HOME`, `CI`, `GITHUB_*`). Add fingerprinted env vars to `cache.env: ['NODE_ENV', 'VITE_*', '!VITE_SECRET']`, or pass through without fingerprinting via `cache.untrackedEnv: ['GITHUB_ACTIONS']`.
  - Override files via `cache.input: [{ auto: true }, '!dist/**']`, `cache.output: ['dist/**']`, or workspace-relative globs `{ pattern: 'shared/**', base: 'workspace' }`.
  - Clear the task cache (`node_modules/.vite/task-cache`) with `vp cache clean`.

## Step 6: Commit Hooks, IDEs, CI/CD & Docker (工程化工作流与部署)

References / 参考文档: [Commit Hooks](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fcommit-hooks), [IDE Integration](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fide-integration), [CI](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fci), [GitHub Actions Cache](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fgithub-actions-cache), [Docker](https://www.google.com/search?q=https%3A%2F%2Fviteplus.dev%2Fguide%2Fdocker).

### 6.1 Commit Hooks & Staged Checks (Git 提交钩子与暂存区检查)

- Configure `staged: { '*': 'vp check --fix' }` in `vite.config.ts` and invoke `vp staged` in `.vite-hooks/pre-commit` (bundles `lint-staged` 17; requires Node.js `>=22.22.1 || >=24.11.0` and Git `>=2.32.0`; Node 20 is unsupported).
- Manage the hook dispatcher via `vp hooks enable [--hooks-dir <dir>]`, `vp hooks disable` (persists local opt-out), `vp hooks status`, and `vp config [--no-hooks] [--no-agent]`. Bypass hooks in CI/scripts with `VP_GIT_HOOKS=0` (or `HUSKY=0`).

### 6.2 IDE Integration (编辑器集成)

- **VS Code**: Install `VoidZero.vite-plus-extension-pack`. In `.vscode/settings.json`, set `oxc.oxc-vscode` as `editor.defaultFormatter` (including `[javascript]`, `[typescript]`, etc. blocks to override global Prettier), `"oxc.disableNestedConfig": true`, `"oxc.fmt.disableNestedConfig": true`, `"editor.formatOnSaveMode": "file"` (Oxfmt does not support partial selection formatting), and `"npm.scriptRunner": "vp"`.
- **Zed**: Install `oxc-zed` and set `lsp.oxfmt.initialization_options.settings["fmt.configPath"] = "./vite.config.ts"`.
- **JetBrains (IntelliJ / WebStorm)**: Install the Oxc plugin (`com.github.oxc.project.oxcintellijplugin`) and un-ignore `!.idea/externalDependencies.xml` in `.gitignore`.

### 6.3 CI/CD Integration (`voidzero-dev/setup-vp`)

- Use `voidzero-dev/setup-vp@<exact-version-or-sha>` in GitHub Actions, GitLab CI/CD, or Azure Pipelines. **Never use the frozen `@v1` tag** (`vp migrate` automatically updates `@v1` to an exact version).
- `setup-vp` sets up Node.js, package manager caches, and runs `vp install` by default (unless `run-install: false`).
- To share Vite Task cache across GitHub Actions runs, restore `node_modules/.vite/task-cache` **after** `setup-vp` installs dependencies, using a rolling primary key and OS/Arch restore prefix:

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

### 6.4 Docker Workflows (`ghcr.io/voidzero-dev/vite-plus`)

- Official image `ghcr.io/voidzero-dev/vite-plus` is for builds, CI, and devcontainers (not a production runtime image / 仅用于构建、CI 与开发容器，勿直接作为生产运行时镜像).
- Runs as non-root user `vp` (with passwordless `sudo`); always use `COPY --chown=vp:vp ...` in Dockerfiles to avoid permission errors.
- **SSR Production Multi-Stage Pattern / SSR 生产多阶段构建要点**:
  1. In `build` stage: run `vp install --frozen-lockfile`, `vp build`, and export the exact resolved Node binary via `cp "$(vp env which node | head -1)" /tmp/node`.
  2. In a separate fresh `deps` stage: run `vp install --frozen-lockfile --prod` (running `--prod` over a full install does not prune `devDependencies` like `vite-plus` / 必须新建独立 `deps` 阶段安装生产依赖).
  3. In slim runtime stage (`debian:bookworm-slim` or `gcr.io/distroless/cc`): copy only `/tmp/node`, `dist`, and `deps` `node_modules`.
- **Playwright Browser Tests in Docker / 容器内运行浏览器测试**: Run `vp exec playwright install --with-deps chromium` (prefer `vp exec` over `vpx` so the browser matches your lockfile Playwright version / 使用 `vp exec` 匹配 lockfile 版本).

## Gotchas (常见避坑指南 / Common Pitfalls)

- **Built-in Commands vs. Custom Scripts / 内置命令与自定义脚本混淆**: `vp dev`, `vp build`, and `vp test` always run built-in Vite/Vitest. To run a custom `package.json` script (e.g. `"dev": "astro dev"`), you must run `vp run dev` (`vpr dev`).
- **`vp test` Is Single-Run by Default / `vp test` 默认非监听模式**: Unlike standalone Vitest, `vp test` runs once and exits. Run `vp test watch` for interactive watch mode.
- **Nested Lint/Format Configs Are Ignored per File / 子目录嵌套配置不按文件覆盖**: `vp check` always uses the workspace root `vite.config.ts` `lint` and `fmt` blocks. Do not use nested package configs; configure `lint.overrides` and `fmt.overrides` in the root `vite.config.ts`.
- **`tsconfig.json` `baseUrl` Breaks Type-Aware Checks / `baseUrl` 导致类型检查跳过**: `tsgolint` does not support `compilerOptions.baseUrl`. Remove it with `vp dlx @andrewbranch/ts5to6 --fixBaseUrl .` and ensure `lint.options.typeAware` and `lint.options.typeCheck` are `true`.
- **`run.tasks` `command` Array Semantics / `command` 数组语义陷阱**: `command: ['vp lint', 'vp build']` runs two sequential shell commands (like `&&`). Never split argv tokens like `['vp', 'build']`.
- **Top-Level Heavy Plugins Slow Down All Commands / 顶层重型插件拖慢所有命令**: Top-level plugins in `vite.config.ts` evaluate even during `vp lint` and `vp fmt`. Wrap them in `lazyPlugins(() => [...])` so they only load when Vite runs.
