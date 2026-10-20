---
trigger: always_on
---
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
