# OpenFin8 - Open Finance Vertical

**Secure Post-Quantum-Safe Consent-First Open Finance Platform That Heals and Scales Itself.**

**Website: [openfin8.dev](https://openfin8.dev/)** - the full documentation:
per-market product pages, the generated API references, the kernel SDK
section, and the security model.

OpenFin8 is the open-finance vertical of the [Sentin8](https://github.com/Sentin8)
platform: the consent authority, recipient registry, and per-jurisdiction
adapters that let an institution share its customers' financial data with
registered third parties under explicit, revocable, field-minimized consent.

The standards moved from open banking to open finance - FDX added payroll
data and targets agentic-AI sharing (US), the UK's Future Entity is chartered
for Open Finance, Brazil's phases cover investments/insurance, the EU's
successor standard *is* the openFinance API Framework, and Australia's CDR is
sector-agnostic by legislation. Banking is OpenFin8's first sector, not its
container: **[Bank8](https://github.com/Bank8-dev) composes the provider
role**, and recipient-only fintechs compose the recipient plane standalone.

> **A note on our history.** If you found your way here looking for
> **PRIME** - `authPrime`, `exchangePRIME`, and family - you're in the
> right place. Those projects are this one: our open-source
> work is now rebranded to **Sentin8** (this vertical is **OpenFin8**) to
> avoid confusion with other brand names and products. At the same
> time we left our self-hosted **Apache SVN** behind and moved
> everything to GitHub. The lineage goes back further than you might
> expect - here's a fun LinkedIn read from 2017, when the platform was
> still called PRIME and still lived on SVN:
> [Advanced Hybrid Securities Exchange](https://www.linkedin.com/pulse/advanced-hybrid-securities-exchange-hamid-jahanzad/).

## Services

| Service | Jurisdiction | Standard | Status |
|---|---|---|---|
| **openfin8-core** | - | - | the kernel library: consent ledger + lifecycle, recipient registry (two-ledger model), CSDF data minimization, HMAC-signed notifications, the runtime [`Profile`](https://github.com/OpenFin8/openfin8-core/blob/main/src/profile.rs) every identity string derives from | 
| **OpenFin8:CA** | Canada | FDX v6.x (consumer-driven banking) | serving - `/fdx/v{major}/*` provider + `/v1/recipient/*` recipient surfaces, version-configurable (`OPENFIN8_CA_VERSION`) |
| **OpenFin8:US** | United States | FDX v6.x | serving - identical surface over the same kernel, US identity |
| **OpenFin8:UK** | United Kingdom | OBL Read/Write API v3.1.x | serving - the intent-based consent model (client-credentials staging + the 24h staged-intent sweep), the FAPI 1 Advanced ceremony + detached JWS signing (fail-closed, OB Directory trust), the `{Data, Links, Meta}` envelope + the OBL error rules, the aisp/pisp/cbpii surfaces (domestic payments, VRP with control-parameter enforcement, funds confirmations, event notifications) |
| OpenFin8:BR | Brazil | Open Finance Brasil | designed (research complete) |
| **OpenFin8:EU** | European Union | Berlin Group openFinance | serving - the XS2A consent + authorisation model (the four-state machine + the pre-step), the three SCA approach ceremonies, eIDAS QWAC/QSEALC secret-referenced trust + bank-by-bank DCR, PIS + signing baskets, AIS/PIIS + the honest extended services |
| OpenFin8:AU | Australia | CDR | designed |

## How a bank composes the provider role

1. Enable `openfin8:ca` (or `:us`) in the bank's environment - the platform's
   `sys.services` enablement; the adapter self-registers its provider
   profile at env-init.
2. The adapter consumes the bank's data plane - Ledger and Payments - via
   their **public APIs and the `bank8::` SDK contracts only** (the OBVERT
   invariant: no vertical's database is ever read by another vertical).
3. The bank's customer surfaces (e.g. the ebank consent dashboard) reach the
   adapter through the BFF like any service call.

A **recipient-only fintech** needs no banking services: the two-ledger
recipient registry is per recipient environment, and the connection flows
(consent start, callback, delegated-token custody, revocation) run
standalone.

## Specification licensing

The FDX API specification (US/Canada) is licensed under the FDX API License
Agreement and distributed exclusively by FDX - these repositories **never
publish it**; each adapter's published spec carries OpenFin8-owned routes
only (CI drift-guarded), and customers supply their licensed copy under
gitignored `specs/`. The OBL (UK) specifications are MIT Open Licensed and
may be vendored with the notice.

## Key invariants

- **No consent, no data** - every provider route passes consent middleware
  on top of RLS; revocation stops serving on the next call
- **Terminal states are final** - revoked consents can never be revived by
  introspection
- **No plaintext secrets in rows** - delegated tokens and webhook secrets are
  secret references, resolved at call time
- **Two ledgers** - the provider's grant ledger and the recipient's registry
  live in separate tenants; no cross-tenant SQL ever
- **The OBVERT boundary** - OpenFin8 reads no other vertical's database;
  cross-vertical data flows ride public APIs + SDK contracts only

## Platform

OpenFin8 is composed on the [Sentin8](https://github.com/Sentin8) platform:
Sentin8:Auth (the OAuth ceremonies), Sentin8:Bus (event transport),
Sentin8:Secrets (token custody), Sentin8:Sys (environment enablement),
Sentin8:Storage (statement bytes), Sentin8:Flow (step execution).

---

## Repositories

| Repo | Purpose |
|---|---|
| [`OpenFin8/openfin8-site`](https://github.com/OpenFin8/openfin8-site) | the openfin8.dev website |
| [`OpenFin8/openfin8-core`](https://github.com/OpenFin8/openfin8-core) | the kernel library |
| [`OpenFin8/openfin8-ca`](https://github.com/OpenFin8/openfin8-ca) | the Canadian adapter |
| [`OpenFin8/openfin8-us`](https://github.com/OpenFin8/openfin8-us) | the US adapter |
| [`OpenFin8/openfin8-uk`](https://github.com/OpenFin8/openfin8-uk) | the UK adapter (OB R/W API) |
| [`OpenFin8/openfin8-br`](https://github.com/OpenFin8/openfin8-br) | the Brazil adapter (designed) |
| [`OpenFin8/openfin8-eu`](https://github.com/OpenFin8/openfin8-eu) | the EU adapter (XS2A) |
| [`OpenFin8/openfin8-au`](https://github.com/OpenFin8/openfin8-au) | the Australia adapter (designed) |
| [`OpenFin8/openfin8-eopenca`](https://github.com/OpenFin8/openfin8-eopenca) | eOpenCA - the recipient app (formerly bank8's eFintech) |
| [`OpenFin8/openfin8-e2e`](https://github.com/OpenFin8/openfin8-e2e) | the recipient-journey e2e suite |

---

**Architecture & Design:** OpenFin8 and its underlying concepts were
designed by [Hamid Jahanzad](https://www.linkedin.com/in/hamidjahanzad/).
