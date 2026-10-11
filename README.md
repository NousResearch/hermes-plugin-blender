> [!IMPORTANT]
> **This repo has moved.** The plugin now lives in [`NousResearch/hermes-official-plugins/blender`](https://github.com/NousResearch/hermes-official-plugins/tree/main/blender), the single repo for Nous's official Hermes plugins. Its history, open issues and pull requests went with it, and this repo is archived.
>
> Already installed? `hermes plugins update` moves you to the new home automatically. New install: `hermes plugins install` from the plugin catalog.

# Blender integration for Hermes

A portable Hermes plugin containing MCP launch configuration and a workflow skill for the [official Blender Lab MCP server](https://www.blender.org/lab/mcp-server/).

## Install

```sh
hermes plugins install NousResearch/hermes-plugin-blender
hermes plugins enable blender
```

Choose the destination profile with the global `hermes -p <profile>` option. Catalog installation by the short name `blender` becomes available only after its catalog entry is merged and published.

## Prerequisites

- Blender 5.1 or newer on the same machine as the Hermes backend. Hermes checks for it before installing and refuses the install when it is missing: on Windows the `Blender` uninstall entry's install folder or `%ProgramFiles%/Blender Foundation/Blender */blender.exe`; on macOS `Blender.app` in `/Applications`, `~/Applications` or the Steam library; on Linux `blender` on `PATH`, the `org.blender.Blender` flatpak, the `blender` snap or the Steam library (native or flatpak Steam). Linux has no version source, so the 5.1 minimum is checked on Windows and macOS only. This needs a Hermes release that reads location lists in `app:` declarations.
- The official Blender Lab MCP add-on installed and enabled in Blender, with Online access enabled and its bridge started on loopback port 9876.
- Git and uv available to the backend. Network access is required for the initial MCP environment setup.
- Optional, for the `*_for_cli` tools that open a saved `.blend` file in a separate background Blender: a `blender` command on the backend's `PATH`. `BLENDER_PATH` set in a shell or `.env` does not reach the server.

See [setup and troubleshooting](skills/blender/references/setup.md). Installing this package does **not** install Blender, change Blender preferences, install its add-on, or open a scene.

## Prepare the server before first activation

The first upstream download/build took several minutes in our Windows checks. Warm its isolated uv environment before asking Hermes to connect, so a normal MCP connection timeout does not interrupt the download:

```sh
uv tool run --from "git+https://projects.blender.org/lab/blender_mcp.git@2cea8d566dde07fbac28a61d698909d69724e853#subdirectory=mcp" blender-mcp --help
```

Restart Hermes, or use the explicit plugin/MCP reload commands supported by your release. The package's skill is available through `skills_list` and its qualified name via `skill_view`. Verify a real `get_objects_summary` response before treating Blender as ready.

## What runs

Hermes launches the official server over stdio with the same pinned source as the preparation command. The MCP server connects to the Blender add-on over `127.0.0.1:9876`. No native Hermes Python hooks, tool overrides, desktop JavaScript or self-updater are included.

The upstream revision is fixed to `2cea8d566dde07fbac28a61d698909d69724e853` (v1.0.3). uv resolves its Python dependencies in an isolated tool environment; those transitive dependencies are not fully locked by this package.

## Security and licensing

Blender Lab warns that its tools execute arbitrary Python in Blender without protection against file deletion or data exfiltration. Use only with trusted workflows, keep the bridge loopback-only, and prefer a disposable scene or isolated machine for experiments. This is not a sandbox.

This uses Blender Lab's source on `projects.blender.org`, not the unrelated PyPI package with the same `blender-mcp` name. Do not substitute an unqualified package install.

The wrapper configuration and original documentation are MIT-licensed. The external Blender Lab server and add-on remain GPL-3.0-or-later, owned by their respective authors. No Blender Lab code is vendored here.

## Verified scope

See [verification](VERIFICATION.md). Headless scene read/create/transform/readback/delete passed on Windows ARM64 and x64 with Blender 5.2.2 LTS. Graphical screenshots, deferred operations and liveness detection (whether Blender and its bridge are running) are not verified or implemented by this wrapper. MCP tool listing alone does not establish scene readiness.
