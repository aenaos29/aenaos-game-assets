# Aenaos Visual Engine v0.1

## Reusable specifications

- Three.js, React Three Fiber, Drei, Postprocessing and Rapier physics.
- Fixed Rapier timestep: 1/60s (60 Hz).
- ACES Filmic tone mapping with exposure approximately 0.88–0.90.
- PCFSoftShadowMap.
- Adaptive profiles: DPR [1, 1.35] mobile and [1, 1.75] desktop.
- Shadow map size 1024 mobile and 2048 desktop.
- Shadow frustum tight around visible camera focus.
- Procedural, cached PBR maps: diffuse, Sobel tangent-space normal, bump and roughness.
- Balanced directional solar key light following camera focus.
- Cool-sky ambient and hemisphere fill.
- Procedural sky dome and lightformer IBL reflections.
- Atmospheric fog with distant horizon haze cylinder.
- Baked architectural contact shadows and grounded character shadow discs.
- Bloom: luminanceThreshold 1.18, intensity 0.22.
- Light vignette.
- Mobile N8AO: half resolution and reduced samples, for subtle crevice depth without darkening.
- Hydro-Quantum Fountain: rotating gyro-ring core, animated droplets, Schlick Fresnel water reflections, dual-wave ripples, rim foam and solar specular highlights.

## Reusable material families

Composite titanium-terrazzo, conductive basalt-carbon, emerald bio-turf, pearl aero-ceramic, carbon-titanium armor, emerald-slate ceramic, anodized bronze-graphite, brushed white-silver aluminum, copper-carbon acoustic slats, tree bark, botanical foliage and striped solar-weave awning canvas.

## Veridian Plaza-specific decisions

The central fountain is placed in the plaza centre and is named Hydro-Quantum Fountain. The material families are assigned to the seven named buildings and street elements described in the handoff. These assignments are design decisions, not independently validated reusable defaults.
