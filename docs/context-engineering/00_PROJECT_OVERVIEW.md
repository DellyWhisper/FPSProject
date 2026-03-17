# 00_PROJECT_OVERVIEW

## What This Project Is

### Verified facts

- `FPSProject.uproject` declares Unreal Engine `5.7` and contains no `Modules` section, so this repository is currently a content-only / Blueprint-first Unreal project.
- Enabled plugins in `FPSProject.uproject` are:
  - `ModelingToolsEditorMode` (Editor only)
  - `GameplayStateTree`
  - `VibeUE`
- There is no `Source/` folder and no project-local `Plugins/` folder.
- The main authored project content lives under:
  - `Content/FirstPerson/`
  - `Content/Variant_Shooter/`
  - `Content/Input/`
  - `Content/LevelPrototyping/`
  - `Content/Weapons/`
  - `Content/Characters/`
- `Config/DefaultEngine.ini` sets both `EditorStartupMap` and `GameDefaultMap` to `Content/FirstPerson/Lvl_FirstPerson.uasset`.
- Asset inspection of `Content/FirstPerson/Lvl_FirstPerson.uasset` and `Content/Variant_Shooter/Lvl_ArenaShooter.uasset` shows that both maps override `default_game_mode` to `Content/Variant_Shooter/Blueprints/BP_ShooterGameMode.uasset`.
- `Config/DefaultEngine.ini` still lists `GlobalDefaultGameMode=/Game/FirstPerson/Blueprints/BP_FirstPersonGameMode...`, so config defaults and map overrides are not aligned.

### Inference

- Confidence: High
- The current playable focus is not the original first-person template loop anymore. It is now a Blueprint-driven shooter prototype layered on top of the template base.
- Confidence: Medium
- The likely genre is a first-person arena shooter / combat sandbox with pickups, multiple weapon types, simple score UI, AI enemies, jump pads, and target props.
- Confidence: Medium
- The project maturity looks like prototype-to-vertical-slice rather than production-ready. Evidence includes Blueprint-heavy runtime logic, minimal documentation, mixed asset naming quality, stale template leftovers, and several unresolved references.

## Current Focus

### Verified facts

- `Content/Variant_Shooter/Blueprints/` contains the main shooter runtime assets:
  - `BP_ShooterGameMode`
  - `BP_ShooterCharacter`
  - `BP_ShooterPlayerController`
  - AI assets under `AI/`
  - weapon/pickup/projectile assets under `Pickups/`
- `Content/Variant_Shooter/UI/` contains shooter HUD widgets:
  - `UI_Shooter`
  - `UI_ShooterBulletCounter`
- `Content/Variant_Shooter/Input/` adds shooter-specific input:
  - `IA_Shoot`
  - `IA_SwapWeapon`
  - `IMC_Weapons`
- `Content/Variant_Shooter/Blueprints/AI/` contains:
  - `BP_ShooterAIController`
  - `BP_ShooterNPC`
  - `BP_ShooterNPCSpawner`
  - `ST_Shooter`
  - `ST_Shooter_ShootAtTarget`
  - `EQS_FindRoamLocation`
  - `EQS_FindSnipingLocation`

### Inference

- Confidence: High
- `Variant_Shooter` is the real gameplay layer future work will most often touch.
- Confidence: High
- `FirstPerson` is now a shared base layer rather than the final gameplay layer.

## Top-Level Architecture Summary

### Verified facts

- Base player framework assets are in `Content/FirstPerson/Blueprints/`.
- Shooter runtime assets extend the base framework:
  - `BP_ShooterCharacter` inherits from `BP_FirstPersonCharacter`
  - `BP_ShooterPlayerController` inherits from `BP_FirstPersonPlayerController`
  - `BP_ShooterNPC` also inherits from `BP_FirstPersonCharacter`
- Base input assets are in `Content/Input/`.
- Shooter-specific input is layered on via `Content/Variant_Shooter/Input/`.
- Weapons are driven by:
  - `Content/Variant_Shooter/Blueprints/Pickups/BP_ShooterWeaponBase.uasset`
  - data table `Content/Variant_Shooter/Blueprints/Pickups/DT_WeaponList.uasset`
  - struct `Content/Variant_Shooter/Blueprints/Pickups/ST_WeaponTableRow.uasset`
- AI is driven by:
  - `BP_ShooterAIController`
  - `StateTreeAIComponent`
  - `ST_Shooter`
  - `EQS` queries
  - `AIPerceptionComponent`
- Both shipped maps are World Partition maps with external actors:
  - `Content/FirstPerson/Lvl_FirstPerson.uasset`
  - `Content/Variant_Shooter/Lvl_ArenaShooter.uasset`

## Important Warnings For Future Agents

- Treat this as a Blueprint-first project. Repo inspection can only partially reveal Blueprint behavior.
- Do not assume `BP_FirstPersonGameMode` is the active runtime GameMode just because it appears in `Config/DefaultEngine.ini`. Both authored maps currently override to `BP_ShooterGameMode`.
- Do not edit World Partition maps casually through raw files. Gameplay-relevant map edits can affect:
  - `Content/*.umap`
  - `Content/__ExternalActors__/`
  - `Content/__ExternalObjects__/`
- `Content/FirstPerson/Blueprints/BP_FirstPersonCharacter.uasset` is a shared base class for both the player and AI-side shooter classes. Changes there can ripple widely.
- There are verified stale or missing references:
  - `BP_WobbleTarget` references missing `/Game/DemoTemplate/_Core/MI_Intro_Colorway`
  - one `BP_DoorFrame` external actor references missing `/Game/LevelPrototyping/Interactable/Door/Assets/Meshes/SM_DoorFrame_Edge`
- Template leftovers still exist in configs and metadata:
  - `Config/DefaultGame.ini` still names the project `First Person BP Game Template`
  - `Config/DefaultEditor.ini` still points to `/Game/FirstPersonBP/Maps/FirstPersonExampleMap`
  - `Config/DefaultEditorPerProjectUserSettings.ini` still points the content browser to `/Game/FirstPersonBP`

## Recommended Reading Order

1. `PROJECT_INDEX.md`
2. `01_REPOSITORY_MAP.md`
3. `02_RUNTIME_ARCHITECTURE.md`
4. `03_SYSTEMS_BREAKDOWN.md`
5. `09_AGENT_GUIDE.md`
