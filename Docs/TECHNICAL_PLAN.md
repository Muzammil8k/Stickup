# Sticky Battle AR --- Technical Plan

**Document status:** Draft v0.1\
**Related documents:** `GAME_DESIGN_DOCUMENT.md`, `GAME_FLOW.md`\
**Purpose:** Describe a simple, beginner-friendly implementation plan
for the first playable Android prototype.

------------------------------------------------------------------------

## 1. Technical Approach

Build the game incrementally in Unity using C#. Start with a small
non-AR prototype so throwing, collisions, turn management, and win
conditions can be tested without AR tracking in the way. Add AR
Foundation and ARCore after the core gameplay works.

The initial version is a local, pass-and-play game for two players on
one Android phone. It does not require a backend, network multiplayer,
user accounts, or cloud services.

### Technology choices

-   **Game engine:** Unity.
-   **Language:** C#.
-   **Target:** Android.
-   **AR framework:** Unity AR Foundation.
-   **Android AR provider:** ARCore XR Plug-in on supported devices.
-   **Physics:** Unity's built-in 3D physics (`Rigidbody`, colliders,
    forces).
-   **Input:** Unity Input System or a simple supported touch-input
    approach; choose one and use it consistently.
-   **Version control:** Git.
-   **IDE:** Unity's supported editor integration and/or VS Code with C#
    support.

Use compatible, stable package versions available for the selected Unity
editor version. Do not mix arbitrary Unity and AR Foundation versions.
Record the selected versions in the repository README once the project
is created.

## 2. Implementation Principles

1.  **Small increments:** Make one milestone work before starting the
    next.
2.  **One source of truth:** A single game controller manages phase,
    current player, counts, and transitions.
3.  **Simple first:** Use primitive shapes and placeholder materials
    until the game loop works.
4.  **Keep AR separate from rules:** Game rules should work in a non-AR
    test scene and should not depend directly on AR plane-detection
    scripts.
5.  **Explicit object states:** Figures transition between available,
    in-flight, attached, and eliminated states as appropriate.
6.  **Safe physics:** Use configurable launch and impact values, clamp
    input, and avoid repeatedly applying a hit to the same figure.
7.  **Test on device early:** AR behavior cannot be fully verified in
    the Unity Editor.
8.  **Avoid unnecessary architecture:** Do not introduce networking,
    dependency injection frameworks, databases, or complex event systems
    in version 0.1.

## 3. Proposed Unity Project Structure

The exact folders may change slightly as the project is created. Keep
the organization understandable.

``` text
Assets/
├── Scenes/
│   ├── MainMenu.unity
│   ├── PrototypeArena.unity
│   └── ARGame.unity
├── Scripts/
│   ├── Game/
│   │   ├── GameManager.cs
│   │   ├── GameState.cs
│   │   ├── PlayerMatchData.cs
│   │   └── MatchRules.cs
│   ├── Gameplay/
│   │   ├── ProjectileLauncher.cs
│   │   ├── Projectile.cs
│   │   ├── StickyFigure.cs
│   │   └── FigurePlacementController.cs
│   ├── AR/
│   │   ├── ARSurfaceController.cs
│   │   └── ARTrackingStatusUI.cs
│   └── UI/
│       ├── MainMenuUI.cs
│       ├── GameHUD.cs
│       └── ResultsUI.cs
├── Prefabs/
│   ├── StickyFigure.prefab
│   └── Ball.prefab
├── Materials/
├── Art/
└── Audio/
```

This is a suggested layout, not a requirement to create every file
immediately. Only add scripts when their milestone needs them.

## 4. Scenes and Prefabs

### 4.1 MainMenu scene

Contains: - Game title. - Play button. - Optional How to Play panel.

The Play button opens the AR game flow. During early development, it is
acceptable to open the prototype arena directly.

### 4.2 PrototypeArena scene

A non-AR test environment containing: - A floor and a few walls. - A
launch point. - A ball prefab. - Sticky figure prefabs. - A simple
HUD. - Game manager.

Use this scene to test input, projectile motion, collisions, figure
elimination, turn switching, and results.

### 4.3 ARGame scene

