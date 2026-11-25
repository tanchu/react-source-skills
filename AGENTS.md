# Working on react-source-skills

This repository holds one agent skill, `skills/react-source-string`, and the Claude Code plugin and marketplace manifests in `.claude-plugin/`. There is no code to build.

- The skill describes three plugins: tanchu/vite-plugin-react-source, tanchu/swc-plugin-react-source (active branch `next-16`) and tanchu/babel-plugin-react-source-string. Their READMEs are the source of truth for options, versions and the `__REACT_SOURCE__` API. When a plugin changes an option or the API, update `SKILL.md` and the matching `references/setup-*.md` in the same week.
- The Next.js version table in `references/setup-swc.md` must match the published npm versions: `npm view swc-plugin-react-source-string dist-tags versions`.
- Keep `SKILL.md` short: what the agent does on every use. Setup details for one bundler go in `references/`.
- The `name` in the `SKILL.md` frontmatter must equal the folder name. The `description` says when to load the skill and stays under 1024 characters.
- Before a commit run `claude plugin validate .`.
- Bump `version` in `.claude-plugin/plugin.json` when the skill changes, so Claude Code users get the update.

## Releasing a new version

The skill is distributed in three places. A release updates all of them:

1. Bump `version` in `.claude-plugin/plugin.json`, run `claude plugin validate .`, commit and push to `main`.
2. Tag the commit `vX.Y.Z` (the same version) and create a GitHub release with the changes. `npx skills add` and the Claude Code marketplace read the repository, so this covers them.
3. Send the new version to [Agensi](https://www.agensi.io), where the skill is listed as "Find the Source Line of Any React Element". Agensi does not sync with GitHub. Zip the skill folder (`cd skills && zip -rX react-source-string.zip react-source-string`) and either upload it to the existing skill in the Agensi creator dashboard or call `update_skill` in the Agensi MCP with the release notes as the changelog. The new version goes through review; buyers keep the previous version until it is approved. If the change affects what the listing says (description, demo, FAQ, requirements), update the listing too.
