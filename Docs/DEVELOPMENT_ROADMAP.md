# Sticky Battle AR --- Development Roadmap

**Document status:** Draft v0.1\
**Related documents:** `GAME_DESIGN_DOCUMENT.md`, `GAME_FLOW.md`,
`TECHNICAL_PLAN.md`\
**Purpose:** Break development into small, verifiable tasks suitable for
a Unity beginner using AI coding assistance.

------------------------------------------------------------------------

## How to Use This Roadmap

-   Work on one milestone at a time.
-   Complete the tasks in order unless a dependency clearly requires a
    change.
-   Do not move on until the milestone's acceptance criteria pass.
-   Keep the project runnable after every milestone.
-   Use placeholder shapes and materials before spending time on art.
-   Test AR behavior on a real ARCore-supported Android device.
-   Update this file when a task is completed or a design decision
    changes.

**Status legend:** `Not Started` · `In Progress` · `Blocked` · `Done`

------------------------------------------------------------------------

## Milestone 0 --- Prepare the Development Environment

**Status:** Not Started

### Tasks

-   [ ] Install a compatible Unity Hub and Unity Editor version.
-   [ ] Include Android Build Support, Android SDK & NDK Tools, and
    OpenJDK modules in the Unity installation.
-   [ ] Install or configure a C#-capable editor such as Visual Studio
    or VS Code.
-   [ ] Check whether the Android phone is supported by ARCore.
-   [ ] Enable Developer Options and USB debugging on the phone if using
    USB installation.
-   [ ] Create a Git repository for the project.
-   [ ] Add Unity's recommended `.gitignore`.
-   [ ] Create a Unity 3D project named `StickyBattleAR`.
-   [ ] Create a `Docs/` directory in the project root and add the
    project documentation.

### Acceptance criteria

-   [ ] Unity opens the project without errors.
-   [ ] A simple scene can be created and saved.
-   [ ] The project is tracked by Git.
-   [ ] The target phone's ARCore compatibility has been checked.

### Do not do yet

Do not install extra assets, build multiplayer, or start implementing AR
gameplay before the project opens reliably.

------------------------------------------------------------------------

## Milestone 1 --- First Scene and Android Build

**Status:** Not Started

### Tasks

-   [ ] Create `PrototypeArena.unity`.
-   [ ] Add a floor, a few walls, a camera, and basic lighting.
-   [ ] Add a simple visible object to prove that the scene runs.
-   [ ] Configure Android as the build platform.
-   [ ] Set a temporary application name and package identifier.
-   [ ] Build and install an APK on the target phone.
-   [ ] Record the Unity Editor version and package versions in the
    project README.

### Acceptance criteria

-   [ ] The scene runs in the Unity Editor.
-   [ ] The APK installs and launches on the phone.
-   [ ] No blocking compile or startup errors remain.

### Deliverable

A minimal Unity project that can run on Android.

------------------------------------------------------------------------

## Milestone 2 --- Ball Throwing Prototype

**Status:** Not Started

### Tasks

-   [ ] Create a ball prefab with a mesh, collider, and Rigidbody.
-   [ ] Add a launch point.
-   [ ] Implement touch drag-and-release controls in the test arena.
-   [ ] Show the player a simple aiming direction or power indicator.
-   [ ] Map drag distance to launch strength.
-   [ ] Clamp the maximum launch strength.
-   [ ] Reset projectile velocity before launch if reusing a projectile.
-   [ ] Add simple walls or targets to test collisions.
-   [ ] Resolve a missed shot when the ball stops, leaves the play area,
    or reaches a timeout.

### Acceptance criteria

-   [ ] A drag-and-release gesture launches a ball.
-   [ ] A longer valid drag can produce a stronger throw, within the
    configured limit.
-   [ ] The ball collides with test surfaces.
-   [ ] Invalid or tiny drags do not launch an unintended projectile.
-   [ ] A missed shot cannot leave the game stuck indefinitely.

### Deliverable

A repeatable, non-AR ball-throwing mechanic.

------------------------------------------------------------------------

## Milestone 3 --- Sticky Figure Attachment and Detachment

**Status:** Not Started

### Tasks

-   [ ] Create a simple figure prefab using a primitive shape or
    placeholder model.
-   [ ] Add a collider and a clear owner/player identifier.
-   [ ] Define figure states: in flight, attached, and eliminated.
-   [ ] Implement a valid landing area in the test arena.
-   [ ] Attach a figure when it reaches a valid surface and settles.
-   [ ] Stop the attached figure from falling under gravity.
-   [ ] Let a qualifying impact from an opposing projectile detach the
    figure.
