---
name: react-source-string
description: Find the file and line that rendered an element on a React page in development, and set up the react-source-string plugins (Vite, Next.js/SWC, Babel) that tag JSX with data-source="path:line". The lookup needs one of these plugins in the project's dev build; without it the skill offers to install the plugin first. Use when the user points at something on the page ("fix this button", "change the text in the checkout card"), pastes a reference like `src/pages/Checkout.tsx:42 <button> "Pay"`, when the DOM has data-source attributes or window.__REACT_SOURCE__ exists, or when the user asks to add source locations, Alt+click-to-source or the overlay to a React project.
license: MIT
compatibility: Requires a React project with one of the react-source-string build plugins enabled in development (vite-plugin-react-source-string, swc-plugin-react-source-string for Next.js, or babel-plugin-react-source-string). Installing one needs npm access and a change to the bundler config.
---

# Find the code behind a React element

The react-source-string plugins tag every JSX element in development with `data-source="path:line"`: the file and the line of the element's opening `<`, relative to the project root. With the `overlay` option on, the page also gets Alt + click to copy a reference and a `window.__REACT_SOURCE__` lookup API.

## Requires a plugin in the project

This skill does not work on its own. The `data-source` attributes, Alt + click and `__REACT_SOURCE__` come from a build plugin, so the project must have one of them enabled in its dev build:

| Bundler | npm package |
| --- | --- |
| Vite | `vite-plugin-react-source-string` |
| Next.js (SWC) | `swc-plugin-react-source-string` |
| Babel (webpack, Rspack, Next.js with a Babel config, Metro) | `babel-plugin-react-source-string` |

Check before the first lookup in a project: look for the package in `package.json` `devDependencies` and in the bundler config. If it is missing, tell the user that the source locations need the plugin, and offer to set it up with [Set up the plugins](#set-up-the-plugins) below. Install it only after the user agrees: it adds a dev dependency and changes the bundler config. Until then, fall back to searching the repository and say that the result is a guess.

Use the source location instead of searching the repository for the text you see on the page. A text search finds every "Pay" in the codebase; `data-source` names the one that is on the screen.

## The user pasted a reference

Alt + click copies text in this shape:

```text
src/pages/Checkout.tsx:42 <button> "Pay"
inside: src/pages/Checkout.tsx:30 ← src/App.tsx:12
```

1. Open the first path at that line. The tag and the quoted name (visible text, `aria-label`, `alt` or `title`) tell you which JSX element it is.
2. The `inside:` line lists the tagged parents, innermost first. Edit a parent when the change is about the surroundings: layout, spacing, the list the item is in, the condition that shows the element.
3. A bare `path:line` copied from DevTools means the same as the first line.

## You can see the page in a browser

Accessibility snapshots (Playwright MCP, Claude in Chrome and other agent browsers) do not include `data-*` attributes, so `data-source` is not in what you read. Run JavaScript in the page instead (`browser_evaluate`, a JavaScript tool, or the DevTools console):

```js
__REACT_SOURCE__.find("Pay");
// [{ source: "src/pages/Checkout.tsx:42", tag: "button", text: "Pay" }]
```

- Pass the name from the snapshot: `button "Pay"` → `find("Pay")`, `img "Red sneakers"` → `find("sneakers")`. The match is a case-insensitive substring of the element's name: `aria-label`, visible text, `alt` or `title`, or the label of an image or icon inside it.
- `find` returns only the innermost matches, so a card that contains the button is not listed next to the button.
- `__REACT_SOURCE__.of(target)` returns `path:line` for an element or a CSS selector, `stack(target)` returns it plus the parents' locations, and `describe(target)` returns the text Alt + click copies.

If `__REACT_SOURCE__` is undefined but the attributes are there, the overlay is off. Read the attribute directly:

```js
document.querySelector("form button")?.closest("[data-source]")?.getAttribute("data-source");
```

If the page has no `[data-source]` elements at all, the plugin is not installed or this is a production build. Say so, and offer the setup below.

## Pick the right line

- **Several matches.** Narrow down by `tag` and `text`, or run `of()` on a more specific selector.
- **The path points to a shared component** (`src/components/Button.tsx:2`) and the user means one usage, such as "this button on the checkout page". Edit the call site, not the component. The call site is a later entry in `stack()`. If the project uses shared wrappers a lot, suggest `preferCallSite: true` (see setup). Edit the shared component only when the user wants every instance to change.
- **Elements from `node_modules` are not tagged.** The nearest tagged ancestor is the code in the project that renders the library component.
- **Line numbers come from the running dev build.** After you edit a file, reload the page or wait for HMR before you look up the same element again.

## Set up the plugins

Pick the plugin by the project's bundler and follow its reference:

| Project has | Reference |
| --- | --- |
| `vite.config.*` (Vite apps and Vite-based frameworks) | [setup-vite.md](references/setup-vite.md) |
| `next.config.*` without a Babel config (Next.js compiles with SWC) | [setup-swc.md](references/setup-swc.md) |
| Babel: `babel.config.*` or `.babelrc` (also Next.js with a Babel config), webpack or Rspack with `babel-loader` | [setup-babel.md](references/setup-babel.md) |

In every setup:

1. Enable the plugin in development only. The overlay code must not reach production.
2. Turn on `overlay: true` so the user gets Alt + click and you get `__REACT_SOURCE__`.
3. Turn on `preferCallSite: true` when the app renders shared wrappers (`<Button>`, `<Card>`) that spread their props onto a DOM element. References then point to the page that uses the wrapper.
4. Add this rule to the project's `AGENTS.md` or `CLAUDE.md` (create `AGENTS.md` if neither exists):

   ```md
   The dev build tags every JSX element with `data-source="path:line"`.
   To find the code behind something on the page, run `__REACT_SOURCE__.find("<visible text or label>")`
   in the browser (or read the element's `data-source`) and open that file at that line
   instead of searching the repository.
   ```

5. Check it: start the dev server, open a page and run `document.querySelectorAll("[data-source]").length` (more than 0) and `typeof __REACT_SOURCE__` (`"object"`).

Do not put the source location into `title`, `aria-label`, `aria-description` or other accessibility attributes to make it visible to agents. `title` can replace an element's accessible name, which breaks screen readers and role-based tests such as `getByRole("button", { name: "Pay" })`.
