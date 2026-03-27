# wtsergo/zend-db-sql-insertmultiple

Multi-row INSERT SQL builder extending Laminas DB. Generates `INSERT INTO ... VALUES (...), (...), (...)` statements.

## Key Abstractions

### InsertMultiple

Single class extending `AbstractPreparableSql`. Supports multi-row INSERT and INSERT...SELECT.

**Usage:**
```php
$insert = new InsertMultiple('users');
$insert->columns(['email', 'name', 'status']);
$insert->values([
    ['alice@example.com', 'Alice', 1],
    ['bob@example.com', 'Bob', 0],
]);
// → INSERT INTO users (email, name, status) VALUES (?, ?. ?), (?, ?, ?)
```

**INSERT...SELECT:**
```php
$insert = new InsertMultiple('users');
$insert->columns(['email', 'name']);
$insert->select($db->select()->from('staging_users'));
// → INSERT INTO users (email, name) SELECT email, name FROM staging_users
```

### Value Merge Strategies

- `VALUES_SET` (default) — replaces entire value set on each `values()` call
- `VALUES_MERGE` — appends row to existing collection

```php
$insert->values([['a@b.com', 'A']]); // sets rows
$insert->values(['c@d.com', 'C'], InsertMultiple::VALUES_MERGE); // appends row
```

### SQL Generation

- `processInsert()` — iterates `$valueRows`, creates named parameters (`insMulti0`, `insMulti1`, ...), quotes identifiers via platform
- `processSelect()` — delegates to Laminas sub-select processing
- Each row is `ksort()`-ed for consistent column ordering
- Non-scalar values (Expressions, Literals) cached per column to avoid redundant SQL generation

### Methods

- `into($table)` — set target table
- `columns(array $columns)` — define column list (must call before `values()`)
- `values($values, $flag)` — set/append rows or provide Select instance
- `select(Select $select)` — convenience for INSERT...SELECT
- `getRawState($key)` — introspect internal state

## Gotchas

- **columns() required**: Must be called before `values()` with array data. Missing columns throws `InvalidArgumentException`.
- **No built-in chunking**: Generates a single INSERT for all rows. MySQL has statement size limits (~1000 tuples default). Calling code must chunk.
- **Empty values = empty string**: If no rows, `processInsert()` returns empty string silently.
- **Cannot mix MERGE + SELECT**: `VALUES_MERGE` flag with Select instance throws.
- **Parameter naming global**: Parameters numbered sequentially across all rows (`insMulti0`, `insMulti1`, ...).
