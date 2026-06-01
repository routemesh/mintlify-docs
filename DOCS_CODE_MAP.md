# Docs ↔ Codebase map

Use this map to verify that **mintlify-docs** stays in sync with the **routemesh** monorepo. Each doc area lists the code paths that define or implement the behavior the docs describe.

**Monorepo layout (relative to repo root):**
- `atlas/` — Router (RPC handling, cooldowns, routing, batch ID, error codes)
- `sentinel/` — Data quality (replay checks, lag checks, staging, disqualification)
- `front-end/` — Dashboard, provider portal, consumer app, URLs, nav
- `mintlify-docs/` — This documentation site
- `api-reference/openapi.json` — API spec (should match atlas + any gateway that sets headers)

---

## Consumer docs (intro/)

| Doc | What to verify | Code paths |
|-----|----------------|------------|
| **getting-started** | Billing URL, dashboard URL, login URL, RPC base URL, chains URL | `front-end/src/lib/constants/external-links.ts`, `front-end/vercel.json` or env for app URL; atlas route path `/rpc/:chainId/:apiKey` |
| **how-it-works** | Terminology (provider, node, route, pathway); routing steps; data quality mention | `atlas/` routing flow; `intro/data-quality.mdx` link |
| **request-behavior** | Cooldowns, 429 on all cooldown, **-32003** code and message "All nodes on cooldown" | `atlas/common/json-rpc/json-rpc.go` (error codes), `atlas/common/custom-errors/custom-errors.go` (`ErrAllNodesOnCooldown`), `atlas/common/node-processor/http-status-aggregator.go` (when 429/503 trigger cooldown) |
| **rpc-error-codes** | All codes: -32700, -32600, -32602, -32601?, -32000, -32001, -32002, -32003; messages | `atlas/common/json-rpc/json-rpc.go` (switch on custom_errors), `atlas/common/custom-errors/custom-errors.go` |
| **debugging** | `X-Batch-Id` header name; RPC URL pattern `lb.routeme.sh/rpc/{chain_id}/{api_key}`; Logs page URL | Atlas or gateway: where response headers are set (BatchID exists in `atlas/api/rpc/handle-rpc-requests.go`, `atlas/common/models/request-context.go`). Front-end logs: `front-end/src/app/(protected)/app/consumer/logs/` |
| **data-quality** | Sentinel concepts (replay, lag, staging); no need to sync exact thresholds/strike counts (keep docs high-level) | `sentinel/` (replay in `common/replay/`, lag in `common/lag/`, config in `config.yaml`). Consumer doc can stay conceptual. |
| **redundancy** | lb.routeme.sh, lb2.routeme.sh; Cloudflare vs AWS; 15s failover | Infrastructure/config (may live outside repo or in terraform). |
| **network-support** | support@routeme.sh; Lite vs Full support (if referenced) | Front-end or backend chain-support logic. |
| **routing-strategies** | Economy vs performance; strategy names | `atlas/` routing + `front-end` (e.g. strategy filters). |
| **pricing-model** | Credits, pricing page URL | `front-end` billing/pricing; external-links. |
| **updates** | Changelog or update content | Editorial; optional link to updates source. |

---

## Provider docs (provider/)

| Doc | What to verify | Code paths |
|-----|----------------|------------|
| **overview** | Contact (k@routeme.sh); routeme.sh app URL; no software required (URL + pricing) | `front-end/src/lib/constants/external-links.ts` (MAIN_WEBSITE); provider portal routes |
| **dashboard** | Dashboard metrics (requests, market share, active routes, revenue); app URL | `front-end/src/app/(protected)/app/provider/page.tsx`; provider dashboard API |
| **plans** | Portal plans URL: `/app/provider/portal/plans` | `front-end/src/config/navigation.ts` (Plans href), `front-end/src/app/(protected)/app/provider/portal/plans/` |
| **methods** | Portal methods URL: `/app/provider/portal/methods` | `front-end/src/config/navigation.ts` (Methods href), `front-end/src/app/(protected)/app/provider/portal/methods/` |
| **nodes** | Portal nodes URL: `/app/provider/portal/nodes` | `front-end/src/config/navigation.ts` (Nodes href), `front-end/src/app/(protected)/app/provider/portal/nodes/` |
| **inspect** | Inspect URL; replay/lag metrics in UI | `front-end/src/config/navigation.ts` (Inspect href), `front-end/src/app/(protected)/app/provider/inspect/`; `front-end/src/app/api/(protected)/provider/lag-checks/` |
| **market-share** | Market share URL; “paid for all requests” vs “market share = who won” | `front-end/src/app/(protected)/app/provider/market-share/`; billing/analytics logic |
| **data-quality, replay-checks, lag-checks, quality-checks-recovery** | Concepts only; no exact thresholds. Replay = read-only/deterministic methods; lag = freshness | `sentinel/config.yaml` (excluded_methods, etc.) — do **not** copy config into docs; keep provider docs high-level. |

---

## API reference

| Asset | What to verify | Code paths |
|-------|----------------|------------|
| **api-reference/openapi.json** | Server URLs (routeme.sh, lb, lb2); error codes in descriptions; `X-Batch-Id` in responses; path `/rpc/{chain_id}/{api_key}` | `atlas/api/rpc/rpc.go` (route registration); `atlas/common/json-rpc/json-rpc.go` (codes); OpenAPI spec source if generated from code. |

---

## Cross-cutting

| Item | Where it lives | Verify |
|------|----------------|--------|
| **routeme.sh, lb.routeme.sh, lb2.routeme.sh** | Docs + openapi.json + intro (redundancy, getting-started, debugging) | Same hostnames in `front-end/src/lib/constants/external-links.ts` and any env/deploy config. |
| **support@routeme.sh, k@routeme.sh** | docs.json (navbar), provider overview, network-support | docs.json `navbar.links`; provider/overview.mdx; intro/network-support.mdx. |
| **Twitter, LinkedIn, GitHub** | docs.json footer; provider overview | `docs.json` `footer.socials`; `front-end/src/lib/constants/external-links.ts` SOCIAL. |
| **Provider portal nav** | Provider doc links to /app/provider/... | Match `front-end/src/config/navigation.ts` (provider section). |

---

## Quick checks for a cron job

1. **Error codes** — Grep `atlas/common/json-rpc/json-rpc.go` and `atlas/common/custom-errors/custom-errors.go` for -32xxx; ensure `intro/rpc-error-codes.mdx` and `intro/request-behavior.mdx` list the same codes and messages.
2. **Provider app paths** — Grep `front-end/src/config/navigation.ts` for provider hrefs; ensure `provider/*.mdx` and `docs.json` use the same paths (e.g. `/app/provider/inspect`, `/app/provider/portal/plans`).
3. **External URLs** — Grep `routeme.sh` in mintlify-docs and compare to `front-end/src/lib/constants/external-links.ts`.
4. **OpenAPI** — If openapi.json is hand-maintained, diff error code list and server URLs against atlas and gateway behavior.
5. **Broken links** — Run `mint broken-links` in mintlify-docs (see AGENTS.md).
