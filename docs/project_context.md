---
project_name: 'knowledge-base'
user_name: 'manhhaycode'
date: '2025-12-17'
sections_completed: ['technology_stack', 'language_rules', 'framework_rules', 'testing_rules', 'quality_rules', 'workflow_rules', 'anti_patterns']
status: 'complete'
rule_count: 42
optimized_for_llm: true
---

# Project Context for AI Agents

_Critical rules and patterns for implementing the knowledge-base MCP server. Focus on unobvious details that agents might miss._

---

## Technology Stack & Versions

| Technology | Version | Critical Notes |
|------------|---------|----------------|
| Python | 3.10+ | Async/await, type hints required |
| FastMCP | ^0.5.0 | MCP protocol SDK, decorator-based tools |
| Neo4j | 5.x+ | **REQUIRED**: Must use 5.x+ for vector indexes |
| SentenceTransformers | all-MiniLM-L6-v2 | 384 dimensions, cosine similarity |
| CocoIndex | ^0.1.0 | Rust-based ETL for performance |
| tree-sitter-typescript | ^0.20.0 | Angular decorator parsing |
| neo4j (driver) | ^5.0.0 | Python driver with connection pooling |
| python-dotenv | ^1.0.0 | Environment configuration |
| ruff | ^0.1.0 | Python linting |
| mypy | ^1.0.0 | Type checking |
| pytest | ^7.0.0 | Testing framework |

**Dependency Management:**
- Pin exact versions in `requirements.txt` for reproducibility
- Use `requirements-dev.txt` for development dependencies
- Run `pip freeze > requirements.lock` after updates

---

## Critical Implementation Rules

### Python Language Rules

- Use `async/await` patterns for all I/O operations
- Define `__all__` in public modules
- **Docstrings:** Google style format

```python
def function(arg: str) -> dict:
    """Short description.
    
    Args:
        arg: Description of argument.
    
    Returns:
        Description of return value.
    
    Raises:
        ValueError: When invalid input.
    """
```

### Error Handling Pattern

```python
# ALWAYS use custom exception hierarchy
class KnowledgeBaseError(Exception):
    """Base exception for all knowledge-base errors"""
    pass

class IndexingError(KnowledgeBaseError):
    """Raised when indexing fails"""
    pass

class QueryError(KnowledgeBaseError):
    """Raised when a query fails"""
    pass

class ConnectionError(KnowledgeBaseError):
    """Raised when Neo4j connection fails"""
    pass
```

### Neo4j Connection Pattern

```python
# Connection settings
NEO4J_MAX_POOL_SIZE = 50
NEO4J_CONNECTION_TIMEOUT = 30  # seconds
NEO4J_MAX_RETRY_TIME = 15  # seconds

class Neo4jClient:
    def __init__(self, uri: str, user: str, password: str):
        self._driver = GraphDatabase.driver(
            uri, auth=(user, password),
            max_connection_pool_size=NEO4J_MAX_POOL_SIZE,
            connection_timeout=NEO4J_CONNECTION_TIMEOUT
        )
    
    @contextmanager
    def session(self):
        session = self._driver.session()
        try:
            yield session
        finally:
            session.close()
```

**Retry on transient failures:** Use `neo4j.exceptions.TransientError`

---

### Neo4j Schema Rules

**Node Labels:** PascalCase
- `Project`, `Library`, `Component`, `Service`, `Feature`, `Document`, `CodeChunk`

**Relationship Types:** SCREAMING_SNAKE_CASE
- `DEPENDS_ON`, `EXPORTS`, `USES_SERVICE`, `IMPLEMENTED_BY`, `DOCUMENTED_BY`, `CONTAINS`

**Properties:** snake_case
- `file_path`, `embedding`, `created_at`, `content_hash`

**Vector Index Configuration:**
```cypher
CREATE VECTOR INDEX feature_embedding IF NOT EXISTS
FOR (f:Feature) ON (f.embedding)
OPTIONS {indexConfig: {`vector.dimensions`: 384, `vector.similarity_function`: 'cosine'}};
```

---

### MCP Tool Response Format

**ALL tools MUST return this structure:**

