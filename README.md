# IELPS — post-correction candidate, 18 August 2026 (evening)

Prepared against *IELPS Response to Anirudha — CEFR Retirement, API Restoration
& New Final Candidate Instruction*, 18 August 2026.

`665fcb3` is superseded and is retained only as intermediate evidence.

---

## 1. Release identity

| | |
|---|---|
| Commit | `64a91d27077433c44615a3bc08a92f87202bed46` |
| Supersedes | `665fcb389986a7dabbbbdd9d5b6c0ca0d97d7229` |
| Branch | `visual-direction-20260815` in `10495109/v0-ielps-platform-h4` |
| Build ID | `Y1wWapdpoU-Vx5W-CrnXk` |
| Source files | 118 tracked (build logs and `.gitignore` excluded) |
| Source inventory | `SOURCE-SHA256-18aug-c.txt` |
| Manifest hash | `395bcfe3b74dcef62dffe564ae22084f5628ac7b93bfb7c4989cc240c4303d2c` |
| Output mode | `standalone` (89 MB) |
| Deploy status | **NOT DEPLOY APPROVED.** Production unchanged. |

---

## 2. Changed files, and the section each answers

| File | Change | § |
|---|---|---|
| `app/access/page.tsx` | **deleted** — standalone CEFR ladder retired | 2 |
| `app/levels/[level]/page.tsx` | **deleted** — all six standalone level pages retired | 2 |
| `components/access/cefr-panel.tsx` | **deleted** — existed only to draw the ladder | 10.3 |
| `components/access/level-row.tsx` | **deleted** — existed only to draw the ladder | 10.3 |
| `components/access/start-level-button.tsx` | **deleted** — existed only on the level pages | 10.3 |
| `components/site-header.tsx` | restored to its approved form; the `away` prop existed only to address the home page from the retired pages | 10.3 |
| `lib/developer-surface.ts` | **deleted** — the switch that hid endpoint labels | 4 |
| `components/app/source-badge.tsx` | restored — `EndpointChip` draws again | 4 |
| `components/pathways-section.tsx` | restored — card strips and the `GET /api/auth/pathways` header chip | 4 |
| `components/adult-flow-section.tsx` | restored — the four step routes | 4 |
| `components/app/panel.tsx` | restored — mini-app panel chips | 4 |
| `components/app/screen-view.tsx` | restored — the Server wiring drawer | 4 |
| `components/lesson-player/lesson-player.tsx` | restored — per-step endpoint strip | 4 |
| `components/level-band.tsx` | reordered to the canonical hierarchy; shows the chosen level's definition | 3 |
| `lib/apps/studio.ts` | three governance panels corrected to the route that really serves them | 4.2 |
| `lib/ielps-data.ts` | Studio upload chip corrected; `GET /api/roster/providers` stopped on and reported | 4.2 |

Six of the seven restorations are **byte-identical** to their approved `2b7966c`
state — they were restored by checking those files out of that commit, not by
re-editing them.

---

## 3. §2 — the seven standalone pages are retired

Repo-wide reference audit before removal. The only references to the ladder and
the six level routes anywhere in the tree were between the five files themselves:

```
app/access/page.tsx:4              imports components/access/cefr-panel
app/levels/[level]/page.tsx:11     imports components/access/start-level-button
app/levels/[level]/page.tsx:89     href="/access/"
app/levels/[level]/page.tsx:121    href={`/levels/${levelSlug(next)}`}
components/access/cefr-panel.tsx:3 imports components/access/level-row
components/access/level-row.tsx:43 href={`/levels/${code}`}
```

Nothing outside those files linked in — no nav item, no home link, no sitemap
entry. After removal, a repo-wide search for `/access`, `/levels/`,
`developer-surface` and `SHOW_ENDPOINT_LABELS` returns nothing.

The build's own route list no longer contains them:

```
┌ ○ /                     ├ ƒ /app/[slug]/[screen]
├ ○ /_not-found           ├ ƒ /app/[slug]/onboarding
├   /app/[slug]           ├ ƒ /app/[slug]/placement
├   /app/[slug]/learner   ├ ○ /certificates/verify
                          └ ○ /preview/lesson-interactions
```

`/access/`, `/levels/a1/`, `/levels/b2/` and `/levels/c2/` all return **404**.

### What was preserved (§2 PRESERVE list)

Every item is still in the product, verified in the rendered page:

- A1–C2 codes — the six change-level chips, and the code in the band.
- Breakthrough / Waystage / Threshold / Vantage / Effective proficiency /
  Mastery — `lib/cefr-levels.ts`, rendered in the band.
- Beginner / Elementary / Intermediate / Upper Intermediate / Advanced /
  Proficiency — same source, rendered as the band heading.
