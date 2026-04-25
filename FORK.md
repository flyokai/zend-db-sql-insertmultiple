# FORK notes — `flyokai/zend-db-sql-insertmultiple`

> User docs → [`README.md`](README.md) · Agent quick-ref → [`CLAUDE.md`](CLAUDE.md) · Agent deep dive → [`AGENTS.md`](AGENTS.md)

This package vendors the `Laminas\Db\Sql\InsertMultiple` builder. It is *not* an upstream Laminas component — Laminas DB does not ship a multi-row INSERT builder. Instead it is adapted from the third-party project [`nanawel/zend-db-sql-insertmultiple`](https://github.com/nanawel/zend-db-sql-insertmultiple).

## Upstream

- Repository: <https://github.com/nanawel/zend-db-sql-insertmultiple>
- License: BSD-3-Clause
- Lineage: the original `Zend\Db\Sql\Insert` (pre-Laminas) restructured to support multi-row `INSERT INTO … VALUES (…), (…)` semantics.

## Why we forked

- `laminas/laminas-db-bulk-update` — the higher-level `InsertOnDuplicate` builder — needs `InsertMultiple` as its base class. Importing the upstream package adds a transitive dependency on a third-party developer's release cadence.
- We need PHP 8.2+ guarantees that the upstream package was not yet asserting (its `composer.json` still allowed PHP 5.3+).
- We renamed the namespace shim from `Zend\Db\Sql\…` to `Laminas\Db\Sql\…` to align with the modern Laminas project.

## What changed

- Namespace `Zend\Db\Sql\…` → `Laminas\Db\Sql\…`.
- PHP version constraint tightened.
- `composer.json` published under the Flyokai namespace so it can be resolved alongside the rest of the framework via the Flyokai package mirror.

The `InsertMultiple` class itself is functionally unchanged. The same fluent API (`into() → columns() → values()`) and the same `VALUES_SET` / `VALUES_MERGE` flags.

## Compatibility

The fork lives under `Laminas\Db\Sql\InsertMultiple` (the upstream lived under `Zend\Db\Sql\InsertMultiple`). Code written for upstream needs a one-line namespace change.

## Tracking upstream

Upstream sees occasional commits. We do not auto-track but check periodically:

```bash
git remote add upstream https://github.com/nanawel/zend-db-sql-insertmultiple.git
git fetch upstream
git log --oneline upstream/master ^HEAD
```

If upstream lands a relevant fix, we apply it manually (with the namespace adjustment).

## License

BSD-3-Clause — same as upstream and as Laminas DB. See `LICENSE`.
