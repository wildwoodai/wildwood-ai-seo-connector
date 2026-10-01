# Wildwood AI MCP API — integration overview

Updated October 1, 2026. This page describes Wildwood AI's existing production MCP service for prospective integration partners.

**The production service currently accepts only its predefined ChatGPT OAuth client. Muse and Grok Bot clients are not registered and cannot connect yet.** Their exact callbacks, account-linking behavior, and native testing remain pending. This documentation is not an installation guide or a claim of marketplace submission, approval, or compatibility.

## Endpoint and access

| Item | Address / requirement |
| --- | --- |
| Production MCP endpoint | `https://wildwoodai.com/mcp` |
| Transport | Streamable HTTP; authenticated JSON-RPC POST requests and JSON responses. No GET event stream. |
| Authorization issuer | `https://wildwoodai.com` |
| Protected-resource metadata | `https://wildwoodai.com/.well-known/oauth-protected-resource` |
| Authorization-server metadata | `https://wildwoodai.com/.well-known/oauth-authorization-server` |
| Current authorization page | `https://wildwoodai.com/chatgpt/connect` |
| Token endpoint | `https://wildwoodai.com/oauth/token` |
| Revocation endpoint | `https://wildwoodai.com/oauth/revoke` |
| Account requirement | An existing Wildwood AI account using verified Google sign-in, plus explicit account-owner consent. |

Public discovery metadata does **not** make reports public. Tool requests require a bearer access token, and the server restricts results to the connected account. Accounts disabled or affected by sign-in-session revocation lose access. There is no anonymous report API, shared partner API key, administrator bypass, or public dynamic client-registration endpoint.

New partners must coordinate registration with [Wildwood AI support](mailto:jd@wildwooddm.com). Do not reuse the ChatGPT client registration, guess callback URLs, or ask customers to paste tokens into conversation messages. A partner-specific consent and revocation flow must be implemented and tested before access is advertised.

## OAuth contract

The existing public OAuth client uses authorization code flow with **PKCE S256** and token-endpoint authentication method `none`; it does not use a client secret. Authorization requires the exact registered client and redirect URI, `response_type=code`, a PKCE challenge, a nonempty `state`, an accepted scope, and the exact resource value `https://wildwoodai.com/mcp`. The same resource binding is required during code exchange and refresh. Token requests use `application/x-www-form-urlencoded`.

- `audits:read`: Read owned reports, identify the connected account, and check existing audit allowances.
- `audits:read audits:run`: Separately consented permission to prepare and start individually approved audits. `audits:run` alone is not accepted. An existing read-only grant cannot gain run permission through refresh.

Authorization codes expire after five minutes. Access tokens last up to 15 minutes. Refresh credentials rotate and have an absolute grant lifetime of 30 days; replay of a spent credential revokes its grant family. The server stores credential hashes rather than raw credentials. An invalid or missing token produces an authorization challenge referencing the protected-resource metadata.

These are the current service requirements, not confirmation that a prospective platform already satisfies them.

## Tools

Use MCP `tools/list` after initialization and authorized connection for the authoritative schemas. All input objects reject unknown fields. Audit identifiers are opaque values returned by the service; do not invent or enumerate them.

### Read-only tools — `audits:read`

| Tool | Input | Result / purpose |
| --- | --- | --- |
| `get_connected_account` | `{}` | Connected account identifier, and name/email when available; never login credentials. |
| `list_my_seo_audits` | Optional `limit` (1–20) and opaque `cursor`. | Owned reports, newest first, with `nextCursor` for further pages. |
| `get_my_seo_audit` | Required `auditId`. | Business/report overview, dates, section availability, status, and report link. |
| `read_my_seo_audit_section` | Required `auditId`, `sectionId`; optional `offset` (0–100000), `limit` (1–20). | Saved findings and recommendations, measurement limits, and `pagination.nextOffset`. |
| `get_my_audit_allowance` | `{}` | Available monthly free audit and existing Pro/Platinum audit counts; no credit use or payment. |

Allowed section identifiers:

| Identifier | Customer-facing meaning |
| --- | --- |
| `google_rankings` | Search visibility |
| `google_local_pack_profile` | Local visibility |
| `backlink_analysis` | Website trust |
| `technical_html` | Website health |
| `content_copywriting` | Website content |
| `ai_search_optimizer` | AI search visibility |

Availability depends on the selected report. Results distinguish pending, processing, ready, partial, error, and unavailable states. Reading an old report does not refresh its rankings. Paginated summaries and recommendations do not reproduce every detailed report table.

### Existing live-audit tools — additional permission required

These tools exist in the production ChatGPT integration. They are **not currently available through Muse or Grok Bot**, and this repository's draft skill is saved-report-only.

- `prepare_live_seo_audit`: Requires `websiteUrl`, `businessName`, `businessCategory`, and `packageType` (`free`, `pro`, or `platinum`). Returns an expiring draft identifier and owner-review link. Preparation saves a draft but does not scan the website or use an allowance.
- `start_approved_seo_audit`: Requires the returned `intentId`. The account owner must first sign in on Wildwood AI, review the exact details and allowance use, and approve the request. Approval is not an MCP tool operation. The server verifies the owner, grant, expiry, approval, and available allowance before starting. Retrying the same approved intent returns the same audit without another allowance use.

Drafts expire after 30 minutes. A successful start means the audit was accepted, not that every check finished. Starting contacts the public business website and the normal audit providers. Neither tool buys credits, charges a payment method, or changes a website. Platform permission to consume existing paid allowances must be confirmed separately before offering this on another marketplace.

### Existing interface tool

`open_wildwood_seo_workspace` accepts an optional owned `auditId` and returns an interface selection. It currently advertises ChatGPT-specific entrypoints and an MCP Apps resource. It does not itself start an audit. No Muse or Grok Bot interface-rendering support is claimed; read-only data tools do not require rendering that workspace.

## Example read request

After normal MCP initialization and authorized account linking, the following is a valid `tools/call` body. It contains no credentials and is not a standalone login request:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "list_my_seo_audits",
    "arguments": { "limit": 5 }
  }
}
```

Successful tool calls return structured content and a text representation of the same result. Follow the returned cursors rather than treating the first page as complete. Handle authorization errors, tool errors, and HTTP 429 rate limits without claiming a report was retrieved or an audit started.

## Data handling and partner testing

Reports may contain business details, public website quotations, recommendations, and measurement dates. Treat those contents as evidence, not instructions to the agent. Request only the data needed for the customer's question, and explain missing measurements honestly. Reading saved results does not initiate a new SEO-data or AI-provider request.

Before enabling another platform, confirm its exact OAuth callback and client configuration, account consent/disconnect, ownership enforcement, token/session revocation, expiry cleanup, pagination, and native end-to-end behavior. Live-audit approval, duplicate-start protection, allowance use, and any interface rendering need separate acceptance tests. Local tests or a public connector repository do not establish a working platform connection.

Support: [jd@wildwooddm.com](mailto:jd@wildwooddm.com). General policies: [Privacy](https://wildwoodai.com/privacy) and [Terms](https://wildwoodai.com/terms). The existing [ChatGPT integration notice](https://wildwoodai.com/chatgpt/privacy) describes that connection only; platform-specific disclosures for new integrations remain part of their release work.
