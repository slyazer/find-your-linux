# AGENTS.md — I Love Linux (find-your-linux)

## Project Overview
Single-file HTML quiz app (~3300 lines) that matches users to Linux distributions. Originally called DistroMatch, renamed to "I Love Linux" / Find Your Linux. Lives at `slyazer/find-your-linux` on GitHub.

**Tech stack:** Vanilla HTML/CSS/JS, no frameworks, no build step. 4 themes (Space, Light, Catppuccin, Dracula), EN/FR translations, localStorage persistence.

## How to make changes
- **Always use FIND/REPLACE blocks.** Never rewrite the full file.
- Use enough context in FIND to uniquely match (5-10 lines minimum).
- Test both English and French after any translation change.
- Test both normal mode and Arch mode after any QS/logic change.
- Hard refresh (Ctrl+Shift+R) after every change — browser caches aggressively.

## Current State (as of May 2026)

### Working
- 25-question quiz across 5 themed parts (Ease & Stability, Setup & Workflow, Privacy & Security, Custom & CLI, Gaming & Beauty)
- 30-distro database with scoring profiles, logos, descriptions, tags
- 9-dimension scoring engine (cosine similarity + signal matching) with `+=` accumulation
- Podium reveal with scan bar, confetti, particle effects, animated cards
- 28 achievement badges with localStorage persistence (4 secret)
- Profile Locks system (gaming/security/lightweight) — toggleable in settings
- "I use Arch btw" meme mode — replaces all 25 questions with absurd ones, always matches Arch, changes brand/footer text
- Tier list drag & drop for previously tried distros
- Follow-up like/dislike questions after Q2
- Ubuntu variants screen
- "Why this distro?" explainer on result page
- Comparison table on podium
- Easter egg: typing Linux distro names shows toast notification
- AI questions (Q20-C, Q24-A) trigger fullscreen red jumpscare
- Share result (clipboard copy)
- Random distro button
- Badge wall UI
- "Tweak answers" replays quiz keeping previous answers
- Glitch effect on title hover
- Mobile-responsive with media queries

### Known Bugs / Edge Cases
1. **Q18 skipped for beginners:** `showNormalQ()` skips index 17 when `userSignals.includes('beginner')`. This means beginners get 24 questions, not 25. Progress bar and "25 questions" claim is off by one for beginners.
2. **BADGES array trailing comma:** After the `indecisive` badge there's `,` on its own line creating an undefined element. `BADGES.forEach()` skips it but it's messy.
3. **Light theme missing styles:** score-breakdown, timer-badge, pick-badge, podium comparison table, spin-txt don't have light mode overrides.
4. **Podium lock note flow:** After clicking "Show with all distros" on podium, then clicking a distro detail, then going back to podium — the lock note can reappear incorrectly.
5. **`goBackToPodium()` fallback:** Uses `showPickScreen()` if both `_podiumTopThree` and `currentTopThree` are empty. Works but shows wrong UI sometimes.
6. **DISTROSEA_KEYS has dead entries:** `bunsenlabs:null` exists but bunsenlabs isn't in DB. `omarchy` is in DB but not in DISTROSEA_KEYS.
7. **`showGoodLinuxUser()` and `goodLinuxTimeout`:** Defined but never called. Dead code.
8. **`triggerGoodJumpscare()`:** Only called when unchecking AI options (o.popup). Functional but obscure trigger.
9. **Q1 has only one option (A):** "Never used it" — no options for experienced users. The "Already Tried" input compensates.
10. **French `q4_optDs` typo:** `"Restaurer des snapshots c'est amusant"` — missing 't' in "amusant".
11. **Brand tag has trailing space:** `'// I.Love.Linux '` — intentional or oversight?
12. **Podium shows "Part 5 of 5" hardcoded on last question:** Should use `QUIZ_PARTS[4].name` dynamically.
13. **`recomputeWithoutCurrentLock()` shows pick screen, not podium:** User loses podium context when overriding locks from detail page.
14. **localStorage keys use inconsistent prefix:** `'I Love Linux -badges'` (with spaces, three words) vs shorter prefixes like `distromatch-` in the old code.

### Design Decisions
- **Single file by design:** No build step, no dependencies beyond Google Fonts and SimpleIcons CDN. Easy to deploy, share, and fork.
- **`Math.max` → `+=` for dimensions:** Originally used `Math.max()` which capped dimensions at 3. Changed to `+=` so scores accumulate across questions. Badge thresholds updated accordingly (Terminal Lover: 40, Gamer: 8, Privacy: 18).
- **Arch mode replaces QS entirely:** Uses a parallel `ARCH_QUESTIONS` array with its own inline translations (m/m_fr, s/s_fr). Doesn't use the `t()` system. Always forces `['arch','arch','arch']` on the podium.
- **Both beta features (locks, arch mode) default OFF:** Set in initialization block, overwriting any saved localStorage.
- **Theme system uses CSS variables:** 4 themes defined in `THEMES` object, applied via `setProperty()` on `:root`. Light mode adds `.light-mode` class for hardcoded overrides (because some elements need `!important`).
- **Badge secrets:** 4 badges hidden until earned (linux-detective, ai-abuser, loyalist, indecisive). Their names/descriptions show as `???` on the badge wall.

## What To Work On Next
1. **Fix the BADGES array trailing comma** (remove the orphaned `,` line)
2. **Fix DISTROSEA_KEYS:** Remove bunsenlabs, add omarchy
3. **Fix Q18 skip for beginners** — adjust total question count or don't skip
4. **Add light mode styles** for score-breakdown, timer-badge, pick-badge, comparison table
5. **Fix podium lock note flow** — prevent reappearance after override
6. **Fix `recomputeWithoutCurrentLock()`** to go to podium, not pick screen
7. **Clean up dead code:** `showGoodLinuxUser()`, `goodLinuxTimeout`, `finishReveal()` (unused `revContinue` reference)
8. **Make podium last-question text dynamic** instead of hardcoded "Part 5 of 5"
9. **Fix French `q4_optDs` typo** (amusant → amusant)
10. **Consider consistent localStorage key naming** — maybe `ilovelinux-` prefix

## Communication Style
- The project owner is called **babe**. Address him that way.
- Update this AGENTS.md whenever you make meaningful code changes.
- Use FIND/REPLACE blocks for all code changes.
- Be concise. Don't over-explain.
