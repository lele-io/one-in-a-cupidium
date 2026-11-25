# One in a Cupidium — notes for Claude
Browser trivia game (rarer valid answers score more) + arcade, casino, dailies, party modes, UCH. Plain HTML/JS, no build.
Relay: separate repo `lele-io/cupidium-relay` (Cloudflare Worker `index.js` + Durable Object `Topic`).

## Rules
- This file loads with every message: keep it short.
- NEVER read `index.html` (~1.9 MB) or `data/answers.js` whole. Grep them; list sections with `grep -n "^// =====" index.html`.
- Run `node tools/check.js` after every edit. Never write the developer passcode into any file.
- Other sessions push to main too: `git pull --rebase` first, push only your own changes.
- After EVERY update add an entry at the top of `updList()` in index.html: next version (5.3 → 5.4…), date, short player-facing bullets.
  Every fix goes in one `"Bug fixes"` section (last); other sections are by area. Title (`t`) only for big updates, otherwise just the version.
- Don't add always-on relay streams or short polling (Durable Object awake time is limited); close streams when hidden/idle.

## Files
`index.html` all code (CSS + 2 scripts) · `data/prompts.js` · `data/answers.js` answer banks · `data/words.js` Cuepon lists ·
`data/decks.js`, `art.js`, `maps.js` · `data/dsgeo.js` Daniel Simons geo prompts · `tools/check.js` · `tools/trmaps.js`, `tools/trsolve.js`.

## Answers
- Tiers `t:[0..4]` (0 common … 4 ONE IN A CUPIDIUM); `|` between answers, `/` between aliases.
- Hand fixes: dated block at the end of script 1 with `put`, `set4`, `rot`, `alias`, `rej`. Tiers are built at load by script 1
  (inspect in Node: run script 1 in a `vm` after the data files, stub `window/document/localStorage/location/navigator`).
- Checked answers (AI/Wikipedia/bank) are tier 2-3, never 4. Autocorrect always asks "did you mean?" (except exact name / Rapid);
  answering NO leads to IT'S REAL.
- Ranking prompts (population/size): keep household names out of tiers 3-4 (see "Nov 20" block); don't apply fame across unrelated prompts.

## UCH (`// ===== ULTIMATE CHICKEN HORSE`, names start `tr`)
Host runs `trHost*` and broadcasts state; every device rebuilds with `trBuild` and runs `trStep`/`trCheck`.
Maps: edit `tools/trmaps.js` only, then `node tools/trmaps.js` and `node tools/trsolve.js` (`START=x,y GOAL=x,y` for one stretch).
New solid tile letters go in BOTH `TR_TILE`/`TR_TCOL` (index.html) and `SOLID` (trmaps.js).

## Arcade
`MGAMES.<id>` objects (`init/update/draw/key/ptr`), listed in `ARCADE_CATS`, icon in `AICON`. Cupidium's symbol is **Ci**.

## Layout (owner is strict)
Pages 640px (wide 980, arcade 700). No empty bands; boxes hug content. Card/button rows centred (add new grids to the
flex-wrap block at the end of the first `<style>`). Empty leaderboards show only their title.

## Browser testing
Playwright in `node_modules`: `chromium.launch({channel:"chrome"})`, open index.html as file://, hide `#nameGate`, call screens
directly, screenshot and LOOK before saying UI is done. BLOCK ALL NETWORK (`page.route` + `page.routeWebSocket`) or the test
shows up as a real player.
