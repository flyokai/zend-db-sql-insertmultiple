# flyokai/zend-db-sql-insertmultiple

> User docs → [`README.md`](README.md) · Agent quick-ref → [`CLAUDE.md`](CLAUDE.md) · Agent deep dive → [`AGENTS.md`](AGENTS.md)

Multi-row INSERT SQL builder for Laminas DB.

See [AGENTS.md](AGENTS.md) for detailed documentation.

## Quick Reference

- **Class**: `InsertMultiple` extends `AbstractPreparableSql`
- **Patterns**: Multi-row `INSERT INTO ... VALUES (...), (...)` and `INSERT...SELECT`
- **API**: `into()` → `columns()` → `values()` (fluent interface)
- **Merge flags**: `VALUES_SET` (replace) / `VALUES_MERGE` (append)
- **No chunking**: Single statement for all rows — caller must batch for large sets
