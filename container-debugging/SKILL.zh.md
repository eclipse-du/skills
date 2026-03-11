# 容器调试与 OpenClaw 管理指南

## 描述
本技能记录了 Docker 容器调试及 OpenClaw 服务管理的最佳实践。涵盖跨平台 Shell 兼容性、配置校验及基于 PM2 的服务持久化。

## 目录
1. [跨平台兼容性处理](#跨平台兼容性处理)
2. [服务探索原则](#服务探索原则)
3. [环境隔离意识](#环境隔离意识)
4. [OpenClaw 专项排障](#openclaw-专项排障)
5. [服务持久化 (PM2)](#服务持久化-pm2)

## 跨平台兼容性处理
- **避免 Shell 拼接错误**：在 Windows PowerShell 直接调用容器指令时，切勿混用 Linux 专有的链式符号（如 `&&`）。
- **编码标准化**：容器操作产出的日志应统一为 UTF-8。严禁在 Windows 侧通过重定向（默认可能产生 UTF-16LE）写入或读取大段内容，推荐使用 `docker cp` 提取日志。

## 服务探索原则
- **优先查阅帮助**：在猜测 CLI 参数（如 `openclaw`）前，应先在容器内运行 `--help` 或阅读 `README.md`。
- **任务执行解剖**：
  1. 探测容器环境：`docker exec <id> ls -R /path`
  2. 验证应用可用性：`docker exec <id> app --version`
  3. 参考原始配置：修改 JSON 配置前，优先参考 Zod 等 Schema 定义文件。

## 环境隔离意识
- **工具选择**：优先在宿主机上使用 `grep_search` 等专用工具进行跨目录搜索，避免通过复杂的 Docker 管道拼接进行低效检索。
- **上下文确认**：执行命令前需明确当前终端是宿主机 PowerShell 还是容器内部进程。

## OpenClaw 专项排障
- **OpenRouter 402 错误**：免费额度超限导致的 `max_tokens` 问题。正确修复方式是在全局 `models` 映射中使用 `merge` 模式，切记**不要**放在 `agents.defaults.models` 下。
- **无效键清理**：使用 `openclaw doctor --fix` 自动修剪和纠偏层级错误的配置键。
- **Node 执行环境**：对于 JSON 修改建议在容器内使用 `node -e` 或临时 `.js` 脚本处理，规避宿主机 Shell 转义导致的大括号报错。

## 服务持久化 (PM2)
- **进程生命周期管理**：使用 PM2 托管主入口（如 `openclaw.mjs`）。
- **自动恢复机制**：将 `pm2 resurrect` 加入容器的 `.bashrc` 或启动脚本中，确保容器重启后服务能自动拉起。
