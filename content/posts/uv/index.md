+++
date = '2026-08-20T10:31:38-03:00'
draft = false
title = 'uv 使用指南与常用指令 / uv Usage Guide and Common Commands'
+++

## 简介 / Introduction

`uv` 是 Astral 开发的 Python 项目与包管理工具。它可以安装和管理 Python、创建虚拟环境、管理项目依赖、生成锁文件、运行项目命令，以及隔离运行或安装 Python CLI 工具。

`uv` is a Python project and package manager developed by Astral. It can install and manage Python, create virtual environments, manage project dependencies, generate lockfiles, run project commands, and run or install Python CLI tools in isolated environments.

本文是一份日常使用速查。完整选项请使用 `uv help`、`uv help <command>`，或查看文末的官方文档。

This article is a practical command reference. For the complete options, use `uv help`, `uv help <command>`, or refer to the official documentation linked at the end.

## 安装与检查 / Installation and Verification

### Windows PowerShell

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### macOS and Linux

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

安装后，重新打开终端并检查版本和帮助信息。

After installation, reopen the terminal and check the version and help output.

```bash
uv --version
uv help
uv help add
```

## Python 版本管理 / Managing Python Versions

`uv` 可以发现系统中的 Python，也可以下载并管理独立的 Python 版本。项目通常使用 `.python-version` 固定所需版本。

`uv` can discover system Python installations or download and manage standalone Python versions. Projects commonly pin the required version in `.python-version`.

```bash
# 安装最新版本 / Install the latest version
uv python install

# 安装指定版本 / Install a specific version
uv python install 3.12

# 列出可用及已安装版本 / List available and installed versions
uv python list

# 查找符合要求的解释器 / Find a matching interpreter
uv python find 3.12

# 为当前项目固定 Python 版本 / Pin the Python version for this project
uv python pin 3.12

# 卸载由 uv 管理的版本 / Uninstall a uv-managed version
uv python uninstall 3.12
```

## 创建项目 / Creating a Project

在新目录中初始化项目，或者在当前目录执行初始化。

Initialize a project in a new directory or initialize the current directory.

```bash
# 创建新项目 / Create a new project
uv init my-project
cd my-project

# 初始化当前目录 / Initialize the current directory
uv init

# 创建库项目 / Create a library project
uv init --lib my-library
```

常见项目文件如下：

Common project files include:

- `.python-version`：项目默认使用的 Python 版本。 / The default Python version for the project.
- `pyproject.toml`：项目元数据和直接依赖声明。 / Project metadata and direct dependency declarations.
- `.venv`：项目虚拟环境，通常在第一次运行项目命令时创建。 / The project virtual environment, usually created by the first project command.
- `uv.lock`：包含完整解析结果的跨平台锁文件，应提交到版本控制。 / The cross-platform lockfile containing the complete resolution; it should be committed to version control.

`uv run`、`uv sync` 或 `uv lock` 等项目命令会在需要时创建 `.venv` 和 `uv.lock`。

Project commands such as `uv run`, `uv sync`, or `uv lock` create `.venv` and `uv.lock` when needed.

## 依赖管理 / Dependency Management

优先使用 `uv add` 和 `uv remove` 修改项目依赖，而不是手动安装到虚拟环境。命令会同时更新 `pyproject.toml`、锁文件和项目环境。

Prefer `uv add` and `uv remove` for project dependencies instead of manually installing into the virtual environment. These commands update `pyproject.toml`, the lockfile, and the project environment together.

```bash
# 添加运行时依赖 / Add a runtime dependency
uv add requests

# 添加多个依赖 / Add multiple dependencies
uv add fastapi uvicorn

# 添加版本约束（引号可避免 shell 解释符号）
# Add a version constraint (quotes prevent shell interpretation)
uv add "django>=5.0,<6"

# 添加开发依赖 / Add a development dependency
uv add --dev pytest ruff

# 删除依赖 / Remove a dependency
uv remove requests

# 查看依赖树 / Display the dependency tree
uv tree
```

## 运行、锁定与同步 / Running, Locking, and Syncing

`uv run` 会先确认项目环境与锁文件一致，再在项目环境中执行命令。因此，多数情况下不需要手动激活 `.venv`。

`uv run` ensures that the project environment matches the lockfile before executing a command in that environment. In most cases, manually activating `.venv` is unnecessary.

```bash
# 运行 Python 文件 / Run a Python file
uv run python main.py

# 运行项目中的命令 / Run a command provided by the project
uv run pytest

# 本次运行临时加入依赖，不写入项目配置
# Add a dependency for this run only without changing project configuration
uv run --with rich python script.py

# 更新锁文件 / Update the lockfile
uv lock

# 按锁文件同步项目环境 / Sync the environment from the lockfile
uv sync

# 严格按锁文件同步，不允许修改锁文件
# Sync without allowing the lockfile to change
uv sync --locked
```

