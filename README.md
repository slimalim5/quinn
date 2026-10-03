# quinn

quinn plans concrete and asphalt slabs for contractors. Type an address, drag a box over a parking lot or slab on a satellite map, and press Generate. The app outlines the surface, subtracts buildings and islands, and reports net square footage. For concrete it also lays out control joints. Built for the Supabase Select 2026 hackathon (2026-10-03).

- Live demo: PLACEHOLDER, to be filled at submission
- Screenshots: PLACEHOLDER, to be filled at submission

## Status

The scaffold is in place: the Next.js and Supabase starter, dependencies, and CI. The product features are being built during the hackathon and land today. This README describes what the app does when built, and will be updated at submission.

## What it does

1. A contractor types an address. The map flies there and drops a default box.
2. They scale or move the box to cover a parking lot or slab.
3. They press Generate. Claude vision outlines the paved surface and the obstacles inside it (buildings, landscape islands). The app reports net square footage: surface minus obstacles.
4. For concrete, a deterministic engine also lays out control joints and reports cut footage and rule warnings.
5. Every edit recalculates the numbers live in the browser: move the boundary, delete an obstacle, slide, add or delete a joint.
6. An estimate can be turned into a deposit through Stripe Checkout (test mode).

Design rules: Claude only detects outlines; it never draws joints. The joint layout is deterministic code. Joint rules produce warnings, not engineering approval.

## Stack

- Next.js (App Router) and TypeScript
- Tailwind and shadcn/ui
- Supabase: Postgres with the OrioleDB storage engine (public beta), PostGIS, Auth, declarative schemas in `supabase/schemas/`
- Mapbox GL and Turf
- Vercel AI SDK calling Claude, through Vercel AI Gateway or the Anthropic API directly
- Stripe Checkout
- Vitest
- Hosted on Vercel

Started from Vercel's `with-supabase` Next.js example.

## Sponsor technology

Locations are planned and may change as features land.

| Sponsor   | Use                                                      | Planned location                     |
| --------- | -------------------------------------------------------- | ------------------------------------ |
| Supabase  | Postgres on OrioleDB, PostGIS, Auth, declarative schemas | `supabase/schemas/`, `lib/plans.ts`  |
| Vercel    | Hosting, AI SDK and AI Gateway                           | `app/api/analyze/`, hosting          |
| Anthropic | Claude vision detection of surface and obstacles         | `lib/ai/`                            |
| Stripe    | Deposit through Checkout (test mode)                     | `app/api/checkout/`, `lib/stripe.ts` |

## Run it locally

Requires Node 22 (the version CI uses).

```bash
git clone https://github.com/slimalim5/quinn.git
cd quinn
npm install
cp .env.example .env.local   # then fill in the values
npm run dev
```

Open http://localhost:3000.

## Environment variables

All go in `.env.local`, copied from `.env.example`. Never commit real keys.

| Variable                               | Required             | Where to get it                                                      | Without it                                                                    |
| -------------------------------------- | -------------------- | -------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `NEXT_PUBLIC_SUPABASE_URL`             | Required             | Supabase dashboard, project settings, API                            | Supabase features and sign-in fail                                            |
| `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` | Required             | Supabase dashboard, project settings, API                            | Supabase features and sign-in fail                                            |
| `NEXT_PUBLIC_MAPBOX_TOKEN`             | Required for the map | Mapbox account, public token (map tiles and static satellite images) | The map shows a message instead                                               |
| `AI_GATEWAY_API_KEY`                   | Optional, preferred  | Vercel dashboard, AI Gateway, API keys                               | Falls back to `ANTHROPIC_API_KEY`                                             |
| `ANTHROPIC_API_KEY`                    | Optional fallback    | Anthropic Console                                                    | With neither AI key, no detection runs and the box itself is used as the slab |
| `STRIPE_SECRET_KEY`                    | Optional             | Stripe dashboard, test mode                                          | The estimate shows and the deposit button is disabled                         |
| `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`   | Optional             | Stripe dashboard, test mode                                          | The estimate shows and the deposit button is disabled                         |

## Commands

| Command             | Purpose                   |
| ------------------- | ------------------------- |
| `npm run dev`       | Start the dev server      |
| `npm run build`     | Production build          |
| `npm run lint`      | ESLint                    |
| `npm run typecheck` | TypeScript check          |
| `npm run test:run`  | Run the Vitest suite once |

The Supabase CLI is a dev dependency. Use `npx supabase ...`, not a global install.

## Project layout

Planned layout; some paths do not exist yet.

| Path                          | Contents                          |
| ----------------------------- | --------------------------------- |
| `lib/geometry/`               | Pure joint engine, unit-tested    |
| `components/map/`             | Map and editing                   |
| `lib/ai/`, `app/api/analyze/` | Claude detection                  |
| `supabase/schemas/`           | Database schema                   |
| `app/plan/`                   | The public page, works signed out |
