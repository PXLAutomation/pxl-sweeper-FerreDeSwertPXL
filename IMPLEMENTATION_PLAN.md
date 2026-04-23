# PXL Sweeper Implementation Plan

## Overview

PXL Sweeper is a single-screen, client-side web implementation of classic Minesweeper. The project delivers a polished, fair, and accessible puzzle game with guaranteed first-click safety and optional no-guess mode. All logic runs in vanilla JavaScript with minimal dependencies, deployed as a static website.

**Project name:** PXL Sweeper  
**Project type:** Browser-based single-page game (SPA)  
**Goal:** Deliver a clean, responsive Minesweeper game that feels familiar but fairer, with no forced guessing  
**Constraints:**
- Client-side only, no backend required
- Must work on desktop and mobile without excessive scrolling
- Lightweight implementation, vanilla JavaScript preferred
- Grid size configurable but fixed per session
- No additional modes beyond standard and optional no-guess variant

**Existing documents:** `REQUIREMENTS.md` (authoritative), project uses `TODO.md` and `DONE.md` for tracking  
**Deployment target:** Static HTML/CSS/JavaScript host (any static server, GitHub Pages, etc.)  
**Risk tolerance:** Low to medium—core gameplay must be correct and fair; no shipped bugs affecting win/loss logic

---

## Assumptions

1. **Default grid configuration:** 10×10 grid with 10 mines (standard difficulty), unless `REQUIREMENTS.md` is updated.
2. **First click guarantee:** First click always reveals a non-mine tile; if that tile is a 0, recursive reveal is triggered (standard behavior).
3. **Chording behavior:** Clicking a revealed number with exactly the correct number of adjacent flags reveals all adjacent non-flagged tiles. Incorrect chording is ignored (no loss).
4. **No-guess mode:** Optional feature; default mode is standard Minesweeper with random placement.
5. **Mobile interaction:** Tap to reveal, long-press to flag; no explicit mode toggle required.
6. **Build environment:** Node.js with npm; a single `npm test` command runs all checks.
7. **No build step for production:** Plain HTML/CSS/JavaScript (no minification, bundling, or transpilation required initially).
8. **Keyboard input:** Optional enhancement; not a blocker for MVP.
9. **Timer and mine counter:** Optional UI enhancements; not required for MVP.
10. **Accessibility baseline:** Sufficient color contrast, numbers distinguishable without color alone; full WCAG 2.1 AA compliance is aspirational.

---

## Delivery Strategy

**Strategy:** Vertical slices with isolated risk phases.

**Rationale:**  
The project uses thin vertical slices to deliver playable functionality early and integrate incrementally. Core game logic (state machine, win/loss, reveal logic) is built first as a library, then integrated into UI and interaction layers. Risky changes—particularly the random mine placement and first-click fairness guarantee—are isolated into their own phase to allow early validation before UI complexity is added. This approach aligns with low-to-medium risk tolerance and allows review gates at each stage.

**Review cadence:** Small phases (1–3 days of work each) enable rapid feedback loops and catch logic errors early.

---

## Phase List

1. **Phase 1: Project scaffolding and testing infrastructure** — Set up repository, package.json, test environment, and build commands.
2. **Phase 2: Core game logic library** — Implement grid state machine, win/loss rules, and reveal mechanics without UI.
3. **Phase 3: Mine placement and fairness validation** — Implement fair mine distribution, first-click safety, and no-guess mode (if enabled).
4. **Phase 4: Interactive UI layer and input handling** — Build HTML, CSS, and input event handlers (mouse, touch, keyboard).
5. **Phase 5: Visual feedback and state reflection** — Connect game state to UI updates, tile rendering, counters, and status display.
6. **Phase 6: Mobile responsiveness and accessibility** — Ensure responsive layout, touch targets, contrast, and keyboard navigation.
7. **Phase 7: Optional enhancements** — Timer, mine counter, animations, sound (if time permits).
8. **Phase 8: Integration testing and final review** — End-to-end tests, manual testing, performance checks, and handoff.

---

## Detailed Phases

### Phase 1: Project Scaffolding and Testing Infrastructure

**Goal**  
Establish a buildable, testable repository with npm scripts, a test framework, and linting configured.

**Scope**  
- Create `package.json` with `type: "module"` for ES modules
- Add test script (`npm test`) running Jest or Vitest
- Add lint script (`npm run lint`) and prettier config
- Create minimal `.gitignore`
- Set up directory structure: `src/`, `test/`, `public/`
- Create `index.html` stub with script includes
- Verify that `npm test` and `npm run lint` run without errors

