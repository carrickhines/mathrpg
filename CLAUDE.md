# MathRPG

A browser-based math game for two kids. The gameplay is a simple turn-based RPG
combat loop: the player and a foe trade attacks, and each attack must be "earned"
by solving a math problem. Solve correctly (and fast enough) to land your hit;
fail or run out of time and the foe gets a free swing.

## Who it's for

Two players, seven skill tracks:

- **Younger son (age 5)** — **Next Number track.** Counting practice: show a
  number from **1 to 30** and ask for the one that comes next (e.g. `17 → ?`,
  answer `18`). The plainest track — no number blocks — meant for a child still
  building number-order fluency before addition clicks.
- **Younger son (age 5)** — **Count On track.** A gentle stepping stone from
  counting to addition: add only **1, 2, or 3** to a starting number 2–9
  (e.g. `8 + 2`). Uses the same push-together number-blocks aid as the addition
  track, so "counting on" is exactly what the blocks show.
- **Younger son (age 5)** — **Addition track.** Very simple single-digit
  addition (e.g. `3 + 4`). Sums should stay small and approachable.
- **Younger son (age 5)** — **Subtraction track.** Single-digit take-away
  (e.g. `7 − 5`), the inverse of addition. The minuend is 2–9 and the result
  is kept **≥ 1**, so there's always something left to count. Uses the same
  number-blocks aid as addition, in "take away" form (see below).
- **Older son (age 8)** — **Multiplication track.** Times-table memorization
  (e.g. `7 × 8`), focused on recall of the standard multiplication tables.
  Both factors range from **2 to 12** (so the 10, 11, and 12 tables are
  included; ×0 and ×1 are skipped).
- **Older son (age 8)** — **Division track.** Clean division with no
  remainder (e.g. `56 ÷ 7`), the inverse of the multiplication tables: the
  dividend is built from two **2 to 12** factors, so it always divides
  evenly and the quotient lands in the same 2–12 range. Reinforces the same
  facts as the times tables, recalled backwards.
- **Older son (turning 9, 3rd grade)** — **Algebra track.** Solve for the
  unknown `x`. Most problems are **one-step across all four operations**
  (`x + 5 = 12`, `x − 4 = 3`, `3 × x = 15`, `x ÷ 2 = 8`); roughly **30%** are
  **two-step** (`2 × x + 1 = 7`, `4 × x − 3 = 9`). Every form resolves to a
  single positive whole-number answer (the value of `x`) so it fits the number
  pad. Challenging but achievable, and the ×/÷ forms reinforce his times tables.
  Generation lives in `makeAlgebra()` in `index.html`.

The math track is chosen at the start of a game, so each kid plays the version
suited to them. The number-blocks aid appears on the addition, subtraction, and
count-on tracks; next-number, multiplication, division, and algebra use the
plain layout (no number blocks).

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

## Number blocks (addition, count-on & subtraction tracks)

To help the younger son, the addition, count-on, and subtraction tracks show the
operands as stacks of counted, colored blocks — **Numberblocks**-style, and meant
to mirror the physical math blocks he has at home (count-on reuses the addition
push-together aid exactly):

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

**Subtraction is the take-away form of the same aid.** It shows a single tower
of the minuend's blocks; the top `b` blocks are marked (dashed/faded) as the
ones to remove. Pressing **"Take N away"** animates those blocks off one at a
time, then lights up the remaining blocks to pace counting. As with addition,
the difference is **never printed** — the child counts what's left and types it.
(Drag-to-merge is addition-only; subtraction uses the button plus tap-to-count.)

This is a learning aid, not an autosolve: combining/removing helps the child
count, but the player still works out and enters the answer to attack. The
blocks area only appears on the addition, count-on, and subtraction tracks (it's
hidden for next-number, multiplication, division, and algebra).

## Difficulty modes

Three modes, distinguished primarily by **time pressure** on each problem:

- **Easy** — long, relaxed timer. Good for the 5-year-old learning the loop.
- **Normal** — moderate countdown per problem.
- **Expert** — shorter countdown; rewards quicker recall.

The math *content* stays tied to the chosen track (any of the seven); the modes
change how much time the player gets, not the kind of math. (If we
later want harder modes to also widen the number range, document that here when
we add it.)

## Double Attack Timer

Every mode (including Easy) shows a timer bar on each problem. The early part of
the bar is a gold **fast zone**, ending at a white marker line:

- Answer correctly **while still in the fast zone** → a **DOUBLE hit** (double
  damage) with a bigger, flashier attack animation and a "DOUBLE!" callout.
- Answer correctly after the fast zone → a normal single hit.
- This applies to **all seven tracks** the same way.

Per-mode timing lives in the `MODES` config in `index.html` (`total` = full bar
duration, `fast` = the double-hit window, `penalty` = whether timing out lets the
foe counterattack).

**Easy stays pressure-free:** the timer is shown and the double-hit bonus is
available, but running out of time does **not** let the foe attack
(`penalty: false`). The timer is purely a reward for speed, never a punishment.
Normal and Expert keep the timeout penalty (`penalty: true`). Note: a *wrong*
answer still lets the foe counterattack in every mode.

## Win reward — iPad game time

Beating every foe (the **Victory** screen, not Game Over) grants a fun,
variable reward: **5–10 minutes of iPad game time** (`rand(5, 10)`, stored as
`state.earnedMins`). The win screen shows the earned minutes and a **"Start the
timer"** button that runs an in-app countdown (`startRewardTimer`). When it
reaches zero (`ringTimer`), the countdown flashes red, shows **"TIME'S UP!"**,
and a **looping two-tone alarm** plays (`Sound.startAlarm` / `stopAlarm`) until
the player taps **"Stop alarm"**. The countdown tracks a wall-clock end time so a
delayed tick can't let the total drift. Navigating away (Play Again / starting a
new game) always calls `clearRewardTimer`, so the alarm never bleeds into the
next screen.

**Known iOS limitation — by design, not a bug:** the in-app timer/alarm only
works while the page is **open and in the foreground with the screen on**. iOS
Safari (and all iOS browsers) freeze JavaScript timers and Web Audio in
backgrounded tabs, and a web page **cannot steal focus or come to the
foreground** — there is no API for it (and no Vibration API on iOS). So the
moment the kid switches to another game, our countdown pauses and the alarm
won't fire on time. That's why the reward screen nudges the player to **keep the
screen open, or ask Siri to set a timer** — the iPad's own Clock/Siri timer is
the only thing that reliably interrupts another app. Don't try to "fix" this
with notifications/PWA push: it needs an installed PWA + permission and still
can't schedule a future alarm without live JS, which breaks the single-file
`file://` design.

## Sound

All sound effects are **synthesized at runtime with the Web Audio API** — there
are no audio files to ship, so the game stays a single self-contained HTML that
works from `file://`. The `Sound` module near the top of the script in
`index.html` builds short tones/noise bursts for: attack hit, the SUPER double
hit, taking damage, wrong/too-slow, foe KO, victory, game over, UI clicks, block
tap, push-together, the per-block count-up beeps, and the looping "time's up"
alarm for the iPad-game-time reward.

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
- **Fast to start.** Minimal menus: pick track (next-number/count-on/addition/
  subtraction/multiplication/division/algebra), pick mode (easy/normal/expert),
  play.
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
