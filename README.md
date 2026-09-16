# ccg-workflow

CCG's internal Claude Code plugin marketplace.

| Plugin | For |
|---|---|
| [`ccg-ba`](plugins/ccg-ba) | The BA → developer specification workflow, backed by the shared Obsidian vault |

## Install

From an interactive `claude` session:

```
/plugin marketplace add C:\Users\hieu.luu\Downloads\Project\ccg-workflow
```

```
/plugin install ccg-ba@ccg-workflow
```

Push this repo to a git host and swap the local path for `owner/repo` so the team installs the
same way.

See [plugins/ccg-ba/README.md](plugins/ccg-ba/README.md) for setup and usage.

## Related

- Vault: https://github.com/lthieu8/ccg-vault
- Upstream this adapts from: [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — see [NOTICE.md](NOTICE.md)

`.bmad-upstream/` is a local shallow clone of BMAD kept for reference when re-deriving adapted
content. It is gitignored and not part of the plugin.
