# 🪨 📄 ✂️ Rock Paper Scissors

A fully interactive **Rock Paper Scissors** browser game built with vanilla JavaScript, featuring animated computer decision sequences, dual scoreboards with persistent storage, and full keyboard & screen reader accessibility support. Developed as an internship front-end project.

---

## 📸 Demo

> Open `dist/index.html` in any modern browser — no build step, no server, no dependencies.

---

## ✨ Features

- 🎮 **Player vs. Computer** — computer selection is randomised each round via `Math.random()`
- 🏆 **Dual scoreboard** — tracks both **Session** wins (resets on refresh) and **All-Time** wins (persisted across sessions)
- 💾 **Persistent scores** — all-time win counts are saved to `localStorage` and restored on every visit
- 🎬 **Animated countdown sequence** — computer displays a 1 → 2 → 3 countdown with CSS `fadeIn`/`fadeOut` keyframe animations before revealing its pick
- 🖼️ **Visual selection feedback** — the chosen card expands and centres; unchosen cards slide out of view with CSS transitions
- ♿ **Accessibility-first** — skip link, `aria-label` updates on every state change, full keyboard navigation (Tab + Enter), and screen-reader-friendly offscreen headings
- 📱 **Responsive layout** — SASS media query mixins adapt the layout from mobile (375 px) up to wide desktop (800 px+)
- 🔄 **Play Again** — resets the board cleanly without a page reload

---

## 🛠️ Tech Stack

| Layer | Technology | Notes |
|---|---|---|
| Markup | **HTML5** | Semantic sectioning, ARIA roles & labels |
| Styling | **SASS (SCSS)** | 7-1 inspired architecture, custom mixins, keyframe animations |
| Compiled CSS | **CSS3** (minified) | Output to `dist/css/main.min.css` with source map |
| Logic | **JavaScript ES6+** | ES Modules (`import`/`export`), OOP `class`, `localStorage` |
| Fonts | **Google Fonts** — Montserrat 600 | Loaded via `<link rel="preconnect">` for performance |

No frameworks. No libraries. Zero dependencies.

---

## 📁 Project Structure

```
js_rock_paper_scissors/
├── dist/                         # Production-ready output (open this in browser)
│   ├── index.html                # Entry point — semantic HTML with ARIA
│   ├── css/
│   │   ├── main.min.css          # Compiled & minified SASS output
│   │   └── main.min.css.map      # Source map for DevTools debugging
│   ├── js/
│   │   ├── Game.js               # Game class — state, scores, win/loss logic
│   │   └── main.js               # App controller — DOM, events, animations
│   └── img/
│       ├── rock.png
│       ├── paper.png
│       └── scissors.png
└── sass/                         # SASS source files
    ├── main.scss                 # Root import manifest
    ├── abstracts/
    │   ├── _colors.scss          # Global colour variables
    │   └── _mixins.scss          # flexCenter, mq(), mqMinWMaxH() mixins
    ├── base/
    │   └── _base.scss            # CSS reset & base typography
    ├── components/
    │   ├── _a11y.scss            # Skip link & offscreen utility classes
    │   ├── _animations.scss      # fadeInFromNone / fadeOut keyframes
    │   └── _utility.scss         # Shared utility classes
    └── layout/
        ├── _scoreboard.scss      # Dual scoreboard layout
        └── _gameboard.scss       # Game card grid & selection transitions
```

---

## ▶️ Getting Started

### Prerequisites

A modern web browser (Chrome, Firefox, Edge, or Safari). No Node.js or build tools required to **run** the game.

### Running the Game

1. **Clone the repository**
   ```bash
   git clone https://github.com/EminPerenda/js_rock_paper_scissors.git
   cd js_rock_paper_scissors/js_rock_paper_scissors
   ```

2. **Open the game** — simply open `dist/index.html` in your browser:
   ```bash
   # macOS
   open dist/index.html

   # Windows
   start dist/index.html

   # Linux
   xdg-open dist/index.html
   ```

> ⚠️ Because `main.js` uses ES Modules (`type="module"`), opening the file directly via `file://` may be blocked by CORS in some browsers. If that happens, serve it locally:
> ```bash
> # Python (no install needed)
> python -m http.server 8080 --directory dist
> # then visit http://localhost:8080
> ```

