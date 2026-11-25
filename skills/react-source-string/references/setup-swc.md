# Next.js (SWC): swc-plugin-react-source-string

Source: https://github.com/tanchu/swc-plugin-react-source

SWC plugins are WebAssembly and must match the `swc_core` version inside the bundler. A mismatch fails at startup with a `Mismatch` or `Failed to deserialize program received from host` error. Check the project's Next.js version (`npm ls next`) and install the matching plugin version:

| Next.js | Install | Overlay |
| --- | --- | --- |
| 16.4.x | `swc-plugin-react-source-string@latest` | yes (0.8.0 and later) |
| 16.1.7 – 16.3.8 | `swc-plugin-react-source-string@0.4.0` | no: attributes only, read them with `closest("[data-source]")` |
| older, or Rspack | no matching build is published | use the Babel plugin if the project builds with Babel; otherwise tell the user |

The list of versions and npm dist-tags is the source of truth: `npm view swc-plugin-react-source-string dist-tags versions`. If the project's Next.js is newer than the table, try `@latest` and look for the errors above.

```bash
npm install -D swc-plugin-react-source-string@latest
```

```ts
// next.config.ts
import type { NextConfig } from "next";

const isDev = process.env.NODE_ENV === "development";

const nextConfig: NextConfig = {
  experimental: {
    swcPlugins: isDev
      ? [["swc-plugin-react-source-string", { root: process.cwd(), preferCallSite: true, overlay: true }]]
      : [],
  },
};

export default nextConfig;
```

- Merge into the existing `experimental.swcPlugins` array if there is one.
- Always pass `root: process.cwd()`. Without it the paths are absolute.
- With `overlay`, the plugin adds `import "swc-plugin-react-source-string/overlay.js"` to each tagged file, after `"use client"`. The code (about 5 KB) does nothing in production, but keep it out of production builds as above.
- The overlay runs through client components. A page made only of server components gets the attributes but no `__REACT_SOURCE__`; read `data-source` directly there.
- If the project has a Babel config (`.babelrc`, `babel.config.*`), Next.js uses Babel instead of SWC. Use [setup-babel.md](setup-babel.md).

## Options

| Option | Default | Use |
| --- | --- | --- |
| `root` | none (absolute paths) | Set to `process.cwd()`. |
| `overlay` | `false` | Alt + click to copy, `window.__REACT_SOURCE__` for agents. |
| `preferCallSite` | `false` | Wrappers that spread props point to where they are used. |
| `excluded` | `[]` | Component or element names to skip, case-insensitive. |
| `libraries` | tag all | Tag only components imported from these packages (HTML elements are always tagged). |
