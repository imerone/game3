# THE LAST TORCH — Phase 1 Audit

Audit date: 2026-11-06  
Unity version: Unity 6.6 (6000.6.4f1)  
Main scene: `Assets/Scenes/SampleScene.unity`

## Verified project state

- The project contains one gameplay scene in Build Settings: `SampleScene`.
- The scene contains 112 serialized GameObject blocks.
- The scene contains 62 systematically named wall objects: `wall1` through `wall62`.
- The scene contains three `HorrorDoor` components:
  - `Door_01_Entrance`
  - `Door_02_RoomLink`
  - `Door_03_RoomLink`
- The scene contains three rooms:
  - `Room_01_Archive`
  - `Room_02_Observation`
  - `Room_03_RedHall`
- The player object is `Capsule` with movement, door interaction, inventory, and survival components.
- `Main Camera` contains the player torch system and persistent torch visual/light children.
- The scene contains one `HollowStalker` prototype.
- A global volume and multiple flickering colored lights are present.
- No baked NavMeshData is assigned in `SampleScene`.
- No `AudioSource` component is serialized in `SampleScene`; audio fields in the gameplay scripts are currently unassigned.

## Imported assets verified on disk

- `Assets/KatsBits-fantasy-base/`: medieval environment package with 346 prefabs, including walls, arches, doors, iron gates, torches, and lanterns.
- `Assets/DungeonCharacters/`: Dungeon Skeleton model, prefab, and walk/idle/attack FBX animations.
- `Assets/LowPolyMedievalPropsLite/`: 48 medieval prop prefabs, including barrels, candles, fire, furniture, and wood pieces.
- `Assets/Caves and Dungeons/`: 42 dungeon ambience/music WAV files with loop variants.
- `Assets/Horror Elements/`: 45 horror ambience, hit, breathing, cry, whisper, and stinger WAV files.
- `Assets/Monster Roars/`: 25 monster roar/growl WAV files.
- `Assets/Sound Effects/`: 69 additional horror sound-effect WAV files.
- `Assets/Dark Forest/`: a separate 2D forest package; it is not currently part of `SampleScene`.

## Core script findings

- `HorrorDoor` has reusable rotation, collider, lock/key, and optional audio fields.
- `DoorInteractor` raycasts from the player camera and uses `E`, but its feedback is still a simple runtime IMGUI label and does not distinguish all key/lock cases requested by the design brief.
- `PlayerTorch` supports `F`, fuel drain, low-fuel flicker, and dynamic spot-light creation, but its audio references are unassigned and it does not yet use a flame particle system.
- `HollowStalker` has patrol, investigate, chase, and attack states, but currently uses direct transform movement rather than `NavMeshAgent` pathfinding.
- Inventory currently tracks keys in memory only; there is no save/load system.

## Phase 1 blockers and risks

- The Unity process is running, but the MCP HTTP endpoint at `http://127.0.0.1:8080` was unreachable during this audit. Live Unity-MCP inspection and play-mode verification therefore could not be repeated in this pass.
- The current scene is still a grey-box environment. Imported production-ready environment prefabs have not yet been integrated into the main scene.
- The current project is not yet a complete multi-level game; only `SampleScene` is in Build Settings.

## Implementation order

1. Restore/verify Unity MCP connectivity and take a fresh scene/console snapshot.
2. Repair and test the door interaction flow, including correct locked-key feedback and audio.
3. Wire the imported dungeon audio and monster sounds through a centralized audio layer.
4. Add a baked NavMesh and convert the stalker to collision-safe navigation.
5. Replace selected grey-box walls/doors/props with compatible imported assets.
6. Build objectives, progression, UI states, saving, and additional playable levels.

