# Sticky Battle AR --- Game Design Document

**Document status:** Draft v0.1\
**Project type:** Android mobile game\
**Genre:** Augmented reality (AR), physics, turn-based local
multiplayer\
**Players:** 2 players sharing one device\
**Primary goal:** Build a small, playable prototype that proves the core
game loop.

------------------------------------------------------------------------

## 1. Game Overview

Sticky Battle AR is a turn-based game for two people sharing one Android
phone. Players launch virtual sticky figures onto surfaces visible
through the phone's camera. Once a figure lands and attaches, it stays
in that position for the rest of the match unless an opponent knocks it
off by hitting it with a virtual ball.

Players take turns throwing balls at the other player's figures. The
goal is to knock all opposing figures off their surfaces while keeping
at least one of your own figures in play.

The real environment is the game's arena. The first prototype will
support surfaces that the phone can detect and track reliably. It will
not promise reliable placement on every possible real-world object.

## 2. Product Goals

### Goals for the first playable version

-   Run on a compatible Android phone.
-   Show the real environment through the device camera.
-   Detect a usable surface and place virtual game objects on it.
-   Let two players take turns on the same phone.
-   Let each player launch three sticky figures during setup.
-   Keep landed figures anchored to their chosen surface.
-   Let players aim and throw virtual balls using simple touch controls.
-   Detect ball-to-figure hits and decide whether a figure is knocked
    loose.
-   Track turns, remaining figures, and remaining shots.
-   Declare a winner and allow a new match to start.

### Not goals for the first version

-   Online multiplayer or connecting two phones.
-   User accounts, profiles, leaderboards, chat, or matchmaking.
-   Monetization, ads, purchases, or in-game currency.
-   Multiple arenas, campaigns, or game modes.
-   Special figure abilities, power-ups, or multiple ball types.
-   Realistic simulation of every limb gripping a surface.
-   Guaranteed tracking on glass, reflective monitors, dark surfaces, or
    every arbitrary object.
-   Advanced destruction, deformable objects, or elaborate character
    animation.

## 3. Target Platform and Technology

-   **Platform:** Android.
-   **Game engine:** Unity.
-   **Programming language:** C#.
-   **AR:** Unity AR Foundation using ARCore on supported Android
    devices.
-   **Input:** Touchscreen drag-and-release controls.
-   **Physics:** Unity physics for projectile motion and collisions.

The project should be tested on a real, ARCore-supported Android device
early. The first non-AR prototype may be built and tested in the Unity
Editor before AR features are added.

## 4. Players and Match Setup

-   There are exactly two players: Player 1 and Player 2.
-   Both players use the same phone and pass it between turns.
-   No network connection between phones is required.
-   Each player starts with three sticky figures.
-   Each player starts with five attack shots.
-   A match has two phases: figure setup and attack.
-   Player names are displayed as "Player 1" and "Player 2" in the
    initial version.

### Phase A: Figure setup

1.  The game asks the player to scan the environment for a suitable
    surface.
2.  The player aims and launches a figure using the same basic
    drag-and-release interaction intended for the ball.
3.  If the figure reaches a valid placement and settles, it attaches to
    that surface.
4.  The figure stays attached in its landed position until it is knocked
    off or the match ends.
5.  The other player takes a turn placing a figure.
6.  Players alternate until all three figures per player have been
    placed.

For the first prototype, figure placement should be simple and
forgiving. If a figure does not reach a valid surface, it should not
count as placed; the player can try again. A clear retry message should
be shown.

### Phase B: Attack

1.  Player 1 starts the attack phase.
2.  The current player aims and throws one ball at an opponent's figure.
3.  The game resolves the ball's movement and any collisions.
4.  If the ball hits a figure with sufficient impact, the figure
    detaches and is eliminated.
5.  The turn changes to the other player after the shot resolves.
6.  Players continue until a win condition is reached or both players
    have used their five shots.

A shot is consumed when the player releases a valid throw. A missed shot
still counts. The implementation should prevent the next player from
throwing while the previous ball is still resolving.

## 5. Core Gameplay Mechanics

### 5.1 Surface detection and placement

