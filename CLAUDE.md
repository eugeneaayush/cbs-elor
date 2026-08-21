# CLAUDE.md

## Project overview
- This repository is a self-contained static web app called **The CBS-elor**.
- It is a parody Tinder-style interface built as a single HTML file with inline CSS and inline JavaScript.
- There is **no backend**, **no package manager**, **no build step**, and **no automated test suite** in the repository.
- All image assets live at the repository root and are referenced directly by filename.

## Repository layout
- `/home/runner/work/cbs-elor/cbs-elor/index.html`
  - The entire application lives here.
  - `<style>` block: all CSS for layout, views, animations, and component styling.
  - `<body>` markup: the phone shell plus the Discover, Matches, Detail, Games, Profile, and Match Overlay views.
  - `<script>` block: all seed data, UI state, rendering functions, swipe logic, and mini-game logic.
- `/home/runner/work/cbs-elor/cbs-elor/*.png`
  - Static image assets for the fake profiles.
  - There are 33 PNGs in the repo root totaling about 116 MB.
- `/home/runner/work/cbs-elor/cbs-elor/.claude/launch.json`
  - Claude-specific launch config that serves the app with `python3 -m http.server 8765`.

## Tech stack
- Plain HTML
- Plain CSS
- Plain browser JavaScript
- Static image assets
- Google Fonts (`Poppins`) loaded via `@import`

## How to run locally
From `/home/runner/work/cbs-elor/cbs-elor`:

```bash
python3 -m http.server 8765
```

Then open:

```text
http://127.0.0.1:8765
```

Notes:
- This matches `.claude/launch.json`.
- You can also use any other static file server, but do not introduce one unless explicitly asked.

## Build, lint, and test status
- **Build:** none
- **Lint:** none configured
- **Unit/integration tests:** none configured
- **Primary validation method:** manual browser testing
- **CI/workflows:** none present in the repository

## Required validation after changes
Because there is no automated tooling, use manual smoke testing in a browser:

1. Start the local static server.
2. Load the app and confirm the UI renders without console errors.
3. In **Discover**:
   - cards render
   - tap zones switch photos
   - drag/swipe works left and right
   - action buttons work
   - empty state appears after exhausting cards
4. In **Matches**:
   - online avatars render
   - list entries render
   - opening a detail profile works
5. In **Detail view**:
   - photos advance/back
   - bio and tags render
   - back/close works
6. In **Games**:
   - each game opens
   - advancing through prompts works
   - returning to the games menu works
7. In **Profile**:
   - profile photo switching works
   - name, title, bio, tags, and stats render
8. If you change asset names or profile data, verify every referenced image still loads.

## Application structure

### File map inside `index.html`
- Lines `1-225`: embedded CSS
- Lines `226-379`: HTML structure
- Lines `380-398`: seed data and randomized discover ordering
- Lines `400-737`: UI logic and game logic

### 1. Discover view
- Card stack rendered into `#cardStack`
- Uses `discoverOrder` to randomize profile order on load
- Maintains per-card image position in `cardPhotoIndex`
- Supports:
  - swipe gestures
  - photo tap navigation
  - match overlay
  - empty-state handling

Key functions:
- `renderCards()`
- `setupDrag(card)`
- `handleSwipe(dir)`
- `swipeLeft()`
- `swipeRight()`
- `superLike()`
- `openDiscoverDetail()`

### 2. Matches view
- Renders an online row and a full match list from `profiles`
- Uses `timeLabels` for fake recency badges
- Can open the shared detail view

Key function:
- `renderMatches()`

### 3. Detail view
- Full-screen overlay for an individual profile
- Reuses the profile data from `profiles`
- Has its own photo index state

Key functions:
- `openDetail(index)`
- `detailNext()`
- `detailPrev()`
- `updateDetailPhoto()`
- `closeDetail()`

### 4. Games view
- Contains four mini-games:
  - Hot Takes
  - Truth or Dare
  - Two Truths & A Lie
  - Most Likely To...
- Games are data-driven from arrays in the script block