**Expected files to change**
- `package.json` (create)
- `package-lock.json` (create)
- `.gitignore` (create/update)
- `.prettierrc` or `prettier.config.js` (create)
- `.eslintrc.json` or `.eslintrc.cjs` (create)
- `public/index.html` (create stub)
- `src/` directory (create)
- `test/` directory (create)

**Dependencies**  
None—this is the foundation.

**Risks**  
Low. Standard Node.js setup; no novel tooling.

**Tests and checks to run**  
- `npm install` succeeds
- `npm test` runs without errors (no tests yet, should pass)
- `npm run lint` runs and reports no errors in empty skeleton
- `npm run build` or equivalent build command (if any) succeeds

**Review check before moving work to `DONE.md`**  
- Verify `package.json` has `type: "module"` and all scripts are present.
- Confirm test runner is installed and runs.
- Confirm linter is installed and runs.
- Check that directory structure is created and git ignores build artifacts.
- Verify no untracked files that should be ignored.

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] Set up package.json with test and lint scripts
- [ ] Install and configure Jest or Vitest
- [ ] Install and configure ESLint and Prettier
- [ ] Create directory structure (src/, test/, public/)
- [ ] Create index.html stub with script includes
- [ ] Verify npm test and npm run lint run without errors

**Exit criteria for moving items to `DONE.md`**  
- `package.json` contains `"type": "module"` and all required scripts.
- `npm install` completes without warnings.
- `npm test` runs and produces a test report (even if no tests exist yet).
- `npm run lint` runs and reports no errors.
- `public/index.html` exists with proper script includes.
- `.gitignore` excludes `node_modules/`, dist, and build artifacts.
- All scaffolding files are committed to git.

---

### Phase 2: Core Game Logic Library

**Goal**  
Implement a pure game state library with win/loss rules, reveal mechanics, and tile state management—testable without UI.

**Scope**  
- Create `src/Game.js` (or similar) as the core state machine:
  - Constructor accepts grid dimensions and mine count
  - `revealTile(row, col)` method
  - `flagTile(row, col)` method (toggle flag state)
  - `getState()` method returning current grid state
  - `isWon()` and `isLost()` methods
  - `chordTile(row, col)` method for chord reveals
- Implement grid initialization without mines (mines added in Phase 3)
- Implement tile reveal logic:
  - Single-tile reveal returns game state
  - Recursive reveal for 0-tiles (iterative to avoid stack overflow)
  - State transition tracking (hidden → revealed, hidden → flagged)
  - Prevent reveals on flagged tiles
- Implement win condition: all non-mine tiles revealed
- Implement loss condition: mine tile revealed
- Implement chord reveal: reveal adjacent non-flagged tiles if flag count matches adjacent mine count
- Comprehensive unit tests for all methods with 100% code coverage for Phase 2 scope

**Expected files to change**  
- `src/Game.js` (create)
- `test/Game.test.js` (create)
- `src/constants.js` or similar (create, if needed for tile state enums)

**Dependencies**  
Depends on Phase 1 (scaffolding complete).

**Risks**  
Medium. State machine logic must be correct; bugs here propagate through entire game. Risks:
- Incorrect win/loss detection (accidental endless loops or false wins)
- Stack overflow in recursive reveal (must use iterative approach)
- Rapid input causing race conditions in state (must be atomic)

**Tests and checks to run**  
- `npm test -- test/Game.test.js` passes with 100% coverage
- `npm run lint` reports no errors in `src/Game.js`
- Manual verification: create a game, simulate reveal patterns, confirm state is correct

**Review check before moving work to `DONE.md`**  
- Code review confirms state machine is correct:
  - Win/loss logic is accurate
  - No hidden assumptions about mine placement (mines will be added in Phase 3)
  - Recursive reveal is iterative (not recursive to avoid stack overflow)
  - All edge cases (chord, flag, rapid input) are handled
- All tests pass.
- Coverage is ≥95% for implemented code.
- No untracked side effects or I/O in the game logic.
- Logic is independent of UI (can be tested without DOM).

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] Create src/Game.js with grid initialization (no mines yet)
- [ ] Implement revealTile() method with single-tile and recursive reveal
- [ ] Implement flagTile() method
- [ ] Implement chordTile() method for chord reveals
- [ ] Implement isWon() and isLost() methods
- [ ] Write unit tests for all Game methods
- [ ] Verify recursive reveal uses iteration, not recursion
- [ ] Achieve ≥95% code coverage for Game.js

**Exit criteria for moving items to `DONE.md`**  
- `src/Game.js` is complete and has no references to mine placement (mines are null or undefined).
- All methods (`revealTile`, `flagTile`, `chordTile`, `isWon`, `isLost`, `getState`) are implemented and tested.
- `npm test -- test/Game.test.js` passes with ≥95% code coverage.
- Recursive reveal is implemented iteratively (no stack overflow risk).
- Code review confirms correctness of state machine.

