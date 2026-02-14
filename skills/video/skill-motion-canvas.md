---
id: skill-motion-canvas
type: skill
name: motion-canvas
description: ''
category: video
complexity: medium
keywords:
- api
- github
- javascript
- performance
- react
- typescript
capabilities: []
token_estimate: 1680
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,680 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# motion-canvas

---
name: motion-canvas
description: Complete production-ready guide for Motion Canvas with ESM/CommonJS workarounds, full setup templates, and troubleshooting for programmatic video creation using TypeScript
version: 2.0.0
author: motion-canvas
repo: https://github.com/motion-canvas/motion-canvas
license: MIT
tags: [Video, TypeScript, Animation, Motion Canvas, Signals, Generators, Canvas API, Vector, Audio Sync, Vite, ESM]
dependencies: [@motion-canvas/core>=3.0.0, @motion-canvas/2d>=3.0.0, @motion-canvas/ui>=3.0.0, @motion-canvas/vite-plugin>=3.0.0]
---

# Motion Canvas - Production-Ready Video Creation with TypeScript

Complete production-ready skill for creating programmatic videos using Motion Canvas, including critical ESM/CommonJS workarounds, full configuration templates, and comprehensive troubleshooting.

## ⚠️ CRITICAL: ESM/CommonJS Interoperability Issue

**IMPORTANT**: The `@motion-canvas/vite-plugin` package is distributed as CommonJS, which causes import errors in modern ESM projects. The standard `import motionCanvas from '@motion-canvas/vite-plugin'` **WILL NOT WORK**.

You MUST use the `createRequire` workaround documented in the Setup section below.

## When to use

Use this skill whenever you are dealing with Motion Canvas code to obtain domain-specific knowledge about:

- Creating animated videos using TypeScript and generator functions
- Building animations with signals and reactive values
- Working with vector graphics and Canvas API
- Synchronizing animations with voice-overs and audio
- Using the real-time preview editor for instant feedback
- Implementing procedural animations with flow control
- Creating informative visualizations and diagrams
- Animating text, shapes, and custom components
- **Setting up Motion Canvas projects from scratch with correct configuration**
- **Troubleshooting common setup and build errors**

## Core Concepts

Motion Canvas allows you to create videos using:
- **Generator Functions**: Describe animations using JavaScript generators with `yield*` syntax
- **Signals**: Reactive values that automatically update dependent properties
- **Real-time Preview**: Live editor with instant preview powered by Vite
- **TypeScript-First**: Write animations in TypeScript with full IDE support
- **Canvas API**: Leverage 2D Canvas for high-performance vector rendering
- **Audio Synchronization**: Sync animations precisely with voice-overs

## Complete Setup Guide

### Step 1: Initialize Project

```bash
# Create project directory
mkdir my-motion-canvas-project
cd my-motion-canvas-project

# Initialize package.json
npm init -y
```

### Step 2: Configure package.json for ESM

**CRITICAL**: Add `"type": "module"` to enable ESM imports.

```json
{
  "name": "my-motion-canvas-project",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

### Step 3: Install ALL Required Dependencies

**CRITICAL**: Must include `@motion-canvas/ui` - the plugin will fail without it.

```bash
npm install --save-dev @motion-canvas/core @motion-canvas/2d @motion-canvas/vite-plugin @motion-canvas/ui vite typescript
```

### Step 4: Create Project Structure

```
my-motion-canvas-project/
├── package.json          # "type": "module" required
├── vite.config.js        # Use .js NOT .ts (see Step 5)
├── tsconfig.json         # TypeScript configuration
├── index.html            # HTML entry point
└── src/
    ├── project.ts        # Project configuration with scenes
    └── scenes/
        └── example.tsx   # Animation scene
```

### Step 5: Create vite.config.js with ESM/CommonJS Workaround

**CRITICAL**: Use `vite.config.js` (NOT `.ts`) with the `createRequire` workaround.

**File: `vite.config.js`**
```javascript
import {defineConfig} from 'vite';
import {createRequire} from 'module';

// WORKAROUND: @motion-canvas/vite-plugin is CommonJS, must use require
const require = createRequire(import.meta.url);
const motionCanvasModule = require('@motion-canvas/vite-plugin');
const motionCanvas = motionCanvasModule.default || motionCanvasModule;

