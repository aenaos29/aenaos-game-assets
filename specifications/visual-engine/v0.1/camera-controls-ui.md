# Camera, Controls & UI

## Reusable camera specification

- Automatic third-person rear over-the-shoulder follow camera.
- Approximately 50° FOV and 5.4 m distance.
- Ray-vs-OBB building-wall pull-in to prevent facade clipping.
- 30° left/right slack before smoothly turning behind the explorer.
- Latch movement reference heading during backward/sideways movement to prevent U-turn spinning while the camera follows behind.
- Optional desktop mouse-drag orbit and scroll-wheel zoom.

## Reusable controls

- WASD / Arrow keys: walk.
- Shift: jog.
- E / Space: speak or inspect entrances and landmarks.
- Touch devices: only one bottom-left movement joystick, plus JOG and ACT buttons. Never add a second camera joystick.

## Reusable minimal UI

Keep the 3D world unobstructed:

- top-left location badge;
- top-right Menu with Resume, Music/SFX toggles, Restart, How to Play and Quit;
- top-centre transient interaction prompts, citizen dialogue cards and architectural scan plaques.

## Veridian Plaza-specific decisions

The explorer, bypassers, entrances and landmarks are the interaction targets for this prototype. No implementation or independent validation is claimed.
