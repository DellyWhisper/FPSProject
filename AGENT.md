# AGENT.md

## Project Identity

- Unreal Engine `5.7` content-only / Blueprint-first repository.
- Practical gameplay focus is a shooter prototype centered in `Content/Variant_Shooter/`.
- Base shared framework lives in `Content/FirstPerson/`.
- No project-local C++ gameplay module exists.

## Read First

1. `Rules.md`
2. `docs/context-engineering/PROJECT_INDEX.md`
3. `docs/context-engineering/00_PROJECT_OVERVIEW.md`
4. `docs/context-engineering/02_RUNTIME_ARCHITECTURE.md`
5. `docs/context-engineering/09_AGENT_GUIDE.md`

## Key Architectural Rules

- Do not assume config defaults are the whole runtime truth.
- Both authored maps currently override to `BP_ShooterGameMode`, even though `DefaultEngine.ini` still points at `BP_FirstPersonGameMode`.
- Treat `Content/Variant_Shooter/` as the active gameplay layer.
- Treat `Content/FirstPerson/` as a shared base layer with higher blast radius.
- Treat Blueprint graph internals as unverified until inspected in Unreal Editor.
- Treat World Partition map changes as higher risk than isolated Blueprint edits.

## Important Folders

| Path | What it is |
| --- | --- |
| `Config/` | Startup/runtime/input/editor defaults |
| `Content/FirstPerson/` | Shared first-person framework |
| `Content/Variant_Shooter/` | Active shooter gameplay layer |
| `Content/Variant_Shooter/Blueprints/AI/` | AI controller, NPC, StateTrees, EQS, spawners |
| `Content/Variant_Shooter/Blueprints/Pickups/` | Weapon, projectile, pickup, and weapon-table assets |
| `Content/Variant_Shooter/UI/` | Shooter UI |
| `Content/Input/` | Shared Enhanced Input assets |
| `Content/Variant_Shooter/Input/` | Shooter-specific input assets |
| `Content/__ExternalActors__/` | World Partition actor state |
| `docs/context-engineering/` | Reusable project context package |

## How To Inspect Before Editing

1. Identify the active runtime owner:
   - map
   - GameMode
   - controller
   - character
   - interface or data table
2. Trace inheritance before editing shared assets.
3. Check whether the target behavior is likely graph-defined inside Blueprints.
4. If maps are involved, account for external actors and World Partition side effects.
5. Prefer verifying active references over trusting legacy config or template names.

## Common Pitfalls

- Editing `BP_FirstPersonCharacter` when the change should have stayed inside `BP_ShooterCharacter`
- Assuming `BP_FirstPersonGameMode` is the live gameplay owner
- Missing map-level overrides in World Settings
- Ignoring stale template references in config and editor settings
- Treating absent systems like save/load or generalized inventory as if they already exist

## Rules For Safe Modifications

- Make the smallest change in the most specific asset that owns the behavior.
- Prefer shooter-layer edits before base-layer edits.
- Do not rename or move World Partition maps casually.
- Do not perform cleanup of legacy/template content unless that is the explicit task.
- When repo evidence is incomplete, mark conclusions as `needs manual verification in Unreal Editor`.

## Unreal-Specific Notes For This Repo

- Enhanced Input is active and split between shared and shooter-specific mapping contexts.
- AI uses StateTree plus EQS.
- Weapons use a shared base Blueprint plus `DT_WeaponList`.
- No custom `GameInstance`, `GameState`, or `PlayerState` layer was found.
- Save/load appears absent.
- General inventory appears absent beyond weapon-holder behavior.

## Links

- `docs/context-engineering/PROJECT_INDEX.md`
- `docs/context-engineering/00_PROJECT_OVERVIEW.md`
- `docs/context-engineering/01_REPOSITORY_MAP.md`
- `docs/context-engineering/02_RUNTIME_ARCHITECTURE.md`
- `docs/context-engineering/03_SYSTEMS_BREAKDOWN.md`
- `docs/context-engineering/04_UNREAL_ASSET_CONVENTIONS.md`
- `docs/context-engineering/05_DATA_MODEL.md`
- `docs/context-engineering/06_BUILD_RUN_DEBUG.md`
- `docs/context-engineering/07_OPEN_QUESTIONS_AND_UNKNOWNS.md`
- `docs/context-engineering/08_TECH_DEBT_AND_RISK.md`
- `docs/context-engineering/09_AGENT_GUIDE.md`
- `docs/context-engineering/10_TASK_ENTRYPOINTS.md`