---

## 🎮 How to Play

1. The scoreboard displays your **Session** and **All-Time** record against the computer
2. Click (or press `Enter`) on **Rock 🪨**, **Paper 📄**, or **Scissors ✂️**
3. Your choice is highlighted; the other two cards slide away
4. The computer runs an animated **3-step countdown** (1 → 2 → 3) before revealing its pick
5. The result message and updated scores appear automatically
6. Click **Play Again** (or press `Enter` when focused) to reset the board

### Win Conditions

| Player | Computer | Result |
|--------|----------|--------|
| 🪨 Rock | ✂️ Scissors | **Player wins** — *Rock smashes Scissors* |
| ✂️ Scissors | 📄 Paper | **Player wins** — *Scissors cut Paper* |
| 📄 Paper | 🪨 Rock | **Player wins** — *Paper wraps Rock* |
| 🪨📄✂️ Any | Same | **Tie** |
| *(opposite of above)* | | **Computer wins** |

---

## 🔍 Key Concepts & Implementation Highlights

### OOP — `Game` Class (`Game.js`)
The game state is encapsulated in an ES6 class with getter/setter methods. This separates business logic from DOM manipulation entirely.
```js
export default class GameObj {
    constructor() {
        this.active = false;  // prevents double-clicks mid-animation
        this.p1AllTime = 0;
        this.cpAllTime = 0;
        this.p1Session = 0;
        this.cpSession = 0;
    }
    p1Wins()  { this.p1Session += 1; this.p1AllTime += 1; }
    cpWins()  { this.cpSession += 1; this.cpAllTime += 1; }
    // ... getters, setters, startGame(), endGame()
}
```

### Animated Countdown Sequence (`main.js`)
A chain of `setTimeout` calls simulates the computer "thinking" before revealing its choice, providing visual feedback that makes the game feel alive:
```js
const computerAnimationSequence = (playerChoice) => {
    let interval = 1000;
    setTimeout(() => computerChoiceAnimation("cp_rock",     1), interval);
    setTimeout(() => computerChoiceAnimation("cp_paper",    2), interval += 500);
    setTimeout(() => computerChoiceAnimation("cp_scissors", 3), interval += 500);
    setTimeout(() => countdownFade(),                           interval += 750);
    setTimeout(() => { deleteCountdown(); finishGameFlow(playerChoice); }, interval += 1000);
    setTimeout(() => askUserToPlayAgain(),                      interval += 1000);
};
```

### Persistent All-Time Scores via `localStorage`
All-time win counts survive page reloads without any backend:
```js
const initAllTimeData = () => {
    Game.setP1AllTime(parseInt(localStorage.getItem("p1AllTime")) || 0);
    Game.setCpAllTime(parseInt(localStorage.getItem("cpAllTime")) || 0);
};
const updatePersistentData = (winner) => {
    const store = winner === "computer" ? "cpAllTime" : "p1AllTime";
    const score = winner === "computer" ? Game.getCpAllTime() : Game.getP1AllTime();
    localStorage.setItem(store, score);
};
```

### SASS Architecture
Styles follow a modular SASS structure — variables and mixins are defined once in `abstracts/` and consumed across all partials, keeping the codebase DRY and easy to maintain.

### Accessibility (a11y)
- **Skip link** jumps keyboard users directly past the scoreboard to the game
- Every score element has a dynamic `ariaLabel` updated after each round (e.g., *"Player One has 3 all time wins."*)
- Images are keyboard-focusable (`tabindex="0"`) and respond to `Enter` as a click
- `focus()` is programmatically moved to the most relevant element at each stage of the game flow

---

## 📂 SASS Architecture Overview

```
sass/
├── abstracts/    ← variables & mixins (no output)
├── base/         ← reset & global defaults
├── components/   ← reusable: a11y, animations, utilities
└── layout/       ← feature-specific: scoreboard, gameboard
```

---

## 👨‍💻 Author

**Emin Perenda**
GitHub: [@EminPerenda](https://github.com/EminPerenda)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

*Developed during a front-end internship to practise DOM manipulation, OOP patterns, CSS animations, localStorage, and web accessibility.*

