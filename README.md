# Orbit Glitter
(README created by Claude Code)
A mesmerizing canvas-based animation featuring orbital particle glitter effects. Built with SvelteKit and Svelte 5

## Features

- 🎨 **Real-time controls** - Adjust alpha, hue, hue separation, and orbital radius on the fly
- 🎥 **Video recording** - Record your animations with automatic MP4 conversion
- ⚡ **60 FPS animation** - Smooth orbital motion with delta time calculation
- 🌈 **Customizable colors** - HSL-based color system with dynamic hue control
- 📱 **Responsive** - Canvas scales to 80% of viewport

## Demo

🔗 [**Live Demo**](https://jakeboraston.github.io/Orbit-Glitter/)

## Getting Started

### Prerequisites

- [Bun](https://bun.sh/) (recommended) or Node.js

### Installation

```bash
# Clone the repository
git clone https://github.com/jakeboraston/Orbit-Glitter.git
cd Orbit-Glitter

# Install dependencies
bun install

# Start development server
bun run dev
```

Visit `http://localhost:5173` to see the animation.

## Usage

### Controls

- **Alpha Slider** - Adjust trail fade rate (lower = longer trails)
- **Hue Separation Slider** - Control color spread between particles
- **Hue Slider** - Set the base color
- **Radius Slider** - Adjust orbital radius

### Recording

1. Click "Start Recording" to begin capturing
2. Animation records for 10 seconds at 60fps
3. Automatically converts to MP4 (works on iPhone!)
4. Toggle "Auto-record on load" to start recording on page load

## Technology Stack

- **SvelteKit 2** with Svelte 5 (runes API)
- **TypeScript** (strict mode)
- **Vite 7**
- **FFmpeg.wasm** for WebM → MP4 conversion
- **MediaRecorder API** for canvas recording

## Building for Production

```bash
bun run build
```

## License

MIT

## Author

Jake
