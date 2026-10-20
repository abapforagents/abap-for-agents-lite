# ABAP for Agents

Instruções para agentes de AI que trabalham em código SAP ABAP, CDS e RAP. Gerado a partir do ABAP for Agents Rules Pack v1.0.1. Edita a fonte, não este ficheiro.

> Edição lite gratuita. O pack completo acrescenta regras de RAP/CDS, ABAP Cloud e clean core, Clean ABAP, ABAP Unit e S/4HANA vs ECC, e todas as skills de tarefas.

## ABAP for Agents: base

### 1. Definir o alvo antes de escrever código
- Descobre (ou pergunta uma vez) três coisas: **(a) versão da linguagem**: ABAP for Cloud Development (BTP ABAP Environment, S/4HANA Cloud ou package Cloud on-prem) ou Standard ABAP; **(b) produto**: S/4HANA ou ECC; **(c) release**: SAP_BASIS / ABAP Platform, p. ex. 7.50, 7.58, 8.16.
- Se não for possível saber, assume o **release mais baixo plausível** e evita sintaxe mais recente. S/4HANA → SAP_BASIS: 1610 = 7.51, 1709 = 7.52, 1809 = 7.53, 1909 = 7.54, 2020 = 7.55, 2021 = 7.56, 2022 = 7.57, 2023 = 7.58, 2025 = 8.16. O ECC 6.0 corre no máximo em SAP_BASIS 7.50 (EHP8), por isso não tem sintaxe 7.51+ nem RAP ou CDS view entities.
- Nunca mistures sintaxe de releases diferentes na mesma resposta.

### 2. Nunca inventar objetos SAP
- Não inventes módulos de função, BAPIs, classes, views CDS, tabelas, BAdIs nem assinaturas de métodos. Se não tens a certeza de que um objeto existe, di-lo e explica como verificar: no ADT "Open ABAP Development Object", nas SE37/SE24/SE11, ou no Cloudification Repository viewer para o estado de release.
- Em ABAP Cloud usa só objetos com estado **released** (contrato C1 "Use System-Internally", visível para Cloud Development). Um nome plausível como `I_*`, `CL_*` ou `BAPI_*` **não** prova que o objeto existe nem que está libertado.
- Quando dependeres de uma API SAP, indica-a explicitamente na resposta para poder ser verificada.

### 3. Estilo por omissão (Clean ABAP)
- Prefere classes e interfaces a módulos de função, FORMs e reports. As cascas procedimentais (FM RFC, report) só delegam numa classe.
- Usa sintaxe moderna apenas onde o release a suporta: declarações inline, expressões construtoras (`NEW`, `VALUE`, `CONV`, `COND`, `CORRESPONDING`), expressões de tabela, string templates.
- **Segue a convenção de nomes já existente no código**. O guia Clean ABAP desaconselha prefixos húngaros (`lv_`, `gt_`), mas muitas equipas mantêm-nos. Nunca mistures os dois estilos no mesmo objeto.
- Usa exceções baseadas em classes, não códigos de retorno nem `sy-subrc` passado entre métodos. Usa `RAISE EXCEPTION NEW` (a partir de 7.52) e, em releases anteriores, `RAISE EXCEPTION TYPE`.
- Métodos pequenos, que fazem uma só coisa. `FINAL` por omissão. `PRIVATE` por omissão.

### 4. Resultado
- Entrega código ativável e limpo no ATC: sem warnings de sintaxe, pragmas (`##...`) em vez de pseudo-comentários (`"#EC`) sempre que exista pragma, sem código morto.
- Para qualquer alteração não trivial, fornece ou atualiza testes ABAP Unit.
- Indica os pressupostos (release, versão da linguagem) numa linha no topo da resposta.

### 5. Ferramentas
- Se houver um servidor MCP de ABAP ligado (por exemplo o servidor MCP do ADT da SAP, nas ABAP development tools para VS Code), usa-o para consultar objetos, assinaturas e estado de release, e para correr a verificação de sintaxe, o ATC e o ABAP Unit **antes** de responder. Por omissão só leitura: nunca atives nem uses ferramentas de escrita sem o consentimento explícito do utilizador.

## Sintaxe disponível por release (confirmar antes de usar)

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

## ABAP SQL

### Sintaxe (strict mode)
- Faz escape de **todas** as variáveis host com `@` e separa as listas com vírgulas. Basta usar uma funcionalidade moderna (`@`, vírgulas, expressões SQL, `@DATA` inline) para a instrução ser verificada em strict mode, por isso é tudo ou nada dentro da mesma instrução.
- `INTO` no fim (7.40 SP08+): `SELECT f1, f2 FROM t WHERE k = @key INTO TABLE @DATA(result).` O `UP TO n ROWS` vem depois do `INTO`.
- Indica as colunas de que precisas. Nada de `SELECT *` em tabelas largas.

### Armadilhas de correção
- **FOR ALL ENTRIES com a tabela de entrada vazia ignora o WHERE inteiro e lê todas as linhas.** Protege sempre com `IF itab IS NOT INITIAL.`
- O FOR ALL ENTRIES remove linhas duplicadas do resultado. Seleciona todos os campos chave, senão perdes dados sem aviso.
- `SELECT SINGLE` é para a chave primária completa. Caso contrário usa `SELECT ... ORDER BY ... INTO @... UP TO 1 ROWS` para o resultado ser determinístico.
- Depois de um `SELECT` verifica `sy-subrc` / `sy-dbcnt`. As expressões de tabela (`itab[ ... ]`) **não** preenchem `sy-subrc`: lançam `CX_SY_ITAB_LINE_NOT_FOUND` (exceção: `ASSIGN itab[ ... ] TO <fs>` preenche `sy-subrc` e não lança nada). Usa `line_exists( )`, `VALUE #( itab[ ... ] OPTIONAL )` (7.40 SP08+) ou `TRY ... CATCH`.