---

### Phase 3: Mine Placement and Fairness Validation

**Goal**  
Implement fair, random mine distribution with first-click safety guarantee and optional no-guess mode.

**Scope**  
- Extend `src/Game.js` to accept mine placement:
  - Constructor or separate method to set mines after grid creation
  - `setupMines(count, firstClickRow, firstClickCol)` method:
    - Generates pseudo-random mine distribution
    - Excludes first-click tile and its adjacent cells (if no-guess mode is enabled)
    - Ensures no mines on first-click tile at minimum
- For no-guess mode (Phase 3b, optional):
  - Implement board validity check (is board solvable without guessing?)
  - Regenerate board until valid if needed
  - Add flag to Game constructor: `noGuessMode: false` by default
- Unit tests verifying:
  - First-click tile never contains a mine
  - Mine distribution is random but deterministic (seed support optional)
  - Board regeneration in no-guess mode works
  - Edge cases (0 mines, all mines, 1 mine) are handled

**Expected files to change**  
- `src/Game.js` (extend)
- `src/MineGenerator.js` (create, optional if mine logic is embedded)
- `test/Game.test.js` (extend with mine placement tests)
- `test/MineGenerator.test.js` (create, if separate module)

**Dependencies**  
Depends on Phase 2 (Game state machine complete).

**Risks**  
Medium-high. First-click fairness is a core promise; incorrect mine placement breaks the entire game. Risks:
- First-click tile is not properly excluded
- No-guess mode validation is too strict or too loose (board regenerates forever or allows guesses)
- Random distribution is biased or predictable
- Performance issue if no-guess validation is expensive (infinite loop on regeneration)

**Tests and checks to run**  
- `npm test -- test/Game.test.js test/MineGenerator.test.js` passes
- Manual play-test: Click random tiles on 5 generated boards; confirm no first-click mines
- If no-guess mode enabled: Generate boards in no-guess mode and attempt to solve without guessing (manual spot-check)
- `npm run lint` reports no errors

**Review check before moving work to `DONE.md`**  
- Code review confirms:
  - First-click exclusion is implemented correctly
  - Random number generation is fair (not biased)
  - No-guess mode validation logic is sound (or is deferred to Phase 7)
  - No infinite loops in board regeneration
  - Mine generation is fast enough for human gameplay (typically <100ms)
- All tests pass.
- Manual play-test confirms first-click is always safe on 5+ boards.
- If no-guess mode is included, manual verification that solvability check works (or is explicitly deferred).

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] Implement setupMines() method with first-click exclusion
- [ ] Verify first-click tile never has a mine (unit tests)
- [ ] Test mine distribution is random and unbiased (unit tests)
- [ ] (Optional) Implement no-guess mode with board validity check
- [ ] (Optional) Test no-guess mode generates solvable boards
- [ ] Manual play-test: Generate 5 boards and verify first-click safety
- [ ] Verify mine placement logic handles edge cases (0 mines, max mines)

**Exit criteria for moving items to `DONE.md`**  
- `src/Game.js` includes `setupMines(count, firstClickRow, firstClickCol)` method.
- First-click tile and its neighbors are never assigned mines.
- `npm test` passes all mine-related tests with coverage ≥95%.
- Manual play-test confirms first-click safety on 5+ boards.
- If no-guess mode is enabled: solvability check is implemented and tested (or deferred with explicit `TODO.md` entry).

---

### Phase 4: Interactive UI Layer and Input Handling

**Goal**  
Build HTML/CSS grid and wire input event handlers (mouse, touch, keyboard) to game logic.

**Scope**  
- Create `public/index.html` with:
  - Single game screen layout (centered grid, top bar for reset/status)
  - CSS grid layout for tile grid (responsive to grid size)
  - Placeholder for tile div elements
  - Reset button
  - Status display area (e.g., "Playing", "Won", "Lost")
- Create `src/UI.js`:
  - `initializeUI(game, config)` method to render grid tiles
  - Tile click handler (left-click → reveal, right-click → flag)
  - Touch handlers for mobile (tap → reveal, long-press → flag)
  - Optional: keyboard input (arrow keys + Enter/Space)
  - Reset button handler to reinitialize game
- Create `public/styles.css`:
  - Clear tile styling (hidden, revealed, flagged, mine, number states)
  - Responsive layout for mobile and desktop
  - Minimal animations (optional, <150ms)
  - High contrast between states
- Create `src/main.js` to orchestrate Game + UI initialization
- Basic manual testing: click tiles, verify UI responds

**Expected files to change**  
- `public/index.html` (extend with grid structure)
- `public/styles.css` (create)
- `src/UI.js` (create)
- `src/main.js` (create)
- `src/Game.js` (no logic changes, only organizational)

