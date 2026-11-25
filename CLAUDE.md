# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The pitch deck for **DormNamo** (a room finder for students near MSU-IIT, Iligan City; live at `dormnamo.ktens.me`), by Team DormNamoPH for CCC100. It is a presentation, not part of the website: one self-contained `index.html` that behaves like a PowerPoint (slides, click builds, speaker notes). It loads nothing from the DormNamo project and the project loads nothing from it.

## Running

No build, no dependencies, no tests. Open `index.html` with VS Code Live Server (or double-click it; `file://` works too). Press **F** for full screen. The only network request is Google Fonts (Bricolage Grotesque + Figtree); offline it falls back to system fonts.

Keys: → / Space / ↓ / Enter / PageDown = next (reveals the next `.step` first), ← / ↑ / Backspace / PageUp = back (hides the last shown step first), Home / End, a number then Enter jumps to that slide, **N** toggles speaker notes, **P** pauses / plays the video on the current slide. Click the right / left half of the screen (links and buttons are ignored), or swipe on touch screens. The cursor hides after 2 s without movement. The address keeps the slide (`#5`), so a reload stays in place and opens that slide fully built. The same key list sits in the comment at the top of `<style>`; keep both in sync.

## Folder

- `index.html`: the whole deck (below).
- `videos/`: the two videos the deck plays.
- `reference/`: source material that isn't loaded by the deck (the earlier pptx).
- Team photos, if added, go next to `index.html` (or set `photo` to a path like `photos/ralph.jpg`).

## Structure (all in `index.html`)

