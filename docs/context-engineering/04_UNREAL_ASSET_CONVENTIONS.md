# 04_UNREAL_ASSET_CONVENTIONS

## Naming Patterns Observed

### Verified facts

- Blueprint classes use `BP_`:
  - `BP_FirstPersonCharacter`
  - `BP_ShooterGameMode`
  - `BP_ShooterNPCSpawner`
- Blueprint interfaces use `BPI_`:
  - `BPI_TouchInterface`
  - `BPI_Pickups`
  - `BPI_WeaponHolder`
  - `BPI_Shooter`
- Widget Blueprints use `UI_`:
  - `UI_Shooter`
  - `UI_ShooterBulletCounter`
  - `UI_TouchSimple`
- Input actions use `IA_`:
  - `IA_Move`
  - `IA_Look`
  - `IA_Shoot`
- Input mapping contexts use `IMC_`:
  - `IMC_Default`
  - `IMC_MouseLook`
  - `IMC_Weapons`
- Animation Blueprints use `ABP_`:
  - `ABP_Unarmed`
  - `ABP_FP_Copy`
  - `ABP_TP_Rifle`
- StateTrees use `ST_`:
  - `ST_Shooter`
  - `ST_Shooter_ShootAtTarget`
  - `ST_WeaponTableRow`
- EQS queries use `EQS_`:
  - `EQS_FindRoamLocation`
  - `EQS_FindSnipingLocation`
- Niagara systems use `NS_`:
  - `NS_WaterSplash`
  - `NS_JumpPad`
- Materials and material instances use `M_` and `MI_`.
- Static meshes use `SM_`.
- Skeletal meshes use `SKM_`.
- Maps use `Lvl_`:
  - `Lvl_FirstPerson`
  - `Lvl_ArenaShooter`

## Folder Conventions Observed

### Verified facts

- Shared base framework content lives under `Content/FirstPerson/`.
- Shooter-specific gameplay content lives under `Content/Variant_Shooter/`.
- AI content is grouped under `Content/Variant_Shooter/Blueprints/AI/`.
- Weapon and projectile runtime assets are grouped under `Content/Variant_Shooter/Blueprints/Pickups/`.
- Weapon art content is grouped under `Content/Weapons/`.
- Base input assets live under `Content/Input/`.
- Shooter-specific input lives under `Content/Variant_Shooter/Input/`.
- World Partition map sidecar data lives under:
  - `Content/__ExternalActors__/`
  - `Content/__ExternalObjects__/`

## Architectural Naming Pattern

### Inference

- Confidence: High
- The repository uses folder names to separate:
  - shared base layer
  - shooter extension layer
  - art/content support
  - prototype environment pieces
- Confidence: High
- `FirstPerson` is the shared base namespace.
- Confidence: High
- `Variant_Shooter` is the gameplay specialization namespace.

## Conventions That Seem Stable

### Verified facts

- Shooter gameplay assets consistently use the `Shooter` infix:
  - `BP_ShooterCharacter`
  - `BP_ShooterGameMode`
  - `BP_ShooterWeaponBase`
  - `BP_ShooterProjectileBase`
  - `BP_ShooterNPC`
- Weapon variants consistently derive from a shared `BP_ShooterWeaponBase`.
- Projectile variants consistently derive from a shared `BP_ShooterProjectileBase`.
- Touch UI is separated from standard input assets.

## Conventions That Are Inconsistent

### Verified facts

- `DefaultGame.ini` still uses template naming: `First Person BP Game Template`.
- `DefaultEditor.ini` and `DefaultEditorPerProjectUserSettings.ini` still reference `/Game/FirstPersonBP`, which does not exist.
- `BP_FirstPersonGameMode` remains in config defaults even though both authored maps override to `BP_ShooterGameMode`.
- Some assets still carry template or prototype-era naming rather than project-specific naming.

### Inference

- Confidence: High
- Naming consistency is strong inside the active shooter layer, but weaker at the project metadata and template-legacy edges.

## Practical Rules For Future Consistency

- Keep new shooter runtime assets under `Content/Variant_Shooter/` unless they are truly generic and reusable.
- Put shared base-framework edits in `Content/FirstPerson/` only when multiple downstream classes need them.
- Continue using established Unreal-style prefixes:
  - `BP_`, `BPI_`, `UI_`, `IA_`, `IMC_`, `ABP_`, `ST_`, `EQS_`, `NS_`, `M_`, `MI_`, `SM_`, `SKM_`
- Keep AI assets grouped in `Content/Variant_Shooter/Blueprints/AI/`.
- Keep weapon definitions and pickup-side runtime assets grouped with the existing pickup or weapon Blueprint hierarchy unless the project later introduces a broader item system.
- Do not casually rename World Partition maps or move map-related assets without planning for external actor side effects.

## Recommendations

### Inference

- Confidence: High
- Future cleanup should prioritize stale config and template references before broad asset renaming.
- Confidence: Medium
- If the project grows, consider separating `Pickups`, `Weapons`, and `Projectiles` into clearer sibling folders to reduce mixed-responsibility naming inside `Blueprints/Pickups/`.
