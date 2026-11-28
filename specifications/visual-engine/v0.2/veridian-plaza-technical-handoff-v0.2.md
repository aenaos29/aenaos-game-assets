# Veridian Plaza Technical Handoff v0.2

## Part A — UAHP Reproducibility Test — Handoff v0.1 Feedback

The original Veridian Plaza handoff was supplied to a new, independent Playground project to evaluate its ability to reproduce the original prototype.

The result demonstrates partial functional reproduction but substantial visual degradation.

### Positive findings

- The third-person camera behaves more consistently.
- Touching or dragging empty screen areas no longer causes unwanted camera movement, according to user testing.
- The single movement joystick and JOG/ACT controls were preserved.
- The minimal interface layout was reproduced.
- The futuristic plaza concept and UNION Café identity were retained.

### Visual reproduction failures

1. Android characters: excessively simplified geometric anatomy, primitive limbs, poor articulation detail, unrealistic proportions, and loss of the original character quality.
2. Materials: excessive reflections, unnatural surface appearance, and insufficient visual distinction between architectural materials.
3. Lighting: overexposed surfaces, excessive contrast, and loss of balanced cinematic daylight.
4. Ground surfaces: strong multicolored visual noise and unnatural texture appearance.
5. Architecture: simplified building geometry, reduced facade detail, and loss of architectural depth.
6. Vegetation: primitive geometric trees and simplified decorative elements.
7. Overall visual fidelity: the result resembles a basic low-poly prototype rather than the intended detailed cinematic environment.
8. Orientation: the supplied screenshot shows portrait presentation despite the original 16:9 landscape requirement.

### Required analysis

The v0.2 handoff makes the previously implicit implementation details explicit, including:

- character geometry, anatomical proportions, mesh construction, and articulation;
- material algorithms and numerical parameters;
- texture resolution, filtering, color space, and normal-map intensity;
- lighting intensities, exposure, reflection strength, and shadow configuration;
- architectural geometry and dimensional relationships;
- vegetation geometry and procedural generation;
- camera behaviour and touch input handling;
- rendering configuration and post-processing order;
- module responsibilities and implementation dependencies.

The objective is to improve knowledge transfer through UAHP, not to repair or replace the original project. Confirmed details, recommendations and assumptions must remain distinguishable. Reproducibility has not been independently validated.

---

## Part B — Veridian Plaza Technical Handoff v0.2

Build a single-player 3D futuristic exploration prototype titled "Veridian Plaza" using React, TypeScript, Three.js, React Three Fiber ("@react-three/fiber"), "@react-three/drei", "@react-three/postprocessing", and "@react-three/rapier" with a fixed "1 / 60" s, 60 Hz timestep. Prioritize cinematic visual fidelity, PBR material accuracy, layered architecture, and smooth traversal. Do NOT add storylines, combat, timers, investigation loops, or building interiors.

### 1. Module architecture and dependency graph

Use a strict acyclic module structure:

- "constants.ts": exports "THEME", "CONFIG" via "getConfig()", "BUILDINGS" with 7 specs, "INTERACTABLES", and "BYPASSERS".
- "aenaos/rendering.ts": "getAenaosRenderProfile(isMobile)" and "configureAenaosRenderer(renderer, exposure)".
- "aenaos/materials.ts": "getAenaosMaterials()" returning cached "PbrMaterialMaps" and building signs.
- "aenaos/lighting.tsx": "AenaosLightingRig", "AenaosSunLight", "CharacterContactShadow", "AenaosHazeWall", "AenaosPostProcessing".
- "aenaos/water.tsx": "AenaosWaterSurface" with custom GLSL Fresnel/ripple fountain shader.
- "aenaos/architecture.tsx": glass panes, extruded arches and pediments, fluted columns, cornice moldings, recessed windows and entrances.
- "aenaos/vegetation.tsx": broadleaf trees, cypress trees and flower urns.
- "aenaos/corridors.ts": "getValidatedPlazaLayout()" and spatial validators.
- "aenaos/index.ts": re-exports all Aenaos modules.
- "components/ExplorerCharacter.tsx" and "components/PedestrianNPC.tsx": articulated android rigs.
- "components/Buildings.tsx" and "components/PlazaEnvironment.tsx": scene assembly.
- "Game.tsx": Canvas, lighting, Rapier "timeStep={1/60}", player rig, camera and post-processing.
- "App.tsx": full-screen 16:9 landscape enforcement, HUD, interaction messages, one mobile joystick, JOG/ACT buttons, menus and audio.