Contains: - AR Session. - XR Origin configured for AR. - AR plane
detection and raycasting components from AR Foundation. - Camera
background. - Placement indicator. - Game manager and HUD. - The
gameplay objects created at runtime.

Follow the setup instructions for the selected AR Foundation version;
Unity's AR object and component names can differ between package
versions.

### 4.4 StickyFigure prefab

A basic figure object with: - A visible mesh or placeholder shape. - A
collider. - `StickyFigure` component. - Owner/player identifier. -
Current state. - Attachment pose or reference to the attachment
surface. - A simple visual distinction between Player 1 and Player 2.

For the first version, adhesion is represented by a stable transform and
state, not a detailed physical simulation of each limb.

### 4.5 Ball prefab

Contains: - A sphere mesh. - Sphere collider. - Rigidbody. -
`Projectile` component.

Configure collision detection appropriately for the projectile's speed.
Start with moderate launch speeds and test for missed collisions before
considering more advanced physics settings.

## 5. Responsibilities of Core Scripts

### `GameManager.cs`

The central controller for a match.

Responsibilities: - Store the current game state and current player. -
Start and reset matches. - Track each player's placed, active, and
eliminated figures. - Track shots used. - Validate whether an action is
allowed in the current state. - Move between setup, attack, resolving,
and results states. - Evaluate win and draw conditions. - Update the HUD
through clear methods or simple events.

Do not let individual UI buttons or collision scripts independently
change the current player. They should report an event or request to the
manager.

### `GameState.cs`

An enum representing the current phase, for example:

``` csharp
public enum GameState
{
    MainMenu,
    Scanning,
    FigureSetup,
    Attack,
    ResolvingShot,
    Results
}
```

The exact state names should remain consistent with `GAME_FLOW.md`.

### `PlayerMatchData.cs`

A small data class containing: - Player ID. - Successful figure
placements. - Active figure count. - Shots used.

Keep this simple; no database is needed.

### `MatchRules.cs`

Contains the configurable first-version rules: - Three figures per
player. - Five attack shots per player. - A qualifying hit eliminates a
figure. - Elimination of all opponent figures wins immediately. - At the
shot limit, compare active figures; equal counts mean a draw.

Keep rule values in one place rather than scattering magic numbers
through scripts.

### `ProjectileLauncher.cs`

Responsibilities: - Read drag-and-release input. - Determine launch
direction and strength. - Clamp maximum drag and launch speed. - Spawn
or activate the ball or figure being launched. - Notify the game manager
when a valid throw begins.

Use the same input concept for figure setup and ball attacks, but allow
separate tuning values.

### `Projectile.cs`

Responsibilities: - Store the player who launched the projectile. -
Track whether the shot has resolved. - Detect collisions and report
relevant hits. - Avoid processing duplicate collision callbacks. -
Determine when the projectile has stopped or reached its timeout. -
Notify the game manager when the shot is complete.

A projectile should not decide the next player or evaluate the entire
match by itself.

### `StickyFigure.cs`

Responsibilities: - Store owner ID and state. - Become attached after a
valid landing. - Stop free physics movement when attached. - Remain
anchored at the attachment pose. - Evaluate qualifying impact
information. - Detach and become eliminated once. - Ignore further
scoring events after elimination.

Use a configurable impact threshold. Do not assume every collision
should knock a figure off.

### `FigurePlacementController.cs`

Responsibilities: - Manage setup-phase figure placement. - In the non-AR
prototype, provide a simple valid placement area. - In AR, use AR
raycasting against detected planes to find a valid pose. - Show a
placement indicator when a valid pose is available. - Confirm a
placement only after the launched figure reaches a valid surface and
settles. - Report successful or failed placement to the game manager.

If fully physical figure launching proves too difficult for the first
build, a simpler fallback is to let the player aim at a detected point
and launch a figure to that point. Document the change rather than
silently changing the rules.

### `ARSurfaceController.cs`

Responsibilities: - Coordinate AR plane detection and raycasts. -
Provide a valid pose for placement. - Report whether tracking is
usable. - Avoid embedding turn rules or figure elimination logic in AR
code.

### `GameHUD.cs`

