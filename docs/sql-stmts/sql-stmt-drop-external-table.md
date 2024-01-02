---
title: DROP EXTERNAL TABLE
---

# DROP EXTERNAL TABLE

Removes an external table definition.

## Synopsis

```sql
DROP EXTERNAL [WEB] TABLE [IF EXISTS] <name> [CASCADE | RESTRICT]
```

## Description

`DROP EXTERNAL TABLE` drops an existing external table definition from the database system. The external data sources or files are not deleted. To run this command you must be the owner of the external table.

## Parameters

**`WEB`**

Optional keyword for dropping external web tables.

**`IF EXISTS`**

Do not throw an error if the external table does not exist. Cloudberry Database issues a notice in this case.

**`name`**

The name (optionally schema-qualified) of an existing external table.

**`CASCADE`**

Automatically drop objects that depend on the external table (such as views).

**`RESTRICT`**

Refuse to drop the external table if any objects depend on it. This is the default.

## Examples

Remove the external table named `staging` if it exists:

```sql
DROP EXTERNAL TABLE IF EXISTS staging;
```

## Compatibility

There is no `DROP EXTERNAL TABLE` statement in the SQL standard.

## See also

[`CREATE EXTERNAL TABLE`](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-create-external-table.md), [`ALTER EXTERNAL TABLE`](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-alter-external-table.md)
