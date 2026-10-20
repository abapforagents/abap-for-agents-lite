---
applyTo: "**/*.abap"
---
# Sintaxe disponível por release (confirmar antes de usar)

| Funcionalidade | Disponível a partir de |
|---|---|
| Declarações inline `DATA(...)`, `FIELD-SYMBOL(...)`; operadores construtores `NEW VALUE REF EXACT CONV CAST COND SWITCH`; expressões de tabela `itab[ ... ]` | 7.40 SP02 |
| `CORRESPONDING #( )`, `LET`, table comprehensions `VALUE #( FOR wa IN itab ( ... ) )` | 7.40 SP05 |
| ABAP SQL: escape `@` das variáveis host, listas separadas por vírgulas, expressões SQL (strict mode) | 7.40 SP05 |
| ABAP SQL: `INTO @DATA(...)` / `INTO TABLE @DATA(...)` inline; `INTO` como última cláusula | 7.40 SP08 |
| `REDUCE`, `FOR ... UNTIL/WHILE`, `FILTER`, `xsdbool( )`, chamadas predicativas `IF meth( ).`, `VALUE #( itab[ ... ] OPTIONAL / DEFAULT ... )` | 7.40 SP08 |
| `SELECT FROM ... FIELDS ...` (FROM primeiro) | 7.50 |
| `TYPES BEGIN OF ENUM` | 7.51 |
| `RAISE EXCEPTION NEW cx_...( )`; tabelas internas como fonte de dados `SELECT ... FROM @itab` | 7.52 |
| `RAISE SHORTDUMP` | 7.53 |
| Atribuições de cálculo `+=  -=  *=  /=  &&=` | 7.54 |
| CDS view entities `DEFINE VIEW ENTITY` | 7.55 (S/4HANA 2020) |

- Em 7.40 SP05/SP06/SP07, `SELECT ... INTO TABLE @DATA(x)` é **erro de sintaxe**. Declara o destino antes.
- Abaixo de 7.54 escreve `index = index + 1.` e não `index += 1.`
- `boolc( )` devolve `string`. Usa `xsdbool( )` (tipo c de comprimento 1, compatível com `abap_bool`).
- Se o release é desconhecido e o utilizador não pediu sintaxe moderna, prefere a forma compatível com 7.50 e menciona a alternativa moderna numa linha.