Displays: - Current phase and player. - Figures remaining. - Shots
remaining. - Short feedback messages. - Buttons that are available in
the current phase.

The HUD displays game state; it does not own it.

### `ARTrackingStatusUI.cs`

Displays a tracking recovery message and prevents gameplay input while
tracking is unusable. It should not delete or re-create figures when
tracking changes.

## 6. Physics and Attachment Model

### 6.1 Projectile launch

-   Read a screen drag.
-   Convert the drag direction to a launch direction.
-   Apply an impulse to the ball's Rigidbody.
-   Clamp launch magnitude.
-   Reset the projectile's velocity before reuse, if pooling is later
    introduced.

The exact mapping from screen drag to world direction must be tested.
For the non-AR arena, use a fixed camera and launch plane first. For AR,
derive the launch ray from the camera and launch point.

### 6.2 Figure landing

For the first prototype: 1. Launch the figure with a Rigidbody. 2.
Detect a valid landing against an allowed surface. 3. Wait until the
figure is settled, using a small velocity threshold and/or short
stability timer. 4. Record its attachment pose. 5. Disable or make its
Rigidbody kinematic. 6. Mark it attached and count the placement as
successful.

In AR, keep the attachment pose in the AR world's coordinate system and
use AR anchors or an equivalent stable attachment approach where
appropriate. Do not create excessive anchors without need; select a
consistent anchor strategy and test it on the target device.

### 6.3 Impact and detachment

-   Use collision contact and relative velocity to estimate impact
    strength.
-   Compare against a configurable threshold.
-   If the target is an active opposing figure and the threshold is
    exceeded, detach it once.
-   Restore Rigidbody dynamics and apply a small impulse away from the
    surface.
-   Mark the figure eliminated immediately so later collisions cannot
    eliminate it again.

The threshold should be tuned in playtests. It is not a scientifically
accurate model of sticky material.

### 6.4 Shot resolution

A shot resolves when one of the following happens: - The projectile has
settled. - The projectile leaves the allowed play area. - A maximum
resolution timeout is reached.

On resolution: 1. Prevent further collision scoring from the projectile.
2. Finalize any figure elimination. 3. Remove or disable the projectile.
4. Check the match outcome. 5. Start the next player's turn or show
results.

## 7. AR Setup

Use AR Foundation with ARCore as the Android provider.

Required concepts: - AR Session and AR camera setup. - Camera background
rendering. - Plane detection for horizontal and vertical surfaces. - AR
raycasting to find a placement pose. - Tracking-state checks. - Stable
placement/attachment coordinates.

Implementation order: 1. Verify the selected Unity editor version and
compatible AR Foundation/ARCore packages. 2. Create a minimal AR scene
that shows the camera feed. 3. Detect and visualize at least one plane.
4. Place a simple cube on a detected plane. 5. Move the phone and verify
that the cube stays aligned while tracking remains valid. 6. Only then
add figure placement and projectile gameplay.

Do not assume that a monitor, reflective glass, featureless wall, or
every arbitrary object can be detected. Test in ordinary, well-lit
conditions and clearly restrict valid placement to detected surfaces.

## 8. UI and Input Boundaries

-   Use one consistent input implementation.
-   Keep input handlers small.
-   Reject input outside the correct game state.
-   Disable aiming controls during scanning, shot resolution, and
    results.
-   Use simple text and buttons at first.
-   Ensure player colors, active counts, and current-turn indicators are
    readable over the camera feed.

Do not build complex menus or settings before the main game loop works.

## 9. Data and Persistence

Version 0.1 does not need persistent user data.

-   Keep match state in memory.
-   A restart creates a clean match.
-   No account system, database, analytics, or server is required.
-   Optional settings such as volume can be added later.

## 10. Testing Strategy

### Editor tests

Use the non-AR prototype scene to verify: - Drag-and-release launch
behavior. - Launch speed limits. - Ball collisions. - Figure attachment
and detachment. - Duplicate collision protection. - Setup alternation. -
Shot counts. - Turn switching after shot resolution. - Win, draw, and
restart behavior.

### Android device tests

