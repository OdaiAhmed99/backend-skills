---
name: er-diagram
description: Draw the database as an ER diagram straight from the Django models (DBML, loaded into an ER MCP such as drawdb-mcp), and compare it with the previous version. Use when asked for an ERD or schema picture, after model changes, and during review to check the old design still fits the new version.
---
# Models -> ER diagram

The models are the schema. This skill reads `models.py` (never the database), writes DBML and loads it into the diagram tool.

## 1. Collect
For the asked scope (one app or a few related apps - never the whole project unless asked):
- every `models.Model`: table, fields, `null`, `unique`, `choices`, `default`
- every `ForeignKey` / `OneToOneField` (target, `on_delete`) and `ManyToManyField` (implicit join table unless `through=`)
- resolve string references like `"orders.Order"` to the real model

## 2. Name tables like humans read them
- Table title = model class name: `Order`, not `orders_order`.
- Implicit M2M: `<Model><Field>` -> `OrderTags`.
- Keep the real name in a note: `note: 'db_table: orders_order'`.
- All `ref:` targets use the new names.

## 3. Field types
| Django | DBML |
|---|---|
| `AutoField` / `BigAutoField` | `int [pk]` |
| `CharField` / `EmailField` / `SlugField` | `varchar` |
| `TextField` | `text` |
| `IntegerField` / `PositiveIntegerField` | `int` |
| `DecimalField` / `FloatField` | `decimal` / `float` |
| `BooleanField` | `boolean` |
| `DateField` / `DateTimeField` / `TimeField` | `date` / `timestamp` / `time` |
| `FileField` / `ImageField` | `varchar` (a path) |
| `ForeignKey` | `<field>_id int [ref: > Target.id]` |

## 4. Load and verify
Call the MCP's `import_dbml` with the DBML. Then `export_dbml` / `export_sql` and check the tables you meant are there - a tool call that didn't error is not proof.

## 5. Old vs new (the review step)
Keep the last approved DBML in `docs/backend/<app>/erd.dbml` (committed). On a change:
1. Generate the new DBML.
2. Diff it with the saved one.
3. For every difference ask: is it intended? Does a removed or renamed column still have readers (serializers, filters, lookups, reports)? Is a new FK protected with the right `on_delete`? Did a field get duplicated that already exists on a related table (`db-integrity`)?
4. Save the new DBML only after the answers are good.
