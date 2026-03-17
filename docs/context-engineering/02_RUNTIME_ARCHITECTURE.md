# 02_RUNTIME_ARCHITECTURE

## Boot Flow

### Verified facts

- `Config/DefaultEngine.ini` sets:
  - `EditorStartupMap=/Game/FirstPerson/Lvl_FirstPerson`
  - `GameDefaultMap=/Game/FirstPerson/Lvl_FirstPerson`
  - `GlobalDefaultGameMode=/Game/FirstPerson/Blueprints/BP_FirstPersonGameMode`
  - `GameInstanceClass=/Script/Engine.GameInstance`
- Direct map inspection shows both authored maps override `default_game_mode` to:
  - `/Game/Variant_Shooter/Blueprints/BP_ShooterGameMode`
- Authored maps are:
  - `/Game/FirstPerson/Lvl_FirstPerson`
  - `/Game/Variant_Shooter/Lvl_ArenaShooter`
- Both authored maps are World Partition maps using external actors.

### Runtime consequence

1. The editor and default standalone launch target `Lvl_FirstPerson`.
2. Config still points to `BP_FirstPersonGameMode`.
3. Actual authored maps override to `BP_ShooterGameMode`.
4. In practice, entering either authored map should route gameplay through the shooter GameMode, not the base template GameMode.

### Important warning

- Do not reason about active gameplay flow from `DefaultEngine.ini` alone.
- The map override is the stronger runtime truth for the authored maps in this repository.

## Runtime Class Anchors

### Base framework

| Asset | Role | Verified connections |
| --- | --- | --- |
| `/Game/FirstPerson/Blueprints/BP_FirstPersonCameraManager` | Base camera manager | Referenced by `BP_FirstPersonPlayerController` |
| `/Game/FirstPerson/Blueprints/BP_FirstPersonPlayerController` | Base player controller | Depends on `IMC_Default`, `IMC_MouseLook`, `UI_TouchSimple`, and `BP_FirstPersonCameraManager` |
| `/Game/FirstPerson/Blueprints/BP_FirstPersonCharacter` | Shared base character | Parent of `BP_ShooterCharacter` and `BP_ShooterNPC`; implements `BPI_TouchInterface` |
| `/Game/FirstPerson/Blueprints/BP_FirstPersonGameMode` | Template-derived base GameMode | Data-only; no `/Game/` referencers were found |

### Shooter framework

| Asset | Role | Verified connections |
| --- | --- | --- |
| `/Game/Variant_Shooter/Blueprints/BP_ShooterGameMode` | Active authored-map GameMode | Referenced by both maps and by `UI_Shooter`; implements `BPI_Shooter` |
| `/Game/Variant_Shooter/Blueprints/BP_ShooterPlayerController` | Shooter player controller | Inherits from `BP_FirstPersonPlayerController`; depends on `IMC_Weapons`, shooter touch UI, `UI_ShooterBulletCounter`, `BPI_Shooter`, `BP_ShooterCharacter` |
| `/Game/Variant_Shooter/Blueprints/BP_ShooterCharacter` | Main player pawn/character | Inherits from `BP_FirstPersonCharacter`; implements `BPI_Pickups`, `BPI_WeaponHolder`, `BPI_Touch_Shooter` |
| `/Game/Variant_Shooter/Blueprints/AI/BP_ShooterNPC` | AI-controlled character | Inherits from `BP_FirstPersonCharacter`; uses `BP_ShooterAIController`; implements `BPI_WeaponHolder` |
| `/Game/Variant_Shooter/Blueprints/AI/BP_ShooterAIController` | AI controller | Uses `StateTreeAIComponent`, `AIPerceptionComponent`, and `PathFollowingComponent` |

## Character and Controller Relationships

### Verified facts

- `BP_ShooterCharacter` inherits from `BP_FirstPersonCharacter`.
- `BP_ShooterPlayerController` inherits from `BP_FirstPersonPlayerController`.
- `BP_ShooterNPC` inherits from `BP_FirstPersonCharacter`.
- `BP_FirstPersonCharacter` components include:
  - `CapsuleComponent`
  - `Mesh`
  - `FirstPersonCamera`
  - `FirstPersonMesh`
  - `CharacterMovement`
- `BP_ShooterCharacter` adds:
  - `Death Camera`
  - `SpringArm`
  - `PawnNoiseEmitter`
- `BP_ShooterNPC` keeps the inherited first-person structure and uses `ABP_TP_Rifle` on the main mesh by default.

### Inference

- Confidence: High
- The project uses one shared first-person character base for both the player-controlled shooter character and the AI character variant.
- Confidence: High
- This makes `BP_FirstPersonCharacter` a high-risk shared edit point.

## Input Boot and Attachment

### Verified facts

- `DefaultInput.ini` sets:
  - `EnhancedPlayerInput`
  - `EnhancedInputComponent`
