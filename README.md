# pico

轻量本地 coding agent——在终端里直接干活，读代码、改文件、跑命令，所有状态落在本地 `.pico/`，不依赖外部服务。纯 Python 标准库实现，零运行时依赖。

## 安装

需要 Python 3.10+。

```bash
# 克隆仓库
git clone git@github.com:ryougi-shiki214/pico-harness.git
cd pico-harness

# 安装（二选一）
pip install -e .          # 用 pip
uv sync                   # 用 uv
```

## 配置

pico 通过环境变量读取 API key。以默认的 DeepSeek 为例：

**Windows (cmd):**
```cmd
set PICO_DEEPSEEK_API_KEY=sk-your-key-here
```

**Linux / macOS / Git Bash:**
```bash
export PICO_DEEPSEEK_API_KEY=sk-your-key-here
```

也可以写到项目根目录的 `.env` 文件里（已 gitignore，不会提交）：

```bash
PICO_DEEPSEEK_API_KEY=sk-your-key-here
```

## 启动

**交互模式：**
```bash
pico
```

启动后会进入 REPL，可以持续对话，会话状态自动保存到 `.pico/sessions/`。

**一次性任务：**
```bash
pico "排查一下最近的测试失败原因"
```

**指定工作目录：**
```bash
pico --cwd /path/to/your/repo
```

## CLI 参数

| 参数 | 默认值 | 说明 |
|---|---|---|
| `--provider` | `deepseek` | 模型后端：`deepseek` / `openai` / `anthropic` / `ollama` |
| `--model` | 自动 | 模型名，覆盖默认值 |
| `--base-url` | 自动 | API 地址，覆盖默认值 |
| `--cwd` | `.` | 工作目录 |
| `--max-steps` | `6` | 单次请求最大工具调用轮数 |
| `--max-new-tokens` | `512` | 模型单步最大输出 token |
| `--temperature` | `0.2` | 采样温度 |
| `--approval` | `ask` | 高风险工具审批：`ask` / `auto` / `never` |
| `--resume` | 无 | 恢复会话，填 session id 或 `latest` |

## 支持的模型后端

| 后端 | 参数 | 环境变量（key） | 默认模型 |
|---|---|---|---|
| DeepSeek | `--provider deepseek` | `PICO_DEEPSEEK_API_KEY` | `deepseek-v4-pro` |
| OpenAI 兼容 | `--provider openai` | `PICO_OPENAI_API_KEY` | `gpt-5.4` |
| Anthropic 兼容 | `--provider anthropic` | `PICO_ANTHROPIC_API_KEY` | `claude-sonnet-4-6` |
| Ollama 本地 | `--provider ollama` | 不需要 | `qwen3.5:4b` |

Ollama 需要先在本机启动服务：
```bash
ollama serve
ollama pull qwen3.5:4b
pico --provider ollama
```

## REPL 内置命令

| 命令 | 说明 |
|---|---|
| `/help` | 查看帮助 |
| `/memory` | 查看当前工作记忆 |
| `/session` | 查看会话文件路径 |
| `/reset` | 重置当前会话 |
| `/exit` | 退出 |

## 运行产物

每次运行结束后，`.pico/runs/<run_id>/` 下会生成：

- `task_state.json` — 任务状态
- `trace.jsonl` — 全链路执行追踪
- `report.json` — 运行报告

## 开发

```bash
uv run pytest tests -q        # 跑测试
uv run ruff check pico tests  # lint
```

## 项目结构

```
pico/
├── pico/              # 主包
│   ├── providers/     # 模型后端适配
│   ├── features/      # 记忆等可选能力
│   └── evaluation/    # benchmark 与评测
├── tests/             # 测试
├── examples/          # 示例（含 mini-pico 教学版）
└── benchmarks/        # 评测任务定义
```

## License

MIT
