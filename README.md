# react-source-skills

An agent skill for the react-source-string plugins: [Vite](https://github.com/tanchu/vite-plugin-react-source), [Next.js / SWC](https://github.com/tanchu/swc-plugin-react-source) and [Babel](https://github.com/tanchu/babel-plugin-react-source-string).

The plugins tag every JSX element in development with `data-source="path:line"`. With the `overlay` option, Alt + click copies a reference for the prompt, and `window.__REACT_SOURCE__` lets agents look sources up from the page. Browser agents read the accessibility tree and never see `data-*` attributes, so they need to be told how to use this. The skill teaches them to:

- open the right file and line when you paste an Alt + click reference;
- call `__REACT_SOURCE__.find("Pay")` in the browser instead of searching the repository for "Pay";
- pick the call site rather than a shared `<Button>` component that the whole app uses;
- install and configure the right plugin for Vite, Next.js or Babel, development only, with the overlay on.

## Install

Any agent that supports [Agent Skills](https://agentskills.io) (Claude Code, Cursor, Codex, GitHub Copilot and others):

```bash
npx skills add tanchu/react-source-skills
```

Claude Code, as a plugin:

```text
/plugin marketplace add tanchu/react-source-skills
/plugin install react-source-string@react-source-skills
```

Or copy [`skills/react-source-string`](skills/react-source-string) into your agent's skills folder (`.claude/skills/` for Claude Code).

## Without the skill

Add this to your project's `AGENTS.md` or `CLAUDE.md`. It covers the lookup, but not the setup:

```md
The dev build tags every JSX element with `data-source="path:line"`.
To find the code behind something on the page, run `__REACT_SOURCE__.find("<visible text or label>")`
in the browser (or read the element's `data-source`) and open that file at that line
instead of searching the repository.
```

## What's inside

```text
skills/react-source-string/
  SKILL.md                    # finding the code behind an element, picking the right line, setup checklist
  references/setup-vite.md    # vite-plugin-react-source-string
  references/setup-swc.md     # swc-plugin-react-source-string, Next.js version table
  references/setup-babel.md   # babel-plugin-react-source-string
.claude-plugin/               # Claude Code plugin and marketplace manifests
```

Issues and PRs are welcome. If the plugins or the skill save you time, a ⭐ on GitHub helps other people find them.

## License

MIT
