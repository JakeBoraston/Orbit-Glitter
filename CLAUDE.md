# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Walker is a canvas-based animation project built with SvelteKit. The application creates a circular glitter effect using multiple "walker" particles that orbit around a central point, creating visual trails through gradual fade effects.

## Technology Stack

- **Framework**: SvelteKit 2.x with Svelte 5.x (using modern runes API)
- **Language**: TypeScript (strict mode enabled)
- **Build Tool**: Vite 7.x
- **Linting**: ESLint 9.x with TypeScript and Svelte plugins
- **Formatting**: Prettier with Svelte plugin

## Common Commands

```bash
# Start development server
npm run dev

# Start dev server and open in browser
npm run dev -- --open

# Build for production
npm run build

# Preview production build
npm run preview

# Type checking
npm run check

# Type checking in watch mode
npm run check:watch

# Format all files
npm run format

# Lint and check formatting
npm run lint
```

## Architecture

### Application Structure

The application follows a minimal SvelteKit structure:

- `src/routes/+page.svelte` - Main application page containing the canvas animation
- `src/routes/+layout.svelte` - Root layout with favicon and global CSS import
- `src/app.css` - Global styles (CSS reset and body centering)

### Canvas Animation System

The core animation is implemented in `src/routes/+page.svelte` using the following architecture:

**Walker Class** (src/routes/+page.svelte:15-75)
- Manages individual particle state: position, velocity, size, color (HSL), angle, and orbital radius
- `spin(delta)` method: Updates position by calculating orbital motion around center point (cx, cy)
- `show(delta)` method: Renders the walker as a colored rectangle and constrains it within canvas bounds
- `setRandDirection(delta)` method: Random walk implementation (currently commented out in favor of orbital motion)

**Animation Loop** (src/routes/+page.svelte:96-108)
- Uses `requestAnimationFrame` for smooth 60fps animation
- Delta time calculation ensures consistent animation speed across different frame rates
- Semi-transparent black overlay (`rgba(0,0,0,0.01)`) creates trailing effect instead of clearing canvas

**Reactive Canvas Sizing** (src/routes/+page.svelte:4-8, 113-117)
- Canvas dimensions are 80% of window size using Svelte 5 `$state` and `$derived` runes
- `$effect` reactively updates canvas dimensions when window resizes
- `<svelte:window>` binding ensures responsive behavior

### Svelte 5 Runes Usage

The project uses Svelte 5's new runes API:
- `$state` for reactive window dimensions
- `$derived` for computed canvas dimensions
- `$props()` for component props (in layout)
- `$effect` for side effects (canvas resizing)

## Development Notes

- The Walker class currently uses orbital motion (`spin` method) rather than random walk (`setRandDirection`)
- Canvas uses gradual fade technique instead of clearing for visual trails
- TypeScript strict mode is enabled; maintain type safety
- The animation creates a cohesive color scheme by using a random base hue and incrementing it for each walker