**Dependencies**  
Depends on Phase 3 (Game logic complete) and Phase 1 (scaffolding).

**Risks**  
Low to medium. UI integration risks:
- Event handler conflicts (rapid clicks, touch/mouse overlap on desktop)
- Responsive layout breaks on certain screen sizes
- Touch long-press is inconsistent across browsers
- Visual feedback delays cause perceived input lag

**Tests and checks to run**  
- `npm run lint` reports no errors in UI code
- Manual testing: click tiles, long-press to flag, verify UI updates match game state
- Manual testing on mobile (or mobile emulator in browser dev tools): tap and long-press work
- Verify layout doesn't require scrolling at common screen sizes (1920×1080, 1024×768, 375×667)
- No console errors in browser dev tools

**Review check before moving work to `DONE.md`**  
- Code review confirms:
  - Event handlers are correctly wired to game logic
  - No race conditions between UI update and game state
  - Input handlers prevent invalid actions (e.g., reveal on flagged tile)
  - CSS is clean and responsive
- Manual testing confirms:
  - Tiles respond immediately to clicks/taps
  - No perceived input lag
  - Layout is usable on desktop and mobile without scrolling
  - Both mouse and touch work (or one is confirmed as primary with other deferred)
- No console errors or warnings.

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] Create public/index.html with grid layout
- [ ] Create public/styles.css with tile styling and responsive layout
- [ ] Create src/UI.js with tile rendering and event handlers
- [ ] Implement left-click to reveal and right-click to flag
- [ ] Implement touch handlers (tap to reveal, long-press to flag)
- [ ] Implement reset button functionality
- [ ] Verify responsive layout on desktop and mobile screen sizes
- [ ] Manual test: click, flag, reset buttons work and update UI
- [ ] Manual test: no console errors on input interactions

**Exit criteria for moving items to `DONE.md`**  
- `public/index.html` contains a grid structure and top bar for status.
- `public/styles.css` defines tile states with clear visual distinction.
- `src/UI.js` exports functions to render grid and attach event handlers.
- `src/main.js` initializes Game and UI on page load.
- Left-click reveals tiles; right-click flags tiles.
- Touch works on mobile (tap to reveal, long-press to flag).
- Reset button reinitializes the game.
- Layout is responsive and doesn't require scrolling at common screen sizes.
- No console errors during interaction.

---

### Phase 5: Visual Feedback and State Reflection

**Goal**  
Connect game state to live UI updates, render tiles correctly, and display game status.

**Scope**  
- Extend `src/UI.js`:
  - `updateTile(row, col, tileState)` to re-render a single tile based on game state
  - `updateGameStatus()` to display current game state (Playing, Won, Lost)
  - Tile rendering logic:
    - Hidden: show placeholder/default appearance
    - Revealed + 0: show empty/blank
    - Revealed + 1–8: show number with appropriate color/styling
    - Revealed + mine: show mine symbol and end game
    - Flagged: show flag symbol
  - Batch updates after reveal (recursive reveals update multiple tiles efficiently)
- Add `src/StateSync.js` or extend `main.js`:
  - After each game action, sync UI to match game state
  - Disable all input after game over (won/lost)
  - Prevent revealing flagged tiles visually (UI enforces before sending to game)
- Unit tests for tile rendering (mock DOM or use jsdom)
- Manual testing: play a full game to completion (win and loss scenarios)

**Expected files to change**  
- `src/UI.js` (extend with rendering logic)
- `src/main.js` (extend with state sync orchestration)
- `test/UI.test.js` (create, if DOM mocking is feasible)
- `public/styles.css` (refine tile appearance as needed)

**Dependencies**  
Depends on Phase 4 (UI layer complete) and Phase 3 (Game logic complete).

**Risks**  
Low to medium. State sync risks:
- UI and game state drift (one updates, the other doesn't)
- Performance issue if UI updates are too frequent or inefficient
- Tile rendering doesn't match game rules (e.g., flagged tile is revealed)

**Tests and checks to run**  
- `npm test` passes, including UI state sync tests
- Manual play-test: play to win (reveal all non-mine tiles)
- Manual play-test: play to loss (click a mine)
- Manual play-test: flag several tiles and verify flags persist
- Verify tile counts and displays match game logic
- `npm run lint` reports no errors

**Review check before moving work to `DONE.md`**  
- Code review confirms:
  - State sync is complete and covers all game outcomes
  - UI never displays impossible state (e.g., revealed mine on loss)
  - Rendering correctly maps game state enums to visual styles
  - No memory leaks from event handlers or DOM references
- Manual play-test confirms:
  - Win and loss scenarios display correct final state
  - Tiles update correctly after reveals
  - Flags are visible and toggle properly
  - Game ends and disables input after win/loss
- All tests pass.

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] Implement tile rendering logic for all states (hidden, revealed, flagged, mine, numbers)
- [ ] Implement updateGameStatus() to display Playing/Won/Lost
- [ ] Implement batch tile updates after recursive reveals
- [ ] Disable input after game over (won or lost)
- [ ] Prevent UI reveal of flagged tiles
- [ ] Unit test tile rendering with various tile states
- [ ] Manual play-test: play to win scenario
- [ ] Manual play-test: play to loss scenario
- [ ] Verify tile display matches game state after all actions

