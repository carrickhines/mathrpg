# MathRPG

A browser-based math game for two kids. The gameplay is a simple turn-based RPG
combat loop: the player and a foe trade attacks, and each attack must be "earned"
by solving a math problem. Solve correctly (and fast enough) to land your hit;
fail or run out of time and the foe gets a free swing.

## Who it's for

Two players, two skill tracks:

- **Younger son (age 5)** — **Addition track.** Very simple single-digit
  addition (e.g. `3 + 4`). Sums should stay small and approachable.
- **Older son (age 8)** — **Multiplication track.** Times-table memorization
  (e.g. `7 × 8`), focused on recall of the standard multiplication tables.

The math track is chosen at the start of a game, so each kid plays the version
suited to them.

## Core gameplay loop

1. A foe appears. Player and foe each have HP.
2. To attack, the player is shown a math problem.
3. The player types the answer.
4. **Correct + in time** → player's attack lands, foe loses HP.
5. **Wrong or out of time** → the attack misses (or the foe counterattacks and
   the player loses HP).
6. Repeat until either the foe's HP or the player's HP hits zero.
7. Win → next foe / victory screen. Lose → game over.

Keep the RPG framing light and fun (simple foes, hits, HP bars) — the point is
to make drilling math feel like a game, not a worksheet.

## Number blocks (addition track)

To help the younger son, the addition track shows each operand as a stack of
counted, colored blocks — **Numberblocks**-style, and meant to mirror the
physical math blocks he has at home:

- Each operand renders as its own tower of plain colored blocks (no numbers
  printed on them — the child counts the physical blocks themselves).
- Each whole number has its own color (1 = red, 2 = orange, 3 = yellow, …),
  matching the Numberblocks convention. The blocks are deliberately big and
  easy to tap.
- Each tower is a CSS grid that fills top-to-bottom, ~3 blocks per column, then
  wraps into a new column (so tall towers stay short rather than overflowing).
- The towers are **draggable**. The child can physically push one tower onto the
  other to combine them, or press the **"Push together"** button.
- On combining, the second tower's blocks move onto the first into a single
  combined tower. **Each operand keeps its own color**, so the two addends stay
  visually distinct in the merged stack. The game then lights the blocks up one
  at a time (just like counting real blocks) to pace the counting.
- The total is **never shown** — combining counts/highlights the blocks but does
  not reveal the answer. The child counts the combined tower and types the sum.
- Tapping an individual block makes it "pop" so it's easy to count along.

This is a learning aid, not an autosolve: combining helps the child count, but
the player still works out and enters the answer to attack. The blocks area only
appears on the addition track (it's hidden for multiplication).

## Difficulty modes

Three modes, distinguished primarily by **time pressure** on each problem:

- **Easy** — long, relaxed timer. Good for the 5-year-old learning the loop.
- **Normal** — moderate countdown per problem.
- **Expert** — shorter countdown; rewards quicker recall.

The math *content* stays tied to the chosen track (addition vs multiplication);
the modes change how much time the player gets, not the kind of math. (If we
later want harder modes to also widen the number range, document that here when
we add it.)

## Double Attack Timer

Every mode (including Easy) shows a timer bar on each problem. The early part of
the bar is a gold **fast zone**, ending at a white marker line:

- Answer correctly **while still in the fast zone** → a **DOUBLE hit** (double
  damage) with a bigger, flashier attack animation and a "DOUBLE!" callout.
- Answer correctly after the fast zone → a normal single hit.
- This applies to **both tracks** (addition and multiplication) the same way.

Per-mode timing lives in the `MODES` config in `index.html` (`total` = full bar
duration, `fast` = the double-hit window, `penalty` = whether timing out lets the
foe counterattack).

**Easy stays pressure-free:** the timer is shown and the double-hit bonus is
available, but running out of time does **not** let the foe attack
(`penalty: false`). The timer is purely a reward for speed, never a punishment.
Normal and Expert keep the timeout penalty (`penalty: true`). Note: a *wrong*
answer still lets the foe counterattack in every mode.

## Sound

All sound effects are **synthesized at runtime with the Web Audio API** — there
are no audio files to ship, so the game stays a single self-contained HTML that
works from `file://`. The `Sound` module near the top of the script in
`index.html` builds short tones/noise bursts for: attack hit, the SUPER double
hit, taking damage, wrong/too-slow, foe KO, victory, game over, UI clicks, block
tap, push-together, and the per-block count-up beeps.

- A 🔊 **mute toggle** sits in the top-right corner; the preference is saved to
  `localStorage` (`mathrpg_muted`).
- The audio context is created/unlocked on the first user gesture (Start), to
  satisfy browser autoplay rules.

## Technical approach

- **Pure HTML game.** Plain HTML + CSS + JavaScript, no build step and no server.
  It should run by opening an `.html` file directly in a browser (`file://`) or
  by serving the folder statically.
- **No external dependencies / frameworks** unless there's a clear reason —
  prefer vanilla JS so it stays easy to open, read, and tweak.
- Keep it a single self-contained game that's easy for a parent to launch on a
  laptop or tablet.

## Design priorities

- **Kid-friendly UI.** Big text, big buttons/number entry, clear feedback, bright
  and simple visuals. Must be usable by a 5-year-old.
- **Immediate, encouraging feedback.** Celebrate correct answers; make wrong
  answers gentle, not punishing.
- **Fast to start.** Minimal menus: pick track (addition/multiplication), pick
  mode (easy/normal/expert), play.
- **Readable code.** This is a hackable family project — favor clarity over
  cleverness so it's easy to adjust problem ranges, timers, and visuals.

## Project structure

Currently empty. Suggested starting layout (update as the project grows):

```
index.html      # entry point / menu + game screen
style.css       # (optional) styles, or inline in index.html
game.js         # (optional) game logic, or inline in index.html
```

## Running

Open `index.html` in a web browser. No build or install step.

## Hosting / deployment

The game is hosted on **GitHub Pages** and playable online (handy for loading it
on an iPad without copying files around):

- **Repo:** https://github.com/carrickhines/mathrpg
- **Live URL:** https://carrickhines.github.io/mathrpg/
- **Source:** GitHub Pages serves the `main` branch root (`/`) — so `index.html`
  at the repo root *is* the site.

Because Pages publishes `main`, **pushing to `main` deploys.** There's no build
step; the same self-contained `index.html` that runs from `file://` is what gets
served. After merging a change, `git push origin main` and Pages rebuilds
automatically (usually live within a minute). Keep the game a single
dependency-free HTML file so it works both locally and when served from Pages.
