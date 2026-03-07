# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A cooking timer app for roast chicken dinners. The user enters the bird's weight and oven start time; the app calculates a complete step-by-step cooking schedule for chicken, roast potatoes, Yorkshire puddings, vegetables, and gravy.

## Commands

```bash
npm install     # Install dependencies
npm run dev     # Start Vite dev server
npm run build   # Production build
npm run preview # Preview production build
npm run check   # Svelte type-check
```

Package manager is npm (uses `package-lock.json`).

## Tech Stack

- Svelte 3 (not SvelteKit — this is a plain Vite + Svelte app)
- TypeScript
- Vite 3
- bonsai.css for base styles

## Architecture

Single-component app. All logic is in `src/App.svelte`.

### Cooking Time Formula

```
totalMinutes = Math.ceil(45 * weightInGrams / 1000 + 20)
```

The timeline is a hardcoded object keyed by minutes-before-serving, relative to `chickenReadyTime`. The user provides an oven start time; all displayed clock times are derived from that.

### Key State

- `chickenWeightStr` — raw input string (grams)
- `chickenWeight` — parsed integer, minimum 1
- `cookStart` — time string ("HH:MM") from a `<input type="time">`
- `cookStartTimestamp` — Date object from cookStart
- `times` — computed schedule: `Record<number, string[]>` mapping minutes-from-start to task list

### No Test Suite

There are no tests in this project.
