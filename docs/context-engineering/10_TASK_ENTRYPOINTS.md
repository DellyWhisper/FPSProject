# 10_TASK_ENTRYPOINTS

## How To Use This File

- When a future task arrives, start from the smallest entrypoint set that matches the requested change.
- Confirm active runtime ownership before editing.
- If a task crosses from shooter-specific behavior into shared framework behavior, pause and re-check inheritance blast radius.

## Add or Modify a Weapon

### Start here

1. `/Game/Variant_Shooter/Blueprints/Pickups/DT_WeaponList`
2. `/Game/Variant_Shooter/Blueprints/Pickups/ST_WeaponTableRow`
3. `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterPickup`
4. `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterWeaponBase`
5. Relevant child weapon Blueprint under `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/`

### Why

- Pickup selection is data-table-driven.
- Weapon children are data-only specializations of a shared base.
- Projectile and animation behavior likely route through the weapon base.

### Verify manually in editor

- equip flow
- fire flow
- ammo handling
- animation selection

## Adjust Input

### Start here

1. `Config/DefaultInput.ini`
2. `/Game/Input/IA_*` and `/Game/Input/IMC_*`
3. `/Game/Variant_Shooter/Input/IA_*` and `/Game/Variant_Shooter/Input/IMC_Weapons`
4. `BP_FirstPersonPlayerController`
5. `BP_ShooterPlayerController`

### Why

- Input is split between shared base mappings and shooter-specific mappings.
- Controller Blueprints are the most likely place where mapping contexts are added or managed.

## Extend UI

### Start here

1. `/Game/Variant_Shooter/UI/UI_Shooter`
2. `/Game/Variant_Shooter/UI/UI_ShooterBulletCounter`
3. `/Game/Variant_Shooter/Blueprints/BPI_Shooter`
4. `/Game/Variant_Shooter/Blueprints/BP_ShooterGameMode`
5. `/Game/Variant_Shooter/Blueprints/BP_ShooterPlayerController`

### Why

- UI appears to read from the shooter interface contract and controller or GameMode layer.

### Verify manually in editor

- widget creation ownership
- bindings or update functions
- local versus shared HUD responsibilities

## Modify AI Behavior

### Start here

1. `/Game/Variant_Shooter/Blueprints/AI/BP_ShooterAIController`
2. `/Game/Variant_Shooter/Blueprints/AI/BP_ShooterNPC`
3. `/Game/Variant_Shooter/Blueprints/AI/ST_Shooter`
4. `/Game/Variant_Shooter/Blueprints/AI/ST_Shooter_ShootAtTarget`
5. `/Game/Variant_Shooter/Blueprints/AI/EQS_FindRoamLocation`
6. `/Game/Variant_Shooter/Blueprints/AI/EQS_FindSnipingLocation`
7. `/Game/Variant_Shooter/Lvl_ArenaShooter`

### Why

- AI is StateTree-driven and validated most naturally in the arena map where spawners and navmesh exist.

## Adjust Pickups

### Start here

1. `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterPickup`
2. `/Game/Variant_Shooter/Blueprints/Pickups/DT_WeaponList`
3. Relevant placed pickup actors in:
   - `/Game/FirstPerson/Lvl_FirstPerson`
   - `/Game/Variant_Shooter/Lvl_ArenaShooter`

### Why

- Pickup presentation and spawned weapon choice are tied together here.

## Modify Projectiles or Hit Behavior

### Start here

1. `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterProjectileBase`
2. Relevant child projectile Blueprint
3. `BP_ShooterWeaponBase`
4. `Config/DefaultEngine.ini` projectile collision profile

### Why

- Collision and movement settings are split between config and projectile assets.

## Extend Player Character Behavior

### Start here

1. `/Game/Variant_Shooter/Blueprints/BP_ShooterCharacter`
2. `/Game/FirstPerson/Blueprints/BP_FirstPersonCharacter`
3. `/Game/Variant_Shooter/Blueprints/BP_ShooterPlayerController`

### Why

- Start in the shooter child first.
- Only move into `BP_FirstPersonCharacter` if the behavior must be shared with both player and NPC derivatives.

## Modify Animation Hooks

### Start here

1. Relevant mesh anim class defaults on character or weapon assets
2. `/Game/FirstPerson/Anims/ABP_FP_Copy`
3. `/Game/Variant_Shooter/Anims/ABP_FP_Weapon`
4. `/Game/Variant_Shooter/Anims/ABP_FP_Pistol`
5. `/Game/Variant_Shooter/Anims/ABP_TP_Rifle`
6. `/Game/Variant_Shooter/Anims/ABP_TP_Pistol`

### Why

- The project mixes shared and weapon-specific animation layers.

## Add a Save System

### Start here

1. Reconfirm absence of existing save architecture
2. Decide where persistent ownership should live:
   - custom `GameInstance`
   - `SaveGame`
   - subsystem or manager pattern
3. Identify which shooter systems need persistence first

### Why

- Save/load does not appear to exist already.
- This is an architecture task, not a simple extension task.

## Add Inventory or Equipment Features

### Start here

1. Re-evaluate current weapon-holder and pickup contracts
2. Inspect:
   - `BPI_WeaponHolder`
   - `BP_ShooterPickup`
   - `BP_ShooterWeaponBase`
3. Decide whether the request can remain weapon-specific or needs a broader inventory architecture

### Why

- The project has weapon equipment behavior, but not a general inventory framework.

## Add a New Gameplay Data Asset

### Start here

1. Determine whether the request fits the existing `DT_WeaponList`-style pattern
2. If not, decide whether to introduce:
   - a new data table
   - a new struct
   - a new DataAsset pattern
3. Check all Blueprint consumers before standardizing the new data path

### Why

- The project currently uses lightweight localized data rather than a broad unified data architecture.

## Modify Maps or Encounters

### Start here

1. Choose target map:
   - `/Game/FirstPerson/Lvl_FirstPerson`
   - `/Game/Variant_Shooter/Lvl_ArenaShooter`
2. Inspect placed actor categories and external actor implications
3. Be explicit about whether the change belongs in map content or reusable Blueprint content

### Why

- Both maps are World Partition maps and carry higher editing risk than isolated Blueprint assets.
