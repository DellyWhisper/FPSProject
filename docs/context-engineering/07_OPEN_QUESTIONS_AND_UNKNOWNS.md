# 07_OPEN_QUESTIONS_AND_UNKNOWNS

## Why This File Exists

- This repository is Blueprint-first and content-only.
- Repo inspection gives strong structural truth, but not full Blueprint graph truth.
- This file marks what should not be over-claimed without Unreal Editor verification.

## Blueprint Runtime Logic That Is Still Unconfirmed

### Needs manual verification in Unreal Editor

- Exact `BeginPlay` and setup flow for:
  - `BP_ShooterGameMode`
  - `BP_ShooterPlayerController`
  - `BP_ShooterCharacter`
- Exact UI creation timing:
  - where `UI_Shooter` is created
  - where `UI_ShooterBulletCounter` is created
  - how data gets pushed into those widgets
- Exact weapon equip and swap flow:
  - where weapon instances are created
  - how the active weapon is stored
  - whether old weapons are destroyed, hidden, or pooled
- Exact projectile spawn logic:
  - spawn origin
  - use of `Get Weapon Target Location`
  - damage or impulse application path
- Exact death, respawn, and score update flow.

## Weapon and Combat Unknowns

### Needs manual verification in Unreal Editor

- Whether ammo is finite, infinite, clip-based, or only visually represented.
- Whether the project has reload logic at all.
- Whether each weapon child overrides gameplay variables beyond mesh and projectile selection.
- Whether `BP_ShooterNPC` and `BP_ShooterCharacter` use the same weapon handling path or diverge in Blueprint graphs.
- Whether all placed arena pickups are intentionally rifle pickups or just currently named that way in external actor object names.

## Animation Unknowns

### Needs manual verification in Unreal Editor

- How weapon-specific animation blueprints are selected at runtime.
- Whether first-person and third-person animation states are switched together or independently.
- Whether aim offsets, recoil, montage triggers, and hand-adjustment rigs are driven by weapon base logic or by character logic.
- Whether pistol use of a rifle shoot montage is a placeholder or a deliberate shared animation choice.

## AI Unknowns

### Needs manual verification in Unreal Editor

- Exact structure of `ST_Shooter` and `ST_Shooter_ShootAtTarget`.
- Exact conditions for roam vs sniping behavior.
- Exact use of `StateTreeTask_SenseEnemies`, `StateTreeTask_SetRandomFloat`, and `StateTreeTask_ShootAtTarget`.
- Whether AI spawns with a default weapon class or receives one dynamically at spawn time.
- Whether the NPC spawner supports team setup, wave logic, or spawn limits.

## UI Unknowns

### Needs manual verification in Unreal Editor

- Whether `UI_Shooter` is the match HUD, score HUD, or both.
- Whether `UI_ShooterBulletCounter` is pure local display or reads shared runtime state.
- Whether touch UI is optional/platform-gated or always initialized when relevant classes load.

## Map and World Partition Unknowns

### Needs manual verification in Unreal Editor

- Whether `Lvl_FirstPerson` is intentionally the default test map or just retained as a familiar launch map.
- Whether the missing World Partition sidecar references matter in current editor sessions or are harmless leftovers.
- Whether the broken external actor reference for the door frame causes visible runtime/editor issues.
- Whether `Lvl_ArenaShooter` is the intended main combat map for ongoing development.

## Networking Unknowns

### Needs manual verification in Unreal Editor

- Whether split-screen support is actually tested and functional.
- Whether any multiplayer logic exists beyond multiple `PlayerStart` actors and split-screen config.
- Whether any gameplay system assumes single-player only.

## Legacy / Ownership Unknowns

### Open questions

- Is `BP_FirstPersonGameMode` intentionally retained as a fallback, or is it legacy content that should eventually be removed from defaults?
- Is the stale `First Person BP Game Template` project naming intentional, or just uncleaned template metadata?
- Are missing references in `BP_WobbleTarget` and the Arena door frame known issues or accidental repository damage?
- Is `BP_Shooter_GrenadeExplosion` being a `Pawn` intentional, experimental, or unfinished?

## Suggested Manual Validation Pass

1. Open `Lvl_FirstPerson` and `Lvl_ArenaShooter` and confirm World Settings, placed actors, and actual startup workflow.
2. Open `BP_ShooterGameMode`, `BP_ShooterPlayerController`, and `BP_ShooterCharacter` Event Graphs and map the startup chain.
3. Open `BP_ShooterWeaponBase` plus one weapon child and trace fire, equip, and ammo logic.
4. Open `BP_ShooterAIController`, `ST_Shooter`, and `BP_ShooterNPCSpawner` to confirm AI spawn and decision flow.
5. Open `UI_Shooter` and `UI_ShooterBulletCounter` to confirm widget responsibilities and data sources.
