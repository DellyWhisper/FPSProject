# 09_AGENT_GUIDE

## Project Identity

- Unreal Engine `5.7` content-only / Blueprint-first project.
- Practical gameplay focus is a shooter prototype built in `Content/Variant_Shooter/` on top of a base framework in `Content/FirstPerson/`.
- No project-local C++ gameplay module exists.

## How To Inspect Before Editing

1. Start with the active map and runtime owner, not with the first asset name that looks relevant.
2. Check `Config/DefaultEngine.ini`, then confirm map-level overrides in:
   - `/Game/FirstPerson/Lvl_FirstPerson`
   - `/Game/Variant_Shooter/Lvl_ArenaShooter`
3. Trace inheritance before editing:
   - `BP_ShooterCharacter` -> `BP_FirstPersonCharacter`
   - `BP_ShooterPlayerController` -> `BP_FirstPersonPlayerController`
   - `BP_ShooterNPC` -> `BP_FirstPersonCharacter`
4. Check interface contracts before changing call sites:
   - `BPI_WeaponHolder`
   - `BPI_Pickups`
   - `BPI_Shooter`
   - touch interfaces
5. Treat Blueprint graph behavior as unverified until inspected in Unreal Editor.

## Important Folders

| Path | Why it matters |
| --- | --- |
| `Content/Variant_Shooter/` | Main gameplay layer and first place to inspect for active shooter systems |
| `Content/FirstPerson/` | Shared base framework used by player and NPC variants |
| `Content/Input/` | Shared locomotion and touch input |
| `Content/Variant_Shooter/Input/` | Shooter-specific input and touch UI |
| `Content/Variant_Shooter/UI/` | Shooter HUD widgets |
| `Content/Variant_Shooter/Blueprints/AI/` | AI controller, NPC, StateTrees, EQS, spawners |
| `Content/Variant_Shooter/Blueprints/Pickups/` | Pickup, weapon, projectile, and weapon table assets |
| `Content/__ExternalActors__/` | World Partition actor state for maps |
| `Config/` | Runtime defaults, startup flow, input config, and stale template references |

## Common Pitfalls

- Do not assume `BP_FirstPersonGameMode` is active just because it appears in `DefaultEngine.ini`.
- Do not change `BP_FirstPersonCharacter` casually; it affects both player and NPC-derived classes.
- Do not infer full Blueprint behavior from names alone.
- Do not treat missing template references as harmless without checking whether they cause actual editor/runtime noise.
- Do not edit World Partition map content casually outside a deliberate map-change task.

## Safe Modification Rules

- Prefer the narrowest asset change that solves the problem.
- Modify shooter-specific assets in `Content/Variant_Shooter/` before touching shared base assets.
- If a task affects maps, assume the change may span `.umap`, `__ExternalActors__`, and `__ExternalObjects__`.
- For weapon changes, inspect:
  - `DT_WeaponList`
  - `BP_ShooterPickup`
  - `BP_ShooterWeaponBase`
  - the relevant child weapon Blueprint
- For AI changes, inspect:
  - `BP_ShooterAIController`
  - `BP_ShooterNPC`
  - `ST_Shooter`
  - relevant EQS assets
- Mark Blueprint-only conclusions as needing manual editor verification when repo evidence is incomplete.

## What Not To Touch Casually

- `Content/FirstPerson/Blueprints/BP_FirstPersonCharacter`
- World Partition maps and their external actor/object data
- `Config/DefaultEngine.ini` defaults unless the task is explicitly about startup/runtime configuration
- Legacy/template cleanup targets unless the task is explicitly cleanup

## Unreal-Specific Notes For This Repo

- Enhanced Input is enabled and split between base and shooter layers.
- AI uses StateTree plus EQS, not a visible BehaviorTree setup.
- No custom `GameInstance`, `GameState`, or `PlayerState` layer was found.
- Save/load appears absent.
- Inventory appears to be weapon-focused rather than a general item system.

## Recommended Reading

1. `docs/context-engineering/PROJECT_INDEX.md`
2. `docs/context-engineering/00_PROJECT_OVERVIEW.md`
3. `docs/context-engineering/02_RUNTIME_ARCHITECTURE.md`
4. `docs/context-engineering/03_SYSTEMS_BREAKDOWN.md`
5. `docs/context-engineering/08_TECH_DEBT_AND_RISK.md`
6. `docs/context-engineering/10_TASK_ENTRYPOINTS.md`