**Exit criteria for moving items to `DONE.md`**  
- `src/UI.js` includes `updateTile()` and `updateGameStatus()` methods.
- Tiles render correctly for all states (hidden, revealed with number, mine, flagged).
- UI updates after every game action without requiring page refresh.
- Input is disabled after win or loss.
- Manual play-test confirms win and loss scenarios display correctly.
- Flagged tiles cannot be revealed without unflagging first.
- All tests pass; no console errors.

---

### Phase 6: Mobile Responsiveness and Accessibility

**Goal**  
Ensure layout and interaction work seamlessly on mobile, keyboard navigation is functional, and visual contrast meets accessibility standards.

**Scope**  
- CSS refinements:
  - Responsive breakpoints for mobile (<768px), tablet (768–1024px), desktop (>1024px)
  - Tile sizing scales appropriately for touch targets (≥44×44px recommended)
  - No horizontal scrolling at any breakpoint
  - Layout centers grid on screen and adapts to safe areas (notch, etc.)
- Keyboard navigation (optional but recommended):
  - Arrow keys to select tile
  - Enter or Space to reveal selected tile
  - 'F' or another key to toggle flag on selected tile
  - Tab to focus on reset button
- Accessibility checks:
  - Color contrast ratio ≥4.5:1 for all text and UI elements (WCAG AA standard)
  - Numbers distinguishable without relying on color alone (e.g., bold font, symbol)
  - Semantic HTML (`<button>` for clickable elements, `<main>` for game area)
  - ARIA labels if necessary (e.g., `aria-label` for tiles)
  - Keyboard focus visible (outline or high-contrast indicator)
- Manual testing on:
  - iPhone/iOS (Safari) with notch
  - Android phone (Chrome) with system navigation
  - Desktop with keyboard navigation
  - Accessibility checker tool (e.g., axe DevTools, Lighthouse)

**Expected files to change**  
- `public/styles.css` (extend with responsive breakpoints and accessibility styles)
- `public/index.html` (refine semantic structure, add ARIA labels if needed)
- `src/UI.js` (extend with keyboard event handlers)
- `src/main.js` (initialize keyboard handler if implemented)
- `test/UI.test.js` (optional: test keyboard input)

**Dependencies**  
Depends on Phase 5 (UI complete) and Phase 4 (input layer complete).

**Risks**  
Low. Standard responsive design and accessibility practices. Risks:
- Long-press implementation is inconsistent across mobile browsers
- Touch target sizing conflicts with visual design (targets too large)
- Keyboard navigation doesn't cover all actions
- Contrast check fails in unexpected scenarios (e.g., flagged tile background)

**Tests and checks to run**  
- Responsive layout test on 3+ screen sizes (1920×1080 desktop, 768×1024 tablet, 375×667 mobile)
- Manual testing on real iOS and Android devices (or emulators)
- Keyboard navigation test: Tab, arrow keys, Enter/Space all work
- Accessibility checker (e.g., Lighthouse, axe) reports no errors on contrast or structure
- `npm run lint` reports no errors

**Review check before moving work to `DONE.md`**  
- Manual testing confirms:
  - Layout is responsive at 3+ breakpoints
  - No horizontal scrolling at any breakpoint
  - Touch targets are ≥44×44px on mobile
  - Long-press for flag works on iOS and Android
  - Keyboard navigation covers all actions (reveal, flag, reset)
  - Focus indicators are visible
- Accessibility checker confirms:
  - Color contrast ratio ≥4.5:1
  - Semantic HTML is correct
  - No missing ARIA labels
- No console warnings or errors.

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] Add CSS responsive breakpoints for mobile, tablet, desktop
- [ ] Ensure touch targets are ≥44×44px on mobile
- [ ] Implement keyboard navigation (arrow keys, Enter/Space, F for flag)
- [ ] Verify color contrast ≥4.5:1 with accessibility checker
- [ ] Ensure numbers are distinguishable without relying on color alone
- [ ] Add semantic HTML and ARIA labels to index.html
- [ ] Test responsive layout on 375px, 768px, and 1920px widths
- [ ] Manual test on iOS Safari and Android Chrome
- [ ] Manual test keyboard navigation: Tab, arrows, Enter, F key
- [ ] Verify no horizontal scrolling at any screen size
- [ ] Run accessibility checker (Lighthouse, axe) and confirm no errors

