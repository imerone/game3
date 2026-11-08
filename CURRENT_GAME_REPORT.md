# THE LAST TORCH — Current Game Report

Report date: 2026-11-08  
Last successful live Unity verification: 2026-11-08 (environment/character pass)  
Unity version: Unity 6.6.0 (6000.6.4f1)  
Main scene: [SampleScene.unity](./Assets/Scenes/SampleScene.unity)

## Executive summary

THE LAST TORCH is a playable single-scene horror vertical slice with a real dungeon asset layer, imported skeleton animation, torch particles, audio, NavMesh, doors, inventory, HUD, and runtime checkpoint flow. It is not yet a finished multi-level release build.

The core loop exists:

1. Explore the dark labyrinth.
2. Use the torch while fuel drains.
3. Find the Archive Key.
4. Open and close doors.
5. Avoid the HollowStalker.
6. Use the locked door to reach deeper rooms.

The remaining gaps are fresh Play Mode verification after the latest runtime additions, a larger authored level sequence, and final production QA/build validation.

## Current scene structure

The active scene contains approximately 112 serialized GameObjects and currently includes:

- 62 renamed wall objects: `wall1` through `wall62`.
- `Ground_Floor` and `Dungeon_Ceiling`.
- `Capsule` player with first-person camera.
- `HorrorExpansion` room/door group.
- Three rooms:
  - `Room_01_Archive`
  - `Room_02_Observation`
  - `Room_03_RedHall`
- Three doors:
  - `Door_01_Entrance`
  - `Door_02_RoomLink`
  - `Door_03_RoomLink`
- `Key_Archive` pickup.
- One `HollowStalker` enemy prototype.
- `DungeonTorches` and several flickering environmental lights.
- `HorrorHUDCanvas` with objective text, torch fuel text, and crosshair.
- `HorrorAudioManager`.
- `DungeonNavigation` with a baked `NavMeshSurface`.

## Implemented gameplay systems

### Player

- First-person movement and mouse look through [FPSController.cs](./Assets/Scripts/FPSController.cs).
- Player object: `Capsule`.
- Main camera under the player.
- Sprint/crouch/jump-related movement fields exist in the controller.
- Player health and respawn through [PlayerSurvival.cs](./Assets/Scripts/PlayerSurvival.cs).

### Torch

[PlayerTorch.cs](./Assets/Scripts/PlayerTorch.cs) supports:

- `F` toggle.
- Fuel starting at 100%.
- Fuel drain while the torch is on.
- Low-fuel flicker.
- Warm spot light with shadows.
- Runtime light fallback if a light reference is missing.
- Torch HUD text.

The scene also contains torch visual/light children under the main camera. `PlayerTorch_FireParticles` and `PlayerTorch_SmokeParticles` are controlled by [PlayerTorch.cs](./Assets/Scripts/PlayerTorch.cs), and runtime fuel pickups are created by [HorrorGameDirector.cs](./Assets/Scripts/HorrorGameDirector.cs).

### Doors and keys

[HorrorDoor.cs](./Assets/Scripts/HorrorDoor.cs) supports:

- Smooth opening and closing.
- Player-relative opening direction.
- Closed-door collision.
- Optional collider disabling while open.
- Locked state.
- Required key ID.
- Optional custom audio clips.

[DoorInteractor.cs](./Assets/Scripts/DoorInteractor.cs) raycasts from the player camera and uses `E`.

Current progression:

- `Door_01_Entrance`: normal door.
- `Door_02_RoomLink`: locked with `archive_key`.
- `Door_03_RoomLink`: normal room-link door.
- `Key_Archive`: adds `archive_key` to [PlayerInventory.cs](./Assets/Scripts/PlayerInventory.cs).

The interaction prompt is part of the existing HUD Canvas. The new runtime pause/checkpoint menu is intentionally separate so it cannot break the current Canvas references.

### Monster

[HollowStalker.cs](./Assets/Scripts/HollowStalker.cs) currently contains:

- Patrol state.
- Investigate state.
- Chase state.
- Attack state.
- Line-of-sight detection.
- Player damage.
- Periodic growl event.
- NavMeshAgent integration.

`DungeonNavigation` now has a baked NavMesh and the stalker has a configured NavMeshAgent. Runtime checks confirmed that the agent can be placed on the NavMesh and enter the Attack state at close range.

The imported Dungeon Skeleton prefab is attached beneath the AI root. [HollowStalker.controller](./Assets/Animations/HollowStalker.controller) drives Idle, Walk, Run-speed, and Attack states from the existing AI.

### HUD

[HorrorHUD.cs](./Assets/Scripts/HorrorHUD.cs) currently displays:

- Objective text.
- Torch fuel percentage.
- Crosshair.
- Health bar and death overlay, when the existing Canvas references are present.
- Runtime status messages for checkpoints, fuel, and horror beats.

Still missing are a level-complete screen and a settings/options screen.

## Audio implementation

[HorrorAudioManager.cs](./Assets/Scripts/HorrorAudioManager.cs) was added as a centralized runtime audio layer.

The active scene was assigned ten real imported clips:

| Event | Imported clip |
|---|---|
| Exploration ambience | `Caves and Dungeons/The Labyrinth/The_Labyrinth_Loop A.wav` |
| Tension ambience | `Horror Elements/Ambient/Amb_claustrophobia.wav` |
| Door open | `Horror Elements/Hits/Hit_Metal.wav` |
| Door close | `Horror Elements/Hits/Hit_metalic.wav` |
| Locked door | `Horror Elements/Hits/Hit_Dark.wav` |
| Torch ignite | `Horror Elements/Ambient/Amb_Burn.wav` |
| Torch extinguish | `Horror Elements/Misc/Misc_Shh.wav` |
| Key pickup | `Horror Elements/Hits/Hit_Piano.wav` |
| Monster growl | `Monster Roars/21. Lurking Growl.wav` |
| Stinger | `Horror Elements/Hits/Hit_stress.wav` |

The audio smoke test confirmed that the manager created runtime sources and that door, torch, and locked-door events produced the expected clips.

There is not yet an Audio Mixer asset with separate Master/Music/SFX groups. The current audio layer uses runtime AudioSources directly.

## Imported assets found

The following packages are present in the project:

- [KatsBits-fantasy-base](./Assets/KatsBits-fantasy-base/): 346 prefabs, including walls, arches, doors, iron gates, torches, and lanterns.
- [DungeonCharacters](./Assets/DungeonCharacters/): Dungeon Skeleton model, prefab, and walk/idle/attack animations.
- [LowPolyMedievalPropsLite](./Assets/LowPolyMedievalPropsLite/): 48 medieval prop prefabs including barrels, candles, fire, furniture, and wood pieces.
- [Caves and Dungeons](./Assets/Caves%20and%20Dungeons/): 42 ambience/music WAV files.
- [Horror Elements](./Assets/Horror%20Elements/): 45 horror ambience/effect WAV files.
- [Monster Roars](./Assets/Monster%20Roars/): 25 roar/growl WAV files.
- [Sound Effects](./Assets/Sound%20Effects/): 69 additional horror WAV files.
- [Dark Forest](./Assets/Dark%20Forest/): separate 2D forest package; not used in the dungeon scene.

## Important clarification: why earlier screenshots showed cubes

The imported visual assets were inspected and catalogued, then a later environment pass added a stone visual layer. The current saved scene contains imported stone visuals while the original cube wall colliders remain preserved for gameplay safety.

This happened because development was split into safe phases:

1. Audit the existing project.
2. Repair and test core mechanics.
3. Add torch, rooms, lighting, HUD, audio, and navigation.
4. Replace the grey-box environment with imported prefabs.

The imported stone material conversion is saved. The original cube meshes remain as collision-safe roots while their visible renderers are disabled; the imported visual layer is collider-free.

## Latest environment and character upgrade

- `KatsBitsWallURP.mat` and `KatsBitsTrimURP.mat` exist and use the URP/Lit shader.
- A camera capture showed the imported stone wall mesh rendering correctly with no magenta materials.
- Original wall colliders remain enabled.
- `DungeonEnvironmentVisuals` contains 42 imported stone visual segments.
- Imported door/gate visuals and URP-compatible KatsBits wood/metal materials are present for the three existing door roots.
- `HollowStalker_Skeleton` is attached beneath the AI root.
- [HollowStalker.controller](./Assets/Animations/HollowStalker.controller) contains Idle, Walk, Run, and Attack states.
- The procedural stalker body and eyes are hidden; the AI root, NavMeshAgent, glow light, and damage logic remain.
- Player torch flame and smoke ParticleSystems are present and controlled by [PlayerTorch.cs](./Assets/Scripts/PlayerTorch.cs).
- The global volume has ACES tonemapping, restrained bloom, color adjustment, and vignette.
- [HorrorGameDirector.cs](./Assets/Scripts/HorrorGameDirector.cs) provides objective refresh, pause state, checkpoint save/load, restart flow, and runtime fuel pickups.
- [HorrorRuntimeUI.cs](./Assets/Scripts/HorrorRuntimeUI.cs) provides a pause/checkpoint menu.
- [HorrorEventDirector.cs](./Assets/Scripts/HorrorEventDirector.cs) triggers restrained flicker/stinger events near the stalker and through the dungeon.

## Labyrinth map — current saved layout

