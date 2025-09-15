# DeepWiki Q&A with Code Context for Repository: getzep/graphiti


## Q1

I want to put 1M rows like this in a my graphs and structure and chat with data smart what is the best way to start ? 
`{"parentUuid":"bfd636c5-3a0f-4afb-9b05-ee3f414944a8","isSidechain":false,"userType":"external","cwd":"/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden","sessionId":"cd336f00-1ac8-48a6-a345-8790b68cd87c","version":"1.0.113","gitBranch":"main","message":{"id":"msg_012vB2RA8M8TjspxpqjQ71K4","type":"message","role":"assistant","model":"claude-opus-4-1-20250805","content":[{"type":"tool_use","id":"toolu_0186JxWK5zweJ668U8Kf7Lvx","name":"Write","input":{"file_path":"/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden/memory-bank/Scrum/20250915-BUG-503-namespace-proxy1-error/README.md","content":"# 🐛 BUG-503: AI Selector NATS Subscription Failure\n\n**Date**: 2025-09-15\n**Status**: ✅ Resolved (Temporary) | 🔧 Permanent Fix Pending\n**Severity**: Critical\n**Namespace**: hypetrain-proxy1\n\n---\n\n## 📁 Documentation Structure\n\n| File | Purpose | Status |\n|------|---------|--------|\n| [`20250915-BUG-503-namespace-proxy1-error.bug.md`](./20250915-BUG-503-namespace-proxy1-error.bug.md) | Main bug report | ✅ Complete |\n| [`root-cause-analysis.md`](./root-cause-analysis.md) | Why it happened (GCP issue) | ✅ Complete |\n| [`fix-instructions-sasha-ru.md`](./fix-instructions-sasha-ru.md) | Russian fix instructions | ✅ Complete |\n| [`nats-inspection-commands.md`](./nats-inspection-commands.md) | NATS debugging commands | ✅ Complete |\n| [`telegram-monitoring.py`](./telegram-monitoring.py) | Monitoring script | ✅ Ready |\n| [`discovery-investigation-hypetrain-api-aibridge.story.md`](./discovery-investigation-hypetrain-api-aibridge.story.md) | Fire-and-forget investigation | 🔄 In Progress |\n\n---\n\n## 🎯 Quick Summary\n\n### Problem\nAI Selector lost NATS subscriptions after disconnect → 503 errors\n\n### Root Cause\nGCP infrastructure issue + reconnection handler bug\n\n### Solution Applied\nService restart restored subscriptions\n\n### Permanent Fix Required\n1. Update reconnection handler in `src/nats/nats_client.py`\n2. Implement fire-and-forget messaging (Sasha's proposal)\n\n---\n\n## 🔗 Navigation\n\n**Previous**: [20250914-deployment-issues](../20250914-deployment-issues/)\n**Next**: [20250916-nats-fire-and-forget](../20250916-nats-fire-and-forget/)\n**Related**:\n- [AI Selector Architecture](../../docs/dev/01-architecture/services/ai_selector/_v3/)\n- [NATS Configuration](../../docs/ops/02-infrastructure/nats.md)\n\n---\n\n## 👥 Team\n\n- **Alex** (Tech Lead): Investigation & temporary fix\n- **Sasha Romanov**: Permanent fix implementation\n- **Anton M.**: Frontend impact analysis"}}],"stop_reason":null,"stop_sequence":null,"usage":{"input_tokens":4,"cache_creation_input_tokens":306,"cache_read_input_tokens":147449,"cache_creation":{"ephemeral_5m_input_tokens":306,"ephemeral_1h_input_tokens":0},"output_tokens":8,"service_tier":"standard"}},"requestId":"req_011CT9uFRGTsw9xDwb2qqehZ","type":"assistant","uuid":"c072c529-6d58-415b-9ef1-4954bed0eb60","timestamp":"2025-09-15T08:52:10.933Z"}`


### Answer

Looking at your query, you want to ingest 1 million rows of structured JSON data (similar to the example you provided) into Graphiti's knowledge graph system and then be able to chat with that data intelligently. <cite/>

## Answer

For ingesting 1M rows of structured JSON data into Graphiti, here's the best approach:

### 1. Use Bulk Episode Processing

Graphiti provides `add_episode_bulk()` specifically for high-volume data ingestion graphiti:792-830 . This method processes multiple episodes concurrently and uses optimized bulk database operations graphiti:109-200 .

### 2. Configure for High Throughput

Set the `SEMAPHORE_LIMIT` environment variable to control concurrency graphiti:195-202 . Start with a higher value (e.g., 50-100) if your LLM provider supports it, or lower (10-20) to avoid rate limits.

### 3. Use JSON Source Type

When adding episodes, use `source="json"` to automatically extract entities and relationships from your structured data graphiti:712-754 . Your JSON data will be processed to create nodes and edges automatically.

### 4. Setup Process

1. **Install Graphiti with appropriate database support**:
   ```bash
   pip install graphiti-core[neo4j]  # or [falkordb] for FalkorDB
   ```

2. **Configure environment variables** graphiti:243-270 :
   - `OPENAI_API_KEY` (required)
   - `NEO4J_URI`, `NEO4J_USER`, `NEO4J_PASSWORD` 
   - `SEMAPHORE_LIMIT=50` (adjust based on your LLM provider limits)

3. **Use the MCP Server for chat interface** graphiti:1-23 . This provides tools for querying your graph data through AI assistants.

### 5. Example Implementation

```python
# Process your 1M rows in batches
episodes = []
for row in your_data:
    episodes.append({
        'name': f"Record {row['id']}",
        'episode_body': json.dumps(row),  # Your JSON data
        'source': EpisodeType.json,
        'group_id': 'your_dataset_id'
    })

# Process in batches of 100-1000
batch_size = 500
for i in range(0, len(episodes), batch_size):
    batch = episodes[i:i+batch_size]
    await graphiti.add_episode_bulk(batch)
```

### 6. Chat Interface

Deploy the MCP server graphiti:24-61  to enable AI assistants (Claude, Cursor, etc.) to query your knowledge graph using natural language. The server provides tools like `search_nodes`, `search_facts`, and `get_episodes` for intelligent data retrieval.

## Notes

The bulk processing functionality is specifically designed for large datasets and includes optimizations for different graph databases (Neo4j, FalkorDB, Kuzu). The JSON processing will automatically extract entities and relationships from your structured data, making it queryable through the graph interface. Consider using `group_id` to namespace your data if you plan to have multiple datasets.

