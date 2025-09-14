---
created: 2025-09-14 13:03
type: reference
sphere: development  
topic: knowledge-graphs
tags: [mcp, ai, coding, integration, deepwiki]
prompt: extracted content from deepwiki MCP integration page for re-use
source: https://deepwiki.com/BloopAI/vibe-kanban/3.3-mcp-integration
---

# MCP Integration | BloopAI/vibe-kanban

## Overview

This document covers the Multi-Client Protocol (MCP) integration system that extends AI coding agent capabilities with external tools and services. MCP allows agents like Claude, Cursor, AMP, and others to access additional functionality beyond their base capabilities through standardized protocol connections.

For information about the core executor architecture and agent configuration, see [Executor Architecture and Configuration](/BloopAI/vibe-kanban/3.1-executor-architecture-and-configuration). For details about process management and execution, see [Process Management and Log Streaming](/BloopAI/vibe-kanban/3.2-process-management-and-log-streaming).

The MCP integration system provides a unified abstraction layer for configuring MCP servers across different AI coding agents, each of which has its own configuration format and file structure.

**Sources:** [frontend/src/pages/McpServers.tsx1-372](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/frontend/src/pages/McpServers.tsx#L1-L372) [crates/server/src/routes/config.rs146-218](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/server/src/routes/config.rs#L146-L218)

## Agent-Specific Configuration

Each coding agent implements MCP support differently, requiring agent-specific configuration handling through the `McpConfig` abstraction.

**Sources:** [crates/executors/src/executors/mod.rs74-128](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/executors/src/executors/mod.rs#L74-L128) [crates/executors/src/executors/mod.rs149-154](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/executors/src/executors/mod.rs#L149-L154)

### StandardCodingAgentExecutor Trait

The `StandardCodingAgentExecutor` trait defines MCP-related methods that each agent implementation must provide:

| Method | Purpose | Return Type |
|--------|---------|-------------|
| `default_mcp_config_path()` | Get the default config file path for the agent | `Option<PathBuf>` |
| `check_availability()` | Verify if the agent's MCP config exists | `bool` |
| `get_mcp_config()` | Get the MCP configuration template | `McpConfig` |
| `supports_mcp()` | Check if agent supports MCP integration | `bool` |

**Sources:** [crates/executors/src/executors/mod.rs147-154](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/executors/src/executors/mod.rs#L147-L154)

## REST API Endpoints

The backend provides REST API endpoints for loading and saving MCP server configurations, with automatic format detection and validation.

**Sources:** [crates/server/src/routes/config.rs146-180](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/server/src/routes/config.rs#L146-L180) [crates/server/src/routes/config.rs182-218](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/server/src/routes/config.rs#L182-L218)

### API Methods

- **GET /api/config/mcp-config**
  - Parameter: `executor` (BaseCodingAgent)
  - Returns: `GetMcpServerResponse` with current MCP configuration

- **POST /api/config/mcp-config**  
  - Parameter: `executor` (BaseCodingAgent)
  - Body: `UpdateMcpServersBody` with server configurations

**Sources:** [crates/server/src/routes/config.rs129-144](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/server/src/routes/config.rs#L129-L144)

## Configuration File Formats

The system handles different configuration file formats and structures across agents through helper functions that abstract the complexity.

**Sources:** [crates/server/src/routes/config.rs255-272](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/server/src/routes/config.rs#L255-L272) [crates/server/src/routes/config.rs274-308](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/server/src/routes/config.rs#L274-L308)

### Agent Configuration Paths

The path-based configuration system allows different agents to store MCP servers at different locations within their configuration files:

| Agent | Configuration Path | File Format | Example Path |
|-------|-------------------|-------------|--------------|
| Claude | `["mcpServers"]` | JSON | `config.mcpServers` |
| AMP | `["amp", "mcpServers"]` | JSON | `config.amp.mcpServers` |
| Opencode | `["mcp"]` | JSON | `config.mcp` |
| Codex | `["mcp_servers"]` | TOML | `config.mcp_servers` |

**Sources:** [crates/executors/src/executors/mod.rs76-121](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/executors/src/executors/mod.rs#L76-L121)

## User Interface Features

The MCP Servers page provides a user-friendly interface for configuring MCP servers across different executor profiles, with automatic validation and configuration management.

**Sources:** [frontend/src/pages/McpServers.tsx54-100](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/frontend/src/pages/McpServers.tsx#L54-L100) [frontend/src/pages/McpServers.tsx122-143](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/frontend/src/pages/McpServers.tsx#L122-L143) [frontend/src/pages/McpServers.tsx145-201](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/frontend/src/pages/McpServers.tsx#L145-L201)

### Key Features

- **Profile Selection**: Users can choose which executor profile to configure MCP servers for, with automatic loading of existing configurations.

- **JSON Editor**: Provides syntax highlighting and validation for editing MCP server configurations in the appropriate format for each agent.

- **Auto-Configuration**: One-click addition of the vibe-kanban MCP server with proper configuration for the selected agent type.

- **Real-time Validation**: Immediate feedback on JSON syntax errors and configuration structure validation.

**Sources:** [frontend/src/pages/McpServers.tsx248-280](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/frontend/src/pages/McpServers.tsx#L248-L280) [frontend/src/pages/McpServers.tsx302-347](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/frontend/src/pages/McpServers.tsx#L302-L347)

## Integration with Executor System

MCP integration is seamlessly incorporated into the executor system, allowing agents to access additional capabilities during task execution without requiring changes to the core execution logic.

The `get_mcp_config()` method on each `CodingAgent` variant returns the appropriate `McpConfig` instance that defines how MCP servers should be configured for that specific agent, including the command template for the vibe-kanban MCP server integration.

**Sources:** [crates/executors/src/executors/mod.rs74-123](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/crates/executors/src/executors/mod.rs#L74-L123) [frontend/src/pages/McpServers.tsx73-75](https://github.com/BloopAI/vibe-kanban/blob/0fba0979/frontend/src/pages/McpServers.tsx#L73-L75)


# Q1. DeepWiki Q&A with Code Context for Repository: BloopAI/vibe-kanban


## Q1

в Claude-Code я добавляю настройки MCP to `.mcp.json` and turn on at `.claude/settings.json` достаточно ли этого чтобы Vibe-Kanban работал с MCP? Я видел у них отдельные настройки в UI просто, может там нужно что-то еще и не нужно настраивать в Claude-Code?


### Answer

Судя по вашему вопросу, вы спрашиваете о настройке MCP (Multi-Client Protocol) для работы с Vibe-Kanban в Claude-Code. Вы упоминаете добавление настроек в `.mcp.json` и `.claude/settings.json`, но интересуетесь, достаточно ли этого или нужно что-то дополнительно настроить в UI Vibe-Kanban.

## Ответ

Нет, добавления настроек только в `.mcp.json` и `.claude/settings.json` недостаточно. <cite/> Согласно коду Vibe-Kanban, система MCP использует другой путь конфигурации для Claude-Code.

### Правильный путь конфигурации для Claude-Code

Claude-Code в Vibe-Kanban ожидает конфигурацию MCP в файле `~/.claude.json`, а не в `.claude/settings.json`: vibe-kanban:170-172 

Система использует путь `["mcpServers"]` внутри этого файла: vibe-kanban:111-121 

### UI настройки в Vibe-Kanban

Да, в Vibe-Kanban есть отдельный UI для настройки MCP серверов. <cite/> Вы можете использовать страницу "MCP Servers" в интерфейсе Vibe-Kanban: vibe-kanban:27-39 

Эта страница позволяет:
- Выбрать профиль исполнителя (executor profile)
- Настроить MCP серверы через JSON редактор
- Автоматически добавить Vibe-Kanban MCP сервер одной кнопкой

### Автоматическая настройка

Система предоставляет кнопку "Add Vibe-Kanban MCP" для автоматического добавления конфигурации: vibe-kanban:122-143 

Эта функция автоматически добавляет правильную конфигурацию для vibe-kanban MCP сервера с командой `npx -y vibe-kanban --mcp`.

## Рекомендация

Используйте UI Vibe-Kanban для настройки MCP вместо ручного редактирования файлов конфигурации. <cite/> Это гарантирует, что настройки будут сохранены в правильном месте (`~/.claude.json`) и в правильном формате для Claude-Code.

## Notes

Система MCP в Vibe-Kanban поддерживает различные AI агенты (Claude, Cursor, Amp, Gemini, Codex, OpenCode, QwenCode), каждый из которых имеет свой собственный путь конфигурации. Для Claude-Code это именно `~/.claude.json`, а не `.claude/settings.json` как вы упомянули.

Wiki pages you might want to explore:
- [AI Executor System (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#3)
- [MCP Integration (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#3.3)
- [Configuration and Settings (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#5.5)


### Citations


**File:** crates/executors/src/executors/claude.rs (L170-172)

```rust
    fn default_mcp_config_path(&self) -> Option<std::path::PathBuf> {
        dirs::home_dir().map(|home| home.join(".claude.json"))
    }
```

**File:** crates/executors/src/executors/mod.rs (L111-121)

```rust
            _ => McpConfig::new(
                vec!["mcpServers".to_string()],
                serde_json::json!({
                    "mcpServers": {}
                }),
                serde_json::json!({
                    "command": "npx",
                    "args": ["-y", "vibe-kanban", "--mcp"],
                }),
                false,
            ),
```

**File:** frontend/src/pages/McpServers.tsx (L27-39)

```typescript
export function McpServers() {
  const { config, profiles } = useUserSystem();
  const [mcpServers, setMcpServers] = useState('{}');
  const [mcpConfig, setMcpConfig] = useState<McpConfig | null>(null);
  const [mcpError, setMcpError] = useState<string | null>(null);
  const [mcpLoading, setMcpLoading] = useState(true);
  const [selectedProfile, setSelectedProfile] = useState<ExecutorConfig | null>(
    null
  );
  const [mcpApplying, setMcpApplying] = useState(false);
  const [mcpConfigPath, setMcpConfigPath] = useState<string>('');
  const [success, setSuccess] = useState(false);

```

**File:** frontend/src/pages/McpServers.tsx (L122-143)

```typescript
  const handleConfigureVibeKanban = async () => {
    if (!selectedProfile || !mcpConfig) return;

    try {
      // Parse existing configuration
      const existingConfig = mcpServers.trim() ? JSON.parse(mcpServers) : {};

      // Add vibe_kanban to the existing configuration using the schema
      const updatedConfig = McpConfigStrategyGeneral.addVibeKanbanToConfig(
        mcpConfig,
        existingConfig
      );

      // Update the textarea with the new configuration
      const configJson = JSON.stringify(updatedConfig, null, 2);
      setMcpServers(configJson);
      setMcpError(null);
    } catch (err) {
      setMcpError('Failed to configure vibe-kanban MCP server');
      console.error('Error configuring vibe-kanban:', err);
    }
  };
```

# Q2. Vibe-Kanban setup MCPs on Mac,Ubuntu
````question
Окей, эти настройки только UI Vibe canban можно или через Cli? 
Судя по тому что я читаю я могу `npx -y vibe-kanban --mc` ? верно?
Ну и для Macos, Ubuntu нет никакой разницы?

У меня Vibe-kanban
- macos: `/Users/user/.nvm/versions/node/v20.18.1/bin/npx`
- ubuntu: `"/home/user/.nvm/versions/node/v22.17.0/bin/npx"`

Дай подробный чеклист и инструкции для AI-Agents,  чтобы он не запутался и правильно настроил мне пачку других MCP на обоих машинах.

```
    "sequential-thinking",
    "brave-search",
    "perplexity",
    "vibe-kanban",
    "time",
    "duckduckgo"
```
````

Да, вы правы! Настройки MCP можно делать как через UI Vibe-Kanban, так и через CLI. [1](#1-0) 

## CLI команда для MCP

Правильная команда: `npx -y vibe-kanban --mcp` (не `--mc`). [2](#1-1)  Эта команда запускает MCP сервер vibe-kanban, который затем может быть использован другими AI агентами.

## Различия между macOS и Ubuntu

Нет никаких различий в настройке между macOS и Ubuntu. [3](#1-2)  CLI автоматически определяет платформу и архитектуру, но логика MCP одинакова для всех систем.

## Подробный чеклист для настройки MCP серверов

### Шаг 1: Установка и проверка vibe-kanban
```bash
# Проверить что vibe-kanban работает
npx -y vibe-kanban --mcp
```

### Шаг 2: Настройка через UI Vibe-Kanban (рекомендуется)

1. Запустите Vibe-Kanban: `npx -y vibe-kanban`
2. Откройте страницу "MCP Servers" в интерфейсе
3. Выберите профиль "CLAUDE_CODE" [4](#1-3) 
4. Используйте JSON редактор для добавления серверов [5](#1-4) 

### Шаг 3: Конфигурация для всех MCP серверов

Добавьте в JSON редактор следующую конфигурацию:

```json
{
  "sequential-thinking": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
  },
  "brave-search": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-brave-search"],
    "env": {
      "BRAVE_API_KEY": "your-brave-api-key"
    }
  },
  "perplexity": {
    "command": "npx",
    "args": ["-y", "@perplexity/mcp-server"]
  },
  "vibe-kanban": {
    "command": "npx",
    "args": ["-y", "vibe-kanban", "--mcp"]
  },
  "time": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-time"]
  },
  "duckduckgo": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-duckduckgo"]
  }
}
```

### Шаг 4: Автоматическое добавление vibe-kanban

Используйте кнопку "Add Vibe-Kanban MCP" для автоматического добавления. [6](#1-5)  Это добавит правильную конфигурацию автоматически.

### Шаг 5: Сохранение настроек

Нажмите "Save Settings" для сохранения конфигурации в `~/.claude.json`. [7](#1-6) 

### Шаг 6: Проверка конфигурации

Конфигурация будет сохранена в файле `~/.claude.json` с путем `["mcpServers"]`. [8](#1-7) 

## Важные моменты

1. **Путь конфигурации**: Claude-Code использует `~/.claude.json`, а не `.claude/settings.json` [9](#1-8) 

2. **API ключи**: Для некоторых серверов (brave-search, perplexity) могут потребоваться API ключи

3. **Проверка доступности**: Система автоматически проверяет доступность MCP конфигурации [10](#1-9) 

## Notes

Vibe-Kanban поддерживает настройку MCP серверов для разных AI агентов (Claude, Cursor, Amp, Gemini, Codex, OpenCode, QwenCode), каждый с собственным путем конфигурации. Для Claude-Code используется стандартный путь `~/.claude.json` с атрибутом `mcpServers`.

Wiki pages you might want to explore:
- [AI Executor System (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#3)
- [MCP Integration (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#3.3)

### Citations

**File:** npx-cli/bin/cli.js (L40-56)
```javascript
function getPlatformDir() {
  if (platform === "linux" && arch === "x64") return "linux-x64";
  if (platform === "linux" && arch === "arm64") return "linux-arm64";
  if (platform === "win32" && arch === "x64") return "windows-x64";
  if (platform === "win32" && arch === "arm64") return "windows-arm64";
  if (platform === "darwin" && arch === "x64") return "macos-x64";
  if (platform === "darwin" && arch === "arm64") return "macos-arm64";

  console.error(`❌ Unsupported platform: ${platform}-${arch}`);
  console.error("Supported platforms:");
  console.error("  - Linux x64");
  console.error("  - Linux ARM64");
  console.error("  - Windows x64");
  console.error("  - Windows ARM64");
  console.error("  - macOS x64 (Intel)");
  console.error("  - macOS ARM64 (Apple Silicon)");
  process.exit(1);
```

**File:** npx-cli/bin/cli.js (L65-65)
```javascript
const isMcpMode = process.argv.includes("--mcp");
```

**File:** npx-cli/bin/cli.js (L100-113)
```javascript
if (isMcpMode) {
  extractAndRun("vibe-kanban-mcp", (bin) => {
    const proc = spawn(bin, [], { stdio: "inherit" });
    proc.on("exit", (c) => process.exit(c || 0));
    proc.on("error", (e) => {
      console.error("❌ MCP server error:", e.message);
      process.exit(1);
    });
    process.on("SIGINT", () => {
      console.error("\n🛑 Shutting down MCP server...");
      proc.kill("SIGINT");
    });
    process.on("SIGTERM", () => proc.kill("SIGTERM"));
  });
```

**File:** frontend/src/pages/McpServers.tsx (L275-280)
```typescript
                </SelectContent>
              </Select>
              <p className="text-sm text-muted-foreground">
                Choose which profile to configure MCP servers for.
              </p>
            </div>
```

**File:** frontend/src/pages/McpServers.tsx (L302-314)
```typescript
                <Label htmlFor="mcp-servers">MCP Server Configuration</Label>
                <JSONEditor
                  id="mcp-servers"
                  placeholder={
                    mcpLoading
                      ? 'Loading current configuration...'
                      : '{\n  "server-name": {\n    "type": "stdio",\n    "command": "your-command",\n    "args": ["arg1", "arg2"]\n  }\n}'
                  }
                  value={mcpLoading ? 'Loading...' : mcpServers}
                  onChange={handleMcpServersChange}
                  disabled={mcpLoading}
                  minHeight={300}
                />
```

**File:** frontend/src/pages/McpServers.tsx (L336-346)
```typescript
                  <Button
                    onClick={handleConfigureVibeKanban}
                    disabled={mcpApplying || mcpLoading || !selectedProfile}
                    className="w-64"
                  >
                    Add Vibe-Kanban MCP
                  </Button>
                  <p className="text-sm text-muted-foreground mt-2">
                    Automatically adds the Vibe-Kanban MCP server.
                  </p>
                </div>
```

**File:** frontend/src/pages/McpServers.tsx (L355-364)
```typescript
            <Button
              onClick={handleApplyMcpServers}
              disabled={mcpApplying || mcpLoading || !!mcpError || success}
              className={success ? 'bg-green-600 hover:bg-green-700' : ''}
            >
              {mcpApplying && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
              {success && <span className="mr-2">✓</span>}
              {success ? 'Settings Saved!' : 'Save Settings'}
            </Button>
          </div>
```

**File:** crates/executors/src/executors/mod.rs (L111-121)
```rust
            _ => McpConfig::new(
                vec!["mcpServers".to_string()],
                serde_json::json!({
                    "mcpServers": {}
                }),
                serde_json::json!({
                    "command": "npx",
                    "args": ["-y", "vibe-kanban", "--mcp"],
                }),
                false,
            ),
```

**File:** crates/executors/src/executors/mod.rs (L149-153)
```rust
    async fn check_availability(&self) -> bool {
        self.default_mcp_config_path()
            .map(|path| path.exists())
            .unwrap_or(false)
    }
```

**File:** crates/executors/src/executors/claude.rs (L170-172)
```rust
    fn default_mcp_config_path(&self) -> Option<std::path::PathBuf> {
        dirs::home_dir().map(|home| home.join(".claude.json"))
    }
```