- Base input assets:
  - `/Game/Input/IA_Move`
  - `/Game/Input/IA_Look`
  - `/Game/Input/IA_MouseLook`
  - `/Game/Input/IA_Jump`
  - `/Game/Input/IMC_Default`
  - `/Game/Input/IMC_MouseLook`
- Shooter input assets:
  - `/Game/Variant_Shooter/Input/IA_Shoot`
  - `/Game/Variant_Shooter/Input/IA_SwapWeapon`
  - `/Game/Variant_Shooter/Input/IMC_Weapons`
- `BP_FirstPersonPlayerController` depends on the base mapping contexts.
- `BP_ShooterPlayerController` adds dependencies on shooter mapping, touch, and UI assets.

### Inference

- Confidence: High
- Shared locomotion input is layered first, then shooter-specific weapon input is added by the shooter controller layer.

## Combat Runtime Spine

### Verified facts

- Weapon runtime assets are centered on:
  - `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterWeaponBase`
  - child weapons under `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/`
- Projectile runtime assets are centered on:
  - `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterProjectileBase`
  - child projectiles for bullet, water, and grenade variants
- Pickup/data entry assets are centered on:
  - `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterPickup`
  - `/Game/Variant_Shooter/Blueprints/Pickups/DT_WeaponList`
  - `/Game/Variant_Shooter/Blueprints/Pickups/ST_WeaponTableRow`
- `BP_ShooterWeaponBase` depends on:
  - `BPI_WeaponHolder`
  - first-person and third-person weapon animation assets
  - a projectile class pipeline
- `BP_ShooterPickup` depends on:
  - `DT_WeaponList`
  - `ST_WeaponTableRow`
  - `BP_ShooterWeaponBase`
  - `BPI_WeaponHolder`

### Inference

- Confidence: High
- The runtime combat path is approximately:
  - player or NPC owns or equips weapon
  - weapon uses holder interface data
  - weapon spawns projectile behavior
  - pickups determine which weapon class to spawn from a data table
- Confidence: Medium
- Exact fire rate, ammo, and equip logic are likely defined inside Blueprint graphs and need manual editor verification.

## AI Runtime Spine

### Verified facts

- `BP_ShooterAIController` contains:
  - `StateTreeAIComponent`
  - `AIPerceptionComponent`
  - `PathFollowingComponent`
- `StateTreeAIComponent` auto-starts logic and points to `/Game/Variant_Shooter/Blueprints/AI/ST_Shooter`.
- `AIPerceptionComponent` has configured senses for:
  - sight
  - hearing
  - damage
- AI content includes:
  - `ST_Shooter`
  - `ST_Shooter_ShootAtTarget`
  - `EQS_FindRoamLocation`
  - `EQS_FindSnipingLocation`
  - several Blueprint StateTree tasks and conditions

### Inference

- Confidence: High
- AI runtime behavior is StateTree-driven rather than BehaviorTree-driven.
- Confidence: Medium
- Likely flow: perceive target, decide roam or sniping behavior, face target or location, then enter nested shoot-at-target logic if line-of-sight conditions are met.

## UI Runtime Spine

### Verified facts

- Shooter UI assets are:
  - `/Game/Variant_Shooter/UI/UI_Shooter`
  - `/Game/Variant_Shooter/UI/UI_ShooterBulletCounter`
- `UI_Shooter` depends on `BP_ShooterGameMode` and `BPI_Shooter`.
- `BP_ShooterPlayerController` depends on `UI_ShooterBulletCounter`.
- `BP_ShooterGameMode` depends on `UI_Shooter`.

### Inference

- Confidence: Medium
- `UI_Shooter` likely exposes score or team state through `BPI_Shooter`.
- Confidence: Medium
- `UI_ShooterBulletCounter` is likely the local ammo display.
- Needs manual verification in Unreal Editor:
  - widget tree layout
  - creation timing
  - bindings and update flow

## Map-Level Runtime Flow

### Verified facts

- `Lvl_FirstPerson`:
  - uses `BP_ShooterGameMode`
  - has AI system enabled
  - has navigation system disabled
  - contains a simple set of placed actors including one shooter pickup and one player start
- `Lvl_ArenaShooter`:
  - uses `BP_ShooterGameMode`
  - has AI system enabled
  - has navigation system enabled
  - contains placed pickups, jump pads, wobble targets, NPC spawners, several player starts, and navmesh actors

### Inference

- Confidence: High
- `Lvl_FirstPerson` is the current launch/bootstrap map but behaves as a simplified shooter sandbox.
- Confidence: High
- `Lvl_ArenaShooter` is the richer combat/prototype map and likely the main feature testbed.

## Unknowns That Need Editor Verification

- Exact Blueprint Event Graph flow for:
  - spawning UI
  - equipping weapons
  - ammo bookkeeping
  - projectile spawn timing
  - score updates
  - death or respawn handling
- Exact StateTree and EQS node graphs.
- Whether animation blueprints swap dynamically when weapon classes change.
- Whether `BP_FirstPersonGameMode` is intentionally retained as a fallback or is now legacy content.
