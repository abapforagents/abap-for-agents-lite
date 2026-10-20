---
applyTo: "**/*.abap"
---
# Do not generate obsolete or outdated ABAP in new code

| Instead of | Write |
|---|---|
| `MOVE a TO b.` / `COMPUTE` | `b = a.` |
| `ADD 1 TO x.` | `x += 1.` (7.54+) or `x = x + 1.` |
| `DATA itab TYPE ... OCCURS 0 WITH HEADER LINE` / `TABLES` statement / `TABLES` parameters | Explicit work area or inline `DATA(...)`; table type as IMPORTING/RETURNING |
| `RANGES r FOR f.` | `DATA r TYPE RANGE OF ...` |
| `CALL METHOD obj->m EXPORTING ...` | `obj->m( ... )` |
| `CREATE OBJECT o TYPE zcl_x.` | `o = NEW zcl_x( ).` |
| `DESCRIBE TABLE itab LINES n.` | `n = lines( itab ).` |
| `READ TABLE ... TRANSPORTING NO FIELDS` + `sy-subrc` check | `line_exists( itab[ ... ] )` |
| `CONCATENATE a b INTO c.` | String template (see below) or `c = a && b.` |
| `TRANSLATE s TO UPPER CASE.` | `s = to_upper( s ).` |
| `FORM` / `PERFORM` (new logic) | Methods of a (local) class |
| Unescaped host variables in SQL | `@var` (strict mode) |
| `boolc( )` in comparisons | `xsdbool( )` |
| Pseudo-comments `"#EC ...` | Pragmas `##...` (where one exists) |
| `define view` + `@AbapCatalog.sqlViewName` | `define view entity` (7.55+) |

- String template for the `CONCATENATE` row: ``c = |{ a }{ b }|.``
- When you **modify** legacy code, do not rewrite unrelated parts. Modernize only the lines you touch, unless the user asks for a refactoring.
- Most obsolete statements are already forbidden inside classes and in ABAP for Cloud Development. If the syntax check would reject it there, never suggest it.
