# 03_SYSTEMS_BREAKDOWN

## Reading Rule

- This file separates verified repository evidence from runtime inferences.
- If a behavior is likely implemented inside Blueprint graphs and not directly visible from repo inspection, it is marked `needs manual verification in Unreal Editor`.

## Player System

### Purpose

- Provide the controllable first-person shooter character and its controller layer.

### Main assets

- `/Game/FirstPerson/Blueprints/BP_FirstPersonCharacter`
- `/Game/FirstPerson/Blueprints/BP_FirstPersonPlayerController`
- `/Game/FirstPerson/Blueprints/BP_FirstPersonCameraManager`
- `/Game/Variant_Shooter/Blueprints/BP_ShooterCharacter`
- `/Game/Variant_Shooter/Blueprints/BP_ShooterPlayerController`

### Verified connections

- `BP_ShooterCharacter` inherits from `BP_FirstPersonCharacter`.
- `BP_ShooterPlayerController` inherits from `BP_FirstPersonPlayerController`.
- The shooter controller depends on shooter input and UI assets.
- The shooter character implements pickup, weapon-holder, and shooter touch interfaces.

### Current status

- Confidence: High
- Implemented and active.
- Both authored maps run through the shooter GameMode path, making the shooter player framework the real active framework.

### Risks and uncertainty

- `BP_FirstPersonCharacter` is a shared base class for both player and NPC variants.
- Exact possession, respawn, and death flow need manual verification in Unreal Editor.

## Input System

### Purpose

- Provide shared locomotion/look/jump input and shooter-specific weapon input.

### Main assets

- `/Game/Input/IA_Move`
- `/Game/Input/IA_Look`
- `/Game/Input/IA_MouseLook`
- `/Game/Input/IA_Jump`
- `/Game/Input/IMC_Default`
- `/Game/Input/IMC_MouseLook`
- `/Game/Variant_Shooter/Input/IA_Shoot`
- `/Game/Variant_Shooter/Input/IA_SwapWeapon`
- `/Game/Variant_Shooter/Input/IMC_Weapons`
- `/Game/Input/Touch/UI_TouchSimple`
- `/Game/Variant_Shooter/Input/UI_TouchInterface_Shooter`

### Verified connections

- Enhanced Input is enabled in `DefaultInput.ini`.
- Base controller uses shared locomotion contexts.
- Shooter controller adds weapon input and shooter-specific touch UI.

### Current status

- Confidence: High
- Implemented and clearly layered.

### Risks and uncertainty

- Exact timing of mapping context add or remove calls needs manual verification in Unreal Editor.
- Touch input behavior is interface-driven and likely graph-defined.

## Combat System

### Purpose

- Connect shooter character or NPC, weapon ownership, projectile spawning, and combat feedback.

### Main assets

- `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterWeaponBase`
- `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterProjectileBase`
- `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterPickup`
- `/Game/Variant_Shooter/Blueprints/Pickups/BPI_WeaponHolder`
- `/Game/Variant_Shooter/Blueprints/Pickups/BPI_Pickups`

### Verified connections

- Weapon base depends on `BPI_WeaponHolder`.
- Pickup depends on the weapon data table and weapon base.
- Projectile base is used by weapon assets and configured with projectile movement and a custom collision channel.

### Current status

- Confidence: High
- Core combat loop exists and is playable enough to support multiple weapon archetypes.

### Risks and uncertainty

- Ammo model, damage application, hit reactions, and friendly-fire rules are not fully visible from repo-only inspection.
- Needs manual verification in Unreal Editor for combat bookkeeping and damage flow.

## Weapons System

### Purpose

- Provide multiple weapon archetypes through a shared base Blueprint plus data-driven pickup selection.

### Main assets

- `/Game/Variant_Shooter/Blueprints/Pickups/BP_ShooterWeaponBase`
- `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_Pistol`
- `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_Rifle`
- `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_GrenadeLauncher`
- `/Game/Variant_Shooter/Blueprints/Pickups/Weapons/BP_ShooterWeapon_Watergun`
- `/Game/Variant_Shooter/Blueprints/Pickups/DT_WeaponList`
- `/Game/Variant_Shooter/Blueprints/Pickups/ST_WeaponTableRow`

### Verified facts

- Child weapon Blueprints are data-only specializations of `BP_ShooterWeaponBase`.
- `DT_WeaponList` row fields are:
  - `Static Mesh`
  - `Spawn Actor`
- Verified weapon rows:
  - `GrenadeLauncher`
  - `Pistol`
  - `Rifle`
  - `Watergun`

### Current status

- Confidence: High
- Implemented.
- The system has a clear base class plus table-driven pickup layer.

### Risks and uncertainty

- Weapon-specific variables beyond mesh or projectile overrides are not fully visible from repo inspection.
- Pistol references a rifle shoot montage, which may be intentional reuse or placeholder content.
- Needs manual verification in Unreal Editor for ammo counts, reload flow, equip flow, and per-weapon tuning.

## Inventory / Equipment System

### Verified facts

- No standalone inventory system was found.
- No item database, inventory UI, or inventory-specific data assets were found.
- Weapon ownership or equipment behavior is implied through:
  - `BPI_WeaponHolder`
  - pickups
  - weapon spawn classes

### Current status

- Confidence: High
- Absent as a distinct generalized inventory system.
- Confidence: Medium
- A lightweight weapon-equipment workflow exists, but it is not a full inventory layer.

### Risks and uncertainty

- Weapon swap behavior exists through input, but exact equipment state storage needs manual verification in Unreal Editor.

## Save / Load System

### Verified facts

- No `SaveGame` assets were found.
- No custom `GameInstance` was found.
- No config or asset evidence indicates a save or load pipeline.

