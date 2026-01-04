# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Orbit Glitter is a canvas-based creative coding project built with SvelteKit. The application creates a mesmerizing orbital glitter effect using multiple particles that orbit around a central point, creating visual trails through gradual fade effects. Features include real-time parameter controls and video recording capabilities with automatic MP4 conversion for iPhone compatibility.

## Technology Stack

- **Framework**: SvelteKit 2.x with Svelte 5.x (using modern runes API)
- **Language**: TypeScript (strict mode enabled)
- **Build Tool**: Vite 7.x
- **Package Manager**: Bun
- **Linting**: ESLint 9.x with TypeScript and Svelte plugins
- **Formatting**: Prettier with Svelte plugin
- **Video Recording**: MediaRecorder API + FFmpeg.wasm for MP4 conversion

## Common Commands

```bash
# Start development server
bun run dev

# Start dev server and open in browser
bun run dev -- --open

# Build for production
bun run build

# Preview production build
bun run preview

# Type checking
bun run check

# Type checking in watch mode
bun run check:watch

# Format all files
bun run format

# Lint and check formatting
bun run lint
```

## Architecture

### Application Structure

The application follows a clean SvelteKit structure separating creative coding from utilities:

- `src/routes/+page.svelte` - Main application page with canvas animation and parameter controls
- `src/lib/RecordingControls.svelte` - Reusable recording component with MediaRecorder + FFmpeg
- `src/routes/+layout.svelte` - Root layout with favicon and global CSS import
- `src/app.css` - Global styles (CSS reset and body centering)

### Canvas Animation System

The core animation is implemented in `src/routes/+page.svelte`:

**Walker Class**
- Manages individual particle state: position, velocity, size, color (HSL), angle, and orbital radius
- `spin(delta)` method: Updates position by calculating orbital motion around center point (cx, cy)
- `show(delta)` method: Renders the particle as a colored rectangle

**Animation Loop**
- Uses `requestAnimationFrame` for smooth 60fps animation
- Delta time calculation ensures consistent animation speed across different frame rates
- Semi-transparent black overlay creates trailing effect instead of clearing canvas
- Configurable fade alpha, hue separation, base hue, and orbital radius

**Reactive Canvas Sizing**
- Canvas dimensions are 80% of window size using Svelte 5 `$state` and `$derived` runes
- `$effect` reactively updates canvas dimensions when window resizes
- `<svelte:window>` binding ensures responsive behavior

**Interactive Controls**
- Alpha slider: Controls trail fade rate (0-0.1)
- Hue Separation slider: Adjusts color spread between particles
- Hue slider: Sets base color hue (0-360)
- Radius slider: Controls orbital radius multiplier (0-1)

### Svelte 5 Runes Usage

The project uses Svelte 5's new runes API:
- `$state` for reactive window dimensions
- `$derived` for computed canvas dimensions
- `$props()` for component props (in layout)
- `$effect` for side effects (canvas resizing)

### Recording System

Located in `src/lib/RecordingControls.svelte` - a reusable component for recording canvas animations:

**Features**
- MediaRecorder API captures canvas at 60fps
- Automatic WebM to MP4 conversion using FFmpeg.wasm for iPhone compatibility
- 10-second auto-stop with progress tracking
- LocalStorage-persisted auto-record preference
- iPhone-compatible H.264 encoding (baseline profile, yuv420p, faststart)

**Usage**
```svelte
<RecordingControls {canvas} />
```

## Development Notes

- Canvas uses gradual fade technique instead of clearing for visual trails
- TypeScript strict mode is enabled; maintain type safety
- The animation creates a cohesive color scheme by using a random base hue and incrementing it for each particle
- Recording component is separated from creative coding logic for reusability