- The six approved definitions — same source; the chosen level's is rendered.
- Real course titles, unit counts and lesson counts — from
  `GET /api/curriculum/deep-catalog`, never typed in and never counted at build
  time. A1 renders "Foundation English — 8 units and 40 lessons".
- The Access Panel level selector/band — unchanged in function.
- "Continue with {LEVEL}" — unchanged, still to `#pathways`.
- Pathway first → level / placement second — unchanged.
- Shared CEFR icons and data — `lib/cefr-levels.ts` is untouched.

`lib/cefr-levels.ts` was **not edited**. It is byte-identical to `2b7966c`.

---

## 4. §3 — the Access Panel selected-level presentation

Rendered, at `/?level=A1`:

```
YOUR CHOSEN LEVEL
A1  Breakthrough
Beginner
Understand and use everyday expressions, introduce yourself and ask simple
questions about personal details. Course: Foundation English — 8 units and
40 lessons. The first lesson is "Language Focus: First Words and Classroom
English".
[ Continue with A1 → ]                       CHANGE LEVEL  A1 A2 B1 B2 C1 C2
```

Code → descriptor → learner-facing name → course title → real counts, in that
order, verified as DOM order and not by eye (`hierarchy order code → descriptor
→ name`, positions 1 → 2 → 3).

- Only the selected level is described. The other five definitions appear
  nowhere on the page — asserted as a check, not an intention.
- The Basic / Independent / Proficient grouping was **not** brought across. No
  grouping label appears in the band.
- The band did not become a catalogue: one level, one definition, and the same
  single primary action it had before.

---

## 5. §4 — the API labels are restored, and checked

All 87 declared `{ method, path }` labels in the shipping tree were matched
against the **266 routes the running backend actually registers**, extracted
from `~/eilps/backend/src/index.js`'s 32 mounts plus each router's own
registrations (`backend-routes-18aug.txt`). Result: **86 matched, 1 stopped on.**

### Three corrected — the endpoint exists, under the route that really serves it

| Was labelled | Now labelled | Evidence |
|---|---|---|
| `GET /api/studio/ai/quota` | `GET /api/studio/ai/governance` | `studio_ai_governance.js` registers exactly one route, `/governance`, and its reply carries a `quotas` block |
| `GET /api/studio/ai/moderation` | `GET /api/studio/ai/governance` | same route; its reply carries a `moderation` block |
| `POST /api/studio/coursebook/projects/:id/uploads` | `.../uploads/pdf` | `coursebook_studio.js` registers GET `.../uploads` (list) and POST `.../uploads/pdf` and `.../uploads/text` (create). There is no plain POST `.../uploads` |

No backend route was added, renamed or duplicated, and no card was removed.
Those three Studio panels previously requested routes that do not exist; they
now request the one that does.

### One stopped on and reported, per §4.2

**`GET /api/roster/providers`** — School pathway card, `lib/ielps-data.ts`.

Searched: all 266 registered routes; `backend/src/roster_provider.js` in full;
a tree-wide grep for `providers` across `backend/src/*.js`.

`index.js:129` mounts `roster_provider.js` at `/api/roster`, and that router
registers only:

```
POST /api/roster/organizations/:id/connections/:provider/exchange
POST /api/roster/organizations/:id/sync/:provider
GET  /api/roster/organizations/:id/connections
```

The provider list itself is a literal array `['google_classroom','clever',
'classlink']` inside the exchange handler and is not exposed by any route.
The nearest real route is `GET /api/roster/organizations/:id/connections`
(parameterised, real, org-scoped).

The card is display-only, so the label makes no request and nothing fails for a
visitor. **It is left exactly as declared.** I have not guessed at a
replacement, removed the card, or created a route. One line changes it the
moment the correct endpoint is confirmed.

### Why route existence had to be read from source

Every unauthenticated GET probe of the 44 distinct chip paths returned **401 or
200 — not one 404**, because `roster_provider.js` and its peers call
`router.use(authRequired)` before any route matches. A 401 from
`/api/roster/providers` therefore proves nothing about whether the route exists.
That is exactly the §4.1 point about what 401/403/402 do and do not prove, so
existence was established from the backend source and the response class
recorded separately (`api-acceptance-18aug.json`).

### §5 security boundary

Restoring a route *name* exposed no data. Every authenticated panel renders
"Sign in to load this account data. Sign in required." — no payload, no
placeholder standing in for real data, no keys, tokens, headers, environment
values, credentials, paths or stack traces. The `Live / Empty / Sign in /
Upgrade required / Unavailable` badge continues to state provenance, so a 200
carrying sample content can never read as Live.

---