### Current status

- Confidence: High
- Absent.

### Risks and uncertainty

- If save behavior exists, it is not discoverable from repository evidence and should be treated as not implemented until proven otherwise.

## UI System

### Purpose

- Provide shooter HUD feedback and touch or mobile interaction widgets.

### Main assets

- `/Game/Variant_Shooter/UI/UI_Shooter`
- `/Game/Variant_Shooter/UI/UI_ShooterBulletCounter`
- `/Game/Input/Touch/UI_TouchSimple`
- `/Game/Input/Touch/UI_Thumbstick`
- `/Game/Variant_Shooter/Input/UI_TouchInterface_Shooter`

### Verified connections

- `UI_Shooter` depends on `BP_ShooterGameMode` and `BPI_Shooter`.
- `BP_ShooterPlayerController` depends on `UI_ShooterBulletCounter`.
- Base and shooter touch widgets are split between shared and shooter-specific layers.

### Current status

- Confidence: Medium
- Implemented for at least basic score, ammo, and touch support.

### Risks and uncertainty

- Exact widget hierarchy, creation timing, and update bindings need manual verification in Unreal Editor.

## AI System

### Purpose

- Control NPC perception, decision-making, movement, and combat behavior.

### Main assets

- `/Game/Variant_Shooter/Blueprints/AI/BP_ShooterAIController`
- `/Game/Variant_Shooter/Blueprints/AI/BP_ShooterNPC`
- `/Game/Variant_Shooter/Blueprints/AI/BP_ShooterNPCSpawner`
- `/Game/Variant_Shooter/Blueprints/AI/ST_Shooter`
- `/Game/Variant_Shooter/Blueprints/AI/ST_Shooter_ShootAtTarget`
- `/Game/Variant_Shooter/Blueprints/AI/EQS_FindRoamLocation`
- `/Game/Variant_Shooter/Blueprints/AI/EQS_FindSnipingLocation`

### Verified connections

- AI controller uses `StateTreeAIComponent`.
- AI controller also uses `AIPerceptionComponent` with sight, hearing, and damage senses.
- Arena map contains placed NPC spawners and navmesh actors.

### Current status

- Confidence: High
- Implemented as a functioning prototype AI stack.

### Risks and uncertainty

- Exact StateTree transitions and EQS scoring logic need manual verification in Unreal Editor.
- NPC weapon or animation defaults may not be fully aligned and could rely on runtime graph logic.

## Animation System

### Purpose

- Drive first-person and third-person motion for unarmed, rifle, and pistol behavior.

### Main assets

- `/Game/Characters/Mannequins/Anims/Unarmed/ABP_Unarmed`
- `/Game/FirstPerson/Anims/ABP_FP_Copy`
- `/Game/Variant_Shooter/Anims/ABP_FP_Weapon`
- `/Game/Variant_Shooter/Anims/ABP_FP_Pistol`
- `/Game/Variant_Shooter/Anims/ABP_TP_Rifle`
- `/Game/Variant_Shooter/Anims/ABP_TP_Pistol`
- Control rigs such as `CtrlRig_FPWarp`, `Ctrl_HandAdjusment`, and `Ctrl_HandAdjusment_Pistol`

### Verified connections

- Base first-person character defaults to `ABP_Unarmed` on the main mesh and `ABP_FP_Copy` on the first-person mesh.
- Shooter or NPC assets reference weapon-specific first-person and third-person animation blueprints.

### Current status

- Confidence: Medium
- Implemented with shared mannequin content plus weapon overlays or variants.

### Risks and uncertainty

- Exact runtime animation switching logic needs manual verification in Unreal Editor.
- Some defaults still point at unarmed or rifle-specific layers, so weapon-specific behavior may be graph-driven rather than component-default driven.

## VFX System

### Verified facts

- Niagara systems found:
  - `/Game/Weapons/WaterGun/VFX/NS_WaterSplash`
  - `/Game/LevelPrototyping/Interactable/JumpPad/NS_JumpPad`
- Projectile and interactable assets reference these effects.

### Current status

- Confidence: Medium
- Present, lightweight, and effect-specific rather than part of a broader VFX framework.

### Risks and uncertainty

- Exact spawn timing and event hookup need manual verification in Unreal Editor.

## Audio System

### Verified facts

- Authored `/Game` audio content appears minimal.
- One verified `SoundWave` exists for the grenade launcher weapon fire.
- No authored `SoundCue` assets were found in the project scan.

### Current status

- Confidence: Medium
- Audio support exists in a limited form.

### Risks and uncertainty

- Combat audio coverage may be incomplete or placeholder-level.
- Needs manual verification in Unreal Editor for actual runtime cue usage.

## Networking / Multiplayer

### Verified facts

- `DefaultEngine.ini` enables split-screen layouts.
- Arena map contains multiple `PlayerStart` actors.
- No replicated Blueprint property evidence was found in the audit.
- No project-local custom `GameState` or `PlayerState` assets were found.

### Current status

- Confidence: Medium
- Split-screen or local multiplayer clues exist.
- Confidence: Low
- There is not enough evidence to claim a deliberate networked multiplayer architecture.

### Risks and uncertainty

- Treat networking support as unverified until manually tested.

## Tools / Editor Automation

### Verified facts

- `VibeUE` plugin is enabled in the project descriptor.
- No project-local editor utility Blueprints or root automation scripts were found.
- `ModelingToolsEditorMode` is enabled for editor use.

### Current status

- Confidence: Medium
- The project is editor-tool capable because plugins are enabled, but repo-local automation content is minimal.

### Risks and uncertainty

- Plugin-specific workflows may exist only in the local editor environment, not as versioned project assets.
