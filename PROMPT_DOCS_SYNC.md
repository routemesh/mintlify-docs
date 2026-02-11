# Prompt: Verify docs are correct and synced to the codebase

**Use this prompt in a cron job or one-off run so an AI can quickly verify that mintlify-docs matches the routemesh codebase.**

---

## Instructions for the AI

You are verifying that the **mintlify-docs** documentation is correct and in sync with the **routemesh** monorepo (atlas, sentinel, front-end).

1. **Read the doc–code map**  
   Open and use **`mintlify-docs/DOCS_CODE_MAP.md`**. It lists each doc area and the code paths that implement or define the behavior the docs describe.

2. **Run these checks (in order)**  
   - **Error codes**  
     - In `atlas/common/json-rpc/json-rpc.go` and `atlas/common/custom-errors/custom-errors.go`, list every JSON-RPC error code (-32xxx) and its message or error name.  
     - Compare with `mintlify-docs/intro/rpc-error-codes.mdx` and `mintlify-docs/intro/request-behavior.mdx`.  
     - Report any mismatch (missing code, wrong message, or extra doc-only code).
   - **Provider portal paths**  
     - In `front-end/src/config/navigation.ts`, read the provider section (Dashboard, Inspect, Market Share, Plans, Methods, Nodes). Note the href paths (e.g. `getRoute('/inspect')` → `/app/provider/inspect`).  
     - Compare with links in `mintlify-docs/provider/*.mdx` (e.g. `https://routeme.sh/app/provider/inspect`, `https://routeme.sh/app/provider/portal/plans`).  
     - Report any wrong or broken provider app URLs.
   - **External URLs and contacts**  
     - In `front-end/src/lib/constants/external-links.ts`, note MAIN_WEBSITE, DOCUMENTATION, and SOCIAL (Twitter, LinkedIn).  
     - In `mintlify-docs/docs.json`, note navbar primary href, footer socials, and support link.  
     - In `mintlify-docs/provider/overview.mdx` and `mintlify-docs/intro/network-support.mdx`, note k@routeme.sh and support@routeme.sh.  
     - Report any mismatch (e.g. different domain or contact).
   - **OpenAPI vs router**  
     - In `mintlify-docs/api-reference/openapi.json`, note server URLs and any documented error codes in response/description.  
     - Compare server URLs with what’s in the map and with atlas route registration (`atlas/api/rpc/rpc.go`: path `/rpc/:chainId/:apiKey`).  
     - Report if openapi.json is missing a code that atlas returns or documents a code atlas doesn’t use.
   - **Broken internal links**  
     - From repo root, run: `cd mintlify-docs && npx mintlify broken-links` (or `mint broken-links` if mintlify CLI is installed).  
     - Report any broken links and fix or note them.

3. **Output**  
   - Summarize: “Docs sync check: PASS” or “Docs sync check: FAIL”.  
   - List each discrepancy (doc path or file, code path, and what’s wrong).  
   - If you have write access, fix minor doc typos or outdated URLs and report what you changed; do not change code.

4. **Do not**  
   - Change Sentinel config or implementation details in the docs (provider and consumer data-quality docs stay high-level).  
   - Add new error codes to the docs unless they exist in atlas.  
   - Assume gateway or infra that isn’t in the repo; only note “documented but not found in repo” for things like X-Batch-Id if the map says to verify and you can’t find where it’s set.

---

## One-line prompt (for cron / automation)

Copy-paste this as the user message if the runner only supports a single prompt:

```
Using mintlify-docs/DOCS_CODE_MAP.md, verify that mintlify-docs is in sync with the routemesh codebase (atlas, sentinel, front-end). Run: (1) Error codes in atlas vs intro/rpc-error-codes.mdx and request-behavior.mdx, (2) Provider portal paths in front-end/src/config/navigation.ts vs provider/*.mdx links, (3) External URLs and contacts in front-end/src/lib/constants/external-links.ts and docs.json, (4) OpenAPI server URLs and error codes vs atlas, (5) mint broken-links in mintlify-docs. Report PASS/FAIL and list discrepancies; fix only docs if you have write access.
```

---

## Where the map lives

- **Doc–code map:** `mintlify-docs/DOCS_CODE_MAP.md`  
- **This prompt:** `mintlify-docs/PROMPT_DOCS_SYNC.md`  
- **Mintlify config:** `mintlify-docs/docs.json`  
- **Link check:** from `mintlify-docs/`, run `npx mintlify broken-links` or `mint broken-links`.
