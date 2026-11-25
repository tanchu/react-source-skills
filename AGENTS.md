# Working on react-source-skills

This repository holds one agent skill, `skills/react-source-string`, and the Claude Code plugin and marketplace manifests in `.claude-plugin/`. There is no code to build.

- The skill describes three plugins: tanchu/vite-plugin-react-source, tanchu/swc-plugin-react-source (active branch `next-16`) and tanchu/babel-plugin-react-source-string. Their READMEs are the source of truth for options, versions and the `__REACT_SOURCE__` API. When a plugin changes an option or the API, update `SKILL.md` and the matching `references/setup-*.md` in the same week.
- The Next.js version table in `references/setup-swc.md` must match the published npm versions: `npm view swc-plugin-react-source-string dist-tags versions`.
- Keep `SKILL.md` short: what the agent does on every use. Setup details for one bundler go in `references/`.
- The `name` in the `SKILL.md` frontmatter must equal the folder name. The `description` says when to load the skill and stays under 1024 characters.
- Before a commit run `claude plugin validate .`.
- Bump `version` in `.claude-plugin/plugin.json` when the skill changes, so Claude Code users get the update.