The following is a schematic top-down map of the current `SampleScene`. It is not to scale, but the named rooms, doors, key, torches, player route, and monster area correspond to the current scene design.

```text
                                  NORTH / +Z

        ┌────────────────────────────────────────────────────┐
        │                                                    │
        │   ROOM 01 — ARCHIVE          ROOM 02 — OBSERVATION │
        │   Archive shelves            Console / blue light   │
        │   [Key_Archive]                                      │
        │          │                                           │
        │          └──── Door_02_RoomLink                     │
        │                LOCKED: archive_key                   │
        │                         │                            │
        │                         │                            │
        │  Door_01_Entrance ─────┴──── Main maze corridors    │
        │        │                     Torch route             │
        │        │                                             │
        │  Player start / Capsule                 Door_03      │
        │        │                              RoomLink       │
        │        └───────────────────────────────┬──────────── │
        │                                        │             │
        │                              ROOM 03 — RED HALL      │
        │                              Red lights / ritual table│
        │                              HollowStalker patrol area│
        │                                                    │
        └────────────────────────────────────────────────────┘

                                  SOUTH / -Z
```

### Known world landmarks

| Landmark | Current world position / role |
|---|---|
| Player `Capsule` | Approximately `(4.39, 1.69, 3.00)` at scene start |
| `Door_01_Entrance` | Approximately `(0, 0, 23.5)` |
| `Door_02_RoomLink` | Approximately `(6.5, 0, 30)`; locked by `archive_key` |
| `Door_03_RoomLink` | Approximately `(18.5, 0, 30)` |
| `HollowStalker` | Starts near `(24, 0.1, 31)` |
| `Room_01_Archive` | Archive/key room in the upper maze branch |
| `Room_02_Observation` | Observation/console room with blue lighting |
| `Room_03_RedHall` | Red-lit room with ritual table and monster pressure |

## Verification results

### Passed

- Normal door opens.
- Normal door closes.
- Locked door refuses to open without its key.
- Archive key unlocks the configured key door in the earlier gameplay test.
- Torch starts on and fuel decreases.
- Torch fallback sound plays.
- Door open/close/locked fallback sounds play.
- Audio manager assigns all ten selected clips.
- NavMeshSurface has baked NavMeshData.
- HollowStalker receives a NavMeshAgent.
- HollowStalker can enter Attack state at close range.

### Not fully verified

- Every imported visual prefab in a finished labyrinth.
- Long-distance monster navigation through every corridor.
- Door-aware NavMesh updates after doors open and close.
- Save/load across scenes.
- Full scene transition flow.
- Windows build.
- Fresh Play Mode confirmation of the latest CharacterController repair while the current editor has domain/scene reload disabled.

## Current blocking problem

The Unity console most recently showed an MCP bridge failure similar to:

```text
MCP-FOR-UNITY: GameObjectSerializer unexpected error serializing TransformHandle
System.NullReferenceException: TransformHandle object is null
connection.state_change: oldState=Running newState=Failed
Process exited unexpectedly, code=-1
```

This is an editor/MCP serialization connection problem, not evidence that the imported assets are missing. The bridge process has been restarted, and [McpBridgeRecovery.cs](./Assets/Editor/McpBridgeRecovery.cs) now requests a reconnect after the next Unity domain reload. The current Unity editor must complete one Stop → Play cycle before the latest movement assembly can be verified.

## Remaining work

1. Complete the fresh Play Mode movement/torch/door/monster acceptance pass after the current editor reload.
2. Finish selected medieval arches, pillars, chains, debris, and room dressing with the already imported prefabs.
3. Add authored notes/puzzles and a dedicated key-count/inventory widget.
4. Create additional authored level scenes and scene transitions.
5. Add a level-complete/ending sequence and settings/options UI.
6. Run a full performance, collider/NavMesh, Play Mode, and Windows build validation.

## Related project records

- [PHASE1_AUDIT.md](./PHASE1_AUDIT.md)
- [DEVELOPMENT_CHANGELOG.md](./DEVELOPMENT_CHANGELOG.md)
- [HorrorDoor.cs](./Assets/Scripts/HorrorDoor.cs)
- [HorrorAudioManager.cs](./Assets/Scripts/HorrorAudioManager.cs)
- [HollowStalker.cs](./Assets/Scripts/HollowStalker.cs)
- [PlayerTorch.cs](./Assets/Scripts/PlayerTorch.cs)
- [DoorInteractor.cs](./Assets/Scripts/DoorInteractor.cs)
- [HorrorGameDirector.cs](./Assets/Scripts/HorrorGameDirector.cs)
- [HorrorRuntimeUI.cs](./Assets/Scripts/HorrorRuntimeUI.cs)
- [HorrorFuelPickup.cs](./Assets/Scripts/HorrorFuelPickup.cs)