**Exit criteria for moving items to `DONE.md`**  
- CSS includes responsive breakpoints for mobile, tablet, and desktop.
- Tile touch targets are ≥44×44px on mobile devices.
- Layout doesn't require horizontal scrolling at 375px, 768px, or 1920px widths.
- Keyboard navigation works: arrows select, Enter/Space reveal, F flags, Tab focuses buttons.
- Color contrast ratio is ≥4.5:1 for all text and UI elements (verified with accessibility checker).
- Numbers are distinguishable without color (bold font, or other visual distinction).
- HTML is semantic (`<button>`, `<main>`, etc.) with appropriate ARIA labels.
- Manual testing on iOS Safari and Android Chrome confirms tap and long-press work.

---

### Phase 7: Optional Enhancements

**Goal**  
Implement polishing features (timer, mine counter, animations, sound) if time and scope allow.

**Scope**  
**This phase is optional and may be deferred or shortened based on review feedback.**

- Mine counter: Display remaining unflagged mines (total mines − flagged count)
- Timer: Elapsed time since game start; stops on win/loss
- Animations:
  - Tile flip animation on reveal (<150ms)
  - Subtle scale/fade for win/loss screen
  - Mine explosion or puff effect on loss (optional, minimal)
- Sound (optional, off by default):
  - Click sound on reveal
  - Flag sound on flag/unflag
  - Win/loss fanfare
  - Mute button to toggle sound
- Optional difficulty presets (if not already in Phase 1 config):
  - Easy: 8×8, 10 mines
  - Medium: 10×10, 30 mines
  - Hard: 12×12, 50 mines
  - Custom: user-input grid size and mine count

**Expected files to change**  
- `public/index.html` (add timer, mine counter, difficulty selector, mute button)
- `public/styles.css` (add animation keyframes and selector styles)
- `src/UI.js` (extend with timer and counter rendering)
- `src/Timer.js` (create, if timer is complex)
- `src/Audio.js` (create, if sound is implemented)
- `src/main.js` (orchestrate new features)
- `test/Timer.test.js` (optional)

**Dependencies**  
Depends on Phase 5 (core UI complete). Phases 4, 5, 6 must be completed before starting this phase.

**Risks**  
Low if features are truly optional and deferred individually. Risks if rushed:
- Animations cause performance issues or perceived input lag
- Sound adds bloat and browser compatibility issues
- Difficulty selector introduces new failure modes (e.g., custom input validation, extreme grid sizes)
- Timer implementation causes memory leaks or stops running

**Tests and checks to run**  
- `npm test` passes (including optional Timer and Audio tests)
- Manual testing: mine counter updates correctly (decrements with flags, increments with flag removal)
- Manual testing: timer ticks every second and stops on game over
- Manual testing: animations don't block interaction (reveal is instant)
- Manual testing: sound mutes and unmutes correctly
- Manual testing: difficulty presets generate correct grid sizes
- `npm run lint` reports no errors
- Performance check: game doesn't lag with new features enabled

**Review check before moving work to `DONE.md`**  
- Code review confirms optional features don't interfere with core game:
  - Animations are <150ms and don't block reveals
  - Sound is off by default
  - Timer doesn't cause memory leaks
  - Difficulty selector validates input and prevents invalid grids
  - No new console warnings
- Manual testing confirms:
  - Mine counter is accurate
  - Timer increments correctly
  - Animations are smooth and don't cause stutter
  - Sound is optional and non-intrusive
  - Difficulty presets work and generate correct sizes
- All tests pass.

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] (Optional) Add mine counter display to top bar
- [ ] (Optional) Implement timer display and increment logic
- [ ] (Optional) Add tile flip animation on reveal
- [ ] (Optional) Add win/loss animation or visual feedback
- [ ] (Optional) Implement sound effects (click, flag, win, loss)
- [ ] (Optional) Add mute button to toggle sound
- [ ] (Optional) Add difficulty preset selector (Easy, Medium, Hard)
- [ ] (Optional) Test mine counter increments/decrements correctly
- [ ] (Optional) Test timer increments and stops on game over
- [ ] (Optional) Test animations don't block reveals
- [ ] (Optional) Test sound mutes/unmutes and plays correctly

**Exit criteria for moving items to `DONE.md`**  
- Mine counter is present and updates correctly (shows mines − flagged).
- Timer displays elapsed time and increments every second.
- Timer stops when game is won or lost.
- Tile flip animation is smooth, <150ms, and doesn't block interaction.
- Win/loss animation or visual feedback is present and non-intrusive.
- Sound is off by default and can be toggled with mute button.
- All sound effects play at appropriate moments without errors.
- Difficulty presets generate correct grid sizes and mine counts.
- Custom difficulty input is validated (prevents invalid grids).
- All tests pass; no console warnings related to enhancements.

