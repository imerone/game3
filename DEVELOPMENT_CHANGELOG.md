# THE LAST TORCH — Development Changelog

## 2026-11-06

### Phase 1 — Project audit

- Audited the current project structure, build settings, scene serialization, gameplay scripts, imported prefabs, and audio assets.
- Recorded verified findings in [PHASE1_AUDIT.md](./PHASE1_AUDIT.md).
- Confirmed that imported dungeon, medieval prop, skeleton, ambience, horror-effect, and monster-roar assets are available on disk.
- Confirmed that the main scene currently has one build scene, no baked NavMeshData, and no serialized AudioSource components.
- Confirmed that the live Unity MCP HTTP endpoint was unreachable at `127.0.0.1:8080` during the audit.

### Core audio milestone

- Added [HorrorAudioManager.cs](./Assets/Scripts/HorrorAudioManager.cs) as a centralized runtime audio layer.
- Connected door open, door close, locked-door, torch ignite, torch extinguish, key pickup, monster growl, tension, stinger, and exploration ambience events.
- Assigned ten verified imported clips from `Caves and Dungeons`, `Horror Elements`, and `Monster Roars` to the active `SampleScene`.
- Verified in Play Mode that the manager creates ambient/tension sources and that door, torch, and locked-door events produce the expected one-shot clips.
- Stopped Play Mode cleanly; the Unity console contained zero error or warning entries after the smoke test.

### Current status report

- Added [CURRENT_GAME_REPORT.md](./CURRENT_GAME_REPORT.md) with the complete current-state report and a schematic top-down labyrinth map.
- Documented that imported visual prefabs are present on disk but have not yet been instantiated into `SampleScene`.
- Documented the current Unity-MCP `TransformHandle` serialization/connection failure that blocked the visual asset integration pass.

## 2026-11-08

### Phase 1 — Backup and environment asset audit

- Created backup at `._codex_backups/20261108-environment-character-upgrade` containing the active scene, manifest, and current C# scripts.
- Confirmed KatsBits wall, arch, pillar, door, gate, lantern, and fire prefabs are available.
- Confirmed the Dungeon Skeleton prefab and idle/walk/attack FBX animation assets are available.
- Confirmed medieval prop prefabs are available for barrels, candles, fire, furniture, and wood details.

### Phase 2 — Wall visual conversion attempt

- Created URP-compatible materials [KatsBitsWallURP.mat](./Assets/Materials/KatsBitsWallURP.mat) and [KatsBitsTrimURP.mat](./Assets/Materials/KatsBitsTrimURP.mat) from the imported KatsBits textures.
- Verified a KatsBits wall prefab renders without the magenta shader error using `Universal Render Pipeline/Lit`.
- Applied a temporary stone visual layer to the existing wall layout while preserving original wall colliders; the camera screenshot showed textured stone corridors.
- The MCP bridge then failed again during the next prefab/animation operation, so the final saved-state of the visual layer and cleanup of the temporary skeleton test instance must be rechecked after reconnecting Unity MCP.

### Current blocker

- The MCP server process exited unexpectedly.
- Restarting the configured `uvx` server is blocked by Windows Application Control because Python `_socket` DLL loading is denied.
- Unity remains open, but Phase 3 skeleton integration and the remaining validation phases are paused until the bridge is restarted and the scene’s unsaved state is inspected.

### Phase 3 — Skeleton character integration

- Attached the imported `DungeonSkeleton_demoPrefab` under `HollowStalker`.
- Hid the old procedural stalker body and eye meshes while preserving the AI root and glow light.
- Created [HollowStalker.controller](./Assets/Animations/HollowStalker.controller).
- Added Idle, Walk, Run, and Attack states using the imported FBX animation clips.
- Added `IsMoving`, `IsChasing`, and `IsAttacking` Animator parameters and transitions.
- Updated [HollowStalker.cs](./Assets/Scripts/HollowStalker.cs) to drive Animator parameters from the existing AI state and NavMesh movement.

### Phase 4 — Torch fire and smoke

- Added `PlayerTorch_FireParticles` and `PlayerTorch_SmokeParticles` under the first-person camera.
- Added [TorchSmoke.mat](./Assets/Materials/TorchSmoke.mat) and [TorchFlameParticle.mat](./Assets/Materials/TorchFlameParticle.mat).
- Updated [PlayerTorch.cs](./Assets/Scripts/PlayerTorch.cs) so flame and smoke follow the existing F toggle and fuel state.
- Runtime checks confirmed torch, flame particles, and smoke particles start together.

### Phase 5 — URP horror atmosphere

- Configured the existing global volume with ACES tonemapping, restrained bloom, color adjustment, and vignette.
- Reduced the effect strength after an initial capture was too dark; the clean-start capture remains readable.
- Confirmed the camera has URP post-processing enabled.

### Phase 6 — Validation

- Clean-start screenshot shows textured stone corridors with no magenta materials.
- Restored the player start position to `(4.391, 1.69, 3.001)` after a runtime AI test moved it temporarily.
- Verified normal door open/close, locked-door blocking, archive-key unlock, torch fuel, wall colliders, NavMesh presence, and runtime particle activation.
- `problems` reports no project errors.
- Unity console has no gameplay/compile errors; only the known MCP WebSocket warning remains.

### Runtime game-flow and movement hardening

- Added [HorrorGameDirector.cs](./Assets/Scripts/HorrorGameDirector.cs) for runtime objectives, pause state, checkpoint save/load, restart flow, and scene-safe discovery of the existing player systems.
- Added [HorrorRuntimeUI.cs](./Assets/Scripts/HorrorRuntimeUI.cs) with a pause/checkpoint menu that does not alter the existing HUD Canvas.
- Added [HorrorFuelPickup.cs](./Assets/Scripts/HorrorFuelPickup.cs) and runtime torch-fuel pickups so the torch loop has recovery points instead of only depletion.
- Added [HorrorEventDirector.cs](./Assets/Scripts/HorrorEventDirector.cs) for restrained proximity horror beats using the existing flicker lights, stinger audio, stalker, and HUD.
- Extended [HorrorHUD.cs](./Assets/Scripts/HorrorHUD.cs) with a runtime-safe key-count label when the existing Canvas does not already contain one.
- Extended [PlayerInventory.cs](./Assets/Scripts/PlayerInventory.cs), [PlayerTorch.cs](./Assets/Scripts/PlayerTorch.cs), and [PlayerSurvival.cs](./Assets/Scripts/PlayerSurvival.cs) with checkpoint-safe state APIs.
- Hardened [FPSController.cs](./Assets/Scripts/FPSController.cs): it now re-enables a disabled CharacterController, restores its capsule geometry, and falls back to direct W/A/S/D reads when an input-axis bridge is unavailable.
- Added [McpBridgeRecovery.cs](./Assets/Editor/McpBridgeRecovery.cs), which reconnects the already-configured Unity MCP transport after a domain reload or manually restarted local server.

### Current live-input note

- The current Unity editor is using Enter Play Mode Options with domain and scene reload disabled. After a script change, the first running session can retain the previous controller state; stop Play Mode once and press Play again to load the hardened controller assembly.

