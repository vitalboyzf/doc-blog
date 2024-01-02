---
title: DROP ROLE
---

# DROP ROLE

Removes a database role.

## Synopsis

```sql
DROP ROLE [IF EXISTS] <name> [, ...]
```

## Description

`DROP ROLE` removes the specified role(s). To drop a superuser role, you must be a superuser yourself. To drop non-superuser roles, you must have `CREATEROLE` privilege.

A role cannot be removed if it is still referenced in any database; an error will be raised if so. Before dropping the role, you must drop all the objects it owns (or reassign their ownership) and revoke any privileges the role has been granted on other objects. The [`REASSIGN OWNED`](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-reassign-owned.md) and [`DROP OWNED`](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-drop-owned.md) commands can be useful for this purpose.

However, it is not necessary to remove role memberships involving the role; `DROP ROLE` automatically revokes any memberships of the target role in other roles, and of other roles in the target role. The other roles are not dropped nor otherwise affected.

## Parameters

**`IF EXISTS`**

Do not throw an error if the role does not exist. A notice is issued in this case.

**`name`**

The name of the role to remove.

## Examples

Remove the roles named `sally` and `bob`:

```sql
DROP ROLE sally, bob;
```

## Compatibility

The SQL standard defines `DROP ROLE`, but it allows only one role to be dropped at a time, and it specifies different privilege requirements than Cloudberry Database uses.

## See also

[`R`EASSIGN OWNED`](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-reassign-owned.md), [`DROP OWNED`](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-drop-owned.md), [`CREATE ROLE`](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-create-role.md), [ALTER ROLE](https://github.com/cloudberrydb/cloudberrydb-site/blob/cbdb-doc-validation/docs/sql-stmts/sql-stmt-alter-role.md), [`SET ROLE]`(/docs/sql-stmts/sql-stmt-set-role.md)
