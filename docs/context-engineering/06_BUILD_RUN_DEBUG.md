# 06_BUILD_RUN_DEBUG

## Build / Run Reality

### Verified facts

- `FPSProject.uproject` has no `Modules` section.
- There is no `Source/` directory.
- There is no project-local `Plugins/` directory.
- The project targets Unreal Engine `5.7`.

### Inference

- Confidence: High
- There is no project gameplay compile step in the usual C++ sense.
- Confidence: High
- The normal path to run the project is to open `FPSProject.uproject` in Unreal Editor 5.7 with the required plugins available.

## Required Plugins

### Verified facts

- Enabled plugins in `FPSProject.uproject`:
  - `ModelingToolsEditorMode`
  - `GameplayStateTree`
  - `VibeUE`

### Practical implication

- If `GameplayStateTree` is unavailable, AI behavior content is likely to fail or open broken.
- If `VibeUE` is unavailable, editor-side workflows tied to that plugin may degrade, even if core gameplay still loads.

## Runtime Startup Expectations

### Verified facts

- `DefaultEngine.ini` points startup and default map to `/Game/FirstPerson/Lvl_FirstPerson`.
- Both authored maps override `default_game_mode` to `/Game/Variant_Shooter/Blueprints/BP_ShooterGameMode`.
- `Lvl_FirstPerson` has AI enabled and navigation disabled.
- `Lvl_ArenaShooter` has AI enabled and navigation enabled.

### Practical implication

- Opening the project should land in a shooter-flavored test map, not a pure untouched first-person template map.
- For AI validation, `Lvl_ArenaShooter` is the stronger test environment because it contains spawners and navmesh actors.

## Renderer / Platform Caveats

### Verified facts

- `DefaultEngine.ini` enables or sets:
  - DX12 as the default graphics RHI
  - ray tracing
  - Lumen GI and reflections
  - Substrate
  - Virtual Shadow Maps

### Inference

- Confidence: Medium
- This project expects a modern Windows rendering path and may behave differently on unsupported hardware or downgraded render settings.

## Possible Failure Points

### Verified facts

- Broken or stale references were found:
  - `/Game/DemoTemplate/_Core/MI_Intro_Colorway` referenced by `BP_WobbleTarget`
  - `/Game/LevelPrototyping/Interactable/Door/Assets/Meshes/SM_DoorFrame_Edge` referenced by one Arena external actor
- Stale config or editor references still point at missing `/Game/FirstPersonBP/...` content.
- World Partition sidecar `.ini` files reference missing directories such as:
  - `/Game/FoliageTypes`
  - `/Game/DataLayers/Lvl_FirstPerson_WP/`
  - `/Game/Variant_Shooter/DataLayers/Lvl_ArenaShooter_WP/`
- Key assets were saved across mixed `5.6` and `5.7` engine versions.

### Inference

- Confidence: High
- The project is functional enough to work with, but cleanup debt is real and some editor warnings or repair prompts are plausible.

## Debugging Entry Points

### Start here for gameplay issues

| Problem area | First inspection targets |
| --- | --- |
| Wrong map or wrong GameMode | `Config/DefaultEngine.ini`, map World Settings for `Lvl_FirstPerson` and `Lvl_ArenaShooter` |
| Player movement or input | `DefaultInput.ini`, `IMC_Default`, `IMC_MouseLook`, `BP_FirstPersonPlayerController`, `BP_ShooterPlayerController` |
| Weapon or pickup issues | `DT_WeaponList`, `ST_WeaponTableRow`, `BP_ShooterPickup`, `BP_ShooterWeaponBase`, child weapon Blueprints |
| Projectile issues | `BP_ShooterProjectileBase` and projectile child Blueprints |
| AI issues | `BP_ShooterAIController`, `BP_ShooterNPC`, `ST_Shooter`, `ST_Shooter_ShootAtTarget`, EQS assets, arena navmesh |
| UI issues | `UI_Shooter`, `UI_ShooterBulletCounter`, shooter player controller, shooter GameMode |
| Animation issues | mesh anim class defaults on character and weapon assets, relevant `ABP_*` assets |

## Logs and Validation

### Verified facts

- `MapCheck` output observed during audit reported `0 Error(s), 0 Warning(s)`.
- Main project logs exist under `Saved/Logs/`.
- Crash data exists under `Saved/Crashes/`.

### Practical debug workflow

1. Check `Saved/Logs/` first for runtime/editor warnings.
2. Verify the active map and map-level GameMode override.
3. Trace the involved Blueprint inheritance chain before editing.
4. For AI issues, validate navmesh presence in `Lvl_ArenaShooter`.
5. For content issues, inspect missing references before assuming runtime logic is wrong.

## Editor Caveats

- Blueprint graph internals are not fully visible from repository inspection alone.
- World Partition maps should be edited carefully because authored state spans:
  - `.umap`
  - `Content/__ExternalActors__/`
  - `Content/__ExternalObjects__/`
- `BP_FirstPersonCharacter` is shared by the player and NPC-derived shooter classes, so changes there have broad blast radius.

## Practical Recommendation

### Inference

- Confidence: High
- Future debugging should start from:
  - the active map
  - the shooter GameMode override
  - the relevant controller or character class
  - the data table or interface contract that feeds that system
- Confidence: High
- Avoid broad cleanup or migration work before first validating the currently active shooter flow in-editor.
