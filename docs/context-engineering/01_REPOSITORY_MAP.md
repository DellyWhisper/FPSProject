# 01_REPOSITORY_MAP

## Top-Level Repository Structure

| Path | Purpose | Confidence |
| --- | --- | --- |
| `FPSProject.uproject` | Unreal project descriptor. Declares engine version and enabled plugins. | High |
| `Config/` | Project `.ini` configuration for runtime, input, rendering, and editor defaults. | High |
| `Content/` | Main authored Unreal content for maps, Blueprints, UI, animation, AI, and art assets. | High |
| `DerivedDataCache/` | Generated local cache data. Not a source-of-truth gameplay location. | High |
| `Intermediate/` | Generated editor/build state. Not a source-of-truth gameplay location. | High |
| `Saved/` | Logs, crash data, autosaves, and editor-generated runtime outputs. | High |
| `docs/context-engineering/` | Reusable context-engineering package created by this audit. | High |
| `Rules.md` | Workspace operating and safety rules for future agents. | High |

## Project Descriptor

### Verified facts

- `FPSProject.uproject`:
  - declares `EngineAssociation: 5.7`
  - enables:
    - `ModelingToolsEditorMode` with editor-only allow list
    - `GameplayStateTree`
    - `VibeUE`
- The `.uproject` file contains no `Modules` array.
- The repository contains no `Source/` directory.
- The repository contains no project-local `Plugins/` directory.

### Inference

- Confidence: High
- This is currently a content-only / Blueprint-first Unreal project rather than a project-centered C++ module architecture.

## Config Folder Map

| Path | What it controls | Notes |
| --- | --- | --- |
| `Config/DefaultEngine.ini` | Maps, default GameMode, rendering, collision profiles, AI system defaults, platform/render settings | Contains both active runtime settings and stale template leftovers |
| `Config/DefaultGame.ini` | Project metadata | Still says `ProjectName=First Person BP Game Template` |
| `Config/DefaultInput.ini` | Enhanced Input defaults and input classes | Sets `EnhancedPlayerInput` and `EnhancedInputComponent` |
| `Config/DefaultEditor.ini` | Editor defaults | Contains stale `/Game/FirstPersonBP/...` map reference |
| `Config/DefaultEditorPerProjectUserSettings.ini` | Editor user-facing defaults | Contains stale content browser path `/Game/FirstPersonBP` |

## Content Folder Map

| Path | What it contains | Notes |
| --- | --- | --- |
| `Content/Characters/` | Shared mannequin assets, animation sequences, control rigs, shared animation support | Foundation content, not the active gameplay root |
| `Content/Collections/` | Unreal collection metadata | Organizational/editor-facing |
| `Content/Developers/` | Developer-specific content area | Present but not a main runtime anchor |
| `Content/FirstPerson/` | Base first-person framework assets and one authored map | Contains template-derived core runtime classes |
| `Content/Input/` | Shared input actions, input mapping contexts, touch UI | Base locomotion/input layer |
| `Content/LevelPrototyping/` | Prototype interactables and environment gameplay pieces | Includes jump pad, wobble target, door frame assets |
| `Content/Variant_Shooter/` | Shooter-specific gameplay layer | Main practical gameplay root |
| `Content/Weapons/` | Weapon meshes, materials, textures, and audio assets | Art/content backing shooter systems |
| `Content/__ExternalActors__/` | World Partition external actors | Critical for map state |
| `Content/__ExternalObjects__/` | World Partition external objects | Critical for map state |

## Where Gameplay Logic Lives

### Verified facts

- Base gameplay framework lives in `Content/FirstPerson/Blueprints/`:
  - `BP_FirstPersonCharacter`
  - `BP_FirstPersonPlayerController`
  - `BP_FirstPersonCameraManager`
  - `BP_FirstPersonGameMode`
- Shooter runtime logic lives in `Content/Variant_Shooter/Blueprints/`:
  - `BP_ShooterCharacter`
  - `BP_ShooterPlayerController`
  - `BP_ShooterGameMode`
- Shooter AI logic lives in `Content/Variant_Shooter/Blueprints/AI/`:
  - `BP_ShooterAIController`
  - `BP_ShooterNPC`
  - `BP_ShooterNPCSpawner`
  - `ST_Shooter`
  - `ST_Shooter_ShootAtTarget`
  - `EQS_FindRoamLocation`
  - `EQS_FindSnipingLocation`
- Pickup, weapon, and projectile logic lives in `Content/Variant_Shooter/Blueprints/Pickups/`.
- Runtime UI lives in:
  - `Content/Variant_Shooter/UI/`
  - `Content/Input/Touch/`
  - `Content/Variant_Shooter/Input/`
- Animation logic hooks live in:
  - `Content/FirstPerson/Anims/`
  - `Content/Variant_Shooter/Anims/`
  - `Content/Characters/Mannequins/`

### Inference

- Confidence: High
- Most future gameplay changes should begin in `Content/Variant_Shooter/` and only descend into `Content/FirstPerson/` if the shared base layer truly needs modification.

## Where Data Lives

### Verified facts

- Project configuration data lives in `Config/*.ini`.
- Input data assets live in:
  - `Content/Input/`
  - `Content/Variant_Shooter/Input/`
- Weapon selection data lives in:
  - `Content/Variant_Shooter/Blueprints/Pickups/DT_WeaponList.uasset`
  - `Content/Variant_Shooter/Blueprints/Pickups/ST_WeaponTableRow.uasset`
- `DT_WeaponList` rows are:
  - `GrenadeLauncher`
  - `Pistol`
  - `Rifle`
  - `Watergun`
- Verified exported fields from `DT_WeaponList` are:
  - `Static Mesh`
  - `Spawn Actor`

### Verified absence

- No project-local Gameplay Tags config was found in `Config/`.
- No `SaveGame` assets were found.
- No `UserDefinedEnum` assets were found.
- No project-local gameplay `PrimaryDataAsset` content was found.
- No custom `GameInstance`, `GameState`, `PlayerState`, or `HUD` assets were found.

## Docs, Scripts, and Tools

### Verified facts

- Before this audit, there was no project documentation package under `docs/`.
- This audit writes reusable repository context into `docs/context-engineering/`.
- No project-local `Plugins/` directory exists.
- No project-local root script/tooling directory was found for `.py`, `.ps1`, `.bat`, or `.sh` automation.

## Repository Character

### Inference

- Confidence: High
- The repository is organized around authored Unreal assets, not around source modules.
- Confidence: High
- `Variant_Shooter` is the practical runtime center of gravity.
- Confidence: Medium
- The project evolved from starter/template content into a shooter prototype, and repository cleanup has not fully caught up with that evolution.