在持续集成或需要可复现安装的场景中，`uv sync --locked` 可以在锁文件过期时直接失败，而不是静默更新它。

For CI or reproducible installations, `uv sync --locked` fails when the lockfile is outdated instead of silently updating it.

## CLI 工具 / CLI Tools

`uvx` 是 `uv tool run` 的别名，适合临时运行 Ruff、Black、HTTPie 等工具。`uv tool install` 则用于需要长期保留的全局命令；每个工具拥有隔离环境。

`uvx` is an alias for `uv tool run` and is useful for temporarily running tools such as Ruff, Black, or HTTPie. Use `uv tool install` for commands that should remain available; every tool has an isolated environment.

```bash
# 临时运行工具 / Run a tool temporarily
uvx ruff check .
uv tool run black --check .

# 安装工具 / Install a tool
uv tool install ruff

# 列出已安装工具 / List installed tools
uv tool list

# 升级工具 / Upgrade a tool
uv tool upgrade ruff

# 卸载工具 / Uninstall a tool
uv tool uninstall ruff
```

## pip 兼容操作 / The pip-Compatible Interface

`uv pip` 面向已有 `requirements.txt` 或尚未迁移到 uv 项目模式的工作流。它直接操作虚拟环境，不会像 `uv add` 那样维护项目的 `pyproject.toml`。虽然命令形式类似 `pip` 和 `pip-tools`，但并非所有行为都完全相同。

The `uv pip` interface is intended for workflows that use `requirements.txt` or have not migrated to uv project management. It operates directly on a virtual environment and does not maintain `pyproject.toml` like `uv add`. Its commands resemble `pip` and `pip-tools`, but their behavior is not identical in every case.

```bash
# 创建虚拟环境 / Create a virtual environment
uv venv

# 在虚拟环境中安装包 / Install packages into the virtual environment
uv pip install requests

# 根据输入文件生成锁定的 requirements 文件
# Compile a locked requirements file from an input file
uv pip compile requirements.in -o requirements.txt

# 使环境严格匹配 requirements 文件
# Make the environment exactly match the requirements file
uv pip sync requirements.txt
```

新项目建议使用 `uv init`、`uv add`、`uv lock` 和 `uv sync` 这一套项目工作流；只有在兼容传统 requirements 工作流时才优先使用 `uv pip`。

For new projects, prefer the project workflow built around `uv init`, `uv add`, `uv lock`, and `uv sync`. Prefer `uv pip` when compatibility with a traditional requirements workflow is needed.

## 更新与缓存 / Updates and Cache

```bash
# 更新通过独立安装器安装的 uv / Update uv installed by the standalone installer
uv self update

# 显示缓存目录 / Show the cache directory
uv cache dir

# 删除过期或不再需要的缓存 / Remove stale or unnecessary cache entries
uv cache prune

# 清空全部缓存 / Clear the entire cache
uv cache clean
```

`uv cache clean` 会删除全部缓存，通常只在排查缓存问题或需要释放空间时使用；日常维护优先选择 `uv cache prune`。

`uv cache clean` removes the entire cache and is generally only needed when troubleshooting cache problems or reclaiming space. Prefer `uv cache prune` for routine maintenance.

## 常用指令速查 / Command Cheat Sheet

| 场景 / Task | 命令 / Command |
| --- | --- |
| 检查版本 / Check the version | `uv --version` |
| 创建项目 / Create a project | `uv init my-project` |
| 固定 Python 版本 / Pin Python | `uv python pin 3.12` |
| 添加依赖 / Add a dependency | `uv add requests` |
| 添加开发依赖 / Add a development dependency | `uv add --dev pytest` |
| 删除依赖 / Remove a dependency | `uv remove requests` |
| 运行脚本 / Run a script | `uv run python main.py` |
| 运行测试 / Run tests | `uv run pytest` |
| 更新锁文件 / Update the lockfile | `uv lock` |
| 同步环境 / Sync the environment | `uv sync` |
| 查看依赖树 / Show the dependency tree | `uv tree` |
| 临时运行工具 / Run a tool temporarily | `uvx ruff check .` |
| 安装 CLI 工具 / Install a CLI tool | `uv tool install ruff` |
| 创建虚拟环境 / Create a virtual environment | `uv venv` |
| 同步 requirements / Sync requirements | `uv pip sync requirements.txt` |
| 清理过期缓存 / Prune stale cache | `uv cache prune` |

## 官方文档 / Official Documentation

- [uv Documentation](https://docs.astral.sh/uv/)
- [Installation](https://docs.astral.sh/uv/getting-started/installation/)
- [Working on Projects](https://docs.astral.sh/uv/guides/projects/)
- [CLI Reference](https://docs.astral.sh/uv/reference/cli/)
- [The pip Interface](https://docs.astral.sh/uv/pip/)