-   Use AR plane detection to identify suitable horizontal or vertical
    surfaces.
-   Only allow a figure to attach when the game has a valid surface
    pose.
-   Show a placement preview or indicator where possible.
-   Store each landed figure's position and rotation relative to its
    surface or stable AR anchor.
-   The figure should remain visually attached when the camera moves, as
    long as AR tracking remains valid.
-   If tracking is temporarily lost, pause gameplay and display a simple
    instruction to move the phone slowly and recover tracking.
-   Do not silently move figures to a different surface after tracking
    loss.

**Prototype limitation:** AR plane detection does not reliably recognize
every object or material. The first version should prioritize trackable
surfaces and make the valid placement area visible to the player.

### 5.2 Sticky figures

-   All figures use the same basic behavior in version 0.1.
-   A figure has a player owner, an attached/active state, and an
    eliminated state.
-   Once attached, a figure does not move freely due to gravity.
-   A figure can be detached by a qualifying ball impact.
-   When detached, the figure falls away from its attachment point and
    is marked eliminated.
-   Eliminated figures cannot be targeted for further scoring and do not
    return during that match.
-   The initial implementation may represent adhesion with a stable
    transform and a simple impact threshold rather than simulating every
    sticky limb.

### 5.3 Ball throwing

-   The player aims by touching the screen, dragging backward from a
    launch point, and releasing.
-   The drag direction determines the launch direction.
-   Drag distance determines launch strength, up to a configured
    maximum.
-   Show a simple aiming indicator or trajectory preview if feasible.
-   A ball follows Unity physics after launch.
-   A throw can miss, hit a surface, or hit a figure.
-   The ball should be removed or reset after its shot resolves so it
    cannot interfere with later turns.
-   Clamp launch strength to prevent extreme or unstable physics
    behavior.

The exact force, speed, and impact threshold are tuning values to
determine through playtesting, not fixed design promises.

### 5.4 Hit and detachment rules

-   Only a collision with an active opposing figure can knock that
    figure off.
-   A hit must exceed a configurable impact threshold to detach a
    figure.
-   Weak hits may have no effect in the first version.
-   A single ball should not eliminate multiple figures unless later
    playtesting explicitly adds that rule.
-   Once a figure is detached, it must be marked eliminated only once,
    even if multiple collision events occur.
-   The result of a shot must be resolved before the next player's turn
    begins.

### 5.5 Turn management

The game should have a single clear source of truth for the current
phase and current player.

Suggested phases:

1.  `MainMenu`
2.  `Scanning`
3.  `FigureSetup`
4.  `Attack`
5.  `ResolvingShot`
6.  `MatchOver`

Only valid actions for the current phase should be accepted. For
example, players cannot throw an attack ball during figure setup or
start another shot while a ball is still resolving.

## 6. Win Conditions and Shot Limit

-   A player wins immediately when all three opposing figures have been
    eliminated.
-   Each player has five attack shots.
-   If both players have used all five shots and neither player has lost
    all figures, compare the number of active figures remaining.
-   The player with more active figures wins.
-   If both players have the same number of active figures, the match is
    a draw.
-   The results screen shows the winner or draw, each player's remaining
    figures, and each player's shots used.

This is the proposed first-version rule set. Shot count and tie handling
can be adjusted after playtesting.

## 7. User Interface

Keep the interface minimal and readable over the camera view.

### Main menu

-   Game title: Sticky Battle AR.
-   Play button.
-   Short instruction or help button.
-   Exit/back behavior appropriate for Android.

### Scanning/setup screen

-   Camera feed.
-   Short instruction such as "Move your phone slowly to find a
    surface."
-   A clear surface/placement indicator when available.
-   Current player label.
-   Figures placed and figures remaining.

### Attack screen

-   Camera feed and anchored figures.
-   Current player's turn.
-   Opponent figures remaining.
-   Current player's shots remaining.
-   Drag-and-release aiming interaction.
-   Short message for events such as "Miss," "Figure knocked off," or
    "Tracking lost."

### Results screen

-   Winner or draw.
-   Figures remaining for each player.
-   Play Again button.
-   Main Menu button.

