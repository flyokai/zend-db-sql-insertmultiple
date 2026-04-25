# flyokai/zend-db-sql-insertmultiple

> User docs → [`README.md`](README.md) · Agent quick-ref → [`CLAUDE.md`](CLAUDE.md) · Agent deep dive → [`AGENTS.md`](AGENTS.md)

> Multi-row `INSERT INTO … VALUES (…), (…), (…)` for Laminas DB.

Adds `Laminas\Db\Sql\InsertMultiple`, a single-statement multi-row insert builder that is otherwise missing from Laminas DB. Adapted from the original `Zend\Db\Sql\Insert`.

## Features

- Multi-row `INSERT … VALUES (…), (…), (…)`
- `INSERT … SELECT`
- Two value-merging strategies: `VALUES_SET` (replace) and `VALUES_MERGE` (append)
- Compatible with Laminas `Sql::buildSqlString()` / `Sql::prepareStatementForSqlObject()`

## Installation

```bash
composer require flyokai/zend-db-sql-insertmultiple
```

## Quick start

### Multi-row insert

```php
use Laminas\Db\Sql\InsertMultiple;
use Laminas\Db\Sql\Sql;

$insert = new InsertMultiple('users');
$insert->columns(['email', 'name', 'status']);
$insert->values([
    ['alice@example.com', 'Alice', 1],
    ['bob@example.com',   'Bob',   0],
]);

$sql = new Sql($adapter);
$stmt = $sql->prepareStatementForSqlObject($insert);
$stmt->execute();

// → INSERT INTO users (email, name, status) VALUES (?, ?, ?), (?, ?, ?)
```

### `INSERT … SELECT`

```php
$insert = new InsertMultiple('users');
$insert->columns(['email', 'name']);
$insert->select(
    $sql->select('staging_users')->columns(['email', 'name'])
);

// → INSERT INTO users (email, name) SELECT email, name FROM staging_users
```

### Value merging

```php
$insert->values([['a@b.com', 'A']]);                                   // sets the rows
$insert->values(['c@d.com', 'C'], InsertMultiple::VALUES_MERGE);       // appends a row
```

## API

| Method | Purpose |
|--------|---------|
| `into(string $table)` | Set target table |
| `columns(array $columns)` | Define column list (call before `values()`) |
| `values(array\|Select $values, int $flag = VALUES_SET)` | Set/append rows or provide a `Select` |
| `select(Select $select)` | Convenience for `INSERT…SELECT` |
| `getRawState($key)` | Introspect internal state (for debugging) |

## Internals

- Each row is `ksort()`-ed for consistent column ordering.
- Named parameters are generated as `insMulti0`, `insMulti1`, …
- Non-scalar values (Expressions, Literals) are cached per column to avoid redundant SQL generation.

## Gotchas

- **`columns()` is required** before `values()` with array data. Missing columns throws `InvalidArgumentException`.
- **No built-in chunking** — generates a single statement for all rows. MySQL has statement size limits (~1000 tuples by default). Calling code must chunk.
- **Empty values produce empty SQL** — if no rows, `processInsert()` returns an empty string silently.
- **Cannot mix `VALUES_MERGE` with a `Select`** — throws.
- **Parameter naming is global** — counter is shared across all rows (`insMulti0`, `insMulti1`, …).

## See also

- [`flyokai/laminas-db-bulk-update`](../laminas-db-bulk-update/README.md) — `InsertOnDuplicate` extends `InsertMultiple` with upsert behaviour and ID resolution
- [`flyokai/laminas-db`](../laminas-db/README.md) — base abstraction

## License

BSD-3-Clause
