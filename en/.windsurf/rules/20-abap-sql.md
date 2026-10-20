---
trigger: glob
globs: **/*.abap
---
# ABAP SQL

## Syntax (strict mode)
- Escape **every** host variable with `@` and separate lists with commas. Once one modern feature (`@`, commas, SQL expressions, inline `@DATA`) is used, the statement is checked in strict mode, so it is all-or-nothing within one statement.
- Put `INTO` last (7.40 SP08+): `SELECT f1, f2 FROM t WHERE k = @key INTO TABLE @DATA(result).` `UP TO n ROWS` then goes after `INTO`.
- Name the columns you need. No `SELECT *` on wide tables.

## Correctness traps
- **FOR ALL ENTRIES with an empty driver table ignores the whole WHERE clause and reads all rows.** Always guard: `IF itab IS NOT INITIAL.`
- FOR ALL ENTRIES removes duplicate result rows. Select all key fields, or you lose data silently.
- `SELECT SINGLE` is for a full primary key. Otherwise use `SELECT ... ORDER BY ... INTO @... UP TO 1 ROWS` so the result is deterministic.
- After `SELECT` check `sy-subrc` / `sy-dbcnt`. Table expressions (`itab[ ... ]`) do **not** set `sy-subrc`: they raise `CX_SY_ITAB_LINE_NOT_FOUND` (exception: `ASSIGN itab[ ... ] TO <fs>` sets `sy-subrc` and raises nothing). Use `line_exists( )`, `VALUE #( itab[ ... ] OPTIONAL )` (7.40 SP08+) or `TRY ... CATCH`.

## Performance
- No `SELECT` inside `LOOP`. Use a JOIN, a subquery, `FROM @itab` (7.52+), or one FOR ALL ENTRIES with a guard.
- Push aggregation, filtering and CASE logic into SQL or CDS. Do not post-filter large result sets in ABAP.
- In S/4HANA prefer released CDS views over direct table reads (see S/4 rules).

## ABAP Cloud
- Only released CDS views or tables may be read. `USING CLIENT` and native SQL (`EXEC SQL`) are not allowed in ABAP for Cloud Development.
