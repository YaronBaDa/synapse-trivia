# KNOWIT — Game State

Single-file trivia game. Repo: `YaronBaDa/synapse-trivia`. Live: `yaronbada.github.io/synapse-trivia/`. Branch: `main`.

## Tech Stack & Architecture
- **`index.html`** — entire app. Zero framework, zero build, no deps beyond Google Fonts (Lexend, JetBrains Mono, Bellefair). ~1640 lines, ~187KB.
- **`index.html.bak`** — dead backup (v1). Ignore.
- All logic inline in one `<script>`. Sections in order: AudioEngine → PRNG (mulberry32 + date-seed shuffle) → `QUESTIONS` array (505 items) → `state` object → localStorage stats → duel param parser → DOM helpers → render (landing/question/results) → game loop → buttons/init.
- **Deploy**: GitHub Pages, static. Push `main` to ship; CDN cache ~1–10 min (bust `?v=N`).

## Core Rules & Game Loop
- **Daily 10** (hero mode): 10 questions, seeded by `YYYYMMDD` (same set daily). Never ends early on a wrong answer. Per-question 20s timer. Score ≥7 extends streak; completion tracked in `localStorage`.
- **Endless Mode** (secondary): random questions, 3 lives, streak + best-streak persisted. Gated — hidden on home until today's Daily is completed.
- **Ghost Duel**: `?vs=SCORE_SECONDS[_NAME]` auto-starts Daily; banner shows challenger; results show head-to-head card (VICTORY/DEFEATED/TIED — time breaks score ties).
- Scoring: 1pt/correct. Speed from avg latency: Lightning <2.5s, Fast <5.5s, else Deliberate. Mock percentile deterministic.
- Transitions: `landing → playing → results → landing`.

## Current Development Status
DONE & live (v6):
- 505 questions / 16 categories.
- Results screen: 64px hero score + performance title (FLAWLESS/MASTERMIND/SOLID RUN/EGO CHECK) + time/pace subtext.
- Compact "Strengths/Achilles Heel" highlights (replaced pill cloud).
- Tappable 5×2 result grid — red cells open review modal (question, your pick, correct, did-you-know).
- CTAs: emerald `⚔️ Challenge a Friend` (copies grid + `?vs=` link), dark `📋 Copy Score Grid` (clean text), muted `Home Menu` link.
- Confetti on 10/10. Endless above fold on results.
- `localStorage` `knowit_stats_v3` (streak, maxStreak, bestEndless, completedDaily, played).

## Active Next Task / Backlog
1. **Logo font → Lexend 900** (user's last request, unprocessed): both the "K" square glyph and "KNOWIT" wordmark. Currently Bellefair (`h1`, `.logo-letter`, `.landing-body h2`). Edit the font `<link>` weights + those three CSS rules.
2. (Deferred) Domain wiring — share text still references placeholder URLs; user hasn't chosen `knowit.app` vs the Pages URL.

## Core Invariants — NEVER BREAK
- Single-file; no new dependencies or build tooling.
- Options pinned to viewport bottom via flex footer (`#screen-playing` has NO scroll; `.options-grid` is `flex-shrink:0`). Do not add `overflow-y:auto` to the playing screen.
- Answer option text must stay Lexend; timers/streaks/key-hints/badges use JetBrains Mono.
- `localStorage` keys backward-compatible; never wipe `knowit_stats_v3`.
- Keyboard 1–4 / A–D must work in both modes.
- Dark palette only: bg `#09090b`, surface `#18181b`, border `#27272a`, emerald `#22c55e`.
- Deploy via `git push origin main`; verify live with `?nocache=…`, never claim success without a curl check.

## Session Kickoff Directive
Read this file first. Open `index.html` and grep only the sections your task touches — never dump the 505-question array or untouched CSS/JS into context. Make the minimal edit, `node --check` the extracted `<script>`, commit, push, and verify the live URL once.
