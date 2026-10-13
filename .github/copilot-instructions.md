# Copilot Instructions for Space Exploration Quiz

## Development & Testing Workflow

This project is a zero-dependency, single-file web application (`index.html`). There is no build pipeline or package manager.

- **Local Preview:** Open `index.html` directly in any web browser or serve locally via:
  ```powershell
  python -m http.server 8000
  ```
- **Testing:** Validation is performed via browser smoke testing (integrated browser canvas or standard web browsers). When testing programmatically:
  - Verify radio group option selection and scoring in DOM context.
  - Test keyboard navigation (`ArrowDown`, `ArrowUp`, and `1`–`4` shortcuts).
  - Test screen view transitions (`#quiz-view` to `#results-view`) and quiz reset via `restartQuiz()`.

## High-Level Architecture

The entire application runs entirely client-side inside `index.html`, structured into three interconnected layers:

1. **State & Data Store (`<script>`):**
   - `questions`: Array of objects defining `category`, `question`, `options` (4 choices), zero-indexed `answer`, and educational `explanation`.
   - Runtime state: `currentIndex` (active question 0–9), `score` (cumulative correct count), and `answered` (boolean lockout guard).
   - View Controller: Toggles display state between `#quiz-view` and `#results-view`.

2. **Mission Control UI Layer (`<style>` & HTML):**
   - **Header & Progress:** Real-time telemetry bar (`SYS // FLIGHT OPS`), live question counter, score tracker, and percentage-based animated progress fill bar.
   - **Interactive Radio Group (`#options-container`):** Dynamically rendered buttons with channel tags (`[CH-A]` through `[CH-D]`), status indicators (`READY`, `STANDBY`), and HUD reticle corner accents (`┌ ┘`) upon selection.
   - **Feedback Banner (`#feedback-banner`):** Reveals correct (`NOMINAL // PASS`) or incorrect (`ANOMALY // REJECT` + `TARGET // CORRECT`) readouts with animated pulses and shakes.
   - **Results Card (`#results-view`):** End-of-quiz scoring tier, dynamic emoji reaction, accuracy breakdown, and rank status (Commander, Astronaut, Specialist, Cadet, Trainee).

3. **Styling & Theming System:**
   - Single source of truth via CSS Custom Properties (`:root`).
   - Native dark mode support using `@media (prefers-color-scheme: dark)`.
   - Motion accessibility handling via `@media (prefers-reduced-motion: reduce)`.

## Key Conventions

- **Strict Zero-Dependency Constraint:** Keep all styles, markup, and scripts self-contained within `index.html`. Do not introduce external CDN scripts, web fonts, or bundlers unless requested.
- **Synthesized Audio (Web Audio API):** All audio effects must be synthesized dynamically using native `AudioContext` oscillators (`OscillatorNode` + `GainNode`). No audio files (MP3/WAV) or external audio libraries are permitted. Always respect browser autoplay policies, default to muted, and persist preference in `localStorage`.
- **Accessibility & Focus Management:**
  - Option items must maintain ARIA compliance (`role="radiogroup"`, `role="radio"`, and `aria-checked`).
  - Screen reader text must be maintained via `.sr-only` indicators.
  - Automatically shift keyboard focus: selection immediately shifts focus to `#next-btn`, and quiz completion shifts focus to `.restart-btn`.
- **Keyboard Shortcut Handling:** Arrow keys (`ArrowUp`, `ArrowDown`, `ArrowLeft`, `ArrowRight`) navigate between available options, while number keys `1`–`4` trigger immediate option selection.
- **Design Token Structure:** Add any new visual styling using theme variables (`--accent`, `--hud-glow`, `--btn-border`, etc.) to preserve dual light and dark theme parity.