## 6. §6 — the watermarked ladder image

Stays deleted. `public/images/learners.jpg` (112,450 bytes) belonged only to the
retired ladder and was referenced by exactly one file, itself now deleted. No
stock, AI or placeholder substitute. No other image was touched.

---

## 7. §7 — Plus Jakarta Sans, checked file by file

At `2b7966c`, immediately before the revocation, the string `Jakarta` appeared
in the entire tree in exactly two files:

```
app/access/page.tsx          lines 2, 22, 24, 37, 38
app/levels/[level]/page.tsx  lines 3, 26, 28, 73, 74
```

Both are now retired. It appeared in no other file, in no stylesheet, in no
token file and in no configuration. No unrelated typography was removed and no
standardisation pass was performed.

---

## 8. §8 — surfaces that must not change

| Surface | State |
|---|---|
| Adult mini-app (My Course, Lesson Player, Discover, Review, Progress) | untouched; chips restored to approved state |
| Public Landing | untouched — see §11 |
| Parent dashboard logic | untouched; renders only server-supported states |
| Keyword truthfulness behaviour | untouched |
| Reading and Writing interaction states | untouched |
| PiP | untouched, and not hovering over any button in any capture |

---

## 9. Functional checks — 28 / 28

Read out of the rendered page, with the real backend behind the candidate.

| | |
|---|---|
| `/access/`, `/levels/a1/`, `/levels/b2/`, `/levels/c2/` gone | 404 × 4 |
| panel home renders | PASS |
| no level band without `?level` | PASS |
| pathway API chips restored | 16 visible route labels |
| `GET /api/auth/pathways` header chip visible | PASS |
| adult flow step routes visible | `/api/progress/lesson` present |
| no Plus Jakarta Sans on any surface | PASS |
| level band present at `?level=A1` | PASS |
| hierarchy order code → descriptor → name | A1@1, Breakthrough@2, Beginner@3 |
| chosen level's definition shown | PASS |
| only the selected level described | PASS |
| course title and real server counts | Foundation English / 8 units / 40 lessons |
| no Basic/Independent/Proficient grouping imported | PASS |
| "Continue with A1" → pathway gateway | `#pathways` |
| six change-level chips, chosen one current | PASS |
| mini-app panel chips restored | `/api/progress`, `/api/curriculum/deep-summary`, `/api/engine/schedule` |
| Server wiring drawer restored | PASS |
| parent dashboard renders with chips | PASS |
| Studio governance names the real route | 3 occurrences |
| no unregistered Studio routes labelled | PASS |
| lesson player step strip | **SIGNED-IN TEST OUTSTANDING** — see below |

TypeScript `0`, ESLint `0`, build `0`.

**The one thing not exercised.** The lesson player's step endpoint strip cannot
be rendered on this account: `/app/adult/learner/` returns *"Upgrade required —
this lesson is behind the trial or membership gate."* Unlocking it would be a
real $3 charge against live Stripe, which §11 excludes. The strip's source is
byte-identical to the approved `2b7966c` version. Recorded as outstanding
rather than claimed.

---

## 10. §16 — no-clutter review with the chips restored

Characters of visible text per 1000px of page height, same measure across all
three candidates.

| Page | `2b7966c` | `665fcb3` | **`64a91d2`** |
|---|---|---|---|
| Panel home | 981 | 876 | **982** |
| Home + level band | 970 | 872 | **985** |
| Parent dashboard | 638 | 510 | **718** |
| Adult mini-app index | — | — | **696** |
| Adult dashboard screen | — | — | **811** |
| Studio governance | — | — | **980** |
| Schools roster | — | — | **474** |

**Density is back up, and that is the instruction working, not a regression.**
`665fcb3` was lighter because the API chips were hidden; §4 restores them, so
the figures return to their approved `2b7966c` level. Home is 982 against 981 —
one character per thousand pixels away from the approved baseline.

The band is +15 over `2b7966c` (985 vs 970): the descriptor moved onto its own
line and the chosen level's definition was added. Both are §3 requirements. In
exchange the ladder page and six level pages are gone entirely.

Reviewed visually at 1280 / 900 / 390 across eight pages. The band reads as four
short lines and one action; the pathway cards carry two chips and a `+n` as they
did when approved; the Studio and Parent panels show one chip under each
heading. Nothing was invented to reduce density, nothing required was deleted to
reduce it, and no accordion, carousel or new card system was introduced.

---

## 11. §9 / §11 — the verification you could not complete

Your web fetch returned 503. Here is the same check from two independent
vantage points, taken today:

