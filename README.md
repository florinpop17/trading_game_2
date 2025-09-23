## TradeGame

Fun, fast-paced trading mini-game. Start with $10,000 and try to grow your portfolio within a short timed session by buying low and selling high in a volatile market.

Live site: [tradegame-xi.vercel.app](https://tradegame-xi.vercel.app/)

### What it is

-   A lightweight, browser-based game simulating price action with bull/bear regimes, volatility clustering, and occasional jumps.
-   Beautiful UI with Tailwind and custom canvas candlestick chart.
-   Short sessions designed for shareability and friendly competition.
-   Optional global leaderboard powered by Supabase.

### How to play

1. Click “Start Trading”.
2. Use Buy/Sell buttons to trade as price moves.
3. Try to end the session with the highest possible portfolio value.
4. Submit your score to the leaderboard and challenge friends.

### Features

-   Responsive UI (mobile-first), glassy cards, subtle animations
-   Real-time candlestick chart rendered on `<canvas>`
-   Sound effects for buy/sell
-   Result screen with final value, P/L and return
-   Leaderboard modal (with Supabase integration)

### Tech stack

-   HTML + Vanilla JavaScript
-   Tailwind CSS via CDN
-   Canvas 2D for charting
-   [Supabase JS](https://supabase.com) (optional) for leaderboard

### Local development

No build step is required. Any static server works.

Option A: Open `index.html` directly in a browser.

Option B: Serve locally (recommended for correct font/CORS handling):

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then open `http://localhost:8000` (or the URL printed by your server).

### Supabase leaderboard (optional)

1. Create a Supabase project and copy your `Project URL` and `anon` key.
2. Create the table in SQL editor:

```sql
create table if not exists public.leaderboard_scores (
  id uuid primary key default gen_random_uuid(),
  name text not null check (char_length(name) <= 30),
  portfolio numeric(12,2) not null check (portfolio >= 0),
  return_pct numeric(6,2) not null,
  created_at timestamptz not null default now()
);

alter table public.leaderboard_scores enable row level security;

create policy "Allow read to anon"
on public.leaderboard_scores for select
to anon
using (true);

create policy "Allow insert to anon"
on public.leaderboard_scores for insert
to anon
with check (
  portfolio >= 0
  and char_length(name) <= 30
  and return_pct between -1000 and 10000
);
```

3. In `index.html`, ensure the Supabase client CDN is included. Configure credentials (either assign to `window.*` or directly set constants in the script):

```html
<script>
    window.SUPABASE_URL = "https://YOUR-PROJECT.supabase.co";
    window.SUPABASE_ANON_KEY = "YOUR_PUBLIC_ANON_KEY";
    window.LEADERBOARD_TABLE = "leaderboard_scores";
</script>
```

4. Submit a score at the end screen and open the leaderboard to see live data.

### Contributing

PRs welcome! Ideas: difficulty modes, session lengths, UI polish, anti-spam/rate limiting for leaderboard, social share cards.

### License

MIT
