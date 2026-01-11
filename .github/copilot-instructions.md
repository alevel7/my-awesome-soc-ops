# Copilot Instructions for Soc Ops

## Development Checklist
Before committing changes, always:
- [ ] `npm run lint` — ESLint passes
- [ ] `npm run build` — TypeScript + Vite build succeeds  
- [ ] `npm test` — All Vitest tests pass

## Overview
Social Bingo for in-person mixers. React 19, TypeScript, Vite, Tailwind CSS 4. Tap 5×5 board squares to match people with questions, get 5 in a row to win.

## Architecture
- **State**: `useBingoGame` hook ([src/hooks/useBingoGame.ts](src/hooks/useBingoGame.ts)) centralizes all game logic
- **Logic**: Pure functions in `src/utils/bingoLogic.ts` (board gen, win detection, toggles)
- **Persistence**: Auto-save to localStorage with version validation (`STORAGE_VERSION`, `validateStoredData()`)
- **Flow**: `App.tsx` → `useBingoGame()` → `StartScreen` | `GameScreen` + `BingoModal`
- **Types**: All in `src/types/index.ts` — `BingoSquareData`, `BingoLine`, `GameState`

## Key Workflows
```bash
npm run dev     # Vite dev server (background task)
npm run build   # Production build  
npm run lint    # ESLint check
npm test        # Vitest (no watch mode)
```
**Deploy**: Auto to GitHub Pages on `main` push (`.github/workflows/deploy.yml` injects `VITE_REPO_NAME`)

**Testing**: Vitest + React Testing Library. See `src/utils/bingoLogic.test.ts` for patterns. Mock with `vi.spyOn(Math, 'random')` for deterministic shuffles. Tests live next to implementation (`.test.ts`).

## Conventions
**Styling (Tailwind CSS 4)**:
- Add design tokens to `@theme` in `src/index.css` (`--color-accent`, `--color-marked`, etc.)
- Use utility classes directly, no CSS modules
- Touch-optimized with `active:` pseudo-classes
- Follow `.github/instructions/frontend-design.instructions.md` for distinctive aesthetics

**Code Patterns**:
- **Immutability**: Spread/map arrays (see `toggleSquare()`, `generateBoard()`)
- **Props**: Interfaces defined inline above components
- **A11y**: Use `aria-pressed`, `aria-label`, `disabled`
- **Structure**: `components/` (UI) | `hooks/` (state) | `utils/` (pure functions) | `types/` | `data/`

## Critical Details
**Board**: 25 squares (5×5), center (index 12) is FREE_SPACE (pre-marked), Fisher-Yates shuffle for randomness

**Win Detection**: 12 lines (5 rows + 5 cols + 2 diagonals) hardcoded in `getWinningLines()`. First match wins, highlight with `bg-amber-200`

**LocalStorage**: Version-gated (`STORAGE_VERSION`), `validateStoredData()` runtime checks, SSR guard (`typeof window === 'undefined'`)

## Common Edits
- **Questions**: Edit `src/data/questions.ts` (keep < 30 chars)
- **Win logic**: Update `getWinningLines()` + tests in `bingoLogic.test.ts`
- **Board size**: Change `BOARD_SIZE`, `CENTER_INDEX`, `getWinningLines()`, grid classes (`grid-cols-5` in `BingoBoard.tsx`)
- **Colors**: Use tokens from `@theme` in `index.css`, avoid inline values