---

### Phase 8: Integration Testing and Final Review

**Goal**  
Verify end-to-end gameplay, performance, and readiness for deployment.

**Scope**  
- End-to-end testing:
  - Start a game and play to win on 5+ different boards
  - Start a game and play to loss on 5+ different boards
  - Flag and unflag tiles multiple times
  - Use chord reveal (if implemented)
  - Reset game mid-play
  - Test all difficulty presets (if implemented)
- Performance verification:
  - Reveal latency <16ms (use browser performance timing)
  - No perceived input lag across all interactions
  - Rendering smooth (no frame drops) during reveals
  - Game startup <1 second
  - Memory usage remains stable over 10+ games
- Final verification:
  - All features from `REQUIREMENTS.md` are implemented
  - No console errors or warnings in dev tools
  - `npm test` passes all tests
  - `npm run lint` reports no errors
  - Build process (if any) succeeds
  - Accessibility checker confirms standards met
- Documentation:
  - Verify `README.md` or similar includes game rules and controls
  - Ensure installation/running instructions are clear

**Expected files to change**  
- `test/integration.test.js` or `test/e2e.test.js` (create, if integration tests are scripted)
- `README.md` (create or update with rules, controls, and setup instructions)
- `DONE.md` (prepare to move completed items)

**Dependencies**  
Depends on Phase 7 (all features complete).

**Risks**  
Low. Verification phase should catch only edge cases; core work should be complete. Risks:
- Hidden bugs only visible in full gameplay
- Performance degradation under stress (rapid reveals)
- Accessibility issues missed in earlier phases
- Documentation incomplete or misleading

**Tests and checks to run**  
- `npm test` passes (all tests including integration)
- `npm run lint` reports no errors
- Manual end-to-end test: play 5+ complete games (win and loss scenarios)
- Performance profiling: measure reveal latency and frame rate
- Accessibility checker (Lighthouse, axe) on final build
- Manual testing on desktop and mobile to confirm no regressions
- Verify `README.md` or docs are accurate and complete

**Review check before moving work to `DONE.md`**  
- Final code review confirms:
  - All features from `REQUIREMENTS.md` are present
  - No console errors or warnings
  - Code is clean and maintainable
  - No dead code or debug statements
- Testing confirms:
  - All automated tests pass
  - Manual play-test covers win, loss, and edge cases
  - Performance is acceptable (<16ms reveal latency)
  - Accessibility standards are met
- Documentation confirms:
  - `README.md` includes game rules and controls
  - Installation and running instructions are clear
  - Any optional features are documented
- No blockers for deployment.

**Exact `TODO.md` entries to refresh from this phase**  
- [ ] Manual end-to-end test: play 5+ games to win
- [ ] Manual end-to-end test: play 5+ games to loss
- [ ] Manual end-to-end test: test flag/unflag and chord (if implemented)
- [ ] Manual end-to-end test: test reset mid-game
- [ ] Manual end-to-end test: test all difficulty presets
- [ ] Performance test: measure reveal latency (target <16ms)
- [ ] Performance test: verify no frame drops during reveals
- [ ] Performance test: confirm game startup <1 second
- [ ] Memory profiling: verify no memory leaks over 10+ games
- [ ] Accessibility check with Lighthouse or axe
- [ ] Run `npm test` and verify all tests pass
- [ ] Run `npm run lint` and verify no errors
- [ ] Verify `README.md` is complete with rules and controls
- [ ] Manual test on desktop and mobile for regressions
- [ ] Prepare final handoff or deployment

**Exit criteria for moving items to `DONE.md`**  
- `npm test` passes all unit and integration tests.
- `npm run lint` reports no errors.
- Manual end-to-end tests on 5+ win scenarios and 5+ loss scenarios complete successfully.
- Reveal latency is measured and confirmed <16ms.
- Memory usage is stable over 10+ consecutive games.
- Accessibility checker confirms no errors or warnings.
- `README.md` includes complete rules, controls, and setup instructions.
- No console errors or warnings during full gameplay.
- All features from `REQUIREMENTS.md` are implemented and working.
- No regressions detected in desktop and mobile testing.
- Code is committed and ready for deployment.

---

## Dependency Notes

**Phase ordering:**
1. Phase 1 (scaffolding) is a strict prerequisite for all others.
2. Phase 2 (core logic) must complete before Phase 3 (mine placement).
3. Phase 3 must complete before Phase 4 (UI layer).
4. Phase 4 must complete before Phase 5 (state sync).
5. Phase 5 must complete before Phase 6 (accessibility and responsiveness).
6. Phase 6 must complete before Phase 7 (optional enhancements).
7. Phase 7 must complete before Phase 8 (final testing).

