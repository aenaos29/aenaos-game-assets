# Aenaos Game Assets v0.01 — verification and handoff

## Completed

- Created the public [aenaos29/aenaos-game-assets](https://github.com/aenaos29/aenaos-game-assets) repository, including all asset directories, a versioned manifest and per-asset licensing documentation.
- Added the 466,364-byte Avocado GLB. Microsoft is credited by Khronos under CC0 1.0. The model retains the original mesh and transform with 682 triangles, three self-contained PNG textures downsampled from 2048 × 2048 to 512 × 512, and no external resource or decoder dependencies.
- Verified [the commit-pinned raw GitHub asset](https://raw.githubusercontent.com/aenaos29/aenaos-game-assets/453bc1b5e74056d18ef7c084be7583a85ebdbb8f/objects/avocado-v0.01.glb) with an anonymous HTTPS GET and the request header `Origin: https://aistudio.google.com`. The response returned HTTP 200 without redirects, `application/octet-stream`, wildcard CORS and `Cross-Origin-Resource-Policy: cross-origin`. All 466,364 downloaded bytes matched the local GLB and the manifest's SHA-256. Raw GitHub serves the integration asset.
- Inserted the verified URL into the [copy-ready Playground prompt](PLAYGROUND_PROMPT.txt).

## Playground acceptance

1. In Vesper Courtyard Preview, use the prompt and check that the textured Avocado appears on the chosen surface. Confirm the successful load in the console. Temporarily use an invalid URL to test the procedural fallback, then restore the verified URL.
2. Publish and verify the textured model in a fresh session. Compare movement, collisions and the fixed three-quarter camera with the baseline in both builds.
3. Run a cold load on the target mobile browser. Compare load time and visible stutter with the baseline and record the device and browser.

Preview, published build, gameplay regression checks and mobile performance remain pending.
