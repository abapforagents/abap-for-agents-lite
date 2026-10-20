---
trigger: always_on
---
# ABAP for Agents: base

## 1. Definir o alvo antes de escrever código
- Descobre (ou pergunta uma vez) três coisas: **(a) versão da linguagem**: ABAP for Cloud Development (BTP ABAP Environment, S/4HANA Cloud ou package Cloud on-prem) ou Standard ABAP; **(b) produto**: S/4HANA ou ECC; **(c) release**: SAP_BASIS / ABAP Platform, p. ex. 7.50, 7.58, 8.16.
- Se não for possível saber, assume o **release mais baixo plausível** e evita sintaxe mais recente. S/4HANA → SAP_BASIS: 1610 = 7.51, 1709 = 7.52, 1809 = 7.53, 1909 = 7.54, 2020 = 7.55, 2021 = 7.56, 2022 = 7.57, 2023 = 7.58, 2025 = 8.16. O ECC 6.0 corre no máximo em SAP_BASIS 7.50 (EHP8), por isso não tem sintaxe 7.51+ nem RAP ou CDS view entities.
- Nunca mistures sintaxe de releases diferentes na mesma resposta.

## 2. Nunca inventar objetos SAP
- Não inventes módulos de função, BAPIs, classes, views CDS, tabelas, BAdIs nem assinaturas de métodos. Se não tens a certeza de que um objeto existe, di-lo e explica como verificar: no ADT "Open ABAP Development Object", nas SE37/SE24/SE11, ou no Cloudification Repository viewer para o estado de release.
- Em ABAP Cloud usa só objetos com estado **released** (contrato C1 "Use System-Internally", visível para Cloud Development). Um nome plausível como `I_*`, `CL_*` ou `BAPI_*` **não** prova que o objeto existe nem que está libertado.
- Quando dependeres de uma API SAP, indica-a explicitamente na resposta para poder ser verificada.

## 3. Estilo por omissão (Clean ABAP)
- Prefere classes e interfaces a módulos de função, FORMs e reports. As cascas procedimentais (FM RFC, report) só delegam numa classe.
- Usa sintaxe moderna apenas onde o release a suporta: declarações inline, expressões construtoras (`NEW`, `VALUE`, `CONV`, `COND`, `CORRESPONDING`), expressões de tabela, string templates.
- **Segue a convenção de nomes já existente no código**. O guia Clean ABAP desaconselha prefixos húngaros (`lv_`, `gt_`), mas muitas equipas mantêm-nos. Nunca mistures os dois estilos no mesmo objeto.
- Usa exceções baseadas em classes, não códigos de retorno nem `sy-subrc` passado entre métodos. Usa `RAISE EXCEPTION NEW` (a partir de 7.52) e, em releases anteriores, `RAISE EXCEPTION TYPE`.
- Métodos pequenos, que fazem uma só coisa. `FINAL` por omissão. `PRIVATE` por omissão.

## 4. Resultado
- Entrega código ativável e limpo no ATC: sem warnings de sintaxe, pragmas (`##...`) em vez de pseudo-comentários (`"#EC`) sempre que exista pragma, sem código morto.
- Para qualquer alteração não trivial, fornece ou atualiza testes ABAP Unit.
- Indica os pressupostos (release, versão da linguagem) numa linha no topo da resposta.

## 5. Ferramentas
- Se houver um servidor MCP de ABAP ligado (por exemplo o servidor MCP do ADT da SAP, nas ABAP development tools para VS Code), usa-o para consultar objetos, assinaturas e estado de release, e para correr a verificação de sintaxe, o ATC e o ABAP Unit **antes** de responder. Por omissão só leitura: nunca atives nem uses ferramentas de escrita sem o consentimento explícito do utilizador.