```
from outside                          from the server, direct to :4302
https://eilps.com/learner/       200   http://127.0.0.1:4302/            200
https://eilps.com/learner/access/     404   /access/                     404
https://eilps.com/learner/levels/a1/  404   /levels/a1/                  404
https://eilps.com/learner/levels/b2/  404   /levels/b2/                  404
```

And the reason, from the deployed build itself: the live release directory
`releases/ielps-a1-c2-learner-20260805-r4-discovery/out/` contains **no
`access/` directory and no `levels/` directory at all.** Those pages were never
part of what is serving.

**Public Landing.** `~/eilps/frontend/dist` last modified 2026-08-17 12:43;
files changed since midnight today: **0**. Not touched, not rebuilt, not
redeployed.

Nothing in §11 was performed: no backend business-logic change, no database or
schema work, no billing/Trial/Stripe/webhook work, no vocabulary production, no
Studio expansion, no broad typography work. The old EILPS wording and the footer
wordmark remain for the separate pass.

---

## 12. Administrator release pack

**This deployment cannot be done from my account and I have not attempted it.**
The candidate is `output: 'standalone'` and the live service serves a static
`out/` directory, so promoting it requires editing a unit file under
`/etc/systemd/system/` — root only.

### The current unit, verbatim

```ini
# /etc/systemd/system/eilps-learner.service
[Unit]
Description=IELPS immutable A1-C2 learner experience
After=network.target eilps-web.service
Wants=eilps-web.service

[Service]
Type=simple
User=anirudhat
Group=anirudhat
WorkingDirectory=/home/anirudhat/eilps/releases/ielps-a1-c2-learner-20260805-r4-discovery
Environment=NODE_ENV=production
Environment=PORT=4302
Environment=DIST=/home/anirudhat/eilps/releases/ielps-a1-c2-learner-20260805-r4-discovery/out
ExecStart=/usr/bin/node /home/anirudhat/eilps/releases/ielps-a1-c2-learner-20260805-r4-discovery/runtime/learner-server.mjs
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

### The four lines that change

```ini
WorkingDirectory=/home/anirudhat/eilps/releases/ielps-learner-20260818-64a91d2
Environment=PORT=4302
Environment=HOSTNAME=127.0.0.1
ExecStart=/usr/bin/node /home/anirudhat/eilps/releases/ielps-learner-20260818-64a91d2/server.js
```

The `DIST=` line is removed — a standalone server does not read it.

### Steps

1. Place the release at
   `~/eilps/releases/ielps-learner-20260818-64a91d2/` (the contents of
   `.next/standalone`, with `.next/static` and `public/` copied in beside
   `server.js`).
2. Verify against the inventory: `sha256sum -c SOURCE-SHA256-18aug-c.txt`, and
   confirm the manifest hash is
   `395bcfe3b74dcef62dffe564ae22084f5628ac7b93bfb7c4989cc240c4303d2c`.
3. Back the current unit up **outside** `/etc/systemd/system/` (a `.bak` left in
   that directory is still read by systemd).
4. Apply the four lines above.
5. `systemctl daemon-reload && systemctl restart eilps-learner`.

### Health checks, through the addresses a learner really uses

```
https://eilps.com/learner/                        200, panel home
https://eilps.com/learner/?level=A1               200, band reads A1 / Breakthrough / Beginner
https://eilps.com/learner/app/adult               200, mini-app index
https://eilps.com/learner/app/adult/dashboard     200, three endpoint chips
https://eilps.com/learner/app/parents/dashboard   200, Sign in required, no payload
https://eilps.com/learner/access/                 404, retired
```

Checking `127.0.0.1:4302` alone is not sufficient — it bypasses the reverse
proxy, which is the layer most likely to need a matching change.

**Expected interruption:** one restart, a few seconds, on `/learner/` only.
The Public Landing is a different service and is not touched.

### Rollback

Restore the backed-up unit and `systemctl daemon-reload && systemctl restart
eilps-learner`. The previous release directory is left in place and untouched,
so rollback is a unit-file change only — nothing is overwritten or deleted.

---

## 13. Evidence index

| File | What it is |
|---|---|
| `desktop__*.png`, `tablet__*.png`, `mobile__*.png` | eight pages at three viewports |
| `desktop__section-band.png`, `mobile__section-band.png`, `tablet__section-band.png` | the reordered level band |
| `desktop__section-pathways.png` | restored API chips on the pathway cards |
| `density.json` | the no-clutter measurements |
| `functional-18c.json` | all 28 functional checks |
| `chip-audit-18aug.json` | all 87 chips against the backend route table |
| `api-acceptance-18aug.json` | live response class for each GET chip |
| `backend-routes-18aug.txt` | the 266 routes the running backend registers |
| `SOURCE-SHA256-18aug-c.txt` | the 118-file source inventory |
