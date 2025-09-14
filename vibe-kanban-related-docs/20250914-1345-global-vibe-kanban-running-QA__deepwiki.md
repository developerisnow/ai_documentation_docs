# DeepWiki Q&A with Code Context for Repository: BloopAI/vibe-kanban


## Q1

Я подключил и установил Vibe-Kanban к одному из проектов
```
{
    "mcpServers": {
      "vibe-kanban": {
        "command": "/Users/user/.nvm/versions/node/v20.18.1/bin/npx",
        "args": ["-y", "vibe-kanban"]
      }
    }
}

```
А потом ко второму и тому подобное, и заметил что он запускается на random-port http://127.0.0.1:{port1,2,3,4} каждый раз когда я открываю новый claude-code session внутри проекта где есть записи с MCP
И все эти запущенные инстансы - отдельные или просто порты разные и ссылаются на один имеют одну БД потому что я вижу подключенные. проекты (как мы знаем подключить можно PATH or REPO from github-account),
Я сейчас думаю что это полная хрень потому что нам же нужно его добавлять везде, тогда вопрос а где нужно нам добавлять его и как запускать правильно? 
Просто глобально что ли?

2) По пути размышления у меня возник второй вопрос. Если это просто как отдельное nodejs приложение сервер по менеджементу Vibe-Kanban то зачем нам вообще его добавлять в `%project%/.mcp.json + %project%/.claude/settings.json` это не имеет смысла же? 
3) А тут нащупывается интересный кейс. Ведь мы добавляем когда instance claude, или может claude-desktop сможет его вызывать? типо создать там Task(в моем понимании Task=Session of Running session claude-code) может Claude-Desktop и оркестрировать их да? А runner-ом таска у нас будут выступать CLaude-Code; типо Claude-Desktop же умный дать ему на 2-3 часа пучок задач он orchestrate там Claude-Code-агентами по сути, а так через Vibe-Kanban mcp-api и тп? Такое задумано вообще было? Вообще не читал в доках и не очевидно, чисто сам додумал?

Изучи документацию и ответь подробно на каждый из 3 вопросов


### Answer

Изучив документацию и код Vibe-Kanban, отвечу на каждый из ваших вопросов:

## Ответ

Вы правильно заметили проблему с множественными инстансами Vibe-Kanban. <cite/> Каждый раз при запуске Claude Code создается новый процесс `npx vibe-kanban`, который получает случайный порт через автоназначение. vibe-kanban:61-64 

### 1. Множественные инстансы и правильная установка

**Проблема**: Каждый инстанс Vibe-Kanban действительно отдельный - у каждого своя база данных SQLite в локальной директории. vibe-kanban:44-50  Это означает, что проекты не синхронизируются между инстансами.

**Решение**: Vibe-Kanban предоставляет централизованную систему управления MCP конфигурациями. vibe-kanban:248-280  Вместо добавления в каждый проект отдельно, используйте глобальную конфигурацию через интерфейс Vibe-Kanban на странице "MCP Servers".

### 2. Архитектура MCP интеграции

Vibe-Kanban не просто Node.js приложение - это полноценная система управления задачами с MCP сервером. vibe-kanban:813-817  

MCP сервер предоставляет инструменты для создания и управления задачами: `list_projects`, `list_tasks`, `create_task`, `get_task`, `update_task`, `delete_task`. vibe-kanban:817-817 

Добавление в проектные конфигурации имеет смысл, потому что каждый Claude Code сеанс получает доступ к этим инструментам для управления задачами в контексте конкретного проекта.

### 3. Оркестрация через Claude Desktop

Ваша идея об оркестрации через Claude Desktop интересна, но **не соответствует текущей архитектуре**. <cite/> 

Система поддерживает различные агенты (Claude, Amp, Gemini, Codex, Opencode, Cursor, QwenCode), но каждый работает независимо. vibe-kanban:63-71  

Конфигурация MCP для каждого агента определяется отдельно с разными путями и форматами. vibe-kanban:74-123  Например, Claude использует путь `["mcpServers"]`, а Amp - `["amp", "mcpServers"]`.

## Notes

Для правильной работы рекомендую использовать централизованную конфигурацию MCP через веб-интерфейс Vibe-Kanban вместо ручного добавления в каждый проект. Система автоматически управляет конфигурационными файлами для разных агентов. vibe-kanban:220-253 

