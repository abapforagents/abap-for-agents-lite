<!-- Repository instructions for GitHub Copilot (SAP ABAP). Topic rules live in `.github/instructions/`. v1.0.1 -->

# ABAP for Agents: core

## 1. Establish the target before writing code
- Find out, or ask once, three things: **(a) language version**: ABAP for Cloud Development (BTP ABAP Environment, S/4HANA Cloud, or a Cloud package on-prem) or Standard ABAP; **(b) product**: S/4HANA or ECC; **(c) release**: SAP_BASIS / ABAP Platform, e.g. 7.50, 7.58, 8.16.
- If you cannot tell, assume the **lowest plausible release** and avoid newer syntax. S/4HANA → SAP_BASIS: 1610 = 7.51, 1709 = 7.52, 1809 = 7.53, 1909 = 7.54, 2020 = 7.55, 2021 = 7.56, 2022 = 7.57, 2023 = 7.58, 2025 = 8.16. ECC 6.0 runs on SAP_BASIS 7.50 at most (EHP8), so it has no 7.51+ syntax and no RAP or CDS view entities.
- Never mix syntax from different releases in one answer.

## 2. Never invent SAP objects
- Do not invent function modules, BAPIs, classes, CDS views, tables, BAdIs, or method signatures. If you are not sure an object exists, say so and tell the user how to verify it: ADT "Open ABAP Development Object", SE37/SE24/SE11, or the Cloudification Repository viewer for release state.
- In ABAP Cloud, use only objects with release state **released** (C1 "Use System-Internally", visible for Cloud Development). Plausible names like `I_*`, `CL_*`, or `BAPI_*` are **not** proof that the object exists or is released.
- When you rely on an SAP API, name it explicitly in your answer so it can be checked.

## 3. Default style (Clean ABAP)
- Prefer classes and interfaces over function modules, FORMs, and reports. Procedural shells (RFC FM, report) only delegate to a class.
- Use modern syntax only where the release allows it: inline declarations, constructor expressions (`NEW`, `VALUE`, `CONV`, `COND`, `CORRESPONDING`), table expressions, string templates.
- **Follow the existing codebase's naming convention**. The Clean ABAP guide says to avoid Hungarian prefixes (`lv_`, `gt_`), but many teams keep them. Never mix both styles in one object.
- Use class-based exceptions, not return codes or `sy-subrc` passing. Use `RAISE EXCEPTION NEW` (from 7.52), otherwise `RAISE EXCEPTION TYPE`.
- Keep methods small and do one thing. `FINAL` by default. `PRIVATE` by default.

## 4. Output
- Produce code that is activatable and ATC-clean: no syntax warnings, pragmas (`##...`) rather than pseudo-comments (`"#EC`) wherever a pragma exists, no dead code.
- For every non-trivial change, also provide or update ABAP Unit tests.
- State assumptions (release, language version) in one line at the top of your answer.

## 5. Tools
- If an ABAP MCP server is connected (for example SAP's ADT MCP server in ABAP development tools for VS Code), use it to look up objects, signatures and release state, and to run the syntax check, ATC and ABAP Unit **before** answering. Read-only by default: never enable or use write tools without the user's explicit consent.

# Self-check before answering (common AI mistakes in ABAP)

1. Did I use an object I am not sure exists? Name it and flag it "verify". Do not guess the parameters of a function module or BAPI: look up the signature (SE37 / ADT) or ask.
2. BAPIs report errors in their `RETURN` table, not via `sy-subrc` or exceptions. Check for message types `E` / `A`. Commit separately (`BAPI_TRANSACTION_COMMIT`), but **never** inside RAP or inside a loop over single records.
3. Did I mix releases (e.g. `@DATA` inline in SQL on 7.40 SP05, `+=` on 7.50, `define view entity` on 7.50)?
4. Did I use Java/C#/JS habits? No `return x;`: statements end with a period, not `;`, and `RETURN expr.` (sets the returning parameter and exits) exists only in functional methods from 7.58 / ABAP Cloud 2211, so check the target release first and below that write `result = x.` then `RETURN.`. Also no `null` (`IS INITIAL` / `IS NOT BOUND`), no `++`, no `!=` (use `<>`), and string literals are `` `...` `` or `'...'`, not `"..."` (in ABAP `"` starts a comment).
5. Every statement ends with a period, and chains `:` are used deliberately. Keywords are not case-sensitive, but follow the project's pretty-printer setting.
6. Did I leave an empty FOR ALL ENTRIES driver unguarded, or a `SELECT` in a loop?
7. Did I catch `CX_ROOT` or swallow exceptions? Catch the specific class, or wrap and re-raise.
8. In ABAP Cloud, did I use `sy-datum`, `WRITE`, a classic FM, or a non-released table?
9. Did I invent a transaction code, a customizing path, or an SAP Note number? Do not cite SAP Notes from memory.
10. Is my answer ATC-clean and covered by a unit test?