### 2. Viewport and 16:9 landscape enforcement

Enforce the landscape presentation in "App.tsx"; do not rely on metadata alone. Preserve a 16:9 frame with letterboxing or display a clean rotate-device overlay when "window.innerHeight > window.innerWidth". Attempt "screen.orientation?.lock?.('landscape')" on supported mobile devices.

Do not pass inline object literals to "<Canvas camera={...} />", "dpr" or "fog" "args"; use module-level constants or memoized values.

### 3. Rendering, lighting, exposure and post-processing

Renderer:

- "THREE.ACESFilmicToneMapping"
- exposure: mobile 0.88, desktop 0.90; keep <= 0.90
- PCFSoftShadowMap
- "THREE.SRGBColorSpace"

Profiles:

- Mobile: DPR [1, 1.35], shadow map 1024, shadow extent 28, environment resolution 128, contact shadows 256, bloom samples 0.
- Desktop: DPR [1, 1.75], shadow map 2048, shadow extent 32, environment resolution 256, contact shadows 512, bloom samples 0.

Lighting:

- background/fog "#c9dceb", fog near 44 and far 140;
- Sky distance 450000, sunPosition [56,20,38], turbidity 3.4, rayleigh 1.35, mieCoefficient 0.0045, mieDirectionalG 0.8;
- Lightformers: circle intensity 2.6 color "#ffeed1" scale 14 at [24,18,16]; rect intensity 1.15 color "#9ac2eb" scale [42,20,1] at [0,18,0]; rect intensity 0.65 color "#b29a80" scale [42,42,1] at [0,-6,0];
- ambient "#89a6c4" intensity 1.5;
- hemisphere "#86bdf0", ground "#5d6b7a", intensity 0.9;
- dynamic sun "#fff1dc", intensity 3.1, shadow near 1, far 90, bias -0.00035, normalBias 0.025, following the camera target with offset [24,30,17];
- ContactShadows position [0,0.013,0], opacity 0.32, scale 68, blur 1.85, far 4.5, resolution profile value, frames 1, color "#181c22";
- haze wall radius 74, height 28, color "#c9dceb", open cylinder, alpha gradient 1 -> 0.25 -> 0, fog false, depthWrite false;
- entrance point lights intensity 7.5 when highlighted, otherwise 3.6–4.2, distance 8.5–9.5, decay 1.6–1.7.

Post-processing order, multisampling 0:

1. N8AO: halfRes mobile, aoRadius 1.1 mobile/1.15 desktop, distanceFalloff 0.85, intensity 1.05/1.15, aoSamples 6/12, denoiseSamples 2/4, color "#1b222c".
2. Bloom: luminanceThreshold 1.18, luminanceSmoothing 0.85, intensity 0.22, mipmapBlur.
3. Vignette: eskil false, offset 0.24, darkness 0.22.

### 4. Procedural PBR materials and texture synthesis

Use seeded RNG "40219". Use RepeatWrapping and anisotropy 8. Diffuse maps and signs are 512x128 and use SRGBColorSpace. Normal, bump and roughness maps use NoColorSpace. Bind only map, normalMap and roughnessMap to meshStandardMaterial; do not attach bumpMap simultaneously with normalMap.

Generate Sobel normals from wrapped 3x3 grayscale height gradients, normalize (-dX * strength, -dY * strength, 1.0), and encode (n * 0.5 + 0.5) * 255.

Material specifications:

- travertine: 512x512, repeat (14,14), 6x6 grid, seam #4c5e70, cool-slate tone 186 ± 9, normalStrength 2.0, panel roughness 0.42, seam roughness 0.85, metalness 0.22, roughness 0.48.
- cobblestone: 512x512, repeat (10,10), 10x5 staggered slabs, seam #24313d, tone 78 ± 8, normalStrength 2.4, roughness 0.44, metalness 0.28–0.30.
- lawnTurf: 512x512, repeat (14,14), base #36654d, 14,000 muted green speckles only, normalStrength 1.4, roughness map #dadada, roughness 0.90, metalness 0.
- limestone: 512x512, repeat (2,2), 6x4 grid, seam #8d99a6, pearl tone 212 ± 5, normalStrength 1.9, metalness 0.14–0.18, roughness 0.48–0.50.
- slateGranite: 512x512, repeat (2,2), 8x4 staggered plates, seam #111827, tone 54 ± 7, normalStrength 2.6, metalness 0.48, roughness 0.42.
- redBrick: 512x512, repeat (3,3), 10x5 staggered panels, seam #435565, tone 104 ± 10, normalStrength 2.2, metalness 0.22, roughness 0.55.
- terracottaBrick: 512x512, repeat (3,3), 10x4 grid, seam #23272e, tone 92 ± 8, normalStrength 2.1, metalness 0.38, roughness 0.44.
- brushedAluminum: 512x512, repeat (2,2), #dce5ee, 2px horizontal brush lines, 4x4 seam grid, normalStrength 2.2, roughness map 0.28–0.39, metalness 0.50–0.52, roughness 0.32–0.34.
- walnutTimber: 512x512, repeat (2,1), 12 vertical slats, tone 96 ± 10, normalStrength 2.1, roughness map #686868, metalness 0.28, roughness 0.48.
- bark: 256x256, repeat (2,3), #4e4844 with #3b3735/#635c57 striations, normalStrength 2.4, roughness map #e2e2e2, roughness 0.90–0.92.
- foliageLeaf: 256x256, repeat (2,2), base #36684f, 900 rotated leaf ellipses, normalStrength 1.8, roughness map #c4c4c4, roughness 0.85.
- awningCanvas: 256x256, repeat (2,1), 8 alternating #0f766e/#f1f5f9 stripes, horizontal weave lines, normal strength 1.2.

### 5. GLSL Fresnel water

Use circleGeometry [2.78,48] at y=0.50, basin floor y=0.43. Use radial plus three sine/cosine octaves, finite-difference normal with eps 0.04, Schlick Fresnel 0.03 + 0.97 * pow(1.0 - max(dot(N,V),0.0),4.5), sky/horizon blend #e8decb to #90bfe8, deep/shallow tint #1a4e5e to #2c768a, solar specular power 140 multiplied by 1.65, rim/center foam <= 0.38, alpha 0.93.

### 6. Multilayered architecture and layout

All seven buildings face inward using rotY = atan2(-x,-z), with ground Y=0, central disc radius 11.6, outer promenade ring radius 17.8–20.6 and radial walkways width 3.9 with 0.16 curbs.

Use recessed windows with depth 0.22, jamb width 0.13, dark backplate #1f252c, glass roughness 0.05, metalness 0.22, clearcoat 1.0, envMapIntensity 1.45, mullions, sill height 0.14 and lintel/arch. Extruded arches and pediments use curveSegments 20, bevel enabled, bevelThickness 0.025, bevelSize 0.025. Fluted columns use plinth 0.98x0.36x0.98, shaft radii 0.34–0.28 and 8 ribs. Cornices use architrave, corona cap and 12–16 dentils. Entrances use twin glazed doors, kickplates, mullion, handles, sconces, portal pillars, lintel/arch and flush illuminated apron.

Building positions and dimensions:

