# Private Distribution

This plugin is intended for private internal use.

Recommended ways to reuse it in other projects:

1. Keep it in a private Git repository and include it in consumer projects as a submodule or subtree under `plugins/segterra-dev-plugin`.
2. Store it in a private local plugins directory such as `~/plugins/segterra-dev-plugin` and reference it from a home-local plugin catalog when needed.

Security guidance:

- Do not publish this plugin or its repository to a public marketplace.
- Do not add public URLs to the plugin manifest unless they are intentionally public.
- Keep any future documentation, assets, or configuration files free of secrets, tokens, or internal endpoints.
- If a private marketplace or catalog is introduced later, use private repository access controls and authenticated distribution.

Recommended cross-project layout:

- Plugin path: `plugins/segterra-dev-plugin`
- Manifest path: `plugins/segterra-dev-plugin/.codex-plugin/plugin.json`
- Skills path: `plugins/segterra-dev-plugin/skills/`