```python
# Success response
{
    "status": "success",
    "data": { ... },  # Tool-specific payload
    "metadata": {
        "query_time_ms": 123,
        "timestamp": "2024-12-14T10:30:00Z"
    }
}

# Error response
{
    "status": "error",
    "error": {
        "code": "FEATURE_NOT_FOUND",
        "message": "Human-readable message",
        "details": { ... }
    },
    "metadata": {
        "timestamp": "2024-12-14T10:30:00Z"
    }
}
```

**Error Codes:**
| Code | Description |
|------|-------------|
| `INDEXING_FAILED` | File parsing or indexing error |
| `QUERY_FAILED` | Database query execution error |
| `FEATURE_NOT_FOUND` | Requested feature doesn't exist |
| `INVALID_PARAMETERS` | Invalid input parameters |
| `CONNECTION_ERROR` | Neo4j connection failure |
| `EMBEDDING_ERROR` | Embedding generation failure |

---

### Project Structure

```
src/
├── main.py                 # MCP server entry point
├── config/settings.py      # Environment configuration
├── mcp/
│   ├── server.py           # FastMCP server setup
│   └── tools/              # One file per MCP tool
├── indexers/               # ETL pipelines
├── queries/                # Graph query logic
├── db/
│   ├── neo4j_client.py     # Connection management
│   └── embeddings.py       # SentenceTransformer wrapper
└── utils/exceptions.py     # Custom exceptions
```

**File Naming:**
- Python files: `snake_case.py`
- Test files: `test_<module>.py` (co-located or in `tests/`)

---

### Testing Rules

- **Test suffix:** `_test.py` or `test_*.py`
- **Fixtures:** Place in `tests/fixtures/`
- **Unit tests:** `tests/unit/`
- **Integration tests:** `tests/integration/` (require Neo4j)
- **E2E tests:** `tests/e2e/` (full MCP tool testing)

---

### Code Quality Rules

- **Linting:** `ruff check src/` must pass
- **Type checking:** `mypy src/` must pass
- **Pre-commit hooks:** Install with `pre-commit install`

---

### Logging Pattern

```python
import logging

logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    level=logging.INFO
)

# Usage:
# DEBUG - Detailed tracing: "Indexing file: {path}"
# INFO - Operation completion: "Indexed 45 files in 2.3s"
# WARNING - Recoverable issues: "Skipped unsupported file type"
# ERROR - Failures: "Neo4j connection failed: {error}"
```

---

### Security Rules

- **NEVER** store credentials in code
- **ALWAYS** use environment variables (`NEO4J_URI`, `NEO4J_USER`, `NEO4J_PASSWORD`)
- **NEVER** store source code in Neo4j - only embeddings and metadata
- Use `bolt://` or `bolt+s://` protocols only

---

## Issue Tracking Rules

**MANDATORY: Use bd (beads) for ALL issue tracking.** See [AGENTS.md](../AGENTS.md) for full CLI reference.

- Always use `--json` flag for programmatic use
- Commit `.beads/issues.jsonl` with code changes
- Use `bd ready` to find unblocked work

**FORBIDDEN:** ❌ Markdown TODOs, external trackers, duplicate systems

---

## Performance Targets

| Metric | Target |
|--------|--------|
| Query response time | < 500ms |
| Full indexing (8000 files) | < 5 minutes |
| Incremental indexing | < 10 seconds |
| Vector search latency | < 200ms |
| Context relevance | > 80% |

---

## MCP Tools Summary

| Tool | Purpose |
|------|---------|
| `index_source_code` | Index Angular/TS source files |
| `index_markdown_specs` | Index feature specifications |
| `query_feature_context` | Get complete feature context |
| `search_similar_features` | Semantic search for features |
| `analyze_feature_impact` | Analyze change impact |
| `get_feature_dependencies` | List feature dependencies |

---

## Usage Guidelines

**For AI Agents:**
- Read this file before implementing any code
- Follow ALL rules exactly as documented
- When in doubt, prefer the more restrictive option
- Update this file if new patterns emerge

**For Humans:**
- Keep this file lean and focused on agent needs
- Update when technology stack changes
- Review quarterly for outdated rules
- Remove rules that become obvious over time

---

_Last Updated: 2025-12-17_