export default defineConfig({
  plugins: [
    motionCanvas({
      project: './src/project.ts',
    }),
  ],
});
```

**Why .js instead of .ts?**
- Vite config runs before TypeScript compilation
- The `createRequire` workaround works reliably in plain JavaScript
- Avoids additional type resolution complexity

### Step 6: Create tsconfig.json

**CRITICAL**: Include `esModuleInterop` and `allowSyntheticDefaultImports`.

**File: `tsconfig.json`**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ES2020",
    "lib": ["ES2020", "DOM"],
    "jsx": "react-jsx",
    "jsxImportSource": "@motion-canvas/2d/lib",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

### Step 7: Create index.html

**File: `index.html`**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Motion Canvas Project</title>
</head>
<body>
  <div id="root"></div>
  <script type="module" src="/src/project.ts"></script>
</body>
</html>
```

### Step 8: Create src/project.ts

**File: `src/project.ts`**
```typescript
import {makeProject} from '@motion-canvas/core';
import example from './scenes/example?scene';

export default makeProject({
  scenes: [example],
});
```

### Step 9: Create First Animation Scene

**File: `src/scenes/example.tsx`**
```typescript
import {makeScene2D} from '@motion-canvas/2d/lib/scenes';
import {Circle} from '@motion-canvas/2d/lib/components';
import {createRef} from '@motion-canvas/core/lib/utils';
import {all} from '@motion-canvas/core/lib/flow';

export default makeScene2D(function* (view) {
  const circleRef = createRef<Circle>();

  view.add(
    <Circle
      ref={circleRef}
      size={70}
      fill="#e13238"
    />,
  );

  // Animate circle size and position
  yield* circleRef().size(140, 1);
  yield* circleRef().position.x(300, 1);
  yield* circleRef().fill('#e6a700', 1);

  // Parallel animations
  yield* all(
    circleRef().scale(1.5, 0.5),
    circleRef().rotation(360, 1)
  );
});
```

### Step 10: Run Development Server

```bash
npm run dev
```

Open browser at `http://localhost:5173` to see the Motion Canvas editor.

## Troubleshooting

### Error: `TypeError: motionCanvas is not a function`

**Cause**: ESM/CommonJS interoperability issue with `@motion-canvas/vite-plugin`

**Solution**: Use the `createRequire` workaround in `vite.config.js` (see Step 5)

```javascript
// ❌ WRONG - Will not work
import motionCanvas from '@motion-canvas/vite-plugin';

// ✅ CORRECT - Use createRequire
import {createRequire} from 'module';
const require = createRequire(import.meta.url);
const motionCanvasModule = require('@motion-canvas/vite-plugin');
const motionCanvas = motionCanvasModule.default || motionCanvasModule;
```

### Error: `Cannot find module '@motion-canvas/ui'`

**Cause**: Missing required dependency

**Solution**: Install the UI package:
```bash
npm install --save-dev @motion-canvas/ui
```

### Error: `Property 'default' does not exist on type ...`

**Cause**: TypeScript configuration missing ESM interop settings

**Solution**: Add to `tsconfig.json`:
```json
{
  "compilerOptions": {
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true
  }
}
```

### Warning: `The CJS build of Vite's Node API is deprecated`

**Status**: This is a known warning and can be safely ignored. It appears because `@motion-canvas/vite-plugin` is CommonJS. The workaround ensures functionality despite the warning.

### Error: `Failed to resolve import "*.tsx?scene"`

**Cause**: Vite plugin not properly loaded or configured

**Solution**:
1. Verify `vite.config.js` has the correct workaround
2. Check `project` path points to correct file: `'./src/project.ts'`
3. Ensure scene imports use `?scene` suffix: `import example from './scenes/example?scene';`

### Build fails with TypeScript errors

**Solution**:
1. Verify `tsconfig.json` includes all required options (see Step 6)
2. Check `jsxImportSource` is set to `@motion-canvas/2d/lib`
3. Ensure all dependencies are installed

## How to use

Read individual rule files for detailed explanations and code examples:

### Core Animation Concepts
- **[references/generators.md](references/generators.md)** - Generator functions for describing animations
- **[references/signals.md](references/signals.md)** - Reactive signals for dynamic properties and dependencies
- **[references/animations.md](references/animations.md)** - Tweening properties and creating smooth animations