-   [ ] Restore physics when a figure detaches so it can fall.
-   [ ] Prevent duplicate collision callbacks from eliminating the same
    figure twice.
-   [ ] Track active figures by player.

### Acceptance criteria

-   [ ] A figure can attach to a valid surface.
-   [ ] An attached figure stays in place.
-   [ ] A qualifying opposing hit detaches it.
-   [ ] A weak hit can be ignored if it is below the configured
    threshold.
-   [ ] An eliminated figure cannot be eliminated or scored again.

### Deliverable

The core sticky-figure mechanic works in a non-AR arena.

------------------------------------------------------------------------

## Milestone 4 --- Setup Turns and Match Rules

**Status:** Not Started

### Tasks

-   [ ] Implement the central game-state controller.
-   [ ] Add the main states: menu, scanning, setup, attack, resolving
    shot, and results.
-   [ ] Add Player 1 and Player 2 match data.
-   [ ] Implement alternating setup turns.
-   [ ] Require three successful figure placements per player.
-   [ ] Ensure failed placements allow the same player to retry.
-   [ ] Start the attack phase only after all six figures are placed.
-   [ ] Implement five attack shots per player.
-   [ ] Switch turns only after the previous shot resolves.
-   [ ] Implement immediate victory when all opposing figures are
    eliminated.
-   [ ] At the shot limit, compare remaining figures and handle a draw.
-   [ ] Add a restart that clears all objects and counters.

### Acceptance criteria

-   [ ] Both players can complete setup in alternating turns.
-   [ ] Setup cannot finish with fewer than six successful placements.
-   [ ] Each valid attack consumes exactly one shot.
-   [ ] Turns cannot overlap while a shot is resolving.
-   [ ] Winner, draw, and restart behavior work repeatedly.

### Deliverable

A complete non-AR match with the agreed rules.

------------------------------------------------------------------------

## Milestone 5 --- Basic User Interface

**Status:** Not Started

### Tasks

-   [ ] Create a simple main menu.
-   [ ] Add a Play button.
-   [ ] Show the current player during setup and attack.
-   [ ] Show active figures remaining for both players.
-   [ ] Show attack shots remaining for both players.
-   [ ] Add short messages for misses, successful hits, and invalid
    actions.
-   [ ] Create a results panel with winner/draw, counts, Play Again, and
    Main Menu.
-   [ ] Ensure UI buttons cannot perform actions in invalid game states.

### Acceptance criteria

-   [ ] A new player can understand whose turn it is.
-   [ ] Figure and shot counts update correctly.
-   [ ] Results are clear.
-   [ ] Restart produces a clean match.

### Deliverable

A playable non-AR prototype with minimal UI.

------------------------------------------------------------------------

## Milestone 6 --- AR Foundation Setup

**Status:** Not Started

### Tasks

-   [ ] Select compatible Unity, AR Foundation, and ARCore XR Plug-in
    versions.
-   [ ] Install AR Foundation and ARCore XR Plug-in using Unity Package
    Manager.
-   [ ] Configure the project for ARCore and Android.
-   [ ] Create `ARGame.unity`.
-   [ ] Add the AR Session and correctly configured XR Origin/AR camera
    for the selected package version.
-   [ ] Configure the camera background.
-   [ ] Request camera permission as needed.
-   [ ] Enable suitable horizontal and vertical plane detection.
-   [ ] Add AR raycasting.
-   [ ] Display a simple plane visualization or placement indicator.
-   [ ] Build and test on the real phone.

### Acceptance criteria

-   [ ] The app opens the AR camera view on a supported device.
-   [ ] At least one suitable plane can be detected in ordinary,
    well-lit conditions.
-   [ ] The placement indicator follows a valid raycast hit.
-   [ ] A simple cube can be placed on the detected surface.
-   [ ] The cube stays aligned as the phone moves while tracking remains
    valid.

### Deliverable

A minimal, working AR scene independent of the game mechanics.

------------------------------------------------------------------------

## Milestone 7 --- Integrate Sticky Figures with AR

**Status:** Not Started

### Tasks

-   [ ] Connect the setup phase to AR plane detection and raycasting.
-   [ ] Launch figures toward a valid target or use the documented
    placement fallback if needed.
-   [ ] Confirm placement only after a figure reaches and settles on a
    valid surface.
-   [ ] Store a stable attachment pose or anchor reference.
-   [ ] Keep attached figures aligned with their surfaces during camera
    movement.
-   [ ] Ensure failed placements do not consume a successful placement.
-   [ ] Test horizontal and vertical surfaces separately.
-   [ ] Add a tracking-lost message and pause gameplay input while
    tracking is unusable.

### Acceptance criteria

