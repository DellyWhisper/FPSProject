# 05_DATA_MODEL

## Data Model Summary

### Verified facts

- The project is not driven by a broad custom C++ data model because there is no `Source/` module.
- The strongest verified data-driven system is the shooter weapon lookup table:
  - `/Game/Variant_Shooter/Blueprints/Pickups/DT_WeaponList`
  - `/Game/Variant_Shooter/Blueprints/Pickups/ST_WeaponTableRow`
- Input is also data-driven through Enhanced Input assets.
- Some runtime behavior is config-driven through `Config/*.ini`.

## Config-Driven Data

### Verified facts

- `Config/DefaultEngine.ini` drives:
  - startup and default maps
  - default GameMode fallback
  - AI system defaults
  - collision profile for projectile behavior
  - renderer and RHI defaults
  - split-screen defaults
- `Config/DefaultInput.ini` drives:
  - `EnhancedPlayerInput`
  - `EnhancedInputComponent`
  - input axis defaults

### Important config-backed runtime facts

- `Projectile` collision profile is explicitly configured in `DefaultEngine.ini`.
- `GameInstanceClass` remains `/Script/Engine.GameInstance`, so there is no verified custom runtime-persistent data owner.

## Input Data Assets

| Asset | Role | Verified notes |
| --- | --- | --- |
| `/Game/Input/IA_Move` | Movement action | `AXIS2D` |
| `/Game/Input/IA_Look` | Look action | `AXIS2D` |
| `/Game/Input/IA_MouseLook` | Mouse look action | `AXIS2D` |
| `/Game/Input/IA_Jump` | Jump action | `BOOLEAN`; pressed and released triggers |
| `/Game/Variant_Shooter/Input/IA_Shoot` | Shoot action | `BOOLEAN`; pressed and released triggers |
| `/Game/Variant_Shooter/Input/IA_SwapWeapon` | Swap weapon action | `BOOLEAN`; pressed trigger |
| `/Game/Input/IMC_Default` | Shared locomotion mapping | WASD, arrows, gamepad move, jump |
| `/Game/Input/IMC_MouseLook` | Mouse look mapping | `Mouse2D` with Y negate |
| `/Game/Variant_Shooter/Input/IMC_Weapons` | Shooter combat mapping | left mouse, gamepad trigger and shoulder, shift swap |

## Weapon Data Table

### Verified facts

- Data table asset:
  - `/Game/Variant_Shooter/Blueprints/Pickups/DT_WeaponList`
- Row struct asset:
  - `/Game/Variant_Shooter/Blueprints/Pickups/ST_WeaponTableRow`
- Verified exported data table fields:
  - `Static Mesh`
  - `Spawn Actor`

### Verified rows

| Row | Static Mesh | Spawn Actor |
| --- | --- | --- |
| `GrenadeLauncher` | `/Game/Weapons/GrenadeLauncher/Meshes/SM_GrenadeLauncher` | `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_GrenadeLauncher` |
| `Pistol` | `/Game/Weapons/Pistol/Meshes/SM_Pistol` | `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_Pistol` |
| `Rifle` | `/Game/Weapons/Rifle/Meshes/SM_Rifle` | `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_Rifle` |
| `Watergun` | `/Game/Weapons/WaterGun/WATERGUN` | `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_Watergun` |

### Relationship

1. Pickup logic references the data table.
2. A row selects display mesh plus runtime weapon class.
3. The weapon class then points into the projectile and animation pipeline.

## Interface-Level Contracts

### Verified facts

- `BPI_WeaponHolder` exists and is implemented by:
  - `BP_ShooterCharacter`
  - `BP_ShooterNPC`
- `BPI_Pickups` exists and is implemented by `BP_ShooterCharacter`.
- `BPI_Shooter` exists and is implemented by:
  - `BP_ShooterGameMode`
  - `BP_ShooterPlayerController`
- `BPI_TouchInterface` exists and is implemented by `BP_FirstPersonCharacter`.
- `BPI_Touch_Shooter` is part of the shooter touch layer.

### Inference

- Confidence: High
- Interfaces are an important part of the project's data and communication model because there is no C++ type system layer defining these contracts.

## Asset-Class Data Patterns

### Verified facts

- Weapon variants are mostly data-only child Blueprints of `BP_ShooterWeaponBase`.
- Projectile variants are child Blueprints of `BP_ShooterProjectileBase`.
- `BP_ShooterGameMode` and `BP_ShooterPlayerController` share the `BPI_Shooter` interface contract.

### Inference

- Confidence: High
- This project favors Blueprint inheritance plus interface contracts over standalone gameplay data asset systems.

## Missing or Minimal Data Systems

### Verified absence

- No project-local Gameplay Tags config was found.
- No `UserDefinedEnum` assets were found.
- No `SaveGame` assets were found.
- No gameplay `PrimaryDataAsset` assets were found.
- No custom `GameState` or `PlayerState` data layer was found.

### Inference

- Confidence: High
- The project's current data model is lightweight and localized rather than unified under a central data framework.

## Data Risks

- Important runtime data may still live inside Blueprint default variables that are not fully inspectable from repo scanning alone.
- The data table currently covers weapon pickup selection, but not a broader weapon stat schema visible from repository inspection.
- Because the project lacks gameplay tags and richer central data assets, cross-system expansion may create more direct Blueprint dependencies unless architecture is intentionally strengthened later.
