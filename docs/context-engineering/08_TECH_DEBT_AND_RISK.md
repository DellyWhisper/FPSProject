# 08_TECH_DEBT_AND_RISK

## Highest-Risk Areas

## 1. Shared Base Character Blast Radius

### Verified facts

- `BP_ShooterCharacter` inherits from `BP_FirstPersonCharacter`.
- `BP_ShooterNPC` also inherits from `BP_FirstPersonCharacter`.

### Risk

- Any change to `BP_FirstPersonCharacter` can affect both player and AI behavior.
- This is the single most important shared gameplay edit point in the project.

## 2. Config Defaults Do Not Match Active Authored Map Flow

### Verified facts

- `DefaultEngine.ini` points `GlobalDefaultGameMode` to `BP_FirstPersonGameMode`.
- Both authored maps override to `BP_ShooterGameMode`.

### Risk

- Future agents may edit the wrong GameMode if they trust config defaults more than map overrides.
- This can cause false debugging paths and incomplete fixes.

## 3. World Partition Editing Risk

### Verified facts

- Both authored maps are World Partition maps.
- Repository state for those maps spans:
  - `.umap`
  - `Content/__ExternalActors__/`
  - `Content/__ExternalObjects__/`

### Risk

- Casual map edits, renames, or content moves can create hard-to-track state mismatches.
- World Partition changes should be treated as higher-risk than ordinary Blueprint edits.

## 4. Missing and Stale References

### Verified facts

- Missing `/Game` references were found:
  - `/Game/DemoTemplate/_Core/MI_Intro_Colorway`
  - `/Game/LevelPrototyping/Interactable/Door/Assets/Meshes/SM_DoorFrame_Edge`
- Stale config/editor references still point at `/Game/FirstPersonBP/...`.
- World Partition sidecar `.ini` files reference missing directories such as `/Game/FoliageTypes`.

### Risk

- These references increase the chance of editor warnings, content repair churn, or fragile assumptions during refactors.

## 5. Template Legacy Still Mixed Into Active Project State

### Verified facts

- `DefaultGame.ini` still says `First Person BP Game Template`.
- `BP_FirstPersonGameMode` remains in config defaults.
- Several editor settings still reference missing template paths.

### Risk

- Legacy template residue makes it harder for future agents to tell what is authoritative and what is obsolete.

## 6. Blueprint-Heavy Logic Limits Repo-Only Truth

### Verified facts

- The project has no C++ gameplay module.
- Core systems are implemented through Blueprints, interfaces, StateTrees, widgets, and data assets.

### Risk

- Many runtime truths live in Event Graphs and defaults that cannot be fully reasoned about from file scanning alone.
- Agents that guess from names instead of verifying can introduce subtle breakage.

## 7. Lightweight Data Model May Not Scale Cleanly

### Verified facts

- The strongest verified data-driven system is `DT_WeaponList`.
- No Gameplay Tags, gameplay `PrimaryDataAsset` framework, or broader centralized data layer were found.

### Risk

- As the project grows, systems may become more tightly coupled through Blueprint references instead of shared data contracts.

## 8. Mixed Engine Save Versions

### Verified facts

- Key assets were saved across mixed `5.6` and `5.7` versions.

### Risk

- Editor resave churn, migration prompts, or subtle version-related content changes are more likely during future maintenance.

## 9. Partial / Prototype-Level Feature Coverage

### Verified facts

- Save/load appears absent.
- A generalized inventory system appears absent.
- Audio content is minimal.
- No strong replicated networking evidence was found.

### Risk

- Future feature requests in those areas will likely require new architecture, not just small extensions.

## 10. Legacy or Orphan Content Candidates

### Verified facts

- `BP_FirstPersonGameMode` had no `/Game/` referencers in the audit.
- Some template/editor references point at content that no longer exists.

### Risk

- There may be more legacy assets that are still present but no longer authoritative.
- Cleanup should be deliberate, not impulsive, because some assets may still matter via non-obvious editor/runtime references.

## Cleanup Opportunities

- Align project metadata and editor defaults with the actual shooter-focused project identity.
- Decide whether `BP_FirstPersonGameMode` is a supported fallback or legacy content.
- Resolve or document missing content references.
- Document the active startup flow inside Blueprint graphs after one manual editor pass.
- Consider introducing a stronger central data pattern if weapons, AI, or UI complexity grows.

## Practical Risk Posture

### Inference

- Confidence: High
- The project is workable and structurally understandable, but its safety depends on disciplined inspection before edits.
- Confidence: High
- The main danger is not broken code generation; it is editing the wrong authoritative asset because legacy/template content still coexists with the active shooter layer.