- `<style>`, in numbered sections listed in its header comment: 1 tokens in `:root` (DormNamo's palette: blue `#0a47a9`, ink `#0a2f73`, gold `#ffc83d` with `#5a4100` text, green `#16a34a`) and base, 2 stage and slides, 3 builds, 4 type, 5 shared parts (cards, `.ic` icons, `.tg` switch, `.rules`, `.solo`, `.viz`, `.stat`, `.cells`), 6 one block per slide in deck order (headed with the slide number), 7 presenter chrome, 8 reduced motion. Put new styles in the matching section; when slides move, move their CSS block and renumber its heading. Slide-specific rules come after the shared ones on purpose, because several override shared rules of equal specificity (e.g. `.mx .one` over `.solo > .one`, `.scam .flag` over `.viz .flag`).
- `<main class="stage">`: a fixed **1600 × 900** canvas that `fit()` scales to the window (`translate(-50%, -50%) scale(k)`), so everything is sized in px at that resolution and stays 16:9 on any screen.
- One `<section class="slide" data-notes="…">` per slide, in order, each preceded by a numbered `<!-- N Name -->` comment (renumber them when slides move). `data-notes` is the speaker note shown with N.
- Slides 5 and 6 are full-bleed videos (`.slide.film`), files in `videos/`: 5 is the hook, `videos/DormNamo [BDEE69D].mp4` (1280 × 720, 29 s, with music, plays once; its `src` is URL-encoded because of the space and brackets), 6 is the ad, `videos/Ad-Wide.mp4` (1920 × 1080, 35 s, no audio track, plays muted on a loop). `show()` restarts any `<video>` on the slide you land on and pauses it when you leave. If the browser blocks playing with sound (no key pressed yet, e.g. a reload on slide 5), the slide's `.hint` shows "Press P to play" and hides once the video plays. File names are case-sensitive on a server.
- `reference/Salat! Questions.pptx` is an earlier version of this pitch; its ideas (the three problems, key features, target audience, survey results, "home away from home") are now worked into the deck.
- `<script>` at the end: the `TEAM` array (edit here to fill in the five members: `name`, `role`, `built`, optional `photo` file next to `index.html`). Initials (shown when there's no photo) come from the first and last name, skipping middle initials and "Jr.", the door logo `MARK` (paths copied from DormNamo's `LOGO_MARK`, injected into `#logo1`, `#logo2`, `#cardMark` and `#namoMark`), then the deck logic in one IIFE: `show(n, back)`, `next()`, `back()`, `countUp()`, the key / click / swipe handlers.
- The IIFE collects slides with `querySelectorAll(".slide")` and sets each `.a`'s `--i` once at startup. So don't use the `slide` class for anything that isn't a slide, and inject any generated `.a` content (like the team cards) before the IIFE runs, or it gets no stagger delay.
- The progress bar, counter, jump readout and notes panel (`#progress`, `#count`, `#jump`, `#notes`) sit outside the stage, so they don't scale with it.

## Animations

- **`.a`** on any element inside a slide: it rises in when the slide appears, one after another in document order (`--i` is set by the script, delay `140ms + i × 95ms`). Add `.fade` (no movement) or `.pop` (slight scale) for variants.
- **`.step`**: hidden until → reveals it (click builds, like PowerPoint). Going back to a slide shows it fully built.
- **`.solo` > `.one` cards** (slides 3, 7 and 8): one card at a time in the same spot. The first shows with the slide; the rest are `.step`s, and each → slides the next one in while the earlier one slides out (CSS `:has(~ .shown)`). Their `.viz` sample pictures build in once their card shows (parts marked `.in` are staggered by position); the pictures are labeled as illustrations on the slide.
- **`.mark`**: a gold underline sweeps under the words after the headline lands.
- **`data-count="N"`**: the number counts up from 0 (`countUp`) when the slide appears, and again when a `.step` holding it is revealed.
- **`<div class="cells" data-fill="2" data-of="13">`**: a block chart, one block per place or person (the script builds the `<i>` blocks and sets `--of`, which sets the column count). They rise in left to right, then the first `data-fill` blocks turn blue around when the count-up lands. Used on the problem slide (of 13) and the validation slide (of 20, of 10).
- Slides glide in from the right going forward and from the left going back (`show()` places the new slide on its starting side with transitions off, forces a reflow, then adds `.on`).
- Keep motion minimal, like the DormNamo site: eased fades and short slides, no bounce, spin or particles. Everything turns off under `prefers-reduced-motion`.

## Adding or changing a slide

Copy an existing `<section class="slide">` with the layout you need (`.grid.g2/.g3/.g4` of `.box` cards, `.rules` centered icon cards with `.ic` line icons, `.solo` one card at a time, `.row` text + visual, `.aud`, `.arch`, `.plans`, `.road` with `.chips`, `.team`, `.close`), give its parts `.a` (or `.step` for click builds), and write a `data-notes` line. The counter and progress bar pick up the new slide automatically. Escape anything inserted with `innerHTML` (`esc()`).

## Content rules (same as the DormNamo product)

- **Only numbers we can back up.** The problem stats come from the team's dataset of the 13 Google Maps places near MSU-IIT (DormNamo's `shared/js/data/data.js`): 1 shows rent, 2 list amenities, 0 verified, a typical place has 3 ratings. Prices match the site's `owners.js` (Basic free for 2 places; boosts ₱149 / 7 days, ₱399 / 30 days, ₱1,499 semester or ₱1,299 early-bird in 2 parts; Owner Pro ₱549 / month or ₱449 / month yearly = ₱5,388 in 4 parts of ₱1,347; 3 Featured spots per barangay). If those change in the product, change them here.
- The "Why it's hard" slide cites the team's sources as given in the pptx: MSU-IIT Cats housing posts, Inquirer Aug 2022 (Baguio scams), Kleinberg et al. 2022 and RecSys '25 (engagement vs. satisfaction). Don't add specifics those sources weren't cited for.
- Never invent users, revenue, growth, survey results or "cheapest / #1" claims. Example data is labeled (the card's match % says it's for an example student).
- The validation slide shows the team's survey: 18 of 20 non-local students would use or prefer a website (90%), 6 of 10 landladies agreed to pay for online listings (60%). It stays honest that these are small samples and stated intent, not sign-ups or payments, next to what's being tested next. Update it when real results exist (sign-ups, first landowners, first payments).
- The matching slide draws the worked example from `../match-formula.txt` (Uy Pad Residences, default preferences: checklist 13 of 49 points known → 78%; after 10 swipes learning counts 25% with a model fit of 70% → 76%; ring split 58.5 + 17.5). If the formula or weights change, redo these numbers.
- The "Ethical use of data" slide (11) keeps its Protection rule to what the product does: phone numbers private unless shown, and nothing sent to Gemini without the student's yes (`namo-ai.js`). Don't claim data "never leaves the account" or that it's anonymized; with consent, chat text goes to Google Gemini.
- Feature claims must match the product: compare is up to 3 places (`home/browse.js`), learning only after opt-in consent (`shared/js/app/consent.js`, `insights.js`).
- No emoji anywhere; icons are plain text marks (✓ ? →) or the door logo.
- Kickers and team roles are shown as written (no forced capitals); write them in normal case.
- Writing: short, plain sentences in the audience's words. Headlines state the point of the slide.
