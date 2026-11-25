# Vite: vite-plugin-react-source-string

Works with Vite 5 and later, including Rolldown-based Vite. Source: https://github.com/tanchu/vite-plugin-react-source

```bash
npm install -D vite-plugin-react-source-string
```

Use the project's package manager (`pnpm add -D`, `yarn add -D`, `bun add -d`).

```ts
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import reactSourceString from "vite-plugin-react-source-string";

export default defineConfig(({ command }) => ({
  plugins: [
    command === "serve" && reactSourceString({ overlay: true, preferCallSite: true }),
    react(),
  ],
}));
```

- The plugin runs with `enforce: "pre"`, so its position in `plugins` does not matter. Keep the existing React plugin (`@vitejs/plugin-react` or `@vitejs/plugin-react-swc`).
- The overlay is injected into `index.html` in `vite serve` only and never reaches a build.
- If the config already exports an object, convert it to the function form above, or keep the object and leave the plugin on: the attributes then also appear in production HTML, which is harmless but adds bytes.

## Frameworks that render their own HTML

When the app has no `index.html` (server-rendered frameworks on Vite), the attributes still work, but the overlay is not injected. Install it from the client entry:

```ts
import { installReactSourceClient } from "vite-plugin-react-source-string/client";

if (import.meta.env.DEV) installReactSourceClient("data-source");
```

## Options

| Option | Default | Use |
| --- | --- | --- |
| `overlay` | `false` | Alt + click to copy, `window.__REACT_SOURCE__` for agents. |
| `preferCallSite` | `false` | Wrappers that spread props point to where they are used. |
| `excluded` | `[]` | Component or element names to skip, case-insensitive. The README examples skip `"Slot"` (Radix `asChild`). |
| `libraries` | tag all | Tag only components imported from these packages (HTML elements are always tagged). |
| `root` | Vite's `root` | Base for the relative paths. |
| `include` / `exclude` | JS/TS(X) files / `node_modules` | Which files to transform. |