Avoid adding complex menus, settings, tutorials, or decorative UI until
the main gameplay loop works.

## 8. Visual Direction and Audio

-   Use simple, colorful, easily distinguishable figures for the
    prototype.
-   Give Player 1 and Player 2 different colors.
-   Use a clearly visible ball and a simple launch indicator.
-   Use basic animations for launch, impact, detachment, and falling.
-   Optional basic sound effects: launch, impact, figure falling, and
    match win.
-   Temporary primitive shapes or placeholder assets are acceptable
    during early development.
-   Do not block gameplay implementation on custom character art or
    polished effects.

## 9. Error and Edge-Case Handling

-   **No surface found:** Keep the player in scanning mode and explain
    how to find a surface.
-   **Invalid figure landing:** Do not consume the placement; allow a
    retry.
-   **Tracking lost:** Pause actions and show a recovery instruction.
-   **Ball misses everything:** Resolve the shot after the ball stops or
    a timeout is reached, then change turns.
-   **Ball never stops:** Use a maximum shot-resolution time so the
    match cannot become stuck.
-   **Figure hit multiple times:** Eliminate it only once.
-   **App paused or interrupted:** Resume safely where possible; if AR
    tracking cannot be recovered, offer a simple restart.
-   **Restart requested:** Clear all match objects and state before
    starting a new match.

## 10. Acceptance Criteria

The first playable version is complete when all of the following work:

1.  The project builds and runs on a compatible Android device.
2.  The app opens a camera-based AR scene and can detect at least one
    suitable surface in a normal, well-lit environment.
3.  Two players can place three figures each, taking turns on one phone.
4.  Successfully placed figures remain anchored as the phone moves,
    while AR tracking remains valid.
5.  A player can aim and launch a ball using drag-and-release controls.
6.  The ball follows a plausible physics trajectory and collides with
    surfaces and figures.
7.  A qualifying hit detaches the correct opposing figure exactly once.
8.  The game switches turns only after the shot resolves.
9.  Figure counts and shot counts update correctly.
10. The game recognizes a winner, a draw, or a shot-limit result.
11. Players can restart and play another match without leftover objects
    or state.
12. The game gives understandable feedback for invalid placement and
    tracking loss.

## 11. Development Order

Build and verify the game in small milestones:

1.  **Unity foundation:** Create the project, a basic scene, and a
    working Android build.
2.  **Non-AR throwing prototype:** Create a simple test arena, a ball,
    drag-and-release controls, and collision physics.
3.  **Sticky figures:** Add figures, attachment state, hit detection,
    and detachment.
4.  **Game rules:** Add setup turns, attack turns, shot limits,
    elimination, and win conditions.
5.  **AR integration:** Add AR Foundation/ARCore, surface detection,
    placement, and stable anchors.
6.  **UI and feedback:** Add menus, turn indicators, counts, messages,
    and results.
7.  **Device testing:** Test tracking, physics, performance,
    interruptions, and complete matches on a real phone.

If a milestone fails, fix it before moving to the next one. Do not
implement all milestones in a single large AI-generated change.

## 12. Future Ideas --- Not Part of Version 0.1

Potential later additions include:

-   Connecting two phones for multiplayer.
-   More figure types, ball types, and abilities.
-   Better sticky-limb animations and surface-specific attachment
    behavior.
-   Additional game modes and challenge levels.
-   More robust shared-space or multiplayer AR.
-   Improved visual effects, sound, progression, and leaderboards.

These ideas must not be implemented in the first version unless the
project owner explicitly changes the scope.

## 13. Decisions and Assumptions to Revisit

The following values are provisional and should be tested:

-   Three figures per player.
-   Five attack shots per player.
-   Whether figures are placed by launching them or by a simpler
    placement action.
-   How strong a hit must be to detach a figure.
-   How long the game waits before resolving a missed shot.
-   Which surface orientations are supported in the first build.
-   Whether a tie after the shot limit should remain a draw or use a
    tiebreaker.

**Guiding principle:** Build the smallest reliable game that proves the
central experience: place sticky figures in the real world, throw balls
at them, knock the opponent's figures off, and finish a complete match
on one phone.