Key functions:
- `startGame(game)`
- `backToGamesMenu()`
- `renderHotTake()`
- `renderTruthDare()`
- `renderTwoTruths()`
- `renderMostLikely()`

### 5. Profile view
- Renders the local `myProfile` object
- Supports photo navigation separate from discover/detail state

Key functions:
- `renderMyProfile()`
- `myProfileNext()`
- `myProfilePrev()`
- `updateMyProfilePhoto()`

## Data model

### Primary objects and arrays
- `myProfile`
  - data for the local profile page
- `profiles`
  - core dataset for discover cards, matches, detail pages, and parts of games
- `timeLabels`
  - mock timestamps for the matches list
- `discoverOrder`
  - shuffled copy of `profiles` used by Discover
- `hotTakes`
- `truthDares`
- `twoTruths`
- `mostLikelys`

### Runtime state
- `currentIndex`
- `startX`
- `currentX`
- `isDragging`
- `cardPhotoIndex`
- `detailPhotoIdx`
- `detailImages`
- `myProfilePhotoIdx`
- `gameIdx`
- `tdMode`

## Behavior notes
- The app uses direct DOM mutation and `innerHTML` rendering instead of a component system.
- State is entirely in-memory; reloading the page resets matches, votes, photo positions, and game progress.
- The experience targets modern browsers with support for:
  - `pointer` events
  - CSS transforms/transitions
  - flexbox
  - template literals and modern JavaScript syntax
- Because the data is hardcoded and trusted, the current `innerHTML` usage is acceptable for this repo shape, but any move to user-generated content would require sanitization.

## Editing guidance
- Keep changes **surgical**. This app is intentionally a single-file prototype.
- Prefer updating existing inline CSS/JS/HTML instead of introducing frameworks, bundlers, or file splits unless explicitly requested.
- Preserve the current visual style:
  - dark theme
  - pink/purple gradients
  - phone-frame layout
  - rounded card-heavy UI
- Reuse existing naming patterns and helper functions where possible.
- If adding a new profile:
  - update the `profiles` array
  - add corresponding image assets at the repo root
  - verify references match filenames exactly
- If adding a new game:
  - add the menu card markup
  - add seed data if needed
  - extend `startGame(game)`
  - add render/advance handlers in the script block
- Keep user-visible strings consistent with the app’s current humorous tone unless the task asks for tone changes.

## Repo-specific gotchas
- The repo has **no README** or other setup docs; `CLAUDE.md` is the main operational guide.
- The app is optimized around a fixed-width phone frame (`393px`) rather than a responsive desktop-first layout.
- The entire repo is tiny in code footprint but relatively large on disk because the PNG assets total about 116 MB.
- Several behaviors are intentionally randomized:
  - discover order is shuffled on load
  - right swipes only sometimes trigger a match overlay
  - match badges in the list are randomly generated on render
- Because of the randomness, exact visual output will vary between reloads.
- All logic is in one file, so unrelated edits can easily create merge conflicts or accidental regressions.
- Some profile names intentionally do not match their asset filename stems exactly (for example, display names vs. image filenames). Do not “clean this up” unless explicitly asked; just keep references consistent.

## Safe change strategy for Claude Code
1. Read `index.html` before editing.
2. Identify whether the change affects CSS, markup, data, or JS behavior.
3. Touch the smallest possible area.
4. Re-run the manual smoke test for every affected view.
5. If images or profile data changed, verify every image path in the browser.

## What not to assume
- Do not assume Node, npm, PHP, Python frameworks, or a bundler are part of this repo.
- Do not assume there are hidden tests.
- Do not assume assets are organized into subdirectories.
- Do not assume randomness is a bug; some of it is deliberate.

## Useful commands
Run the app:

```bash
cd /home/runner/work/cbs-elor/cbs-elor
python3 -m http.server 8765
```

Quick file inspection:

```bash
cd /home/runner/work/cbs-elor/cbs-elor
ls
```

## Summary for future agents
- This is a lightweight static prototype.
- `index.html` is the app.
- `.png` files are the assets.
- `.claude/launch.json` shows the intended local launch command.
- Manual browser verification is the source of truth for changes.
