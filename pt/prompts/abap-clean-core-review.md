# Tarefa: revisão Clean ABAP + clean core

Devolve uma tabela: `# | Severidade (Blocker/Major/Minor) | Linha/objeto | Finding | Porquê | Correção`.

Verifica, por esta ordem:
1. **Correção:** FOR ALL ENTRIES sem proteção, `SELECT SINGLE` sem a chave completa, falta de verificação de `sy-subrc` depois de SELECT/READ, exceções não tratadas, `COMMIT WORK` no RAP ou dentro de loops, erros de BAPI não lidos da `RETURN`.
2. **Nível clean core:** classifica cada objeto SAP usado como libertado (A), API clássica (B), interno (C), ou não permitido / modificação / enhancement implícito (D). Marca os objetos cujo estado não consegues confirmar com "verificar no Cloudification Repository". Não adivinhes.
3. **Preparação para ABAP Cloud** (se o alvo for cloud): tabelas ou FMs não libertados, `sy-datum`, instruções de dynpro/lista, `CALL TRANSACTION`, `EXEC SQL`.
4. **Performance:** SELECT em loop, `SELECT *`, falta de WHERE em tabelas grandes, loops aninhados sobre standard tables (usa tabelas sorted/hashed ou chaves secundárias).
5. **Clean ABAP:** instruções obsoletas, códigos de retorno em vez de exceções, parâmetros booleanos, métodos longos, números mágicos, código comentado.
6. **Testes:** se existe cobertura ABAP Unit para a lógica alterada.

Termina com: as 3 correções a fazer primeiro, e o que não conseguiste avaliar sem acesso ao sistema (p. ex. correr o ATC, where-used list).