-   [ ] A player can place a figure on a supported detected surface.
-   [ ] The figure remains visually attached while tracking is valid.
-   [ ] The other player's figures can be placed in the same AR session.
-   [ ] Tracking loss does not silently delete or reposition figures.
-   [ ] The game can recover from temporary tracking loss or offer a
    safe restart.

### Deliverable

Players can create a shared battlefield on one phone using real-world
surfaces.

------------------------------------------------------------------------

## Milestone 8 --- Integrate Ball Physics with AR Gameplay

**Status:** Not Started

### Tasks

-   [ ] Add the attack launch point and drag controls to the AR scene.
-   [ ] Map screen drag to a consistent 3D launch direction.
-   [ ] Tune launch strength and maximum speed on the target phone.
-   [ ] Configure projectile colliders and collision detection.
-   [ ] Detect qualifying hits on active opposing figures.
-   [ ] Detach hit figures and animate their fall.
-   [ ] Ensure shots resolve if the ball misses or leaves the play area.
-   [ ] Verify that turns and counts remain correct after each shot.
-   [ ] Test impacts from different angles and distances.

### Acceptance criteria

-   [ ] A player can aim and throw a ball in AR.
-   [ ] Ball collisions behave consistently enough for a playable
    prototype.
-   [ ] A qualifying hit detaches the correct opposing figure.
-   [ ] Misses and timeouts advance the game safely.
-   [ ] No extra turns, duplicate eliminations, or duplicate shot counts
    occur.

### Deliverable

The full core game loop works in AR.

------------------------------------------------------------------------

## Milestone 9 --- End-to-End Testing and Polish

**Status:** Not Started

### Tasks

-   [ ] Play complete matches on the target phone.
-   [ ] Test both horizontal and vertical surfaces.
-   [ ] Test invalid placement and tracking recovery.
-   [ ] Test missed shots, weak hits, strong hits, and repeated
    collisions.
-   [ ] Test win by elimination.
-   [ ] Test the shot-limit result and draw.
-   [ ] Test Play Again and Main Menu.
-   [ ] Test Android pause/resume and camera permission denial.
-   [ ] Fix blocking bugs and performance problems.
-   [ ] Add basic sound and simple effects only if the game loop is
    reliable.
-   [ ] Record known device and surface limitations.
-   [ ] Create a final debug APK for demonstration.

### Acceptance criteria

-   [ ] A full match can be completed and restarted reliably.
-   [ ] The app does not get stuck in setup or shot resolution during
    normal play.
-   [ ] No known blocker prevents the documented acceptance criteria
    from passing.
-   [ ] The APK can be installed and demonstrated on the target phone.

### Deliverable

A first playable Android AR prototype.

------------------------------------------------------------------------

## Progress Tracker

Update the status as work proceeds.

  Milestone                           Status        Notes
  ----------------------------------- ------------- -------
  0\. Environment setup               Not Started   
  1\. First scene and Android build   Not Started   
  2\. Ball throwing                   Not Started   
  3\. Sticky figures                  Not Started   
  4\. Match rules                     Not Started   
  5\. Basic UI                        Not Started   
  6\. AR Foundation                   Not Started   
  7\. Figure placement in AR          Not Started   
  8\. Ball physics in AR              Not Started   
  9\. End-to-end testing              Not Started   

------------------------------------------------------------------------

## AI Coding Assistant Workflow

For each milestone:

1.  Ask the assistant to read the three project documents and this
    roadmap.
2.  Give it only the current milestone's tasks.
3.  Ask it to identify required Unity Editor setup before writing code.
4.  Implement the smallest working slice.
5.  Compile and run the project.
6.  Test the acceptance criteria.
7.  Fix errors before continuing.
8.  Update the milestone status and notes.
9.  Commit the working state to Git.

Suggested prompt:

> Read `Docs/GAME_DESIGN_DOCUMENT.md`, `Docs/GAME_FLOW.md`,
> `Docs/TECHNICAL_PLAN.md`, and `Docs/DEVELOPMENT_ROADMAP.md`. Work only
> on the current milestone. First explain the steps and files required.
> Make small, focused changes, do not add out-of-scope features, and
> tell me exactly how to test the result in Unity and on Android. Do not
> mark the milestone complete until its acceptance criteria pass.

## Scope Protection

Do not add online multiplayer, multiple ball types, special character
abilities, leaderboards, monetization, or extra game modes during this
roadmap. Keep them as future ideas until a complete single-device AR
match is stable.

**Definition of success:** Two people can share one Android phone, place
their sticky figures on supported real-world surfaces, take turns
throwing balls, knock figures off, finish a match, and restart without
the game getting stuck.
