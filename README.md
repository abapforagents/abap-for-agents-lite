# ABAP for Agents Lite (free, MIT)

Stop Cursor, Claude Code, GitHub Copilot, Windsurf and Codex from writing **wrong ABAP**: invented function modules, syntax from the wrong release, unescaped SQL host variables, and FOR ALL ENTRIES on an empty table.

This free edition contains 5 rules and 1 skill, in English (`en/`) and Portuguese (`pt/`):
- `00-core`: establish release and language version, never invent SAP objects, Clean ABAP defaults, safe use of the ADT MCP server
- `10-release-syntax`: which syntax exists in which release (7.40 SP02 ... 7.55)
- `20-abap-sql`: strict mode, `@` escaping, FOR ALL ENTRIES trap, performance
- `60-obsolete`: obsolete statements and their replacements
- `90-llm-pitfalls`: self-check of typical AI mistakes in ABAP
- Skill `abap-clean-core-review`: review code for Clean ABAP and clean core level A-D

## Install

### Option 1: `npx skills` (Claude Code, Cursor, Copilot, Codex and other agents)
```bash
npx skills add abapforagents/abap-for-agents-lite
```
Installs two agent skills (English) into your project: `abap-coding-rules` (the 5 rules below) and `abap-clean-core-review`. Add `-g` to install them for all projects. Uses the [skills CLI](https://github.com/vercel-labs/skills).

### Option 2: Claude Code plugin
Inside Claude Code:
```
/plugin marketplace add abapforagents/abap-for-agents-lite
/plugin install abap-for-agents-lite@abap-for-agents
```
Skills are then available as `/abap-for-agents-lite:abap-coding-rules` and `/abap-for-agents-lite:abap-clean-core-review`.

### Option 3: copy the per-tool files (EN or PT)
Copy the files from `en/` (or `pt/`) into the root of your abapGit repo or VS Code ABAP workspace:

| Tool | Copy |
|---|---|
| Cursor | `.cursor/rules/` (not together with `AGENTS.md`, to avoid duplicates) |
| Claude Code | `AGENTS.md`, `CLAUDE.md`, `.claude/skills/` |
| GitHub Copilot | `.github/` |
| Windsurf / Devin Desktop | `.windsurf/` |
| Codex / other agents | `AGENTS.md` |
| ChatGPT / SAP Joule chat | `prompts/` |

Option 3 is the only one with the Portuguese version and with always-on rule files (`AGENTS.md`, Cursor rules, Copilot instructions). Skills from options 1 and 2 load when the agent decides they are relevant.

## Want the full pack?
**ABAP for Agents Rules Pack** adds:
- rules for RAP and CDS view entities, ABAP Cloud and released APIs, clean core levels, Clean ABAP, ABAP Unit with test doubles, and the S/4HANA vs ECC data model
- task skills for creating a RAP BO, refactoring legacy reports and writing ABAP Unit tests
- the generator, so you can add your own house rules

-> **https://abapforagents.com**

---

## PT: ABAP for Agents Lite (grátis, MIT)
Impede o Cursor, o Claude Code, o GitHub Copilot, o Windsurf e o Codex de escreverem **ABAP errado**: módulos de função inventados, sintaxe do release errado, SQL sem `@`, FOR ALL ENTRIES com tabela vazia.

Esta edição gratuita tem 5 regras e 1 skill (em `pt/` e `en/`). Copia o conteúdo de `pt/` para a raiz do teu repositório abapGit ou do workspace ABAP no VS Code, seguindo a tabela acima (opção 3). As opções `npx skills add` e plugin do Claude Code instalam a versão inglesa.

O pack completo acrescenta regras de RAP/CDS, ABAP Cloud e clean core, Clean ABAP, ABAP Unit e S/4HANA vs ECC, skills de tarefas e o gerador: **https://abapforagents.com**

Not affiliated with SAP SE. SAP, ABAP and S/4HANA are trademarks of SAP SE.