Use a real ARCore-supported phone to verify: - Camera permission and AR
session startup. - Plane detection in a well-lit room. - Valid placement
on supported horizontal and vertical planes. - Figure stability as the
phone moves. - Tracking-loss recovery. - Collision behavior at device
performance limits. - Android pause/resume behavior. - Full match
completion and restart.

### Regression checks

After changing a script, rerun the tests for its milestone and any
dependent gameplay. For example, changes to `Projectile` must not break
shot counts, elimination, or turn switching.

## 11. Milestones and Definition of Done

### Milestone 1 --- Unity foundation

-   Project opens without errors.
-   A basic scene runs in the Editor.
-   A simple Android build installs and launches.

### Milestone 2 --- Throwing prototype

-   Ball launches by drag and release.
-   Launch strength is clamped.
-   Ball collides with arena objects.
-   A missed shot resolves rather than hanging indefinitely.

### Milestone 3 --- Sticky figures

-   Figures can be placed in the test arena.
-   Attached figures stay still.
-   Strong opposing hits detach figures.
-   A figure can be eliminated only once.

### Milestone 4 --- Game rules

-   Two players alternate figure setup.
-   Attack turns alternate correctly.
-   Each player has five shots.
-   Win, draw, and restart work.

### Milestone 5 --- AR foundation

-   AR camera feed works on device.
-   Plane detection and raycasting work.
-   A virtual object remains aligned with a detected surface while
    tracking is valid.

### Milestone 6 --- AR gameplay

-   Figures can be launched and attached to detected surfaces.
-   Ball throwing and collision work in AR.
-   Turn and match rules remain correct.

### Milestone 7 --- Usability and release build

-   HUD shows player, figures, and shots.
-   Tracking loss and invalid placement provide clear feedback.
-   A complete match can be played and restarted on the target phone.
-   Known limitations are recorded.

A milestone is not complete because code was generated; it is complete
when its acceptance checks pass.

## 12. AI-Assisted Development Rules

When using an AI coding assistant:

1.  Ask it to read `GAME_DESIGN_DOCUMENT.md`, `GAME_FLOW.md`, and this
    file before changing project code.
2.  Give it one milestone or narrowly defined task at a time.
3.  Ask it to list the files it intends to change before large edits.
4.  Ask it to explain any Unity Editor setup that cannot be completed
    through code.
5.  Ask for complete, compatible scripts and instructions for attaching
    them to GameObjects.
6.  Do not accept package-version changes without an explanation.
7.  Compile and test after each change.
8.  If a change breaks a working milestone, fix or revert it before
    adding new features.
9.  Update documentation when a deliberate design decision changes.
10. Never treat code that compiles as proof that AR behavior works on a
    real device.

## 13. Technical Risks

-   **AR surface tracking:** Some materials and lighting conditions are
    difficult for ARCore to track.
-   **Shared-world expectations:** Both players use one device, so no
    shared AR alignment across phones is required.
-   **Physics versus AR geometry:** AR plane data is not a complete
    collision mesh of the room. Initial collisions should use explicit
    colliders and simple gameplay surfaces.
-   **Figure launch and attachment:** A figure may fail to land on a
    valid surface. Failed attempts must not corrupt setup counts.
-   **Camera-relative aiming:** Touch direction and 3D launch direction
    can be unintuitive; prototype and tune this before polishing.
-   **Performance:** Camera rendering, AR tracking, physics, and effects
    must run smoothly on the target phone.
-   **Scope creep:** Multiplayer networking, extra abilities, and
    complex destruction are excluded until the basic match is reliable.

## 14. Open Technical Decisions

Resolve these when they become relevant rather than guessing early: -
Exact Unity editor and package versions. - Whether the first test arena
uses a fixed camera or a simple third-person view. - The final mapping
from drag direction to launch direction. - Whether setup figures use
full Rigidbody trajectories or a simpler aimed placement fallback. - The
anchor strategy for attached figures. - The impact threshold and shot
timeout. - The minimum Android version and target device range.

**Guiding principle:** Prefer the simplest implementation that is easy
to test, understand, and change. Build the gameplay loop first, add AR
in a controlled step, and keep the game playable after every milestone.
