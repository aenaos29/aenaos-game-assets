# Aenaos Game Assets v0.01 — verification and handoff

## Completed

- Prepared the repository tree, versioned manifest, per-asset license notice, GLB and English Playground prompt.
- Verified Khronos's asset-specific notice credits Microsoft and identifies CC0 1.0.
- Reduced the three 2048 × 2048 textures to 512 × 512. The 466,364-byte GLB retains 682 triangles, all three embedded PNGs and the original node transform.
- Checked that the binary includes no external resource URI or required decoder extension.
- Retrieved the original upstream GLB over public HTTPS. Its raw GitHub response returned HTTP 200, binary content and wildcard CORS. That evidence applies to the upstream file.

## Resume

1. Create a public repository named `aenaos-game-assets` under `aenaos29` with the description “Reusable 3D models, textures and game assets for Aenaos projects.” Commit the contents at the repository root and include the empty-directory markers. The GitHub connector exposes file operations but lacks repository creation and binary upload. The browser reached GitHub sign-in.
2. Set up GitHub Pages from the repository root, or use the raw GitHub URL pinned to the commit that contains the GLB. Test the exact candidate URL using an anonymous GET with the actual Preview and published Origins when available. Require HTTPS, HTTP 200 with no redirect, binary content type, an applicable CORS header, and a SHA-256 match with the manifest. A browser GET of this asset should use no credentials or custom headers. Save the verified URL to `manifest.json` and replace the single placeholder in `tests/PLAYGROUND_PROMPT.txt`.
3. In Vesper Courtyard Preview, verify the textured GLB renders, the console identifies successful loading, and the prop sits clear of navigation. Use a temporary invalid URL to check that the fallback appears and the rest of the game remains playable, then restore the verified URL.
4. Publish the project and check the published URL from a fresh session. Compare movement, collisions and camera against the baseline. Repeat on the target mobile browser, tracking cold-load time and visible stutter during a baseline and asset run.

Hosting, Preview, published build, gameplay regression checks and mobile performance are pending. The manifest keeps the public URL empty until the Aenaos-hosted binary is verified.