### Performance
- Nada de `SELECT` dentro de `LOOP`. Usa JOIN, subquery, `FROM @itab` (7.52+) ou um único FOR ALL ENTRIES protegido.
- Leva agregações, filtros e lógica CASE para o SQL ou CDS. Não filtres grandes resultados em ABAP.
- Em S/4HANA prefere views CDS libertadas a leituras diretas de tabelas (ver regras S/4).

### ABAP Cloud
- Só podes ler views CDS ou tabelas libertadas. `USING CLIENT` e SQL nativo (`EXEC SQL`) não são permitidos em ABAP for Cloud Development.

## Não gerar ABAP obsoleto ou desatualizado em código novo

| Em vez de | Escreve |
|---|---|
| `MOVE a TO b.` / `COMPUTE` | `b = a.` |
| `ADD 1 TO x.` | `x += 1.` (7.54+) ou `x = x + 1.` |
| `DATA itab TYPE ... OCCURS 0 WITH HEADER LINE` / instrução `TABLES` / parâmetros `TABLES` | Work area explícita ou `DATA(...)` inline; tipo tabela como IMPORTING/RETURNING |
| `RANGES r FOR f.` | `DATA r TYPE RANGE OF ...` |
| `CALL METHOD obj->m EXPORTING ...` | `obj->m( ... )` |
| `CREATE OBJECT o TYPE zcl_x.` | `o = NEW zcl_x( ).` |
| `DESCRIBE TABLE itab LINES n.` | `n = lines( itab ).` |
| `READ TABLE ... TRANSPORTING NO FIELDS` + verificação de `sy-subrc` | `line_exists( itab[ ... ] )` |
| `CONCATENATE a b INTO c.` | String template (ver abaixo) ou `c = a && b.` |
| `TRANSLATE s TO UPPER CASE.` | `s = to_upper( s ).` |
| `FORM` / `PERFORM` (lógica nova) | Métodos de uma classe (local) |
| Variáveis host sem escape no SQL | `@var` (strict mode) |
| `boolc( )` em comparações | `xsdbool( )` |
| Pseudo-comentários `"#EC ...` | Pragmas `##...` (quando existe) |
| `define view` + `@AbapCatalog.sqlViewName` | `define view entity` (7.55+) |

- String template para a linha do `CONCATENATE`: ``c = |{ a }{ b }|.``
- Quando **alteras** código legado, não reescrevas partes que não estão relacionadas. Moderniza só as linhas em que mexes, a não ser que o utilizador peça um refactoring.
- A maior parte das instruções obsoletas já é proibida dentro de classes e em ABAP for Cloud Development. Se a verificação de sintaxe a rejeitaria nesse contexto, nunca a sugiras.

## Autoverificação antes de responder (erros comuns da AI em ABAP)

1. Usei algum objeto que não tenho a certeza de que existe? Indica-o e assinala "verificar". Não adivinhes os parâmetros de um módulo de função ou BAPI: consulta a assinatura (SE37 / ADT) ou pergunta.
2. As BAPIs reportam erros na tabela `RETURN`, não via `sy-subrc` nem exceções. Verifica os tipos de mensagem `E` / `A`. Faz o commit à parte (`BAPI_TRANSACTION_COMMIT`), mas **nunca** dentro do RAP nem dentro de um loop registo a registo.
3. Misturei releases (p. ex. `@DATA` inline em SQL em 7.40 SP05, `+=` em 7.50, `define view entity` em 7.50)?
4. Usei hábitos de Java/C#/JS? Não existe `return x;`: as instruções terminam com ponto, não com `;`, e `RETURN expr.` (define o parâmetro de retorno e sai) só existe em métodos funcionais a partir do 7.58 / ABAP Cloud 2211, por isso confirma primeiro o release de destino e, abaixo disso, escreve `result = x.` e depois `RETURN.`. Também não existe `null` (usa `IS INITIAL` / `IS NOT BOUND`), não existe `++`, não existe `!=` (usa `<>`), e os literais string são `` `...` `` ou `'...'`, não `"..."` (em ABAP o `"` inicia um comentário).
5. Todas as instruções terminam em ponto final, e as cadeias com `:` são usadas de propósito. As keywords não distinguem maiúsculas de minúsculas, mas segue a configuração do pretty printer do projeto.
6. Deixei sem proteção uma tabela de entrada vazia num FOR ALL ENTRIES, ou um `SELECT` dentro de um loop?
7. Apanhei `CX_ROOT` ou engoli exceções? Apanha a classe específica, ou encapsula e volta a lançar.
8. Em ABAP Cloud, usei `sy-datum`, `WRITE`, um FM clássico ou uma tabela não libertada?
9. Inventei um código de transação, um caminho de customizing ou um número de SAP Note? Não cites SAP Notes de memória.
10. A resposta está limpa no ATC e coberta por um teste unitário?