Wiki pages you might want to explore:
- [Environment Variables (getzep/graphiti)](/wiki/getzep/graphiti#9.1)


### Citations


**File:** graphiti_core/graphiti.py (L792-830)

```python
            hydrated_nodes_results: list[list[EntityNode]] = await semaphore_gather(
                *[
                    extract_attributes_from_nodes(
                        self.clients,
                        nodes_by_episode_unique[episode.uuid],
                        episode,
                        previous_episodes,
                        entity_types,
                    )
                    for episode, previous_episodes in episode_context
                ]
            )

            final_hydrated_nodes = [node for nodes in hydrated_nodes_results for node in nodes]

            edges_by_episode_unique: dict[str, list[EntityEdge]] = {}
            edges_uuid_set: set[str] = set()
            for episode_uuid, edges in edges_by_episode.items():
                edges_with_updated_pointers = resolve_edge_pointers(edges, uuid_map)
                edges_by_episode_unique[episode_uuid] = []

                for edge in edges_with_updated_pointers:
                    if edge.uuid not in edges_uuid_set:
                        edges_by_episode_unique[episode_uuid].append(edge)
                        edges_uuid_set.add(edge.uuid)

            edge_results = await semaphore_gather(
                *[
                    resolve_extracted_edges(
                        self.clients,
                        edges_by_episode_unique[episode.uuid],
                        episode,
                        hydrated_nodes,
                        edge_types or {},
                        edge_type_map or edge_type_map_default,
                    )
                    for episode in episodes
                ]
            )
```

**File:** graphiti_core/utils/bulk_utils.py (L109-200)

```python
async def add_nodes_and_edges_bulk_tx(
    tx: GraphDriverSession,
    episodic_nodes: list[EpisodicNode],
    episodic_edges: list[EpisodicEdge],
    entity_nodes: list[EntityNode],
    entity_edges: list[EntityEdge],
    embedder: EmbedderClient,
    driver: GraphDriver,
):
    episodes = [dict(episode) for episode in episodic_nodes]
    for episode in episodes:
        episode['source'] = str(episode['source'].value)
        episode.pop('labels', None)
        if driver.provider == GraphProvider.NEO4J:
            episode['group_label'] = 'Episodic_' + episode['group_id'].replace('-', '')

    nodes = []

    for node in entity_nodes:
        if node.name_embedding is None:
            await node.generate_name_embedding(embedder)

        entity_data: dict[str, Any] = {
            'uuid': node.uuid,
            'name': node.name,
            'name_embedding': node.name_embedding,
            'group_id': node.group_id,
            'summary': node.summary,
            'created_at': node.created_at,
        }

        entity_data['labels'] = list(set(node.labels + ['Entity']))
        if driver.provider == GraphProvider.KUZU:
            attributes = convert_datetimes_to_strings(node.attributes) if node.attributes else {}
            entity_data['attributes'] = json.dumps(attributes)
        else:
            entity_data.update(node.attributes or {})
            entity_data['labels'] = list(
                set(node.labels + ['Entity', 'Entity_' + node.group_id.replace('-', '')])
            )

        nodes.append(entity_data)

    edges = []
    for edge in entity_edges:
        if edge.fact_embedding is None:
            await edge.generate_embedding(embedder)
        edge_data: dict[str, Any] = {
            'uuid': edge.uuid,
            'source_node_uuid': edge.source_node_uuid,
            'target_node_uuid': edge.target_node_uuid,
            'name': edge.name,
            'fact': edge.fact,
            'fact_embedding': edge.fact_embedding,
            'group_id': edge.group_id,
            'episodes': edge.episodes,
            'created_at': edge.created_at,
            'expired_at': edge.expired_at,
            'valid_at': edge.valid_at,
            'invalid_at': edge.invalid_at,
        }

        if driver.provider == GraphProvider.KUZU:
            attributes = convert_datetimes_to_strings(edge.attributes) if edge.attributes else {}
            edge_data['attributes'] = json.dumps(attributes)
        else:
            edge_data.update(edge.attributes or {})

        edges.append(edge_data)

    if driver.provider == GraphProvider.KUZU:
        # FIXME: Kuzu's UNWIND does not currently support STRUCT[] type properly, so we insert the data one by one instead for now.
        episode_query = get_episode_node_save_bulk_query(driver.provider)
        for episode in episodes:
            await tx.run(episode_query, **episode)
        entity_node_query = get_entity_node_save_bulk_query(driver.provider, nodes)
        for node in nodes:
            await tx.run(entity_node_query, **node)
        entity_edge_query = get_entity_edge_save_bulk_query(driver.provider)
        for edge in edges:
            await tx.run(entity_edge_query, **edge)
        episodic_edge_query = get_episodic_edge_save_bulk_query(driver.provider)
        for edge in episodic_edges:
            await tx.run(episodic_edge_query, **edge.model_dump())
    else:
        await tx.run(get_episode_node_save_bulk_query(driver.provider), episodes=episodes)
        await tx.run(get_entity_node_save_bulk_query(driver.provider, nodes), nodes=nodes)
        await tx.run(
            get_episodic_edge_save_bulk_query(driver.provider),
            episodic_edges=[edge.model_dump() for edge in episodic_edges],
        )
        await tx.run(get_entity_edge_save_bulk_query(driver.provider), entity_edges=edges)
```

**File:** README.md (L195-202)

```markdown
## Default to Low Concurrency; LLM Provider 429 Rate Limit Errors

Graphiti's ingestion pipelines are designed for high concurrency. By default, concurrency is set low to avoid LLM Provider 429 Rate Limit Errors. If you find Graphiti slow, please increase concurrency as described below.

Concurrency controlled by the `SEMAPHORE_LIMIT` environment variable. By default, `SEMAPHORE_LIMIT` is set to `10` concurrent operations to help prevent `429` rate limit errors from your LLM provider. If you encounter such errors, try lowering this value.

If your LLM provider allows higher throughput, you can increase `SEMAPHORE_LIMIT` to boost episode ingestion performance.

```

**File:** README.md (L243-270)

```markdown
## Optional Environment Variables

In addition to the Neo4j and OpenAi-compatible credentials, Graphiti also has a few optional environment variables.
If you are using one of our supported models, such as Anthropic or Voyage models, the necessary environment variables
must be set.

### Database Configuration

Database names are configured directly in the driver constructors:

- **Neo4j**: Database name defaults to `neo4j` (hardcoded in Neo4jDriver)
- **FalkorDB**: Database name defaults to `default_db` (hardcoded in FalkorDriver)

As of v0.17.0, if you need to customize your database configuration, you can instantiate a database driver and pass it to the Graphiti constructor using the `graph_driver` parameter.

#### Neo4j with Custom Database Name

```python
from graphiti_core import Graphiti
from graphiti_core.driver.neo4j_driver import Neo4jDriver

# Create a Neo4j driver with custom database name
driver = Neo4jDriver(
    uri="bolt://localhost:7687",
    user="neo4j",
    password="password",
    database="my_custom_database"  # Custom database name
)
```

**File:** mcp_server/graphiti_mcp_server.py (L712-754)

```python
        source (str, optional): Source type, must be one of:
                               - 'text': For plain text content (default)
                               - 'json': For structured data
                               - 'message': For conversation-style content
        source_description (str, optional): Description of the source
        uuid (str, optional): Optional UUID for the episode

    Examples:
        # Adding plain text content
        add_memory(
            name="Company News",
            episode_body="Acme Corp announced a new product line today.",
            source="text",
            source_description="news article",
            group_id="some_arbitrary_string"
        )

        # Adding structured JSON data
        # NOTE: episode_body must be a properly escaped JSON string. Note the triple backslashes
        add_memory(
            name="Customer Profile",
            episode_body="{\\\"company\\\": {\\\"name\\\": \\\"Acme Technologies\\\"}, \\\"products\\\": [{\\\"id\\\": \\\"P001\\\", \\\"name\\\": \\\"CloudSync\\\"}, {\\\"id\\\": \\\"P002\\\", \\\"name\\\": \\\"DataMiner\\\"}]}",
            source="json",
            source_description="CRM data"
        )

        # Adding message-style content
        add_memory(
            name="Customer Conversation",
            episode_body="user: What's your return policy?\nassistant: You can return items within 30 days.",
            source="message",
            source_description="chat transcript",
            group_id="some_arbitrary_string"
        )

    Notes:
        When using source='json':
        - The JSON must be a properly escaped string, not a raw Python dictionary
        - The JSON will be automatically processed to extract entities and relationships
        - Complex nested structures are supported (arrays, nested objects, mixed data types), but keep nesting to a minimum
        - Entities will be created from appropriate JSON properties
        - Relationships between entities will be established based on the JSON structure
    """
```

**File:** mcp_server/README.md (L1-23)

```markdown
# Graphiti MCP Server

Graphiti is a framework for building and querying temporally-aware knowledge graphs, specifically tailored for AI agents
operating in dynamic environments. Unlike traditional retrieval-augmented generation (RAG) methods, Graphiti
continuously integrates user interactions, structured and unstructured enterprise data, and external information into a
coherent, queryable graph. The framework supports incremental data updates, efficient retrieval, and precise historical
queries without requiring complete graph recomputation, making it suitable for developing interactive, context-aware AI
applications.

This is an experimental Model Context Protocol (MCP) server implementation for Graphiti. The MCP server exposes
Graphiti's key functionality through the MCP protocol, allowing AI assistants to interact with Graphiti's knowledge
graph capabilities.

## Features

The Graphiti MCP server exposes the following key high-level functions of Graphiti:

- **Episode Management**: Add, retrieve, and delete episodes (text, messages, or JSON data)
- **Entity Management**: Search and manage entity nodes and relationships in the knowledge graph
- **Search Capabilities**: Search for facts (edges) and node summaries using semantic and hybrid search
- **Group Management**: Organize and manage groups of related data with group_id filtering
- **Graph Maintenance**: Clear the graph and rebuild indices

```

**File:** mcp_server/README.md (L24-61)

```markdown
## Quick Start

### Clone the Graphiti GitHub repo

```bash
git clone https://github.com/getzep/graphiti.git
```

or

```bash
gh repo clone getzep/graphiti
```

### For Claude Desktop and other `stdio` only clients

1. Note the full path to this directory.

```
cd graphiti && pwd
```

2. Install the [Graphiti prerequisites](#prerequisites).

3. Configure Claude, Cursor, or other MCP client to use [Graphiti with a `stdio` transport](#integrating-with-mcp-clients). See the client documentation on where to find their MCP configuration files.

### For Cursor and other `sse`-enabled clients

1. Change directory to the `mcp_server` directory

`cd graphiti/mcp_server`

2. Start the service using Docker Compose

`docker compose up`

3. Point your MCP client to `http://localhost:8000/sse`

```

## Q2

current size where each folder containes 30-50 files with 50-200 rows like before illustrated 
```
.claude (feat/merge-claude) ❯ cd /Users/user/.claude/projects                                                                                                                          12:04:54
projects (feat/merge-claude) ❯ du                                                                                                                                                      12:52:00
6.9M   ┌── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-6950-ht-dvps-sa│██                                                                           │   1%
 12M   ├── -Users-user---Repositories-HypeTrain-repositories-hypetrain-backend-e2e-aibridge                 │██                                                                           │   2%
 13M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-c13c-es2-migrat│███                                                                          │   3%
 13M   ├── private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-eb52            │███                                                                          │   3%
 14M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-4b05-concept-bp│███                                                                          │   3%
 14M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-9774-ht-dvps-es│███                                                                          │   3%
 15M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-bb54-job-aibrid│███                                                                          │   3%
 16M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-31b3-slow-work │███                                                                          │   3%
 18M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-9a57-job-aibrid│███                                                                          │   4%
 21M   ├── -Users-user---Repositories-HypeTrain-repositories-hypetrain-backend                              │████                                                                         │   4%
 25M   ├── -Users-user-----Sandruk----PKM                                                                   │████                                                                         │   5%
 29M   ├── -Users-user---Repositories-HypeTrain-repositories-happyai-hypetrain-selector-socketio            │█████                                                                        │   6%
 29M   ├── -Users-user---Repositories-LLMs-claude-code-exporter                                             │█████                                                                        │   6%
 44M   │ ┌── 50615d13-8ade-4ad1-922c-87b19e1e2744.jsonl                                                     │███████░                                                                     │   9%
 48M   ├─┴ -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-23f3-setup-prje│████████                                                                     │  10%
 49M   ├── -Users-user---Repositories-HypeTrain-repositories-hypetrain-backend-e2e                          │████████                                                                     │  10%
8.3M   │ ┌── 665bb3b0-6b17-47fa-bd51-d800d2501141.jsonl                                                     │██░░░░░░░░░░░░░░░░                                                           │   2%
8.6M   │ ├── 88556fef-bf5b-4cbb-8877-aadbc8d7caff.jsonl                                                     │██░░░░░░░░░░░░░░░░                                                           │   2%
 10M   │ ├── 7136dbae-66c4-4cc3-a184-d7e38f51a5c0.jsonl                                                     │██░░░░░░░░░░░░░░░░                                                           │   2%
115M   ├─┴ -Users-user---Repositories-HypeTrain-repositories-hypetrain-garden                               │██████████████████                                                           │  23%
504M ┌─┴ .                                                                                                  │████████████████████████████████████████████████████████████████████████████ │ 100%
projects (feat/merge-claude) ❯ 
```
i want self-hosted neo4j and all other; is ubuntu/debian server okay? if I add 10-100x data could you in a table markdown suggest me something like that `size,RAM,DDR,%any other columns%`
500mb
5Gb
50Gb
is it okay to work with such size of data?


### Answer

Based on your data size analysis showing ~500MB total with plans to scale 10-100x, you're looking at deploying a self-hosted Neo4j setup for Graphiti that can handle 5-50GB of data. <cite/> Ubuntu/Debian servers are excellent choices for this deployment. <cite/>

## Hardware Requirements for Self-Hosted Neo4j + Graphiti

| Data Size | RAM | Storage | CPU Cores | Neo4j Heap | Page Cache | Notes |
|-----------|-----|---------|-----------|------------|------------|-------|
| 500MB | 4GB | 50GB SSD | 2 cores | 512MB | 512MB | Current size - minimal setup |
| 5GB | 8GB | 100GB SSD | 4 cores | 1GB | 2GB | 10x scale - recommended |
| 50GB | 16GB | 200GB SSD | 8 cores | 2GB | 8GB | 100x scale - production ready |

## Configuration for Each Scale

### Current Size (500MB)
The Docker Compose configuration already provides good defaults for your current size graphiti:9-11 :

```yaml
NEO4J_server_memory_heap_initial__size=512m
NEO4J_server_memory_heap_max__size=1G
NEO4J_server_memory_pagecache_size=512m
```

### 10x Scale (5GB)
For 5GB of data, you'll want to increase the memory settings graphiti:23-26 :

```bash
NEO4J_server_memory_heap_initial__size=1G
NEO4J_server_memory_heap_max__size=2G
NEO4J_server_memory_pagecache_size=2G
```

### 100x Scale (50GB)
For production-scale deployment:

```bash
NEO4J_server_memory_heap_initial__size=2G
NEO4J_server_memory_heap_max__size=4G
NEO4J_server_memory_pagecache_size=8G
```

## Ubuntu/Debian Deployment

Yes, Ubuntu/Debian is perfectly suitable for this setup. <cite/> The Graphiti MCP server provides Docker Compose configuration that works well on these platforms graphiti:1-47 . 

### Key Considerations:

1. **Concurrency Control**: Adjust `SEMAPHORE_LIMIT` based on your OpenAI API limits graphiti:133-136 
2. **Storage**: Use SSD storage for Neo4j data volumes for better performance graphiti:45-47 
3. **Network**: Ensure ports 7474 (HTTP) and 7687 (Bolt) are accessible graphiti:4-6 

## Performance Optimization

For larger datasets, consider enabling Neo4j's parallel runtime if you have Enterprise edition graphiti:99-99 :

```bash
USE_PARALLEL_RUNTIME=true
```

## Notes

Your current 500MB dataset with plans to scale 10-100x is well within Graphiti's capabilities. The Docker-based deployment approach using the provided configuration will scale smoothly on Ubuntu/Debian servers. The memory recommendations follow Neo4j best practices where page cache should be larger than heap for read-heavy workloads typical in knowledge graphs.

Wiki pages you might want to explore:
- [Environment Variables (getzep/graphiti)](/wiki/getzep/graphiti#9.1)


### Citations


**File:** mcp_server/docker-compose.yml (L1-47)

```yaml
services:
  neo4j:
    image: neo4j:5.26.0
    ports:
      - "7474:7474" # HTTP
      - "7687:7687" # Bolt
    environment:
      - NEO4J_AUTH=${NEO4J_USER:-neo4j}/${NEO4J_PASSWORD:-demodemo}
      - NEO4J_server_memory_heap_initial__size=512m
      - NEO4J_server_memory_heap_max__size=1G
      - NEO4J_server_memory_pagecache_size=512m
    volumes:
      - neo4j_data:/data
      - neo4j_logs:/logs
    healthcheck:
      test: ["CMD", "wget", "-O", "/dev/null", "http://localhost:7474"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

  graphiti-mcp:
    image: zepai/knowledge-graph-mcp:latest
    build:
      context: .
      dockerfile: Dockerfile
    env_file:
      - path: .env
        required: false # Makes the file optional. Default value is 'true'
    depends_on:
      neo4j:
        condition: service_healthy
    environment:
      - NEO4J_URI=${NEO4J_URI:-bolt://neo4j:7687}
      - NEO4J_USER=${NEO4J_USER:-neo4j}
      - NEO4J_PASSWORD=${NEO4J_PASSWORD:-demodemo}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - MODEL_NAME=${MODEL_NAME}
      - PATH=/root/.local/bin:${PATH}
      - SEMAPHORE_LIMIT=${SEMAPHORE_LIMIT:-10}
    ports:
      - "8000:8000" # Expose the MCP server via HTTP for SSE transport
    command: ["uv", "run", "graphiti_mcp_server.py", "--transport", "sse"]

volumes:
  neo4j_data:
```

**File:** mcp_server/.env.example (L23-26)

```text
# Optional: Memory settings for Neo4j (used in Docker Compose)
# NEO4J_server_memory_heap_initial__size=512m
# NEO4J_server_memory_heap_max__size=1G
# NEO4J_server_memory_pagecache_size=512m
```

**File:** mcp_server/README.md (L133-136)

```markdown
Graphiti's ingestion pipelines are designed for high concurrency, controlled by the `SEMAPHORE_LIMIT` environment variable.
By default, `SEMAPHORE_LIMIT` is set to `10` concurrent operations to help prevent `429` rate limit errors from your LLM provider. If you encounter such errors, try lowering this value.

If your LLM provider allows higher throughput, you can increase `SEMAPHORE_LIMIT` to boost episode ingestion performance.
```

**File:** CLAUDE.md (L99-99)

```markdown
- `USE_PARALLEL_RUNTIME` - Optional boolean for Neo4j parallel runtime (enterprise only)
```

## Q3

did you remember example of data? `{"parentUuid":"bfd636c5-3a0f-4afb-9b05-ee3f414944a8","isSidechain":false,"userType":"external","cwd":"/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden","sessionId":"cd336f00-1ac8-48a6-a345-8790b68cd87c","version":"1.0.113","gitBranch":"main","message":{"id":"msg_012vB2RA8M8TjspxpqjQ71K4","type":"message","role":"assistant","model":"claude-opus-4-1-20250805","content":[{"type":"tool_use","id":"toolu_0186JxWK5zweJ668U8Kf7Lvx","name":"Write","input":{"file_path":"/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden/memory-bank/Scrum/20250915-BUG-503-namespace-proxy1-error/README.md","content":"# 🐛 BUG-503: AI Selector NATS Subscription Failure\n\n**Date**: 2025-09-15\n**Status**: ✅ Resolved (Temporary) | 🔧 Permanent Fix Pending\n**Severity**: Critical\n**Namespace**: hypetrain-proxy1\n\n---\n\n## 📁 Documentation Structure\n\n| File | Purpose | Status |\n|------|---------|--------|\n| [`20250915-BUG-503-namespace-proxy1-error.bug.md`](./20250915-BUG-503-namespace-proxy1-error.bug.md) | Main bug report | ✅ Complete |\n| [`root-cause-analysis.md`](./root-cause-analysis.md) | Why it happened (GCP issue) | ✅ Complete |\n| [`fix-instructions-sasha-ru.md`](./fix-instructions-sasha-ru.md) | Russian fix instructions | ✅ Complete |\n| [`nats-inspection-commands.md`](./nats-inspection-commands.md) | NATS debugging commands | ✅ Complete |\n| [`telegram-monitoring.py`](./telegram-monitoring.py) | Monitoring script | ✅ Ready |\n| [`discovery-investigation-hypetrain-api-aibridge.story.md`](./discovery-investigation-hypetrain-api-aibridge.story.md) | Fire-and-forget investigation | 🔄 In Progress |\n\n---\n\n## 🎯 Quick Summary\n\n### Problem\nAI Selector lost NATS subscriptions after disconnect → 503 errors\n\n### Root Cause\nGCP infrastructure issue + reconnection handler bug\n\n### Solution Applied\nService restart restored subscriptions\n\n### Permanent Fix Required\n1. Update reconnection handler in `src/nats/nats_client.py`\n2. Implement fire-and-forget messaging (Sasha's proposal)\n\n---\n\n## 🔗 Navigation\n\n**Previous**: [20250914-deployment-issues](../20250914-deployment-issues/)\n**Next**: [20250916-nats-fire-and-forget](../20250916-nats-fire-and-forget/)\n**Related**:\n- [AI Selector Architecture](../../docs/dev/01-architecture/services/ai_selector/_v3/)\n- [NATS Configuration](../../docs/ops/02-infrastructure/nats.md)\n\n---\n\n## 👥 Team\n\n- **Alex** (Tech Lead): Investigation & temporary fix\n- **Sasha Romanov**: Permanent fix implementation\n- **Anton M.**: Frontend impact analysis"}}],"stop_reason":null,"stop_sequence":null,"usage":{"input_tokens":4,"cache_creation_input_tokens":306,"cache_read_input_tokens":147449,"cache_creation":{"ephemeral_5m_input_tokens":306,"ephemeral_1h_input_tokens":0},"output_tokens":8,"service_tier":"standard"}},"requestId":"req_011CT9uFRGTsw9xDwb2qqehZ","type":"assistant","uuid":"c072c529-6d58-415b-9ef1-4954bed0eb60","timestamp":"2025-09-15T08:52:10.933Z"}` I want start fast, is there any auto-build graph and organize graph database smart for future re-use ?
As I understand I can add my own key and use LLM for it? but what is the checklist todo and is it предусмотренный путь? почитай документы предложил 1 основной и или если есть несколько сценариев! Условно чтобы я дал ту папку что указал выше и оно там все сделало

```bash
projects (feat/merge-claude) ❯ du                                                                                                                                                      12:52:00
6.9M   ┌── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-6950-ht-dvps-sa│██                                                                           │   1%
 12M   ├── -Users-user---Repositories-HypeTrain-repositories-hypetrain-backend-e2e-aibridge                 │██                                                                           │   2%
 13M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-c13c-es2-migrat│███                                                                          │   3%
 13M   ├── private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-eb52            │███                                                                          │   3%
 14M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-4b05-concept-bp│███                                                                          │   3%
 14M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-9774-ht-dvps-es│███                                                                          │   3%
 15M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-bb54-job-aibrid│███                                                                          │   3%
 16M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-31b3-slow-work │███                                                                          │   3%
 18M   ├── -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-9a57-job-aibrid│███                                                                          │   4%
 21M   ├── -Users-user---Repositories-HypeTrain-repositories-hypetrain-backend                              │████                                                                         │   4%
 25M   ├── -Users-user-----Sandruk----PKM                                                                   │████                                                                         │   5%
 29M   ├── -Users-user---Repositories-HypeTrain-repositories-happyai-hypetrain-selector-socketio            │█████                                                                        │   6%
 29M   ├── -Users-user---Repositories-LLMs-claude-code-exporter                                             │█████                                                                        │   6%
 44M   │ ┌── 50615d13-8ade-4ad1-922c-87b19e1e2744.jsonl                                                     │███████░                                                                     │   9%
 48M   ├─┴ -private-var-folders-dw-d6symylx7sz0b30vzcrfkdg80000gq-T-vibe-kanban-worktrees-vk-23f3-setup-prje│████████                                                                     │  10%
 49M   ├── -Users-user---Repositories-HypeTrain-repositories-hypetrain-backend-e2e                          │████████                                                                     │  10%
8.3M   │ ┌── 665bb3b0-6b17-47fa-bd51-d800d2501141.jsonl                                                     │██░░░░░░░░░░░░░░░░                                                           │   2%
8.6M   │ ├── 88556fef-bf5b-4cbb-8877-aadbc8d7caff.jsonl                                                     │██░░░░░░░░░░░░░░░░                                                           │   2%
 10M   │ ├── 7136dbae-66c4-4cc3-a184-d7e38f51a5c0.jsonl                                                     │██░░░░░░░░░░░░░░░░                                                           │   2%
115M   ├─┴ -Users-user---Repositories-HypeTrain-repositories-hypetrain-garden                               │██████████████████                                                           │  23%
504M ┌─┴ .                                                                                                  │████████████████████████████████████████████████████████████████████████████ │ 100%
projects (feat/merge-claude) ❯ cd -Users-user---Repositories-HypeTrain-repositories-hypetrain-garden                                                                                   12:52:02
-Users-user---Repositories-HypeTrain-repositories-hypetrain-garden (feat/merge-claude) ❯ du                                                                                            12:56:25
1.8M   ┌── b48aedcc-3a0c-4e99-9acf-425994ef7718.jsonl│███                                                                                                                                 │   2%
1.9M   ├── 40eefbb9-f81c-4e4d-ace8-3c99c80ee22c.jsonl│███                                                                                                                                 │   2%
1.9M   ├── dca6d01f-9ff8-4971-ad7b-e1065b289cd7.jsonl│███                                                                                                                                 │   2%
2.1M   ├── 8cb8dcd2-c9e0-4180-91e0-7c1ffb79a36f.jsonl│███                                                                                                                                 │   2%
2.2M   ├── 30f69dab-020f-44e5-b835-7c34e4f28ff0.jsonl│███                                                                                                                                 │   2%
2.2M   ├── 5449504c-1b18-441c-94fa-b0d69270fccc.jsonl│███                                                                                                                                 │   2%
2.5M   ├── fb70552c-d38e-448f-98a1-acb8b1d917ba.jsonl│███                                                                                                                                 │   2%
3.4M   ├── be1a9021-4103-4837-80e3-6324b78a4634.jsonl│████                                                                                                                                │   3%
3.4M   ├── 58bea6a2-f100-4ac1-bf42-ed11564c4679.jsonl│████                                                                                                                                │   3%
3.4M   ├── 19a06262-4b6b-4337-8164-4e8503160398.jsonl│████                                                                                                                                │   3%
3.5M   ├── 991c1dd7-947d-4822-99dc-84bb736c6ce7.jsonl│████                                                                                                                                │   3%
3.7M   ├── 1730d993-3dc1-4445-8625-3e25ae2abe72.jsonl│█████                                                                                                                               │   3%
3.8M   ├── a20f08ec-76c1-4191-8d6a-f416070c0e1f.jsonl│█████                                                                                                                               │   3%
4.4M   ├── 544b9159-3c37-4d99-a42f-f038f2bf74a0.jsonl│█████                                                                                                                               │   4%
5.1M   ├── 9cc9766a-8a2f-4cc3-a77d-d1d5f7f79612.jsonl│██████                                                                                                                              │   4%
5.2M   ├── bb2882a1-6123-427c-9df5-610dc4feb59f.jsonl│██████                                                                                                                              │   5%
6.4M   ├── b64a012d-1917-4b4b-b5ee-6a5b54f8bef2.jsonl│████████                                                                                                                            │   6%
8.3M   ├── 665bb3b0-6b17-47fa-bd51-d800d2501141.jsonl│██████████                                                                                                                          │   7%
8.6M   ├── 88556fef-bf5b-4cbb-8877-aadbc8d7caff.jsonl│██████████                                                                                                                          │   7%
 10M   ├── 7136dbae-66c4-4cc3-a184-d7e38f51a5c0.jsonl│█████████████                                                                                                                       │   9%
115M ┌─┴ .                                           │███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████ │ 100%
-Users-user---Repositories-HypeTrain-repositories-hypetrain-garden (feat/merge-claude) ❯   
```


### Answer

# Graphiti's Support for Automated JSONL Ingestion and Custom LLM Integration

## Automated JSONL File Processing

Graphiti provides **bulk episode ingestion capabilities** but does not include built-in directory scanning functionality. The framework offers the experimental `add_episode_bulk` method for processing multiple episodes simultaneously graphiti:577-622 . 

To process multiple JSONL files from a directory, you would need to implement your own directory scanning logic that:
1. Reads JSONL files from the target directory
2. Converts each JSON record to a `RawEpisode` object graphiti:59-66 
3. Passes the list of episodes to the `add_episode_bulk` method

## Auto-Building and Graph Organization

The `add_episode_bulk` method provides comprehensive graph auto-building capabilities including:
- **Automated node and edge extraction** from episode content
- **Embedding generation** for semantic search capabilities  
- **Deduplication** of similar nodes and edges across episodes
- **Bulk database operations** for efficient storage graphiti:86-107 

The framework automatically handles:
- **Episodic edge creation** linking entities to their source episodes
- **Entity resolution** to merge duplicate entities across episodes
- **Temporal tracking** with bi-temporal data model support
- **Index and constraint management** for query optimization

## Custom LLM Integration Checklist

### Method 1: Azure OpenAI Integration
For Azure OpenAI deployments, ensure v1 API opt-in for structured outputs support graphiti:340-346 . Configure separate endpoints for LLM and embedding services graphiti:354-405 .

### Method 2: Google Gemini Integration  
Install the Google GenAI extra and configure the Gemini client with your API key graphiti:407-456 .

### Method 3: Local LLM with Ollama
Use Ollama's OpenAI-compatible API endpoint for local model deployment graphiti:460-500 .

### Core Configuration Requirements
All custom LLM integrations require:
1. **LLM Client Configuration** using the `LLMConfig` class graphiti:28-68 
2. **API Key Management** through the config object
3. **Model Specification** for both default and small model variants
4. **Embedder Configuration** for semantic search capabilities
5. **Cross-encoder Setup** for result reranking

## Performance Considerations

Bulk operations support concurrency control through the `SEMAPHORE_LIMIT` environment variable to manage LLM provider rate limits graphiti:200-202 . The framework processes episodes in chunks and handles deduplication across the entire batch graphiti:236-321 .

## Notes

- The `add_episode_bulk` method is marked as experimental and does not perform edge invalidation or date extraction steps that are available in the single-episode `add_episode` method
- Graphiti works best with LLM services supporting structured output (OpenAI, Gemini) as smaller models may produce incorrect schemas
- The framework supports multiple graph database backends including Neo4j, FalkorDB, Kuzu, and Amazon Neptune
- Directory-level automation would need to be implemented as a wrapper around Graphiti's bulk processing capabilities


### Citations


**File:** graphiti_core/graphiti.py (L577-622)

```python
    ##### EXPERIMENTAL #####
    async def add_episode_bulk(
        self,
        bulk_episodes: list[RawEpisode],
        group_id: str | None = None,
        entity_types: dict[str, type[BaseModel]] | None = None,
        excluded_entity_types: list[str] | None = None,
        edge_types: dict[str, type[BaseModel]] | None = None,
        edge_type_map: dict[tuple[str, str], list[str]] | None = None,
    ):
        """
        Process multiple episodes in bulk and update the graph.

        This method extracts information from multiple episodes, creates nodes and edges,
        and updates the graph database accordingly, all in a single batch operation.

        Parameters
        ----------
        bulk_episodes : list[RawEpisode]
            A list of RawEpisode objects to be processed and added to the graph.
        group_id : str | None
            An id for the graph partition the episode is a part of.

        Returns
        -------
        None

        Notes
        -----
        This method performs several steps including:
        - Saving all episodes to the database
        - Retrieving previous episode context for each new episode
        - Extracting nodes and edges from all episodes
        - Generating embeddings for nodes and edges
        - Deduplicating nodes and edges
        - Saving nodes, episodic edges, and entity edges to the knowledge graph

        This bulk operation is designed for efficiency when processing multiple episodes
        at once. However, it's important to ensure that the bulk operation doesn't
        overwhelm system resources. Consider implementing rate limiting or chunking for
        very large batches of episodes.

        Important: This method does not perform edge invalidation or date extraction steps.
        If these operations are required, use the `add_episode` method instead for each
        individual episode.
        """
```

**File:** graphiti_core/utils/bulk_utils.py (L59-66)

```python
class RawEpisode(BaseModel):
    name: str
    uuid: str | None = Field(default=None)
    content: str
    source_description: str
    source: EpisodeType
    reference_time: datetime

```

**File:** graphiti_core/utils/bulk_utils.py (L86-107)

```python
async def add_nodes_and_edges_bulk(
    driver: GraphDriver,
    episodic_nodes: list[EpisodicNode],
    episodic_edges: list[EpisodicEdge],
    entity_nodes: list[EntityNode],
    entity_edges: list[EntityEdge],
    embedder: EmbedderClient,
):
    session = driver.session()
    try:
        await session.execute_write(
            add_nodes_and_edges_bulk_tx,
            episodic_nodes,
            episodic_edges,
            entity_nodes,
            entity_edges,
            embedder,
            driver=driver,
        )
    finally:
        await session.close()

```

**File:** graphiti_core/utils/bulk_utils.py (L236-321)

```python
async def dedupe_nodes_bulk(
    clients: GraphitiClients,
    extracted_nodes: list[list[EntityNode]],
    episode_tuples: list[tuple[EpisodicNode, list[EpisodicNode]]],
    entity_types: dict[str, type[BaseModel]] | None = None,
) -> tuple[dict[str, list[EntityNode]], dict[str, str]]:
    embedder = clients.embedder
    min_score = 0.8

    # generate embeddings
    await semaphore_gather(
        *[create_entity_node_embeddings(embedder, nodes) for nodes in extracted_nodes]
    )

    # Find similar results
    dedupe_tuples: list[tuple[list[EntityNode], list[EntityNode]]] = []
    for i, nodes_i in enumerate(extracted_nodes):
        existing_nodes: list[EntityNode] = []
        for j, nodes_j in enumerate(extracted_nodes):
            if i == j:
                continue
            existing_nodes += nodes_j

        candidates_i: list[EntityNode] = []
        for node in nodes_i:
            for existing_node in existing_nodes:
                # Approximate BM25 by checking for word overlaps (this is faster than creating many in-memory indices)
                # This approach will cast a wider net than BM25, which is ideal for this use case
                node_words = set(node.name.lower().split())
                existing_node_words = set(existing_node.name.lower().split())
                has_overlap = not node_words.isdisjoint(existing_node_words)
                if has_overlap:
                    candidates_i.append(existing_node)
                    continue

                # Check for semantic similarity even if there is no overlap
                similarity = np.dot(
                    normalize_l2(node.name_embedding or []),
                    normalize_l2(existing_node.name_embedding or []),
                )
                if similarity >= min_score:
                    candidates_i.append(existing_node)

        dedupe_tuples.append((nodes_i, candidates_i))

    # Determine Node Resolutions
    bulk_node_resolutions: list[
        tuple[list[EntityNode], dict[str, str], list[tuple[EntityNode, EntityNode]]]
    ] = await semaphore_gather(
        *[
            resolve_extracted_nodes(
                clients,
                dedupe_tuple[0],
                episode_tuples[i][0],
                episode_tuples[i][1],
                entity_types,
                existing_nodes_override=dedupe_tuples[i][1],
            )
            for i, dedupe_tuple in enumerate(dedupe_tuples)
        ]
    )

    # Collect all duplicate pairs sorted by uuid
    duplicate_pairs: list[tuple[str, str]] = []
    for _, _, duplicates in bulk_node_resolutions:
        for duplicate in duplicates:
            n, m = duplicate
            duplicate_pairs.append((n.uuid, m.uuid))

    # Now we compress the duplicate_map, so that 3 -> 2 and 2 -> becomes 3 -> 1 (sorted by uuid)
    compressed_map: dict[str, str] = compress_uuid_map(duplicate_pairs)

    node_uuid_map: dict[str, EntityNode] = {
        node.uuid: node for nodes in extracted_nodes for node in nodes
    }

    nodes_by_episode: dict[str, list[EntityNode]] = {}
    for i, nodes in enumerate(extracted_nodes):
        episode = episode_tuples[i][0]

        nodes_by_episode[episode.uuid] = [
            node_uuid_map[compressed_map.get(node.uuid, node.uuid)] for node in nodes
        ]

    return nodes_by_episode, compressed_map

```

**File:** README.md (L200-202)

```markdown

If your LLM provider allows higher throughput, you can increase `SEMAPHORE_LIMIT` to boost episode ingestion performance.

```

**File:** README.md (L340-346)

```markdown
> [!IMPORTANT]
> **Azure OpenAI v1 API Opt-in Required for Structured Outputs**
> 
> Graphiti uses structured outputs via the `client.beta.chat.completions.parse()` method, which requires Azure OpenAI deployments to opt into the v1 API. Without this opt-in, you'll encounter 404 Resource not found errors during episode ingestion.
> 
> To enable v1 API support in your Azure OpenAI deployment, follow Microsoft's guide: [Azure OpenAI API version lifecycle](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/api-version-lifecycle?tabs=key#api-evolution).

```

**File:** README.md (L354-405)

```markdown
# Azure OpenAI configuration - use separate endpoints for different services
api_key = "<your-api-key>"
api_version = "<your-api-version>"
llm_endpoint = "<your-llm-endpoint>"  # e.g., "https://your-llm-resource.openai.azure.com/"
embedding_endpoint = "<your-embedding-endpoint>"  # e.g., "https://your-embedding-resource.openai.azure.com/"

# Create separate Azure OpenAI clients for different services
llm_client_azure = AsyncAzureOpenAI(
    api_key=api_key,
    api_version=api_version,
    azure_endpoint=llm_endpoint
)

embedding_client_azure = AsyncAzureOpenAI(
    api_key=api_key,
    api_version=api_version,
    azure_endpoint=embedding_endpoint
)

# Create LLM Config with your Azure deployment names
azure_llm_config = LLMConfig(
    small_model="gpt-4.1-nano",
    model="gpt-4.1-mini",
)

# Initialize Graphiti with Azure OpenAI clients
graphiti = Graphiti(
    "bolt://localhost:7687",
    "neo4j",
    "password",
    llm_client=OpenAIClient(
        config=azure_llm_config,
        client=llm_client_azure
    ),
    embedder=OpenAIEmbedder(
        config=OpenAIEmbedderConfig(
            embedding_model="text-embedding-3-small-deployment"  # Your Azure embedding deployment name
        ),
        client=embedding_client_azure
    ),
    cross_encoder=OpenAIRerankerClient(
        config=LLMConfig(
            model=azure_llm_config.small_model  # Use small model for reranking
        ),
        client=llm_client_azure
    )
)

# Now you can use Graphiti with Azure OpenAI
```

Make sure to replace the placeholder values with your actual Azure OpenAI credentials and deployment names that match your Azure OpenAI service configuration.
```

**File:** README.md (L407-456)

```markdown
## Using Graphiti with Google Gemini

Graphiti supports Google's Gemini models for LLM inference, embeddings, and cross-encoding/reranking. To use Gemini, you'll need to configure the LLM client, embedder, and the cross-encoder with your Google API key.

Install Graphiti:

```bash
uv add "graphiti-core[google-genai]"

# or

pip install "graphiti-core[google-genai]"
```

```python
from graphiti_core import Graphiti
from graphiti_core.llm_client.gemini_client import GeminiClient, LLMConfig
from graphiti_core.embedder.gemini import GeminiEmbedder, GeminiEmbedderConfig
from graphiti_core.cross_encoder.gemini_reranker_client import GeminiRerankerClient

# Google API key configuration
api_key = "<your-google-api-key>"

# Initialize Graphiti with Gemini clients
graphiti = Graphiti(
    "bolt://localhost:7687",
    "neo4j",
    "password",
    llm_client=GeminiClient(
        config=LLMConfig(
            api_key=api_key,
            model="gemini-2.0-flash"
        )
    ),
    embedder=GeminiEmbedder(
        config=GeminiEmbedderConfig(
            api_key=api_key,
            embedding_model="embedding-001"
        )
    ),
    cross_encoder=GeminiRerankerClient(
        config=LLMConfig(
            api_key=api_key,
            model="gemini-2.5-flash-lite-preview-06-17"
        )
    )
)

# Now you can use Graphiti with Google Gemini for all components
```
```

**File:** README.md (L460-500)

```markdown
## Using Graphiti with Ollama (Local LLM)

Graphiti supports Ollama for running local LLMs and embedding models via Ollama's OpenAI-compatible API. This is ideal for privacy-focused applications or when you want to avoid API costs.

Install the models:
```bash
ollama pull deepseek-r1:7b # LLM
ollama pull nomic-embed-text # embeddings
```

```python
from graphiti_core import Graphiti
from graphiti_core.llm_client.config import LLMConfig
from graphiti_core.llm_client.openai_generic_client import OpenAIGenericClient
from graphiti_core.embedder.openai import OpenAIEmbedder, OpenAIEmbedderConfig
from graphiti_core.cross_encoder.openai_reranker_client import OpenAIRerankerClient

# Configure Ollama LLM client
llm_config = LLMConfig(
    api_key="ollama",  # Ollama doesn't require a real API key, but some placeholder is needed
    model="deepseek-r1:7b",
    small_model="deepseek-r1:7b",
    base_url="http://localhost:11434/v1",  # Ollama's OpenAI-compatible endpoint
)

llm_client = OpenAIGenericClient(config=llm_config)

# Initialize Graphiti with Ollama clients
graphiti = Graphiti(
    "bolt://localhost:7687",
    "neo4j",
    "password",
    llm_client=llm_client,
    embedder=OpenAIEmbedder(
        config=OpenAIEmbedderConfig(
            api_key="ollama",  # Placeholder API key
            embedding_model="nomic-embed-text",
            embedding_dim=768,
            base_url="http://localhost:11434/v1",
        )
    ),
```

**File:** graphiti_core/llm_client/config.py (L28-68)

```python
class LLMConfig:
    """
    Configuration class for the Language Learning Model (LLM).

    This class encapsulates the necessary parameters to interact with an LLM API,
    such as OpenAI's GPT models. It stores the API key, model name, and base URL
    for making requests to the LLM service.
    """

    def __init__(
        self,
        api_key: str | None = None,
        model: str | None = None,
        base_url: str | None = None,
        temperature: float = DEFAULT_TEMPERATURE,
        max_tokens: int = DEFAULT_MAX_TOKENS,
        small_model: str | None = None,
    ):
        """
        Initialize the LLMConfig with the provided parameters.

        Args:
                api_key (str): The authentication key for accessing the LLM API.
                                                This is required for making authorized requests.

                model (str, optional): The specific LLM model to use for generating responses.
                                                                Defaults to "gpt-4.1-mini".

                base_url (str, optional): The base URL of the LLM API service.
                                                                        Defaults to "https://api.openai.com", which is OpenAI's standard API endpoint.
                                                                        This can be changed if using a different provider or a custom endpoint.

                small_model (str, optional): The specific LLM model to use for generating responses of simpler prompts.
                                                                Defaults to "gpt-4.1-nano".
        """
        self.base_url = base_url
        self.api_key = api_key
        self.model = model
        self.small_model = small_model
        self.temperature = temperature
        self.max_tokens = max_tokens
```

## Q4

в примере как видишь часть данных не особо несут ценности но тоже по идеи нужны чтобы прослеживать связи и тому подобное
например 

`userType,cwd,sessionId,versiongitBranch..`  
a) часть данных или дублируются или нужны единожды, или только периодически когда отличаются словить связь parent-child, неужели их все надо загонять? как правильно в graph-database paragigm?
b) не будет ли это захламлять данными в будущем чтобы extract нужное и ухудшать результаты? как graphiti настроен на это?
c) я к тому может на этапе ingestion предусмотреть обрезать ненужно? как senior как бы ты делал?
```jsonl
{
    "parentUuid": "4b2be75b-a9aa-4439-ac67-8f75a554f95d",
    "isSidechain": false,
    "userType": "external",
    "cwd": "/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden",
    "sessionId": "cd336f00-1ac8-48a6-a345-8790b68cd87c",
    "version": "1.0.113",
    "gitBranch": "main",
    "type": "user",
    "message": {
        "role": "user",
        "content": "```telegram\nAlex ., [15/09/2025 10:44]\n@nodeprizhik заработало минут 10-15 назад, забыл отписать короче пока разбираюсь похоже на то что у GCP глюкнул контейнер и надо было рестартовать\n\nиз-за неподписок заспамилось\n\nАлександр Python Backend HappyAI Hypetrain Романов, [15/09/2025 10:45]\nПочему 70к тогда? За это время, пока контейнер глючил, накопилось аж 70к сообщений?\n\nAlex ., [15/09/2025 10:46]\nUpdate reconnection handler in src/nats/nats_client.py:\n   ```python\n   async def _on_reconnect(self):\n       await self._ensure_subscriptions_active()  # Add this\n   ``\n\nАлександр Python Backend HappyAI Hypetrain Романов, [15/09/2025 10:47]\nЭто у меня такое?\n\nAlex ., [15/09/2025 11:03]\nпроблема была похоже с провайдером но в коде лучше предосмотреть на этот случай такое, детально не вникал(еще бы +1ч) - но выглядеть должно норм - на поверку тебе\n\nAlex ., [15/09/2025 11:03]\n\n\nАлександр Python Backend HappyAI Hypetrain Романов, [15/09/2025 11:36]\nНам бы вообще как то сделать так, чтобы эти сообщения не приходили, если случилась с nats какая-то жопа. То есть нам не нужна гарантия доставки. Это не тот случай, когда она обычно необходима. То есть если пользователь создал селектор/написал сообщение и тд, то сообщение должно один раз попытаться доставиться и всё. Если nats лежит, AI сервис лежит либо ещё что то лежит, то значит не досиавилось. Пользователю покажется ошибка, мол пусть пробует потом. Нам не нужно эти сообщения складывать в очередь и пытаться доставить ещё раз потом. Это приведёт наоборот к проблемам. Я пересмотрю то что ты скинул, может там как раз эта проблема и решается. Но скорее всего это должно решаться не на уровне консюмера (то есть AI сервиса), а на уровне того, кто доставляет, то есть деливера, то есть бека. Когда бек отправляет определённые сообщения в nats для AI сервиса, должен передаваться какой то флаг, опция, я хз как там в nats реализовано у вас. Чтобы nats это сообщение отправлял и в любом случае (успешно/не успешно) это сообщение нигде не оставалось. Это 100% можно сделать, но когда с Вадимом это обсуждали, он сказал, что вроде как у вас такого нет. Нужно в эту сторону капнуть и это решит все проблемы\n\nAlex ., [15/09/2025 11:50]\nХорошая идея - поштормлю сейчас в том thread\n```\nSasha romanov Idea.\nМожешь сформировать в папке \"/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden/memory-bank/Scrum/20250915-BUG-503-namespace-proxy1-error/*\" index.md, README.md + header(next,previous,related) and discovery-investigation-hypetrain-api-aibridge.story.md чтобы дать другим sub-agents потом на исполнение?\nИ да, учитывай что у тебя 6% context left, нужно вложиться чтобы не потерять контекст.\nТакже написать полный промпт следующее задание с указанием всех файлов references (я тебе еще уйму вначале же кидал c4 cheatsheets and deploy,cicd docs их тоже указать с @ в тех же промптах как умный context engineering)\nUse sequential thinking mcp too."
    },
    "uuid": "7bd79a7e-8824-4d3c-b5fe-e19f91ab8f68",
    "timestamp": "2025-09-15T08:51:34.435Z"
}
{
    "parentUuid": "7bd79a7e-8824-4d3c-b5fe-e19f91ab8f68",
    "isSidechain": false,
    "userType": "external",
    "cwd": "/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden",
    "sessionId": "cd336f00-1ac8-48a6-a345-8790b68cd87c",
    "version": "1.0.113",
    "gitBranch": "main",
    "message": {
        "id": "msg_01QumZWkzrsutcmtMni4cawW",
        "type": "message",
        "role": "assistant",
        "model": "claude-opus-4-1-20250805",
        "content": [
            {
                "type": "tool_use",
                "id": "toolu_01EzuiTN85hvXW58YSsYYe8p",
                "name": "mcp__sequential-thinking__sequentialthinking",
                "input": {
                    "thought": "With 6% context left, I need to: 1) Create index.md and README.md with navigation headers, 2) Create a discovery story for Sasha's idea about NATS fire-and-forget messaging, 3) Write a complete prompt for the next agent with all file references. The key idea from Sasha is to implement fire-and-forget messaging in NATS to prevent message accumulation when services are down.",
                    "nextThoughtNeeded": true,
                    "thoughtNumber": 1,
                    "totalThoughts": 3
                }
            }
        ],
        "stop_reason": null,
        "stop_sequence": null,
        "usage": {
            "input_tokens": 4,
            "cache_creation_input_tokens": 147449,
            "cache_read_input_tokens": 0,
            "cache_creation": {
                "ephemeral_5m_input_tokens": 147449,
                "ephemeral_1h_input_tokens": 0
            },
            "output_tokens": 213,
            "service_tier": "standard"
        }
    },
    "requestId": "req_011CT9uEPbEFMXBTghWcHD6K",
    "type": "assistant",
    "uuid": "2cc0cdd3-7a50-423d-81d7-b8f887cfae51",
    "timestamp": "2025-09-15T08:51:48.696Z"
}
{
    "parentUuid": "2cc0cdd3-7a50-423d-81d7-b8f887cfae51",
    "isSidechain": false,
    "userType": "external",
    "cwd": "/Users/user/__Repositories/HypeTrain/repositories/hypetrain-garden",
    "sessionId": "cd336f00-1ac8-48a6-a345-8790b68cd87c",
    "version": "1.0.113",
    "gitBranch": "main",
    "type": "user",
    "message": {
        "role": "user",
        "content": [
            {
                "tool_use_id": "toolu_01EzuiTN85hvXW58YSsYYe8p",
                "type": "tool_result",
                "content": [
                    {
                        "type": "text",
                        "text": "{\n  \"thoughtNumber\": 1,\n  \"totalThoughts\": 3,\n  \"nextThoughtNeeded\": true,\n  \"branches\": [],\n  \"thoughtHistoryLength\": 17\n}"
                    }
                ]
            }
        ]
    },
    "uuid": "bfd636c5-3a0f-4afb-9b05-ee3f414944a8",
    "timestamp": "2025-09-15T08:51:48.757Z",
    "toolUseResult": [
        {
            "type": "text",
            "text": "{\n  \"thoughtNumber\": 1,\n  \"totalThoughts\": 3,\n  \"nextThoughtNeeded\": true,\n  \"branches\": [],\n  \"thoughtHistoryLength\": 17\n}"
        }
    ]
}
```


### Answer

Based on my analysis of the Graphiti codebase, here's how the `add_episode_bulk` method handles structured JSON data and the mechanisms available for filtering and transforming data during ingestion:

## JSON Data Processing

Graphiti's `add_episode_bulk` method supports structured JSON data through the `EpisodeType.json` enumeration graphiti:124-127 . When processing JSON episodes, the system uses a specialized `extract_json` prompt that focuses on extracting relevant entities from JSON structure graphiti:131-160 .

The JSON extraction prompt specifically instructs the LLM to extract entities that the JSON represents, often from fields like "name" or "user" fields, while explicitly excluding date properties graphiti:153-156 .

## Entity Type Filtering Mechanisms

The system provides robust filtering capabilities through the `excluded_entity_types` parameter, which allows selective exclusion of entity types during ingestion graphiti:163-167 . This mechanism supports:

- Excluding the default "Entity" type while preserving custom types
- Filtering specific custom entity types 
- Validation of excluded types against defined entity schemas graphiti:149-174 

The filtering is implemented during node extraction, where entities are checked against the exclusion list and dropped if their type matches graphiti:163-167 .

## Bulk Processing Pipeline

The `add_episode_bulk` method implements an optimized pipeline for handling multiple episodes simultaneously graphiti:577-622 . The process includes:

1. **Episode Storage**: All episodes are first saved to the database graphiti:658-667 
2. **Context Retrieval**: Previous episode context is retrieved for each new episode graphiti:669-669 
3. **Bulk Extraction**: Nodes and edges are extracted from all episodes in batch graphiti:203-233 
4. **Deduplication**: Both nodes and edges undergo deduplication processes graphiti:236-320 

## Data Transformation and Optimization

The system includes several mechanisms for optimizing graph structure:

### Attribute Transformation
Node attributes are extracted and transformed based on entity type definitions using Pydantic models graphiti:302-330 . This allows structured data from JSON to be properly typed and validated.

### Deduplication Algorithms
The bulk processing implements sophisticated deduplication for both nodes and edges using semantic similarity scoring and word overlap detection graphiti:236-320 . Nodes are deduplicated using a minimum similarity score of 0.8, while edges use 0.6 graphiti:243-243  and graphiti:332-332 .

### Search Optimization
Query results can be filtered using the `SearchFilters` class, which supports filtering by node labels, edge types, and temporal properties graphiti:44-55 . This enables efficient querying of the optimized graph structure.

## Regarding Specific Fields

While the specific fields you mentioned (`userType`, `cwd`, `sessionId`, `version`, `gitBranch`) don't appear as hardcoded filtering mechanisms in the codebase, the system's architecture supports handling such fields through:

1. **Generic Attribute Storage**: Entity nodes can store arbitrary attributes in their `attributes` property graphiti:142-148 
2. **Custom Entity Types**: These fields could be defined as structured attributes in custom Pydantic entity type models
3. **Search Filtering**: The filtering system can be extended to handle custom attribute filtering

## Important Limitations

The bulk operation doesn't perform edge invalidation or date extraction steps that are available in the individual `add_episode` method graphiti:619-622 . For scenarios requiring these features, individual episode processing should be used instead.

## Notes

The `add_episode_bulk` method is marked as experimental in the codebase graphiti:577-577 , indicating it may undergo changes. The system prioritizes efficiency for bulk ingestion while providing flexible filtering and transformation mechanisms that can be customized based on specific use cases and data structures.


### Citations


**File:** graphiti_core/utils/maintenance/node_operations.py (L124-127)

```python
        elif episode.source == EpisodeType.json:
            llm_response = await llm_client.generate_response(
                prompt_library.extract_nodes.extract_json(context), response_model=ExtractedEntities
            )
```

**File:** graphiti_core/utils/maintenance/node_operations.py (L163-167)

```python
        # Check if this entity type should be excluded
        if excluded_entity_types and entity_type_name in excluded_entity_types:
            logger.debug(f'Excluding entity "{extracted_entity.name}" of type "{entity_type_name}"')
            continue

```

**File:** graphiti_core/utils/maintenance/node_operations.py (L302-330)

```python
async def extract_attributes_from_nodes(
    clients: GraphitiClients,
    nodes: list[EntityNode],
    episode: EpisodicNode | None = None,
    previous_episodes: list[EpisodicNode] | None = None,
    entity_types: dict[str, type[BaseModel]] | None = None,
) -> list[EntityNode]:
    llm_client = clients.llm_client
    embedder = clients.embedder
    updated_nodes: list[EntityNode] = await semaphore_gather(
        *[
            extract_attributes_from_node(
                llm_client,
                node,
                episode,
                previous_episodes,
                entity_types.get(next((item for item in node.labels if item != 'Entity'), ''))
                if entity_types is not None
                else None,
                clients.ensure_ascii,
            )
            for node in nodes
        ]
    )

    await create_entity_node_embeddings(embedder, updated_nodes)

    return updated_nodes

```

**File:** graphiti_core/prompts/extract_nodes.py (L131-160)

```python
def extract_json(context: dict[str, Any]) -> list[Message]:
    sys_prompt = """You are an AI assistant that extracts entity nodes from JSON. 
    Your primary task is to extract and classify relevant entities from JSON files"""

    user_prompt = f"""
<ENTITY TYPES>
{context['entity_types']}
</ENTITY TYPES>

<SOURCE DESCRIPTION>:
{context['source_description']}
</SOURCE DESCRIPTION>
<JSON>
{context['episode_content']}
</JSON>

{context['custom_prompt']}

Given the above source description and JSON, extract relevant entities from the provided JSON.
For each entity extracted, also determine its entity type based on the provided ENTITY TYPES and their descriptions.
Indicate the classified entity type by providing its entity_type_id.

Guidelines:
1. Always try to extract an entities that the JSON represents. This will often be something like a "name" or "user field
2. Do NOT extract any properties that contain dates
"""
    return [
        Message(role='system', content=sys_prompt),
        Message(role='user', content=user_prompt),
    ]
```

**File:** graphiti_core/helpers.py (L149-174)

```python

def validate_excluded_entity_types(
    excluded_entity_types: list[str] | None, entity_types: dict[str, type[BaseModel]] | None = None
) -> bool:
    """
    Validate that excluded entity types are valid type names.

    Args:
        excluded_entity_types: List of entity type names to exclude
        entity_types: Dictionary of available custom entity types

    Returns:
        True if valid

    Raises:
        ValueError: If any excluded type names are invalid
    """
    if not excluded_entity_types:
        return True

    # Build set of available type names
    available_types = {'Entity'}  # Default type is always available
    if entity_types:
        available_types.update(entity_types.keys())

    # Check for invalid type names
```

**File:** graphiti_core/graphiti.py (L577-622)

```python
    ##### EXPERIMENTAL #####
    async def add_episode_bulk(
        self,
        bulk_episodes: list[RawEpisode],
        group_id: str | None = None,
        entity_types: dict[str, type[BaseModel]] | None = None,
        excluded_entity_types: list[str] | None = None,
        edge_types: dict[str, type[BaseModel]] | None = None,
        edge_type_map: dict[tuple[str, str], list[str]] | None = None,
    ):
        """
        Process multiple episodes in bulk and update the graph.

        This method extracts information from multiple episodes, creates nodes and edges,
        and updates the graph database accordingly, all in a single batch operation.

        Parameters
        ----------
        bulk_episodes : list[RawEpisode]
            A list of RawEpisode objects to be processed and added to the graph.
        group_id : str | None
            An id for the graph partition the episode is a part of.

        Returns
        -------
        None

        Notes
        -----
        This method performs several steps including:
        - Saving all episodes to the database
        - Retrieving previous episode context for each new episode
        - Extracting nodes and edges from all episodes
        - Generating embeddings for nodes and edges
        - Deduplicating nodes and edges
        - Saving nodes, episodic edges, and entity edges to the knowledge graph

        This bulk operation is designed for efficiency when processing multiple episodes
        at once. However, it's important to ensure that the bulk operation doesn't
        overwhelm system resources. Consider implementing rate limiting or chunking for
        very large batches of episodes.

        Important: This method does not perform edge invalidation or date extraction steps.
        If these operations are required, use the `add_episode` method instead for each
        individual episode.
        """
```

**File:** graphiti_core/graphiti.py (L658-667)

```python
            # Save all episodes
            await add_nodes_and_edges_bulk(
                driver=self.driver,
                episodic_nodes=episodes,
                episodic_edges=[],
                entity_nodes=[],
                entity_edges=[],
                embedder=self.embedder,
            )

```

**File:** graphiti_core/graphiti.py (L669-669)

```python
            episode_context = await retrieve_previous_episodes_bulk(self.driver, episodes)
```

**File:** graphiti_core/utils/bulk_utils.py (L142-148)

```python
            attributes = convert_datetimes_to_strings(node.attributes) if node.attributes else {}
            entity_data['attributes'] = json.dumps(attributes)
        else:
            entity_data.update(node.attributes or {})
            entity_data['labels'] = list(
                set(node.labels + ['Entity', 'Entity_' + node.group_id.replace('-', '')])
            )
```

**File:** graphiti_core/utils/bulk_utils.py (L203-233)

```python
async def extract_nodes_and_edges_bulk(
    clients: GraphitiClients,
    episode_tuples: list[tuple[EpisodicNode, list[EpisodicNode]]],
    edge_type_map: dict[tuple[str, str], list[str]],
    entity_types: dict[str, type[BaseModel]] | None = None,
    excluded_entity_types: list[str] | None = None,
    edge_types: dict[str, type[BaseModel]] | None = None,
) -> tuple[list[list[EntityNode]], list[list[EntityEdge]]]:
    extracted_nodes_bulk: list[list[EntityNode]] = await semaphore_gather(
        *[
            extract_nodes(clients, episode, previous_episodes, entity_types, excluded_entity_types)
            for episode, previous_episodes in episode_tuples
        ]
    )

    extracted_edges_bulk: list[list[EntityEdge]] = await semaphore_gather(
        *[
            extract_edges(
                clients,
                episode,
                extracted_nodes_bulk[i],
                previous_episodes,
                edge_type_map=edge_type_map,
                group_id=episode.group_id,
                edge_types=edge_types,
            )
            for i, (episode, previous_episodes) in enumerate(episode_tuples)
        ]
    )

    return extracted_nodes_bulk, extracted_edges_bulk
```

**File:** graphiti_core/utils/bulk_utils.py (L236-320)

```python
async def dedupe_nodes_bulk(
    clients: GraphitiClients,
    extracted_nodes: list[list[EntityNode]],
    episode_tuples: list[tuple[EpisodicNode, list[EpisodicNode]]],
    entity_types: dict[str, type[BaseModel]] | None = None,
) -> tuple[dict[str, list[EntityNode]], dict[str, str]]:
    embedder = clients.embedder
    min_score = 0.8

    # generate embeddings
    await semaphore_gather(
        *[create_entity_node_embeddings(embedder, nodes) for nodes in extracted_nodes]
    )

    # Find similar results
    dedupe_tuples: list[tuple[list[EntityNode], list[EntityNode]]] = []
    for i, nodes_i in enumerate(extracted_nodes):
        existing_nodes: list[EntityNode] = []
        for j, nodes_j in enumerate(extracted_nodes):
            if i == j:
                continue
            existing_nodes += nodes_j

        candidates_i: list[EntityNode] = []
        for node in nodes_i:
            for existing_node in existing_nodes:
                # Approximate BM25 by checking for word overlaps (this is faster than creating many in-memory indices)
                # This approach will cast a wider net than BM25, which is ideal for this use case
                node_words = set(node.name.lower().split())
                existing_node_words = set(existing_node.name.lower().split())
                has_overlap = not node_words.isdisjoint(existing_node_words)
                if has_overlap:
                    candidates_i.append(existing_node)
                    continue

                # Check for semantic similarity even if there is no overlap
                similarity = np.dot(
                    normalize_l2(node.name_embedding or []),
                    normalize_l2(existing_node.name_embedding or []),
                )
                if similarity >= min_score:
                    candidates_i.append(existing_node)

        dedupe_tuples.append((nodes_i, candidates_i))

    # Determine Node Resolutions
    bulk_node_resolutions: list[
        tuple[list[EntityNode], dict[str, str], list[tuple[EntityNode, EntityNode]]]
    ] = await semaphore_gather(
        *[
            resolve_extracted_nodes(
                clients,
                dedupe_tuple[0],
                episode_tuples[i][0],
                episode_tuples[i][1],
                entity_types,
                existing_nodes_override=dedupe_tuples[i][1],
            )
            for i, dedupe_tuple in enumerate(dedupe_tuples)
        ]
    )

    # Collect all duplicate pairs sorted by uuid
    duplicate_pairs: list[tuple[str, str]] = []
    for _, _, duplicates in bulk_node_resolutions:
        for duplicate in duplicates:
            n, m = duplicate
            duplicate_pairs.append((n.uuid, m.uuid))

    # Now we compress the duplicate_map, so that 3 -> 2 and 2 -> becomes 3 -> 1 (sorted by uuid)
    compressed_map: dict[str, str] = compress_uuid_map(duplicate_pairs)

    node_uuid_map: dict[str, EntityNode] = {
        node.uuid: node for nodes in extracted_nodes for node in nodes
    }

    nodes_by_episode: dict[str, list[EntityNode]] = {}
    for i, nodes in enumerate(extracted_nodes):
        episode = episode_tuples[i][0]

        nodes_by_episode[episode.uuid] = [
            node_uuid_map[compressed_map.get(node.uuid, node.uuid)] for node in nodes
        ]

    return nodes_by_episode, compressed_map
```

**File:** graphiti_core/utils/bulk_utils.py (L332-332)

```python
    min_score = 0.6
```

**File:** graphiti_core/search/search_filters.py (L44-55)

```python
class SearchFilters(BaseModel):
    node_labels: list[str] | None = Field(
        default=None, description='List of node labels to filter on'
    )
    edge_types: list[str] | None = Field(
        default=None, description='List of edge types to filter on'
    )
    valid_at: list[list[DateFilter]] | None = Field(default=None)
    invalid_at: list[list[DateFilter]] | None = Field(default=None)
    created_at: list[list[DateFilter]] | None = Field(default=None)
    expired_at: list[list[DateFilter]] | None = Field(default=None)

```

## Q5

Смотри - меня устроит что в моих файлах есть дубли, я где-то использовал copypaste промптов и output, это же логи моих сессий в claude-code-cli with ai-agents, где я решал 4 месяца реальные задачи поэтому я не хотел бы чтобы без того если я захочу данные потерялись порезались, как это контролировать и как правильно? И вообще срезюмируй мою задачу и сделай четкий чеклист внедрения с учетом знания репозитория


### Answer

Based on my analysis of the Graphiti codebase, here's how the `add_episode_bulk` method handles deduplication and the available options for controlling this process:

## Node Deduplication in `add_episode_bulk`

The `add_episode_bulk` method performs node deduplication through the `dedupe_nodes_bulk` function graphiti:236-321 . This process uses a two-stage approach:

1. **Similarity Detection**: First checks for word overlap in node names, then applies semantic similarity using embeddings with a hardcoded minimum similarity threshold of 0.8 graphiti:243-243 

2. **LLM-Based Resolution**: Uses language model prompts to make final deduplication decisions graphiti:106-169 

3. **Compression**: Applies a UnionFind data structure to compress duplicate mappings into a consistent hierarchy graphiti:306-320 

## Edge Deduplication in `add_episode_bulk`

Edge deduplication follows a similar pattern through the `dedupe_edges_bulk` function graphiti:323-415 :

1. **Constraint Filtering**: Only considers edges as potential duplicates if they connect the same source and target nodes graphiti:353-357 

2. **Similarity Threshold**: Uses a hardcoded minimum similarity score of 0.6 for semantic similarity between edge facts graphiti:332-332 

3. **LLM Resolution**: Employs dedicated prompts for edge deduplication decisions graphiti:117-161 

## Control and Disabling Deduplication

**Critical Limitation**: There are currently **no built-in parameters or configuration options** to control or disable the deduplication process in `add_episode_bulk`. The deduplication steps are integral to the bulk processing workflow graphiti:681-704  and the similarity thresholds are hardcoded in the implementation.

The deduplication process is embedded directly in the bulk processing pipeline where it:
- Calls `dedupe_nodes_bulk` after node extraction graphiti:681-684 
- Calls `dedupe_edges_bulk` after edge extraction graphiti:696-704 

## Implications for Claude AI Agent Session Data

For your specific use case with Claude AI agent sessions containing copied prompts and outputs, this presents a significant challenge:

1. **Intentional Duplicates Lost**: The current deduplication logic will identify copied prompts and repeated outputs as duplicates and merge them, potentially losing important temporal or contextual information.

2. **High Similarity Thresholds**: The 0.8 threshold for nodes and 0.6 for edges are quite permissive, meaning even slightly different versions of similar content may be deduplicated.

## Potential Workarounds

Since there's no built-in way to disable deduplication, you have several options:

1. **Use Individual Episodes**: Instead of `add_episode_bulk`, use the regular `add_episode` method which processes episodes individually and may be less aggressive in deduplication.

2. **Modify Source Code**: Fork the repository and modify the `dedupe_nodes_bulk` and `dedupe_edges_bulk` functions to either skip deduplication entirely or use much higher similarity thresholds.

3. **Pre-process Data**: Add unique identifiers or timestamps to your nodes and edges before processing to ensure they're treated as distinct entities.

4. **Custom Implementation**: Implement a custom bulk processing method that bypasses the deduplication steps while maintaining other bulk processing benefits.

## Notes

The deduplication system is designed around the assumption that duplicates represent the same real-world entities or relationships graphiti:86-92 . For use cases involving intentionally repeated content (like agent conversation logs), this assumption may not hold, making the current system unsuitable without modifications.


### Citations


**File:** graphiti_core/utils/bulk_utils.py (L236-321)

```python
async def dedupe_nodes_bulk(
    clients: GraphitiClients,
    extracted_nodes: list[list[EntityNode]],
    episode_tuples: list[tuple[EpisodicNode, list[EpisodicNode]]],
    entity_types: dict[str, type[BaseModel]] | None = None,
) -> tuple[dict[str, list[EntityNode]], dict[str, str]]:
    embedder = clients.embedder
    min_score = 0.8

    # generate embeddings
    await semaphore_gather(
        *[create_entity_node_embeddings(embedder, nodes) for nodes in extracted_nodes]
    )

    # Find similar results
    dedupe_tuples: list[tuple[list[EntityNode], list[EntityNode]]] = []
    for i, nodes_i in enumerate(extracted_nodes):
        existing_nodes: list[EntityNode] = []
        for j, nodes_j in enumerate(extracted_nodes):
            if i == j:
                continue
            existing_nodes += nodes_j

        candidates_i: list[EntityNode] = []
        for node in nodes_i:
            for existing_node in existing_nodes:
                # Approximate BM25 by checking for word overlaps (this is faster than creating many in-memory indices)
                # This approach will cast a wider net than BM25, which is ideal for this use case
                node_words = set(node.name.lower().split())
                existing_node_words = set(existing_node.name.lower().split())
                has_overlap = not node_words.isdisjoint(existing_node_words)
                if has_overlap:
                    candidates_i.append(existing_node)
                    continue

                # Check for semantic similarity even if there is no overlap
                similarity = np.dot(
                    normalize_l2(node.name_embedding or []),
                    normalize_l2(existing_node.name_embedding or []),
                )
                if similarity >= min_score:
                    candidates_i.append(existing_node)

        dedupe_tuples.append((nodes_i, candidates_i))

    # Determine Node Resolutions
    bulk_node_resolutions: list[
        tuple[list[EntityNode], dict[str, str], list[tuple[EntityNode, EntityNode]]]
    ] = await semaphore_gather(
        *[
            resolve_extracted_nodes(
                clients,
                dedupe_tuple[0],
                episode_tuples[i][0],
                episode_tuples[i][1],
                entity_types,
                existing_nodes_override=dedupe_tuples[i][1],
            )
            for i, dedupe_tuple in enumerate(dedupe_tuples)
        ]
    )

    # Collect all duplicate pairs sorted by uuid
    duplicate_pairs: list[tuple[str, str]] = []
    for _, _, duplicates in bulk_node_resolutions:
        for duplicate in duplicates:
            n, m = duplicate
            duplicate_pairs.append((n.uuid, m.uuid))

    # Now we compress the duplicate_map, so that 3 -> 2 and 2 -> becomes 3 -> 1 (sorted by uuid)
    compressed_map: dict[str, str] = compress_uuid_map(duplicate_pairs)

    node_uuid_map: dict[str, EntityNode] = {
        node.uuid: node for nodes in extracted_nodes for node in nodes
    }

    nodes_by_episode: dict[str, list[EntityNode]] = {}
    for i, nodes in enumerate(extracted_nodes):
        episode = episode_tuples[i][0]

        nodes_by_episode[episode.uuid] = [
            node_uuid_map[compressed_map.get(node.uuid, node.uuid)] for node in nodes
        ]

    return nodes_by_episode, compressed_map

```

**File:** graphiti_core/utils/bulk_utils.py (L323-415)

```python
async def dedupe_edges_bulk(
    clients: GraphitiClients,
    extracted_edges: list[list[EntityEdge]],
    episode_tuples: list[tuple[EpisodicNode, list[EpisodicNode]]],
    _entities: list[EntityNode],
    edge_types: dict[str, type[BaseModel]],
    _edge_type_map: dict[tuple[str, str], list[str]],
) -> dict[str, list[EntityEdge]]:
    embedder = clients.embedder
    min_score = 0.6

    # generate embeddings
    await semaphore_gather(
        *[create_entity_edge_embeddings(embedder, edges) for edges in extracted_edges]
    )

    # Find similar results
    dedupe_tuples: list[tuple[EpisodicNode, EntityEdge, list[EntityEdge]]] = []
    for i, edges_i in enumerate(extracted_edges):
        existing_edges: list[EntityEdge] = []
        for j, edges_j in enumerate(extracted_edges):
            if i == j:
                continue
            existing_edges += edges_j

        for edge in edges_i:
            candidates: list[EntityEdge] = []
            for existing_edge in existing_edges:
                # Approximate BM25 by checking for word overlaps (this is faster than creating many in-memory indices)
                # This approach will cast a wider net than BM25, which is ideal for this use case
                if (
                    edge.source_node_uuid != existing_edge.source_node_uuid
                    or edge.target_node_uuid != existing_edge.target_node_uuid
                ):
                    continue

                edge_words = set(edge.fact.lower().split())
                existing_edge_words = set(existing_edge.fact.lower().split())
                has_overlap = not edge_words.isdisjoint(existing_edge_words)
                if has_overlap:
                    candidates.append(existing_edge)
                    continue

                # Check for semantic similarity even if there is no overlap
                similarity = np.dot(
                    normalize_l2(edge.fact_embedding or []),
                    normalize_l2(existing_edge.fact_embedding or []),
                )
                if similarity >= min_score:
                    candidates.append(existing_edge)

            dedupe_tuples.append((episode_tuples[i][0], edge, candidates))

    bulk_edge_resolutions: list[
        tuple[EntityEdge, EntityEdge, list[EntityEdge]]
    ] = await semaphore_gather(
        *[
            resolve_extracted_edge(
                clients.llm_client,
                edge,
                candidates,
                candidates,
                episode,
                edge_types,
                clients.ensure_ascii,
            )
            for episode, edge, candidates in dedupe_tuples
        ]
    )

    # For now we won't track edge invalidation
    duplicate_pairs: list[tuple[str, str]] = []
    for i, (_, _, duplicates) in enumerate(bulk_edge_resolutions):
        episode, edge, candidates = dedupe_tuples[i]
        for duplicate in duplicates:
            duplicate_pairs.append((edge.uuid, duplicate.uuid))

    # Now we compress the duplicate_map, so that 3 -> 2 and 2 -> becomes 3 -> 1 (sorted by uuid)
    compressed_map: dict[str, str] = compress_uuid_map(duplicate_pairs)

    edge_uuid_map: dict[str, EntityEdge] = {
        edge.uuid: edge for edges in extracted_edges for edge in edges
    }

    edges_by_episode: dict[str, list[EntityEdge]] = {}
    for i, edges in enumerate(extracted_edges):
        episode = episode_tuples[i][0]

        edges_by_episode[episode.uuid] = [
            edge_uuid_map[compressed_map.get(edge.uuid, edge.uuid)] for edge in edges
        ]

    return edges_by_episode
```

**File:** graphiti_core/prompts/dedupe_nodes.py (L86-92)

```python
        Entities should only be considered duplicates if they refer to the *same real-world object or concept*.
        Semantic Equivalence: if a descriptive label in existing_entities clearly refers to a named entity in context, treat them as duplicates.

        Do NOT mark entities as duplicates if:
        - They are related but distinct.
        - They have similar names or purposes but refer to separate instances or concepts.

```

**File:** graphiti_core/prompts/dedupe_nodes.py (L106-169)

```python
def nodes(context: dict[str, Any]) -> list[Message]:
    return [
        Message(
            role='system',
            content='You are a helpful assistant that determines whether or not ENTITIES extracted from a conversation are duplicates'
            ' of existing entities.',
        ),
        Message(
            role='user',
            content=f"""
        <PREVIOUS MESSAGES>
        {to_prompt_json([ep for ep in context['previous_episodes']], ensure_ascii=context.get('ensure_ascii', True), indent=2)}
        </PREVIOUS MESSAGES>
        <CURRENT MESSAGE>
        {context['episode_content']}
        </CURRENT MESSAGE>
        
        
        Each of the following ENTITIES were extracted from the CURRENT MESSAGE.
        Each entity in ENTITIES is represented as a JSON object with the following structure:
        {{
            id: integer id of the entity,
            name: "name of the entity",
            entity_type: "ontological classification of the entity",
            entity_type_description: "Description of what the entity type represents",
            duplication_candidates: [
                {{
                    idx: integer index of the candidate entity,
                    name: "name of the candidate entity",
                    entity_type: "ontological classification of the candidate entity",
                    ...<additional attributes>
                }}
            ]
        }}
        
        <ENTITIES>
        {to_prompt_json(context['extracted_nodes'], ensure_ascii=context.get('ensure_ascii', True), indent=2)}
        </ENTITIES>
        
        <EXISTING ENTITIES>
        {to_prompt_json(context['existing_nodes'], ensure_ascii=context.get('ensure_ascii', True), indent=2)}
        </EXISTING ENTITIES>

        For each of the above ENTITIES, determine if the entity is a duplicate of any of the EXISTING ENTITIES.

        Entities should only be considered duplicates if they refer to the *same real-world object or concept*.

        Do NOT mark entities as duplicates if:
        - They are related but distinct.
        - They have similar names or purposes but refer to separate instances or concepts.

        Task:
        Your response will be a list called entity_resolutions which contains one entry for each entity.
        
        For each entity, return the id of the entity as id, the name of the entity as name, and the duplicate_idx
        as an integer.
        
        - If an entity is a duplicate of one of the EXISTING ENTITIES, return the idx of the candidate it is a 
        duplicate of.
        - If an entity is not a duplicate of one of the EXISTING ENTITIES, return the -1 as the duplication_idx
        """,
        ),
    ]

```

**File:** graphiti_core/prompts/dedupe_edges.py (L117-161)

```python
def resolve_edge(context: dict[str, Any]) -> list[Message]:
    return [
        Message(
            role='system',
            content='You are a helpful assistant that de-duplicates facts from fact lists and determines which existing '
            'facts are contradicted by the new fact.',
        ),
        Message(
            role='user',
            content=f"""
        <NEW FACT>
        {context['new_edge']}
        </NEW FACT>
        
        <EXISTING FACTS>
        {context['existing_edges']}
        </EXISTING FACTS>
        <FACT INVALIDATION CANDIDATES>
        {context['edge_invalidation_candidates']}
        </FACT INVALIDATION CANDIDATES>
        
        <FACT TYPES>
        {context['edge_types']}
        </FACT TYPES>
        

        Task:
        If the NEW FACT represents identical factual information of one or more in EXISTING FACTS, return the idx of the duplicate facts.
        Facts with similar information that contain key differences should not be marked as duplicates.
        If the NEW FACT is not a duplicate of any of the EXISTING FACTS, return an empty list.
        
        Given the predefined FACT TYPES, determine if the NEW FACT should be classified as one of these types.
        Return the fact type as fact_type or DEFAULT if NEW FACT is not one of the FACT TYPES.
        
        Based on the provided FACT INVALIDATION CANDIDATES and NEW FACT, determine which existing facts the new fact contradicts.
        Return a list containing all idx's of the facts that are contradicted by the NEW FACT.
        If there are no contradicted facts, return an empty list.

        Guidelines:
        1. Some facts may be very similar but will have key differences, particularly around numeric values in the facts.
            Do not mark these facts as duplicates.
        """,
        ),
    ]

```

**File:** graphiti_core/graphiti.py (L681-704)

```python
            # Dedupe extracted nodes in memory
            nodes_by_episode, uuid_map = await dedupe_nodes_bulk(
                self.clients, extracted_nodes_bulk, episode_context, entity_types
            )

            # Create Episodic Edges
            episodic_edges: list[EpisodicEdge] = []
            for episode_uuid, nodes in nodes_by_episode.items():
                episodic_edges.extend(build_episodic_edges(nodes, episode_uuid, now))

            # re-map edge pointers so that they don't point to discard dupe nodes
            extracted_edges_bulk_updated: list[list[EntityEdge]] = [
                resolve_edge_pointers(edges, uuid_map) for edges in extracted_edges_bulk
            ]

            # Dedupe extracted edges in memory
            edges_by_episode = await dedupe_edges_bulk(
                self.clients,
                extracted_edges_bulk_updated,
                episode_context,
                [],
                edge_types or {},
                edge_type_map or edge_type_map_default,
            )
```

## Q6

а не проще сделать все на neo4j чем возится с ними? top killer features, tradeoffs and cons pros in a table! think hard!


### Answer

Based on my analysis of the Graphiti codebase, here's a comprehensive comparison of using Graphiti's `add_episode_bulk` method versus directly using Neo4j for ingesting structured JSON data like Claude AI agent session logs:

## Killer Features of Graphiti's `add_episode_bulk`

### 1. **Automated LLM-Powered Entity Extraction from JSON**
Graphiti provides specialized JSON processing capabilities with a dedicated `extract_json` prompt that automatically identifies and extracts relevant entities from structured JSON data. graphiti:131-160  The system intelligently processes JSON by focusing on meaningful entities while avoiding temporal data extraction, making it ideal for agent session logs.

### 2. **Built-in Deduplication at Scale**
The bulk processing includes sophisticated multi-level deduplication that combines semantic similarity (embeddings) with word overlap detection. graphiti:236-320  This prevents duplicate entities across multiple JSON episodes, which is crucial when processing agent sessions that may reference the same entities repeatedly.

### 3. **Automatic Relationship Discovery**
Beyond entity extraction, Graphiti automatically discovers and creates relationships between entities found in the JSON data, building a connected knowledge graph rather than isolated data points. graphiti:203-233 

## Automated Graph Building

**Pros:**
- **Schema-Free Ingestion**: Processes JSON without requiring predefined schemas graphiti:62-70 
- **Intelligent Entity Classification**: Uses LLM to classify entities into appropriate types based on context graphiti:124-127 
- **Automatic Embeddings Generation**: Creates semantic embeddings for all entities and relationships for advanced search capabilities graphiti:125-150 

**Cons:**
- **LLM Dependency**: Every JSON ingestion requires LLM calls, adding latency and cost
- **Limited Control**: Less fine-grained control over how JSON structures map to graph elements compared to custom Cypher queries

## Deduplication Control

**Pros:**
- **Multi-Algorithm Approach**: Combines semantic similarity (0.8+ threshold) with lexical overlap detection graphiti:262-278 
- **Cross-Episode Deduplication**: Identifies duplicates across different JSON episodes, not just within single documents
- **UUID Mapping Compression**: Uses Union-Find algorithm to efficiently resolve duplicate chains graphiti:440-455 

**Cons:**
- **Fixed Thresholds**: Deduplication thresholds (0.8 for nodes, 0.6 for edges) are hardcoded and may not suit all use cases graphiti:243-244 
- **Performance Overhead**: Semantic similarity calculations can be computationally expensive for large datasets

## LLM Integration

**Pros:**
- **Context-Aware Processing**: Uses previous episodes as context for better entity extraction graphiti:104-112 
- **Reflexion Mechanism**: Iteratively improves extraction by identifying missed entities graphiti:134-148 
- **Source-Specific Prompts**: Different processing strategies for JSON vs text vs message formats graphiti:149-156 

**Cons:**
- **Cost and Latency**: Multiple LLM calls per episode (extraction + reflexion + relationship discovery)
- **Quality Variability**: Results depend on LLM performance and may vary between runs

## Data Organization for Future Reuse

**Pros:**
- **Temporal Awareness**: Episodes maintain timestamps and temporal relationships graphiti:267-273 
- **Group-based Partitioning**: Supports logical data separation via group_ids for multi-tenant scenarios graphiti:627-629 
- **Rich Search Capabilities**: Built-in hybrid search combining vector and text search graphiti:890-947 
- **Community Detection**: Automatic clustering of related entities for high-level insights graphiti:857-888 

## Key Tradeoffs vs Direct Neo4j

**Graphiti Advantages:**
1. **Zero Configuration**: No need to design schemas or write complex Cypher queries
2. **Intelligent Processing**: Understands semantic meaning rather than just structure
3. **Built-in Features**: Search, deduplication, and relationship discovery out-of-the-box

**Direct Neo4j Advantages:**
1. **Performance**: Direct Cypher operations are faster than LLM-mediated processing
2. **Cost**: No LLM API costs for data ingestion
3. **Deterministic**: Consistent results without LLM variability
4. **Granular Control**: Precise control over graph structure and relationships

**When to Choose Graphiti's `add_episode_bulk`:**
- Processing unstructured or semi-structured JSON with varying schemas
- Need automatic entity recognition and relationship discovery
- Want built-in deduplication across episodes
- Prioritize ease of use over raw performance
- Working with conversational or agent-generated content where context matters

**When to Choose Direct Neo4j:**
- Well-defined JSON schemas that map clearly to graph structures
- Performance-critical applications with high ingestion volumes
- Budget constraints around LLM API usage
- Need deterministic, repeatable processing results
- Custom relationship logic that doesn't fit standard patterns

## Notes

Graphiti's `add_episode_bulk` method is marked as "EXPERIMENTAL" graphiti:577-577 , indicating it may undergo significant changes. The method excels at semantic understanding and automated graph construction from diverse JSON formats, making it particularly valuable for agent session logs where entities and relationships may not follow rigid patterns. However, the LLM dependency introduces cost and latency considerations that may not be suitable for all use cases.


### Citations


**File:** graphiti_core/prompts/extract_nodes.py (L131-160)

```python
def extract_json(context: dict[str, Any]) -> list[Message]:
    sys_prompt = """You are an AI assistant that extracts entity nodes from JSON. 
    Your primary task is to extract and classify relevant entities from JSON files"""

    user_prompt = f"""
<ENTITY TYPES>
{context['entity_types']}
</ENTITY TYPES>

<SOURCE DESCRIPTION>:
{context['source_description']}
</SOURCE DESCRIPTION>
<JSON>
{context['episode_content']}
</JSON>

{context['custom_prompt']}

Given the above source description and JSON, extract relevant entities from the provided JSON.
For each entity extracted, also determine its entity type based on the provided ENTITY TYPES and their descriptions.
Indicate the classified entity type by providing its entity_type_id.

Guidelines:
1. Always try to extract an entities that the JSON represents. This will often be something like a "name" or "user field
2. Do NOT extract any properties that contain dates
"""
    return [
        Message(role='system', content=sys_prompt),
        Message(role='user', content=user_prompt),
    ]
```

**File:** graphiti_core/utils/bulk_utils.py (L125-150)

```python
    nodes = []

    for node in entity_nodes:
        if node.name_embedding is None:
            await node.generate_name_embedding(embedder)

        entity_data: dict[str, Any] = {
            'uuid': node.uuid,
            'name': node.name,
            'name_embedding': node.name_embedding,
            'group_id': node.group_id,
            'summary': node.summary,
            'created_at': node.created_at,
        }

        entity_data['labels'] = list(set(node.labels + ['Entity']))
        if driver.provider == GraphProvider.KUZU:
            attributes = convert_datetimes_to_strings(node.attributes) if node.attributes else {}
            entity_data['attributes'] = json.dumps(attributes)
        else:
            entity_data.update(node.attributes or {})
            entity_data['labels'] = list(
                set(node.labels + ['Entity', 'Entity_' + node.group_id.replace('-', '')])
            )

        nodes.append(entity_data)
```

**File:** graphiti_core/utils/bulk_utils.py (L203-233)

```python
async def extract_nodes_and_edges_bulk(
    clients: GraphitiClients,
    episode_tuples: list[tuple[EpisodicNode, list[EpisodicNode]]],
    edge_type_map: dict[tuple[str, str], list[str]],
    entity_types: dict[str, type[BaseModel]] | None = None,
    excluded_entity_types: list[str] | None = None,
    edge_types: dict[str, type[BaseModel]] | None = None,
) -> tuple[list[list[EntityNode]], list[list[EntityEdge]]]:
    extracted_nodes_bulk: list[list[EntityNode]] = await semaphore_gather(
        *[
            extract_nodes(clients, episode, previous_episodes, entity_types, excluded_entity_types)
            for episode, previous_episodes in episode_tuples
        ]
    )

    extracted_edges_bulk: list[list[EntityEdge]] = await semaphore_gather(
        *[
            extract_edges(
                clients,
                episode,
                extracted_nodes_bulk[i],
                previous_episodes,
                edge_type_map=edge_type_map,
                group_id=episode.group_id,
                edge_types=edge_types,
            )
            for i, (episode, previous_episodes) in enumerate(episode_tuples)
        ]
    )

    return extracted_nodes_bulk, extracted_edges_bulk
```

**File:** graphiti_core/utils/bulk_utils.py (L236-320)

```python
async def dedupe_nodes_bulk(
    clients: GraphitiClients,
    extracted_nodes: list[list[EntityNode]],
    episode_tuples: list[tuple[EpisodicNode, list[EpisodicNode]]],
    entity_types: dict[str, type[BaseModel]] | None = None,
) -> tuple[dict[str, list[EntityNode]], dict[str, str]]:
    embedder = clients.embedder
    min_score = 0.8

    # generate embeddings
    await semaphore_gather(
        *[create_entity_node_embeddings(embedder, nodes) for nodes in extracted_nodes]
    )

    # Find similar results
    dedupe_tuples: list[tuple[list[EntityNode], list[EntityNode]]] = []
    for i, nodes_i in enumerate(extracted_nodes):
        existing_nodes: list[EntityNode] = []
        for j, nodes_j in enumerate(extracted_nodes):
            if i == j:
                continue
            existing_nodes += nodes_j

        candidates_i: list[EntityNode] = []
        for node in nodes_i:
            for existing_node in existing_nodes:
                # Approximate BM25 by checking for word overlaps (this is faster than creating many in-memory indices)
                # This approach will cast a wider net than BM25, which is ideal for this use case
                node_words = set(node.name.lower().split())
                existing_node_words = set(existing_node.name.lower().split())
                has_overlap = not node_words.isdisjoint(existing_node_words)
                if has_overlap:
                    candidates_i.append(existing_node)
                    continue

                # Check for semantic similarity even if there is no overlap
                similarity = np.dot(
                    normalize_l2(node.name_embedding or []),
                    normalize_l2(existing_node.name_embedding or []),
                )
                if similarity >= min_score:
                    candidates_i.append(existing_node)

        dedupe_tuples.append((nodes_i, candidates_i))

    # Determine Node Resolutions
    bulk_node_resolutions: list[
        tuple[list[EntityNode], dict[str, str], list[tuple[EntityNode, EntityNode]]]
    ] = await semaphore_gather(
        *[
            resolve_extracted_nodes(
                clients,
                dedupe_tuple[0],
                episode_tuples[i][0],
                episode_tuples[i][1],
                entity_types,
                existing_nodes_override=dedupe_tuples[i][1],
            )
            for i, dedupe_tuple in enumerate(dedupe_tuples)
        ]
    )

    # Collect all duplicate pairs sorted by uuid
    duplicate_pairs: list[tuple[str, str]] = []
    for _, _, duplicates in bulk_node_resolutions:
        for duplicate in duplicates:
            n, m = duplicate
            duplicate_pairs.append((n.uuid, m.uuid))

    # Now we compress the duplicate_map, so that 3 -> 2 and 2 -> becomes 3 -> 1 (sorted by uuid)
    compressed_map: dict[str, str] = compress_uuid_map(duplicate_pairs)

    node_uuid_map: dict[str, EntityNode] = {
        node.uuid: node for nodes in extracted_nodes for node in nodes
    }

    nodes_by_episode: dict[str, list[EntityNode]] = {}
    for i, nodes in enumerate(extracted_nodes):
        episode = episode_tuples[i][0]

        nodes_by_episode[episode.uuid] = [
            node_uuid_map[compressed_map.get(node.uuid, node.uuid)] for node in nodes
        ]

    return nodes_by_episode, compressed_map
```

**File:** graphiti_core/utils/bulk_utils.py (L440-455)

```python
def compress_uuid_map(duplicate_pairs: list[tuple[str, str]]) -> dict[str, str]:
    """
    all_ids: iterable of all entity IDs (strings)
    duplicate_pairs: iterable of (id1, id2) pairs
    returns: dict mapping each id -> lexicographically smallest id in its duplicate set
    """
    all_uuids = set()
    for pair in duplicate_pairs:
        all_uuids.add(pair[0])
        all_uuids.add(pair[1])

    uf = UnionFind(all_uuids)
    for a, b in duplicate_pairs:
        uf.union(a, b)
    # ensure full path‐compression before mapping
    return {uuid: uf.find(uuid) for uuid in all_uuids}
```

**File:** graphiti_core/nodes.py (L62-70)

```python
    json : str
        Represents an episode containing a JSON string object with structured data.
    text : str
        Represents a plain text episode.
    """

    message = 'message'
    json = 'json'
    text = 'text'
```

**File:** graphiti_core/nodes.py (L267-273)

```python
    valid_at: datetime = Field(
        description='datetime of when the original document was created',
    )
    entity_edges: list[str] = Field(
        description='list of entity edges referenced in this episode',
        default_factory=list,
    )
```

**File:** graphiti_core/utils/maintenance/node_operations.py (L104-112)

```python
    context = {
        'episode_content': episode.content,
        'episode_timestamp': episode.valid_at.isoformat(),
        'previous_episodes': [ep.content for ep in previous_episodes],
        'custom_prompt': custom_prompt,
        'entity_types': entity_types_context,
        'source_description': episode.source_description,
        'ensure_ascii': clients.ensure_ascii,
    }
```

**File:** graphiti_core/utils/maintenance/node_operations.py (L124-127)

```python
        elif episode.source == EpisodeType.json:
            llm_response = await llm_client.generate_response(
                prompt_library.extract_nodes.extract_json(context), response_model=ExtractedEntities
            )
```

**File:** graphiti_core/utils/maintenance/node_operations.py (L134-148)

```python
        if reflexion_iterations < MAX_REFLEXION_ITERATIONS:
            missing_entities = await extract_nodes_reflexion(
                llm_client,
                episode,
                previous_episodes,
                [entity.name for entity in extracted_entities],
                clients.ensure_ascii,
            )

            entities_missed = len(missing_entities) != 0

            custom_prompt = 'Make sure that the following entities are extracted: '
            for entity in missing_entities:
                custom_prompt += f'\n{entity},'

```

**File:** graphiti_core/graphiti.py (L577-577)

```python
    ##### EXPERIMENTAL #####
```

**File:** graphiti_core/graphiti.py (L627-629)

```python
            # if group_id is None, use the default group id by the provider
            group_id = group_id or get_default_group_id(self.driver.provider)
            validate_group_id(group_id)
```

**File:** graphiti_core/graphiti.py (L857-888)

```python
    async def build_communities(
        self, group_ids: list[str] | None = None
    ) -> tuple[list[CommunityNode], list[CommunityEdge]]:
        """
        Use a community clustering algorithm to find communities of nodes. Create community nodes summarising
        the content of these communities.
        ----------
        query : list[str] | None
            Optional. Create communities only for the listed group_ids. If blank the entire graph will be used.
        """
        # Clear existing communities
        await remove_communities(self.driver)

        community_nodes, community_edges = await build_communities(
            self.driver, self.llm_client, group_ids
        )

        await semaphore_gather(
            *[node.generate_name_embedding(self.embedder) for node in community_nodes],
            max_coroutines=self.max_coroutines,
        )

        await semaphore_gather(
            *[node.save(self.driver) for node in community_nodes],
            max_coroutines=self.max_coroutines,
        )
        await semaphore_gather(
            *[edge.save(self.driver) for edge in community_edges],
            max_coroutines=self.max_coroutines,
        )

        return community_nodes, community_edges
```

**File:** graphiti_core/graphiti.py (L890-947)

```python
    async def search(
        self,
        query: str,
        center_node_uuid: str | None = None,
        group_ids: list[str] | None = None,
        num_results=DEFAULT_SEARCH_LIMIT,
        search_filter: SearchFilters | None = None,
    ) -> list[EntityEdge]:
        """
        Perform a hybrid search on the knowledge graph.

        This method executes a search query on the graph, combining vector and
        text-based search techniques to retrieve relevant facts, returning the edges as a string.

        This is our basic out-of-the-box search, for more robust results we recommend using our more advanced
        search method graphiti.search_().

        Parameters
        ----------
        query : str
            The search query string.
        center_node_uuid: str, optional
            Facts will be reranked based on proximity to this node
        group_ids : list[str | None] | None, optional
            The graph partitions to return data from.
        num_results : int, optional
            The maximum number of results to return. Defaults to 10.

        Returns
        -------
        list
            A list of EntityEdge objects that are relevant to the search query.

        Notes
        -----
        This method uses a SearchConfig with num_episodes set to 0 and
        num_results set to the provided num_results parameter.

        The search is performed using the current date and time as the reference
        point for temporal relevance.
        """
        search_config = (
            EDGE_HYBRID_SEARCH_RRF if center_node_uuid is None else EDGE_HYBRID_SEARCH_NODE_DISTANCE
        )
        search_config.limit = num_results

        edges = (
            await search(
                self.clients,
                query,
                group_ids,
                search_config,
                search_filter if search_filter is not None else SearchFilters(),
                center_node_uuid,
            )
        ).edges

        return edges
```

