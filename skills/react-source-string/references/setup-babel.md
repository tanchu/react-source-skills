# Babel: babel-plugin-react-source-string

Works with Babel 7 and 8, in any setup that runs Babel on JSX: webpack or Rspack with `babel-loader`, Next.js with a Babel config, Metro, Jest. Source: https://github.com/tanchu/babel-plugin-react-source-string

```bash
npm install -D babel-plugin-react-source-string
```

```js
// babel.config.js
module.exports = (api) => ({
  // keep the project's existing presets
  plugins: [
    ...(api.env("development")
      ? [["babel-plugin-react-source-string", { overlay: true, preferCallSite: true }]]
      : []),
  ],
});
```

- Merge into the existing config. For `.babelrc` (JSON) use the `env.development.plugins` section.
- `api.env()` reads `BABEL_ENV`, then `NODE_ENV`. Make sure the dev server runs with one of them set to `development`.
- With `overlay`, the plugin adds `import "babel-plugin-react-source-string/overlay.js"` to each tagged file, after directives such as `"use client"`. The code (about 5 KB) does nothing on the server or in production, but keep it out of production builds as above.
- In tests (Jest) the attributes are harmless, but snapshot tests will include them. Leave the plugin off in the `test` env if the project has DOM snapshots.

## Options

| Option | Default | Use |
| --- | --- | --- |
| `overlay` | `false` | Alt + click to copy, `window.__REACT_SOURCE__` for agents. |
| `preferCallSite` | `false` | Wrappers that spread props point to where they are used. |
| `excluded` | `[]` | Component or element names to skip, case-insensitive. |
| `libraries` | tag all | Tag only components imported from these packages (HTML elements are always tagged). |
| `root` | Babel's `cwd` | Base for the relative paths. An empty string keeps them absolute. |