For additional topics like scenes, shapes, text rendering, audio synchronization, and advanced features, refer to the comprehensive [Motion Canvas official documentation](https://motioncanvas.io/docs).

## Complete Working Example

This is a complete, tested project structure that works out of the box:

```
my-motion-canvas-project/
├── package.json
│   {
│     "name": "my-motion-canvas-project",
│     "type": "module",
│     "scripts": {
│       "dev": "vite",
│       "build": "vite build"
│     },
│     "devDependencies": {
│       "@motion-canvas/core": "^3.0.0",
│       "@motion-canvas/2d": "^3.0.0",
│       "@motion-canvas/vite-plugin": "^3.0.0",
│       "@motion-canvas/ui": "^3.0.0",
│       "vite": "^5.0.0",
│       "typescript": "^5.0.0"
│     }
│   }
│
├── vite.config.js (with createRequire workaround)
├── tsconfig.json (with esModuleInterop)
├── index.html
└── src/
    ├── project.ts (makeProject with scenes array)
    └── scenes/
        └── example.tsx (makeScene2D with animations)
```

## Best Practices

1. **Always use the createRequire workaround** - Don't try standard ESM imports for the Vite plugin
2. **Use vite.config.js not .ts** - Avoids additional compilation complexity
3. **Include all dependencies** - Don't forget `@motion-canvas/ui`
4. **Use generator functions** - All scene animations should use `function*` and `yield*` syntax
5. **Leverage signals** - Create reactive dependencies between properties
6. **Think in durations** - Specify animation duration in seconds as the second parameter
7. **Use refs for control** - Create references to nodes for precise animation control
8. **Preview frequently** - Take advantage of the real-time editor for instant feedback
9. **Organize scenes** - Break complex animations into multiple scenes
10. **Type everything** - Use TypeScript for better IDE support and fewer errors

## Common Pitfalls to Avoid

1. ❌ Forgetting `"type": "module"` in package.json
2. ❌ Using standard import for `@motion-canvas/vite-plugin`
3. ❌ Not installing `@motion-canvas/ui`
4. ❌ Missing `esModuleInterop` in tsconfig.json
5. ❌ Using `vite.config.ts` instead of `vite.config.js`
6. ❌ Forgetting `?scene` suffix in scene imports

## Resources

- **Documentation**: https://motioncanvas.io/docs
- **Repository**: https://github.com/motion-canvas/motion-canvas
- **Examples**: https://motioncanvas.io/docs/quickstart
- **Community**: Discord and GitHub Discussions
- **License**: MIT


---


## 📚 Reference Materials


### Animations

# Animations in Motion Canvas

Motion Canvas uses a property-based animation system where you tween individual properties over time using generator functions.

## Basic Property Animation

```typescript
import {makeScene2D} from '@motioncanvas/2d/lib/scenes';
import {Circle} from '@motioncanvas/2d/lib/components';
import {createRef} from '@motioncanvas/core/lib/utils';

export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // Animate size from current value (100) to 200 over 1.5 seconds
  yield* circle().size(200, 1.5);
});
```

## Multiple Property Animation

Animate several properties at once:

```typescript
import {all} from '@motioncanvas/core/lib/flow';

export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // Animate multiple properties simultaneously
  yield* all(
    circle().size(200, 1.5),
    circle().position.x(300, 1.5),
    circle().fill('#e6a700', 1.5),
  );
});
```

## Easing Functions

Control animation curves with easing:

```typescript
import {easeInOutCubic, easeInBounce} from '@motioncanvas/core/lib/tweening';

export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // Use cubic easing
  yield* circle().size(200, 1.5, easeInOutCubic);

  // Use bounce easing
  yield* circle().position.y(200, 1, easeInBounce);
});
```

## From-To Animation

Specify both start and end values explicitly:

```typescript
export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // Animate from 50 to 200
  yield* circle().size(200, 1.5).from(50);

  // Or use the longer syntax
  yield* tween(1.5, value => {
    circle().size(map(50, 200, value));
  });
});
```

## Looping Animations

Create repeating animations:

```typescript
import {loop} from '@motioncanvas/core/lib/flow';

export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // Loop 3 times
  yield* loop(3, () => circle().size(200, 1).to(100, 1));

  // Infinite loop
  yield* loop(Infinity, function* () {
    yield* circle().rotation(360, 2);
  });
});
```

## Chain Animations

Sequence animations fluently:

```typescript
export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // Chain multiple tweens
  yield* circle()
    .size(200, 1)
    .to(150, 0.5)
    .to(100, 0.5);
});
```

## Relative Animations

Animate relative to current value:

```typescript
export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} x={0} size={100} fill="#e13238" />);

  // Move 100 pixels to the right (relative)
  yield* circle().position.x(circle().position.x() + 100, 1);

  // Or use the .by() method
  yield* circle().position.x(100, 1).by();
});
```

## Resources

- [Motion Canvas Animation Documentation](https://motioncanvas.io/docs/flow/)
- [Easing Functions Reference](https://motioncanvas.io/docs/tweening/)




### Signals

# Signals in Motion Canvas

Signals represent values that may change over time. They create reactive dependencies between properties, automatically updating dependent values when source values change.

## Creating Signals

```typescript
import {createSignal} from '@motioncanvas/core/lib/signals';

// Create a simple signal
const radius = createSignal(10);

// Get the current value
console.log(radius()); // 10

// Set a new value
radius(20);
console.log(radius()); // 20
```

## Computed Signals

Create signals that depend on other signals:

```typescript
import {createSignal, createComputed} from '@motioncanvas/core/lib/signals';

const radius = createSignal(10);

// Area automatically updates when radius changes
const area = createComputed(() => Math.PI * radius() ** 2);

console.log(area()); // ~314.159

radius(20);
console.log(area()); // ~1256.637
```

## Signals in Components

Components use signals for reactive properties:

```typescript
import {makeScene2D} from '@motioncanvas/2d/lib/scenes';
import {Circle} from '@motioncanvas/2d/lib/components';
import {createRef, createSignal} from '@motioncanvas/core/lib/utils';

export default makeScene2D(function* (view) {
  const circleRef = createRef<Circle>();
  const radiusSignal = createSignal(50);

  view.add(
    <Circle
      ref={circleRef}
      size={() => radiusSignal() * 2} // Reactive binding
      fill="#e13238"
    />
  );

  // Animate the signal
  yield* radiusSignal(100, 2);
});
```

## Binding Signals

Link signals together for synchronized updates:

```typescript
export default makeScene2D(function* (view) {
  const circle1 = createRef<Circle>();
  const circle2 = createRef<Circle>();

  view.add(
    <>
      <Circle ref={circle1} x={-200} size={100} fill="#e13238" />
      <Circle ref={circle2} x={200} size={100} fill="#e6a700" />
    </>
  );

  // Bind circle2's size to circle1's size
  circle2().size(circle1().size);

  // Now both circles resize together
  yield* circle1().size(200, 1);
});
```

## Animating Signals

Signals can be tweened over time:

```typescript
import {createSignal} from '@motioncanvas/core/lib/signals';
import {tween} from '@motioncanvas/core/lib/tweening';
import {easeInOutCubic} from '@motioncanvas/core/lib/tweening';

export default makeScene2D(function* (view) {
  const mySignal = createSignal(0);

  // Tween from 0 to 100 over 2 seconds
  yield* tween(2, value => {
    mySignal(easeInOutCubic(value, 0, 100));
  });
});
```

## Resources

- [Motion Canvas Signals Documentation](https://motioncanvas.io/docs/signals/)
- [Reactive Programming Concepts](https://motioncanvas.io/docs/signals/)




### Generators

# Generator Functions in Motion Canvas

Generator functions are the core building block for describing animations in Motion Canvas. They use JavaScript's `function*` syntax and `yield*` to control animation flow.

## Basic Generator Function

```typescript
import {makeScene2D} from '@motioncanvas/2d/lib/scenes';

export default makeScene2D(function* (view) {
  // Animation code goes here
  yield* waitFor(1); // Wait for 1 second
});
```

## Yielding Animations

Use `yield*` to execute animations and wait for them to complete:

```typescript
export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // This will wait for the animation to complete
  yield* circle().size(200, 1.5);

  // This executes after the previous animation finishes
  yield* circle().fill('#e6a700', 1);
});
```

## Parallel Animations

Run multiple animations simultaneously using `all()`:

```typescript
import {all} from '@motioncanvas/core/lib/flow';

export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();
  const rect = createRef<Rect>();

  view.add(
    <>
      <Circle ref={circle} x={-200} size={100} fill="#e13238" />
      <Rect ref={rect} x={200} size={100} fill="#e6a700" />
    </>
  );

  // Both animations run at the same time
  yield* all(
    circle().position.x(0, 1),
    rect().position.x(0, 1),
  );
});
```

## Sequential Flow

Chain animations in sequence:

```typescript
export default makeScene2D(function* (view) {
  const circle = createRef<Circle>();

  view.add(<Circle ref={circle} size={100} fill="#e13238" />);

  // Execute one after another
  yield* circle().size(200, 1);
  yield* circle().position.x(300, 1);
  yield* circle().fill('#e6a700', 1);
  yield* circle().size(100, 1);
});
```

## Resources

- [Motion Canvas Flow Documentation](https://motioncanvas.io/docs/flow/)
- [JavaScript Generators MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)




---

## 🚀 Usage

**Reference this template:** `@skill-motion-canvas.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
