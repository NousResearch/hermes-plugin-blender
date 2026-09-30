# Verification

## Executed

Official Blender Lab source: `2cea8d566dde07fbac28a61d698909d69724e853` (v1.0.3).

| Check | Windows ARM64 | Windows x64 |
|---|---|---|
| Blender version | 5.2.2 LTS | 5.2.2 LTS |
| Isolated background add-on bridge | Pass | Pass |
| Exact package launcher, MCP initialize | Pass | Pass |
| MCP tools/list | 26 tools | 26 tools |
| Scene summary | Pass | Pass |
| Create scratch mesh and set location | Pass | Pass |
| Readback | Location `[1.5, 2.5, 3.5]`, four vertices | Same |
| Delete scratch object and confirm absent | Pass | Pass |
| Owned processes/tasks stopped; bridge port free | Confirmed independently | Confirmed independently |

Tests used factory-startup scenes and separate Blender user config/scripts/extensions directories. No existing user project or normal preferences were changed. The first ARM64 attempt had a syntax error in the test's deletion code; the server returned the error, the corrected run passed, and the leftover scratch object was removed separately.

The launcher came directly from mcp.json. Both JSON-RPC/MCP errors and the inner Blender response status were checked. This was a protocol client smoke, not an autonomous model run through the full Windows Hermes UI.

Separately on macOS: Hermes portable package loading discovered the companion skill and translated the MCP configuration; the translated launcher initialized and listed the tools. Catalog-admission validation is rerun against the catalog PR's Hermes checkout.

## Limits

- Background mode does not test interactive screenshots, viewport rendering or deferred completion.
- Add-on setup used a scratch profile. Ordinary users must still follow Blender Lab's installation instructions.
- Full Windows Hermes install/activation and profile isolation were not established by the protocol smoke alone.
- The first uv download/build took several minutes. README preparation avoids relying on a short MCP discovery deadline.
- MCP serverInfo reported `1.30.0`; the server source is identified by the immutable Git revision above, not that advertised string.
- `plugin.json` declares Blender's presence and version (5.1+, Windows and macOS). No liveness gate is declared: connection failure reports an unavailable bridge; it does not prove why Blender is unavailable.
- Presence was checked on macOS with Blender 5.2.1 in `/Applications`. The Windows and Linux locations are not yet checked on a real machine.

Machine-specific raw logs and remote harnesses are retained privately and excluded from this repository.
