# Install

This repository is intended to be used as a project-local Codex plugin in another repository.

## Recommended setup

Add this repository to the consumer repository as a Git submodule at:

```bash
git submodule add https://github.com/segterra/codex-dev-plugin.git plugins/segterra-dev-plugin
```

The resulting layout in the consumer repository should be:

```text
<consumer-repo>/
  .agents/
    plugins/
      marketplace.json
  plugins/
    segterra-dev-plugin/
      .codex-plugin/plugin.json
      skills/
```

## Marketplace registration

In the consumer repository, create or update:

```text
.agents/plugins/marketplace.json
```

Add the following entry to the `plugins` array:

```json
{
  "name": "segterra-dev-plugin",
  "source": {
    "source": "local",
    "path": "./.plugins/segterra-dev-plugin"
  },
  "policy": {
    "installation": "AVAILABLE",
    "authentication": "ON_INSTALL"
  },
  "category": "Productivity"
}
```

If the consumer repository does not yet have a marketplace file, use:

```json
{
  "name": "private-plugins",
  "interface": {
    "displayName": "Segterra Private Plugins"
  },
  "plugins": [
    {
      "name": "segterra-dev-plugin",
      "source": {
        "source": "local",
        "path": "./.plugins/segterra-dev-plugin"
      },
      "policy": {
        "installation": "AVAILABLE",
        "authentication": "ON_INSTALL"
      },
      "category": "Productivity"
    }
  ]
}
```

## Notes

- Keep `marketplace.json` in the consumer repository, not in this plugin repository.
- The consumer repository owns plugin ordering and any future additional plugin entries.
- If the submodule path differs, update the marketplace `source.path` to match it.

## Private distribution

This plugin is intended for private internal use.

- Do not publish this plugin or its repository to a public marketplace.
- Do not add public URLs to the plugin manifest unless they are intentionally public.
- Keep any future documentation, assets, or configuration files free of secrets, tokens, or internal endpoints.
- If a private marketplace or catalog is introduced later, use private repository access controls and authenticated distribution.