1. Courthouse [0,0,-27], halfExtents [7.2,5.2], entranceLocalZ 6.4, body 14.2x9.2x9.6.
2. Police Station [-23,0,-19], halfExtents [6.0,4.6], entranceLocalZ 5.6, hull 11.8x7.6x8.6.
3. Research Laboratory [23,0,-19], halfExtents [6.2,4.8], entranceLocalZ 5.8, core 12.2x8.6x8.2, wing 12.6x3.6x9.2.
4. Office Building [-29,0,3], halfExtents [6.2,4.8], entranceLocalZ 5.8, tower 12.2x12.0x9.0.
5. Lounge [29,0,3], halfExtents [6.0,4.6], entranceLocalZ 5.6, pavilion 11.8x5.6x8.2.
6. School [-19,0,24], halfExtents [6.4,4.8], entranceLocalZ 5.8, hall 12.6x7.8x8.8.
7. UNION Café [19,0,24], halfExtents [5.8,4.6], entranceLocalZ 5.8, bistro 11.4x6.8x8.4.

The distinctive facade elements of each building remain project-specific design decisions, as specified in the original handoff.

### 7. Vegetation and protected corridors

Broadleaf trees use a root flare, trunk, 3 angled boughs, 6 asymmetrical icosahedron foliage clusters, bark/foliage PBR, stone curb radius 0.96–1.02 and soil disc. Cypress trees use a bark trunk and four cone foliage whorls at y=1.75, 2.75, 3.75 and 4.65.

Validate before placing any prop or tree: fountain clear radius 3.65m; spawn [0,10.2] clear 3.4m; central plaza 11.6m; promenade 17.6–20.8m; radial corridor half-width 2.45m; entrance clearance 4.25m; rotated building OBB plus 1.25m margin; pairwise obstacle footprint gaps. Use 28 interior trees seed 91042 and 28 perimeter cypresses radius 40.5m.

### 8. Articulated android rigs

Avoid blocky, primitive and low-poly characters.

ExplorerCharacter is authored facing -Z with boot soles at Y=0. Use pelvis y=0.86, belt y=0.91, torso y=0.90, chest carapace scale [1.1,1,0.8] and clearcoat 0.7, sternum reactor at z=-0.148, dorsal spine at z=+0.138 with 4 LEDs, pauldrons at ±0.19/0.46, head y=0.54, cranium scale [0.9,1.08,0.98], visor arc 0.64pi–1.36pi, temple pods, crown crest, multi-jointed arms and legs, strideFreq lerp(6.8,10.6,jogBlend), knee bend, torso lean/twist and grounded contact shadow.

Six NPCs are EL-7 Elena, MK-4 Marcus, NR-9 Noor, JL-2 Julian, CL-5 Claire and MT-3 Mateo. Use varied shell/frame/accent/LED colors, crest styles halo/antennae/visorCap/fin and props holopad/coffee/tote/briefcase. Kinematic Rapier capsules patrol walkable waypoints, pause and turn within 2.85m, and cycle through 3 dialogue lines.

### 9. Third-person camera, input and UI

Camera values: FOV 50, distance 5.4, pitch 0.20, shoulderOffset 0.45, PLAYER_LOOK_HEIGHT 1.48, cameraSmoothness 9.0 and perspectiveFollowRate 2.8.

Use ±30° slack, PERSPECTIVE_DEADZONE_RAD = degToRad(30), PERSPECTIVE_ALIGNED_RAD = degToRad(2.5), MAX_CAMERA_TURN_SPEED = 3.6. Latch lockedMoveYaw during backward/sideways movement using rawInputVec.y >= -0.15. Clamp against buildings with margin 0.45 and minAllowedT 1.6m.

For touch input, ignore touch pointers in mouse-drag orbit handling: if (e.pointerType === 'touch') return. Touching or dragging empty mobile screen areas must not move the camera. Render only one bottom-left movement joystick plus JOG and ACT buttons; no camera joystick.

The minimal UI has a top-left location badge, top-right Menu, and top-centre prompts, dialogue cards and scan captions.

## Status and evidence

The v0.2 document combines confirmed observations from the reproduction test with explicit implementation requirements and project-specific decisions. It is a technical handoff, not proof of implementation. Reproducibility remains unvalidated until an independent implementation is compared against the original using a documented test procedure.
