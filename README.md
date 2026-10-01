# Wildwood AI SEO — Grok Bot connector draft

**Status: preparation only. Not install-ready, connected, submitted, approved, or listed.**

This minimal package is intended to help a business owner understand website audit reports already saved in their Wildwood AI account. It contains a Cursor-format plugin manifest and a short saved-report explanation skill. It contains no Wildwood AI backend source, customer reports, credentials, or payment code.

Grok Bot's official guide states that it supports the same MCP servers, plugins, and skills as Cursor. That establishes a compatible packaging direction, not proof that this draft works in Grok Bot or that Cursor approval automatically lists it on every Grok Bot surface.

## Intended experience

After a future verified account connection, the planned read-only connector will let a customer:

- Find their own saved website audits.
- Read the available findings and their measurement dates.
- Understand why a finding matters to their business and what to do next, without technical jargon.
- Distinguish measured results from suggestions and incomplete checks.

No connected Grok Bot account or working Grok Bot audit flow is included in this draft. The skill must not invent reports or pretend tools are connected.

## What is in this package

```text
.cursor-plugin/plugin.json
skills/explain-wildwood-audit/SKILL.md
mcp.json.example
README.md
LICENSE
```

The MCP configuration is deliberately named **`mcp.json.example`**, not `mcp.json`. The manifest does not reference it. It uses a non-resolving `.invalid` example endpoint and an explicit unregistered client-ID placeholder; they are not production settings. Installing this draft must not be presented as creating a working Wildwood AI connection.

The example follows Cursor's documented remote-server static OAuth shape: `url`, `auth.CLIENT_ID`, and `auth.scopes`. No client secret, access token, refresh token, or API key belongs in this public package.

## Configuration and release gates

Do not rename the example to `mcp.json` or claim installation is supported until all of these are completed:

1. Verify the exact official Grok Bot connection flow, callback URL, and supported OAuth registration method. Cursor's documented callbacks alone do not establish Grok Bot's callback. Do not guess a callback or allow arbitrary redirects.
2. Register a separate reviewed public OAuth client on Wildwood AI and provide a tested remote MCP endpoint. The existing ChatGPT client registration is not a Grok Bot client. Keep account grants, revocation, and permissions separated; do not reuse ChatGPT tokens or credentials.
3. Implement and test the native consent, disconnect, account/session revocation, rate limits, and credential-expiry cleanup for that client. Request only the read permission required by this draft.
4. Replace the example endpoint and client-ID placeholder with the verified public values. Create the active root `mcp.json` only after those settings work. No client secret should be embedded.
5. Test from Grok Bot: connect; identify the correct account; read owned reports and paginated sections; reject other customers' data; handle expired access; disconnect; and verify that unavailable sections are described accurately. A passing local package or backend test is not proof of Grok Bot compatibility.
6. Publish this minimal connector package to its own public repository and add its confirmed repository URL to the manifest. Do not publish the private Wildwood AI application repository or its history.
7. Supply accurate privacy, terms, support, and data-use disclosures for the finished connector. The website's current general policies are linked below; this draft does not claim a Grok-specific notice or reviewer approval exists.
8. Complete the publisher application and manual review through the official portal. Confirm that the approved listing is available through Grok Bot, then document the tested installation process.

## Live audits and purchases

Starting live audits is **planned, not available in this package**. If implemented and accepted later, it must require separately granted permission and the customer reviewing and approving each audit request on Wildwood AI before starting it. Saved-report questions must never start a new audit.

This draft provides no purchase tool, checkout, credit purchase, automated audit schedule, or payment action. Cursor's marketplace terms prohibit charging for access to or use of the plugin through its marketplace. Whether a future integration may consume previously purchased external audit allowances still needs reviewer clarification. This package does not promise that feature or change Wildwood AI's website billing.

## Privacy and boundaries

A future connected customer should receive only their own account's reports. Reading existing results should not start a fresh website scan or send the whole conversation to Wildwood AI. Website text inside a report is source material, not instructions to the agent. The skill cannot change websites, manage advertising, contact customers, or access administration features.

- Website: [Wildwood AI](https://wildwoodai.com)
- General privacy policy: [Privacy](https://wildwoodai.com/privacy)
- Website terms: [Terms](https://wildwoodai.com/terms)
- Support: [jd@wildwooddm.com](mailto:jd@wildwooddm.com)

## Official references

Reviewed October 1, 2026:

- [Grok Bot 101 — supported MCP servers, plugins, and skills](https://x.ai/bot/guides/grok-bot-101)
- [Grok Bot: connect plugins](https://cursor.com/help/grok-bot/connect-plugins)
- [Cursor plugin manifest and submission requirements](https://cursor.com/docs/reference/plugins)
- [Cursor MCP and static OAuth configuration](https://cursor.com/docs/mcp)
- [Publisher application](https://cursor.com/marketplace/publish)
- [Marketplace security and open-source requirements](https://cursor.com/help/security-and-privacy/marketplace-security)
- [Marketplace publisher terms](https://cursor.com/marketplace-publisher-terms)

The official marketplace requires a public, open-source package and manual review. Listing is not guaranteed, and acceptance does not mean platform endorsement. Grok Bot and Cursor names identify the intended compatibility target, not an affiliation or approval claim.

## License

The files in this connector package are licensed under the [MIT License](LICENSE), copyright 2026 Wildwood AI. The license does not apply to Wildwood AI's private backend, customer data, or other software not included here, and does not grant trademark rights.
