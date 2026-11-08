# Environment and Character Asset Audit

Audit date: 2026-11-08  
Project: THE LAST TORCH v1  
Scene: `Assets/Scenes/SampleScene.unity`

## Selected assets

| Purpose | Actual asset path | Status |
|---|---|---|
| Stone wall visual | `Assets/KatsBits-fantasy-base/Prefabs/Walls_plain/Wall.Plain.4U.prefab` | Used for the stone visual layer |
| URP wall material | `Assets/Materials/KatsBitsWallURP.mat` | Created from imported wall texture/normal data |
| URP trim material | `Assets/Materials/KatsBitsTrimURP.mat` | Created from imported trim texture/normal data |
| Skeleton prefab | `Assets/DungeonCharacters/Skeletons_demo/DungeonSkeleton_demoPrefab.prefab` | Attached under `HollowStalker` |
| Idle animation | `Assets/DungeonCharacters/Skeletons_demo/animation/DS_onehand_idle_A.FBX` | Assigned to Animator controller |
| Walk animation | `Assets/DungeonCharacters/Skeletons_demo/animation/DS_onehand_walk.FBX` | Assigned to Walk/Run states |
| Attack animation | `Assets/DungeonCharacters/Skeletons_demo/animation/DS_onehand_attack_A.FBX` | Assigned to Attack state |
| Medieval doors/gates | `Assets/KatsBits-fantasy-base/Prefabs/Doors/` | Imported door/gate visuals placed on the three existing door roots; original interaction colliders preserved |
| Medieval props | `Assets/LowPolyMedievalPropsLite/Prefabs/` | Barrels, furniture, candle, stone, and wood-plank props placed in the existing room areas; more dressing remains |

## Placement strategy

- Preserve existing cube colliders and gameplay coordinates.
- Add imported visual meshes at matching wall transforms.
- Use URP-compatible materials because the original KatsBits materials referenced the built-in shader and rendered magenta in URP.
- Keep visual meshes collider-free so existing collision and NavMesh geometry remain stable.
- Replace the procedural stalker body only; preserve the existing AI root, NavMeshAgent, damage, and line-of-sight logic.

## Validation status

- Stone visuals render without magenta materials.
- Original wall colliders remain enabled.
- `DungeonEnvironmentVisuals` contains 42 imported stone visual segments.
- The skeleton prefab has an Animator and is attached to `HollowStalker`.
- `HollowStalker.controller` contains Idle, Walk, Run, and Attack states.
- Flame and smoke ParticleSystems are attached to the player torch.
- The scene saved successfully after the environment and character changes.
- The runtime layer now adds torch-fuel recovery pickups and horror-event hooks without replacing the imported scene geometry.

