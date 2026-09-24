# Changelog

This is the pending release-note queue for the next FnQuake3 release.

Keep short user-facing bullets under `Unreleased` as changes land. During release publishing, the workflow asks GitHub Copilot to dedupe and categorize the notes for the GitHub release details, then clears this section for the next cycle.

## [Unreleased]

### Highlights
- _None yet._

### Compatibility
- _None yet._

### Rendering and Display
- World dlight sidecars can now fade a light out over an authored `fadeStart`/`fadeEnd` viewer-distance band instead of popping when the light loses the per-view budget or leaves the PVS. Omitting both keys keeps the previous behaviour, so existing `.dlight` files are unaffected.
- Added `r_dlightDebugDraw`, a wireframe overlay showing each world dlight's origin, reach, and spot cone for the lights in the current PVS, with unpromoted lights greyed out, plus an `r_dlightWorldStatus` command that prints every gate a world dlight has to clear and where the current map's lights are being dropped. Both are cheat-protected and available on the OpenGL and Vulkan renderers.

### Audio
- _None yet._

### Builds and Packaging
- _None yet._

### Fixes
- Removed the engine's `0.1`–`10` mouse sensitivity limit. Set values outside that range with the `sensitivity` console command or in a config file.
- Generated world dlights washed areas out: every proxy emitted at full strength regardless of its `q3map_surfaceLight` value, double-counting light the map already has baked in. Proxy radiance is now relative to the authored emission and capped, tunable with the new `r_surfaceLightProxyRadiance` (default `0.15`), and proxy reach is capped at 1024 units.
- Generated world dlights came out white. A shader that declares only `q3map_surfaceLight` — which is most retail light shaders — fell through to the lightmap and vertex averages, both blown out on a light panel. The emitter's own texture is now averaged for its color the way q3map2 does.
- Dynamic-light shadows were culled on whether the light's own origin projected onto the screen, so a shadow blinked out whenever its light passed behind or beside the camera — exactly when that light throws its longest shadows into view. Culling now tests the light's volume against the frustum.
- Models rejected by the view frustum were dropped before they could enter the shadow atlas, so a player or item walking off-screen took its shadow with it. A model a shadow-casting light still reaches is now kept as a shadow-only caster, across the MD3, MDR, and IQM paths.
- Generated world dlights sat 8 to 64 units off their emitting surface; they now sit 4 units off it, so the apparent source stays on the light fixture.
- Generated world dlights were placed *behind* their emitting surface, inside the brush the surface belongs to, where they lit nothing and cast no shadows. The proxy builder took its facing from the triangle winding cross product, which is the negation of a Quake III face's outward normal; it now takes the facing from the surface's own authored normal, and backs an origin out of solid space if it still lands there. Spot direction, hemisphere weighting, and spot-shadow frustums were all inverted by the same bug. Existing sidecars need `r_dlightGenerateWorld force` to pick this up.
- `r_dlightGenerateWorld` wrote every surface light as a spotlight, so baking a `.dlight` sidecar silently stripped the cube shadow from every generated point light. Generated entries now carry each light's own point/spot classification, its derived cone angle, its shadow-casting flag, a shadow-map resolution scaled to the light's reach, and the emitting surface's shader name; truncation to the 256-light format limit is now reported instead of silent.
- The Windows system console stayed blank for the whole of startup, flashing its accumulated text only once as the main window came up. Console output is batched behind a Win32 timer, and no message pump runs during `Com_Init`, so nothing drained it; startup diagnostics are now flushed synchronously and batching resumes once the game is up.
- Fixed an unbounded `strncat` in the Windows system console's input line: the remaining-space argument underflowed to `SIZE_MAX` once the pending-command buffer filled, letting a submitted command write past it.

### Documentation and Tooling
- _None yet._