**Parallelizable work within phases:**
- Within Phase 4: HTML layout and CSS styling can be developed in parallel, then integrated.
- Within Phase 6: Responsive CSS breakpoints and keyboard navigation can be developed in parallel.

**No backward dependencies:** Once a phase completes its review gate, earlier phases do not require revisiting unless a bug is discovered.

---

## Review Policy

**Expected review frequency:** After each phase completes, before moving to the next phase.

**Review size guidelines:**
- Phase 1: ~1 hour (scaffolding setup)
- Phase 2: ~2 hours (core logic is critical; thorough review of state machine)
- Phase 3: ~1.5 hours (fairness logic is critical)
- Phase 4: ~1.5 hours (UI integration)
- Phase 5: ~1 hour (state sync verification)
- Phase 6: ~1.5 hours (accessibility and responsiveness verification)
- Phase 7: ~1 hour (optional features, can be shortened or skipped)
- Phase 8: ~2 hours (final end-to-end and integration testing)

**Oversized phase policy:** If any phase review exceeds 2.5 hours of planned review time (excluding rework), the phase must be split before implementation begins. This prevents review bottlenecks.

**Rework policy:** If a phase fails review, the reviewer identifies specific items to address and returns it to in-progress status. The implementer reworks only the flagged items, not the entire phase.

---

## Definition of Done for the Plan

The project is complete when all of the following conditions are met:

1. **All phases are complete and reviewed:** Phases 1–6 are fully completed, reviewed, and merged. Phase 7 is either completed or explicitly marked as deferred/out-of-scope. Phase 8 is completed.

2. **Core gameplay is correct:**
   - Tile reveal, flag, and chord mechanics work as specified in `REQUIREMENTS.md`
   - Win and loss conditions are accurate
   - First-click is always safe (no mine on first click)
   - Game state is never inconsistent

3. **All tests pass:**
   - `npm test` runs successfully and all tests pass
   - `npm run lint` reports no errors
   - Code coverage is ≥90% for all game logic

4. **Performance meets spec:**
   - Reveal latency is <16ms
   - No perceived input lag
   - Game startup <1 second
   - Memory usage is stable

5. **Accessibility and responsiveness verified:**
   - Layout works on desktop (1920×1080), tablet (768×1024), and mobile (375×667)
   - No horizontal scrolling at any breakpoint
   - Touch targets are ≥44×44px on mobile
   - Color contrast ratio is ≥4.5:1
   - Keyboard navigation is functional (if implemented)

6. **Documentation is complete:**
   - `README.md` includes rules, controls, and setup instructions
   - All optional features are documented

7. **Manual testing confirms:**
   - 5+ end-to-end play-throughs to win
   - 5+ end-to-end play-throughs to loss
   - No regressions on desktop or mobile

8. **Code is deployable:**
   - All files are committed to git
   - Build process (if any) succeeds
   - No debug code or console statements
   - Ready for static hosting

---

## Open Questions

**Blocking questions** (must be resolved before implementation can safely proceed):

1. **Default grid configuration:** Is the default 10×10 with 10 mines? Or should it be 8×8 with 10 mines (easier) or 12×12 with 40+ mines (harder)? This affects Phase 1 constants and Phase 3 mine placement.

2. **First-click guarantee scope:** Should the first-click guarantee exclude only the clicked tile, or also adjacent tiles (to guarantee a 0-tile reveal and trigger recursive reveal)? This affects Phase 3 mine placement and Phase 2 win condition testing.

3. **No-guess mode requirement:** Is no-guess mode required for MVP, optional, or deferred to Phase 7? This affects Phase 3 scope and complexity.

**Non-blocking questions** (can be clarified during development):

4. **Chording behavior on incorrect flags:** Should clicking a revealed number with incorrect adjacent flags result in a loss, or be silently ignored? Current plan assumes silent ignore.

5. **Timer inclusion:** Should the timer be included in MVP (Phase 5) or as an optional enhancement (Phase 7)?

6. **Mine counter inclusion:** Should the mine counter be in MVP or optional?

7. **Difficulty selector:** Should difficulty presets be in MVP or optional?

8. **Sound effects:** Assumed optional and Phase 7. Confirm this is acceptable.

9. **Keyboard navigation:** Assumed optional and Phase 6. Confirm this is acceptable or defer to Phase 7.

10. **Accessibility baseline:** Full WCAG 2.1 AA compliance is aspirational. Is a simpler baseline (≥4.5:1 contrast, semantic HTML) acceptable for MVP?

---
