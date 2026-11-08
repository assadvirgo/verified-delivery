# Rule library

Starting non-negotiable rules to offer at setup. Pick only those that fit the project; reword them to its names. Each must be checkable by the reviewer.

## Any project

- **One transaction per business action.** An action that changes several records changes all of them or none.
- **Migrations are additive first** (expand, migrate, contract), and each release stays compatible with the previous one.
- **No secrets** in code, tests, fixtures or logs. Never ask the owner to paste a secret into the conversation; ask them to set it in the secrets store and confirm.
- **Text for users** goes through the translation layer.

## Multi-tenant SaaS

- **Tenant isolation.** Every tenant-owned table has a tenant id; every query filters on it; row-level security (or the platform's equivalent) is enabled. Every new table gets a test proving tenant A cannot read or write tenant B's rows. Any code path that crosses tenants is named in a decision record approved by the owner.
- **Nothing is branded in code.** Product name, logo, colours, fonts and domains come from one brand configuration; one setting rebrands everything.

## Money, stock and balances

- **Money** is integer minor units plus a currency code. Never floating point.
- **Ledgers, not balances.** Stock, cash, credit and similar quantities are append-only entries, and the ledger is the source of truth. A running total may be kept only as a copy updated in the same transaction and rebuildable from the ledger. Corrections are new reversing entries.

## Public or client-facing APIs

- **Idempotent writes.** Every state-changing endpoint requires an idempotency key and returns the original result on a repeat. Exemptions are listed with their reason in the contract.
- **Contract first.** Request and response schemas are written before either side is built and shared by both.
