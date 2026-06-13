# Paisa Tracker

A personal income and expense tracker for Indian households and farmers, with full ₹ (Indian Rupee) support.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port assigned by workflow)
- `pnpm --filter @workspace/finance-tracker run dev` — run the frontend
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string (auto-provisioned)

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS + shadcn/ui + Recharts
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — API contract (source of truth)
- `lib/db/src/schema/transactions.ts` — transactions table
- `lib/db/src/schema/stocks.ts` — godown stock table
- `artifacts/api-server/src/routes/` — Express route handlers (transactions, stocks, summary)
- `artifacts/finance-tracker/src/pages/` — React pages (Dashboard, Income, Expenses, Stocks, Transactions)
- `artifacts/finance-tracker/src/index.css` — warm earthy theme (HSL tokens)

## Architecture decisions

- All amounts stored as `numeric(12,2)` in PostgreSQL for precision; converted to `Number` in API responses.
- `date` column uses Drizzle's `{ mode: "string" }` to keep YYYY-MM-DD without timezone shifts.
- Summary (cash/bank balances) computed at query time from all transactions — no denormalized balance field.
- Category is NULL for income rows; one of `household|farmland|medical|other` for expense rows.

## Product

- Dashboard with live ₹ balances (Cash in Hand, Bank, Godown), expense pie chart by category, recent transactions
- Income page: add/edit/delete income with destination (cash/bank)
- Expenses page: add/edit/delete expenses with category (Household/Farmland/Medical/Other) and source (cash/bank)
- Stocks page: track crops in godown — name, quantity, unit, estimated ₹ value
- Transactions page: full history with filter by type and category, edit/delete

## User preferences

- Indian Rupees (₹) exclusively — no other currencies
- Expense categories must include: Household, Farmland, Medical, Other
- Account options: Cash in Hand and Bank Account
- Clean, mobile-friendly UI

## Gotchas

- After any OpenAPI spec change, run codegen before using updated hooks: `pnpm --filter @workspace/api-spec run codegen`
- Summary balances can go negative if expenses exceed income — the API clamps `cashBalance`/`bankBalance` to 0 but `netBalance` can be negative.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