Wiki pages you might want to explore:
- [MCP Integration (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#3.3)


### Citations


**File:** crates/server/src/main.rs (L61-64)

```rust
        .unwrap_or_else(|| {
            tracing::info!("No PORT environment variable set, using port 0 for auto-assignment");
            0
        }); // Use 0 to find free port if no specific port provided
```

**File:** crates/server/src/bin/mcp_task_server.rs (L44-50)

```rust
            let database_url = format!(
                "sqlite://{}",
                asset_dir().join("db.sqlite").to_string_lossy()
            );

            let options = SqliteConnectOptions::from_str(&database_url)?.create_if_missing(false);
            let pool = SqlitePool::connect_with(options).await?;
```

**File:** frontend/src/pages/McpServers.tsx (L248-280)

```typescript
            <div className="space-y-2">
              <Label htmlFor="mcp-executor">Profile</Label>
              <Select
                value={
                  selectedProfile
                    ? Object.keys(profiles || {}).find(
                        (key) => profiles![key] === selectedProfile
                      ) || ''
                    : ''
                }
                onValueChange={(value: string) => {
                  const profile = profiles?.[value];
                  if (profile) setSelectedProfile(profile);
                }}
              >
                <SelectTrigger id="mcp-executor">
                  <SelectValue placeholder="Select executor" />
                </SelectTrigger>
                <SelectContent>
                  {profiles &&
                    Object.entries(profiles)
                      .sort((a, b) => a[0].localeCompare(b[0]))
                      .map(([profileKey]) => (
                        <SelectItem key={profileKey} value={profileKey}>
                          {profileKey}
                        </SelectItem>
                      ))}
                </SelectContent>
              </Select>
              <p className="text-sm text-muted-foreground">
                Choose which profile to configure MCP servers for.
              </p>
            </div>
```

**File:** crates/server/src/mcp/task_server.rs (L813-817)

```rust
            server_info: Implementation {
                name: "vibe-kanban".to_string(),
                version: "1.0.0".to_string(),
            },
            instructions: Some("A task and project management server. If you need to create or update tickets or tasks then use these tools. Most of them absolutely require that you pass the `project_id` of the project that you are currently working on. This should be provided to you. Call `list_tasks` to fetch the `task_ids` of all the tasks in a project`. TOOLS: 'list_projects', 'list_tasks', 'create_task', 'get_task', 'update_task', 'delete_task'. Make sure to pass `project_id` or `task_id` where required. You can use list tools to get the available ids.".to_string()),
```

**File:** crates/executors/src/executors/mod.rs (L63-71)

```rust
pub enum CodingAgent {
    ClaudeCode,
    Amp,
    Gemini,
    Codex,
    Opencode,
    Cursor,
    QwenCode,
}
```

**File:** crates/executors/src/executors/mod.rs (L74-123)

```rust
    pub fn get_mcp_config(&self) -> McpConfig {
        match self {
            Self::Codex(_) => McpConfig::new(
                vec!["mcp_servers".to_string()],
                serde_json::json!({
                    "mcp_servers": {}
                }),
                serde_json::json!({
                    "command": "npx",
                    "args": ["-y", "vibe-kanban", "--mcp"],
                }),
                true,
            ),
            Self::Amp(_) => McpConfig::new(
                vec!["amp.mcpServers".to_string()],
                serde_json::json!({
                    "amp.mcpServers": {}
                }),
                serde_json::json!({
                    "command": "npx",
                    "args": ["-y", "vibe-kanban", "--mcp"],
                }),
                false,
            ),
            Self::Opencode(_) => McpConfig::new(
                vec!["mcp".to_string()],
                serde_json::json!({
                    "mcp": {},
                    "$schema": "https://opencode.ai/config.json"
                }),
                serde_json::json!({
                    "type": "local",
                    "command": ["npx", "-y", "vibe-kanban", "--mcp"],
                    "enabled": true
                }),
                false,
            ),
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
        }
    }
```

**File:** crates/server/src/routes/config.rs (L220-253)

```rust
async fn update_mcp_servers_in_config(
    config_path: &std::path::Path,
    mcpc: &McpConfig,
    new_servers: HashMap<String, Value>,
) -> Result<String, Box<dyn std::error::Error + Send + Sync>> {
    // Ensure parent directory exists
    if let Some(parent) = config_path.parent() {
        fs::create_dir_all(parent).await?;
    }
    // Read existing config (JSON or TOML depending on agent)
    let mut config = read_agent_config(config_path, mcpc).await?;

    // Get the current server count for comparison
    let old_servers = get_mcp_servers_from_config_path(&config, &mcpc.servers_path).len();

    // Set the MCP servers using the correct attribute path
    set_mcp_servers_in_config_path(&mut config, &mcpc.servers_path, &new_servers)?;

    // Write the updated config back to file (JSON or TOML depending on agent)
    write_agent_config(config_path, mcpc, &config).await?;

    let new_count = new_servers.len();
    let message = match (old_servers, new_count) {
        (0, 0) => "No MCP servers configured".to_string(),
        (0, n) => format!("Added {} MCP server(s)", n),
        (old, new) if old == new => format!("Updated MCP server configuration ({} server(s))", new),
        (old, new) => format!(
            "Updated MCP server configuration (was {}, now {})",
            old, new
        ),
    };

    Ok(message)
}
```

## Q2

Ты плохо объяснил что значит централизированную? Как ты понял я использую Claude-Code, у меня там 50 проектов, мне внутри каждого прописывать? Или наоборот - ни в коем случае этого не делать и сделать это один раз лишь глобально для моего девайса в `~/.claude/.claude.json` или каком конкретно месте? может еще включать в `~/.claude/settings.json` ?


### Answer

## Ответ

Вы правы, я плохо объяснил централизованную конфигурацию. Позвольте уточнить конкретно для Claude Code.

**НЕ нужно** добавлять MCP конфигурацию в каждый из 50 проектов. <cite/> Вместо этого используйте **глобальную конфигурацию** в файле `~/.claude.json`. vibe-kanban:170-172 

### Правильный подход для Claude Code

1. **Один раз глобально**: Добавьте MCP сервер в `~/.claude.json` vibe-kanban:170-172 

2. **Используйте веб-интерфейс Vibe-Kanban**: Откройте страницу "MCP Servers" в Vibe-Kanban, выберите профиль "CLAUDE_CODE" и настройте конфигурацию там. vibe-kanban:73-75 

3. **Автоматическое управление**: Система автоматически обновит ваш `~/.claude.json` файл с правильной структурой. vibe-kanban:172-179 

### Структура конфигурации

Claude Code использует путь `["mcpServers"]` в конфигурационном файле. vibe-kanban:111-122  Это означает, что в `~/.claude.json` будет создана секция:

```json
{
  "mcpServers": {
    "vibe-kanban": {
      "command": "npx",
      "args": ["-y", "vibe-kanban", "--mcp"]
    }
  }
}
```

### Почему это работает

Когда вы запускаете Claude Code в любом из ваших 50 проектов, он автоматически читает глобальную конфигурацию из `~/.claude.json` и подключается к MCP серверам, определенным там. vibe-kanban:170-172  Таким образом, все проекты будут использовать один и тот же инстанс Vibe-Kanban MCP сервера.

## Notes

Не используйте `~/.claude/settings.json` - это не тот путь, который использует система. Правильный путь именно `~/.claude.json` в домашней директории. vibe-kanban:170-172 

Wiki pages you might want to explore:
- [AI Executor System (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#3)
- [MCP Integration (BloopAI/vibe-kanban)](/wiki/BloopAI/vibe-kanban#3.3)


### Citations


**File:** crates/executors/src/executors/claude.rs (L170-172)

```rust
    fn default_mcp_config_path(&self) -> Option<std::path::PathBuf> {
        dirs::home_dir().map(|home| home.join(".claude.json"))
    }
```

**File:** frontend/src/pages/McpServers.tsx (L73-75)

```typescript
        const result = await mcpServersApi.load({
          executor: profileKey as BaseCodingAgent,
        });
```

**File:** crates/server/src/routes/config.rs (L172-179)

```rust
    let mut mcpc = coding_agent.get_mcp_config();
    let raw_config = read_agent_config(&config_path, &mcpc).await?;
    let servers = get_mcp_servers_from_config_path(&raw_config, &mcpc.servers_path);
    mcpc.set_servers(servers);
    Ok(ResponseJson(ApiResponse::success(GetMcpServerResponse {
        mcp_config: mcpc,
        config_path: config_path.to_string_lossy().to_string(),
    })))
```

**File:** crates/executors/src/executors/mod.rs (L111-122)

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
        }
```

## Q3

ты меня как бы обрубил что claude-desktop не может быть orchestrator, это не для этого сделано - но явно мне не объяснил. Еще раз, давай think step by step, sequential ultrathink!
а может ли claude-desktop использовать его как mcp условно говоря создать task, а этот task=session-claude-code который пойдет там 5-100 минут что-то делать, в это время claude-desktop ждет и мониторит статус задачи, как доделана смотрит output task? 

Типо это наоборот MCP в другую сторону - сервере Vibe-Kanban имеет методы дергать "ручки" Claude-Code и прочих агентов чтобы использовать их для выполнения задачи, по сути он им дает промпт, транслирует их output streaming промежуточный и финальный?

Давай четко изучи посмотри в коде!


### Answer

