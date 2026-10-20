---
description: Review ABAP code for Clean ABAP, ABAP Cloud readiness and clean core level (A-D) and return a prioritized findings list. Use when the user asks for a code review, ATC-style check or clean core assessment.
---
# Task: Clean ABAP + clean core review

Return a table: `# | Severity (Blocker/Major/Minor) | Line/object | Finding | Why | Fix`.

Check, in this order:
1. **Correctness:** unguarded FOR ALL ENTRIES, `SELECT SINGLE` without the full key, missing `sy-subrc` checks after SELECT/READ, unhandled exceptions, `COMMIT WORK` in RAP or in loops, BAPI errors not read from `RETURN`.
2. **Clean core level:** every SAP object used is classified as released (A), classic API (B), internal (C), or not allowed / modification / implicit enhancement (D). Mark the objects whose state you cannot confirm as "verify in Cloudification Repository". Do not guess.
3. **ABAP Cloud readiness** (if the target is cloud): non-released tables or FMs, `sy-datum`, dynpro/list statements, `CALL TRANSACTION`, `EXEC SQL`.
4. **Performance:** SELECT in a loop, `SELECT *`, missing WHERE on large tables, nested loops on standard tables (use sorted/hashed tables or secondary keys).
5. **Clean ABAP:** obsolete statements, return codes instead of exceptions, Boolean parameters, long methods, magic numbers, commented-out code.
6. **Tests:** whether there is ABAP Unit coverage for the changed logic.

End with: the top 3 fixes to do first, and what you could not assess without system access (e.g. ATC run, where-used list).
