# AdaptiveRoads

A Cities: Skylines mod that extends network and road customization capabilities with advanced flagging and tagging systems.

## Recent Updates - March 2026

### Platform Support
- ✅ **macOS Build Support** – Previously Windows-only, now builds and deploys on both Windows and macOS
- OS-aware build configuration using MSBuild conditionals (`$(OS)`)
- Auto-deployment to correct Mods folder location per platform


### Race Day Update Compatibility (Current Game Version)
The codebase has been fully updated to support the latest Cities: Skylines API changes and to ensure all Harmony patches target the correct overloads.

#### DynamicFlags API Migration
The game's tag/flag system was refactored to use generics. All calls updated:
- `NetInfo.AddTags()` → `DynamicFlags<NetInfo>.AddTags()`
- `NetInfo.GetFlags()` → `DynamicFlags<NetInfo>.GetFlags()`
- `NetInfo.allTags` → `DynamicFlags<NetInfo>.allTags`
- Old `Check()` method → `CheckAll()` and `CheckAny()` on generic struct

#### Type System
- Changed non-generic `DynamicFlags` to generic `DynamicFlags<T>`
- Network tags now use `DynamicFlags<NetInfo>` everywhere
- Updated method signatures, fields and return types accordingly

#### Harmony Patch Fixes
- All reflection calls now specify explicit parameter types to avoid
  `AmbiguousMatchException` (e.g. `CalculateCorner`, `RenderInstance`)
- Corrected `CalculateCorner_ShiftPatch`, `SharpPatch` and anti‑flicker
  patch signatures to match the new game overloads
- Added missing `using System;` where needed for `Type` references
- **Runtime safety improvements** – transpilers now catch missing IL
  patterns and log warnings instead of crashing; this makes the mod
  degrade gracefully on future game changes.

#### Initialization & Utility Corrections
- `NetUtil` static constructor now retrieves tag dictionary from
  `DynamicFlags<NetInfo>.kTags` instead of non-existent `NetInfo.kTags`
- Blue road/node overlay issue resolved by fixing the type initializer
- Various nodes/segments use proper `DynamicFlags<NetInfo>` casts
- **CheckFlags API change** – game added a `Flags2` parameter to
  `NetInfo.Segment.CheckFlags`.  The helper method was rewritten to
  avoid calling the old signature and simply use the `turnAround`
  value already computed by the game.

These changes bring AdaptiveRoads up‑to‑date with the current game API,
eliminating runtime Harmony errors and visual glitches.
#### Affected Files
- `Manager/NetInfoExtension/Flags.cs` – Tag validation system
- `Manager/NetInfoExtension/Net.cs` – Network metadata
- `Data/Flags/TagSource.cs` – Tag registry
- `Data/NetworkExtensions/LaneTransition.cs` – Lane configuration
- `Data/NetworkExtensions/TrackRenderData.cs` – Rendering system
- `KianCommons/Util/NetUtil.cs` – Network utilities
- `Util/DirectConnectUtil.cs` – Connection groups
- `KianCommons/Util/DynamicFlags2.cs` – Legacy flag wrapper
- `Patches/Segment/CheckSegmentFlagsCommons.cs` – new CheckFlags
  implementation
- `Patches/Node/AntiFlickering/RefreshJunctionDataPatch.cs` –
  improved error reporting

#### Method Updates
- `NetSegment.PopulateGroupData()` now requires `Vector2 objectColorIndex` parameter
- Extension methods refactored to use new game API
- Removed unused `Epic.OnlineServices.Presence` dependency

## Building



```bash
# Build for current platform
dotnet build

# Deploy to Mods folder
dotnet msbuild /t:DeployToModDirectory
```

Build automatically detects your OS and uses appropriate paths:
- **Windows**: Game assemblies from Steam install, deploys to `%LOCALAPPDATA%/...Mods/`
- **macOS**: Game assemblies from app bundle, deploys to `~/Library/Application Support/.../Mods/`

## Dependencies

- `.NET Framework 3.5` (target)
- CitiesHarmony
- ColossalFramework
- UnifiedUILib
- PrefabMetadataAPI (internal)

## Compatibility

✅ Works with current Cities: Skylines version (post-Race Day update)  
✅ Builds on Windows and macOS  
✅ Auto-deployment on both platforms
