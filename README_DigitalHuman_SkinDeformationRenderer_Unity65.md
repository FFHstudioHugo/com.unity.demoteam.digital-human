# Digital Human Unity 6.5 patch - SkinDeformationRenderer

## Summary

Fixes the Unity 6.5 `Object.GetInstanceID()` obsolete API error in `Runtime/SkinDeformationRenderer.cs`.

Unity 6.5 deprecates `Object.GetInstanceID()`. The original disable path checked `smr.sharedMesh.GetInstanceID() >= 0` to decide whether the active mesh was the generated mesh instance. The patch removes that dependency and compares object references directly.

## Changed file

- `Runtime/SkinDeformationRenderer.cs`

## Change list

- Replaces the `smr.sharedMesh.GetInstanceID() >= 0` guard.
- Uses a direct reference check against `meshInstance` instead:

```csharp
if (smr == null || smr.sharedMesh == null || meshInstance == null || smr.sharedMesh != meshInstance)
    return;
```

- Keeps the old `GetInstanceID()` mention only in a comment/log line, outside active compile-sensitive code.


## PR title

```text
Fix Unity 6.5 SkinDeformationRenderer mesh check
```

## PR description

```markdown
## Summary

Fixes the Unity 6.5 obsolete `Object.GetInstanceID()` compile error in `SkinDeformationRenderer`.

Unity 6.5 deprecates `GetInstanceID()`. The renderer used the sign of `smr.sharedMesh.GetInstanceID()` to decide whether it owned the generated mesh instance during disable cleanup. This PR replaces that with a direct reference comparison against the generated `meshInstance`.

## Changes

- Removes the active `smr.sharedMesh.GetInstanceID() >= 0` compile path.
- Uses `smr.sharedMesh != meshInstance` to skip cleanup when the renderer is not using the generated mesh instance.
- Leaves older comment-only `GetInstanceID()` references untouched.

## Files

- `Runtime/SkinDeformationRenderer.cs`

## Test plan

1. Update the package in Unity 6.5.
2. Recompile the project.
3. Confirm `SkinDeformationRenderer.cs(169,58)` no longer reports CS0619.
4. Enter/exit play mode with a Digital Human renderer and verify generated mesh cleanup still behaves correctly.
```
