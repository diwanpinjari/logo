# Mission Control — Minimalist Logo Idea Generator

AI-powered logo studio: describe a brand, generate minimalist concepts (Google Gemini with a built-in studio fallback), save favorites, track checklists, and export SVG/PNG + project reports.

**Stack:** Next.js 16 + React 19 + TypeScript, Tailwind CSS 4, Supabase (Postgres + Auth), Gemini API, Vercel.

## Quick start

```bash
cd minimalist-logo-generator
npm install
cp .env.example .env.local   # fill in keys (see below)
npm run dev                  # http://localhost:3000
```

The app runs **without any keys** in honest demo mode (local persistence + built-in SVG studio). Add keys for production behavior.

## Environment variables

| Var | Where | Required for |
|---|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | client+server | Supabase persistence/auth |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | client+server | Supabase persistence/auth |
| `GEMINI_API_KEY` | **server only** | Gemini copy (`/api/generate`, `/api/insights`) |
| `GEMINI_MODEL` | server only | optional, default `gemini-2.0-flash` |

Get keys:
- **Supabase:** supabase.com → New project → Settings → API → copy URL + `anon` key.
- **Gemini:** [Google AI Studio](https://aistudio.google.com/) → Get API key → set as `GEMINI_API_KEY` (never `NEXT_PUBLIC_`).

## Database

Run `supabase/migrations/001_schema.sql` in Supabase → SQL editor. It creates `profiles`, `projects`, `logo_concepts`, `checklist_tasks`, `activity_records` with foreign keys, indexes, `updated_at` triggers, and **Row Level Security** (each user sees only their own rows).

## Scripts

- `npm run dev` — local dev
- `npm run build` / `npm start` — production build/run
- `npm run lint` — eslint

## Testing checklist (verified)

- [ ] Sign up → login → logout → password reset (Supabase) or demo sign-in (no keys)
- [ ] Dashboard shows real per-user stats, recent projects, recent logos, activity
- [ ] Create/edit/delete project (Projects Wall search, status filter, sort all work)
- [ ] Project page: edit brief sections, checklist add/edit/toggle/delete persists after refresh
- [ ] Generate 4 concepts (with + without `GEMINI_API_KEY`), invalid SVG never renders
- [ ] Favorite, edit/recolor, SVG + PNG export, delete concept
- [ ] Insights endpoint with loading + retry; JSON/CSV report export
- [ ] Two accounts can't see each other's data (RLS + server checks; Switch Student only swaps authorized demo workspaces)
- [ ] Mobile (360px), tablet, desktop layouts; keyboard + focus visible; `prefers-reduced-motion` respected
- [ ] `npm run build` clean, no console errors on happy paths

## Deploy (Vercel)

1. Push to GitHub, import in Vercel.
2. Set the env vars above in Vercel → Settings → Environment Variables.
3. Apply the SQL migration in Supabase production.
4. In Supabase → Authentication → URL config, allow your Vercel domain.
5. Deploy, then smoke-test the checklist above on the live URL.

## Notes

- Gemini returns **text/JSON design suggestions**, not image files — every concept is rendered from validated, allowlisted SVG (`src/lib/svg.ts`). Arbitrary AI markup is sanitized and blocked.
- Demo mode stores data in `localStorage` (`mlg-state-v1`); Supabase mode persists server-side with RLS. "Switch Student" only switches between the three built-in authorized demo profiles — it cannot address arbitrary user IDs.
