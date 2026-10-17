# Sticky Battle AR --- Game Flow

**Document status:** Draft v0.1\
**Related document:** `GAME_DESIGN_DOCUMENT.md`\
**Purpose:** Define the screens, phases, player actions, and transitions
for the first playable version.

------------------------------------------------------------------------

## 1. High-Level Flow

``` text
Main Menu
   |
   v
Start Game
   |
   v
AR Scan / Find Surface
   |
   v
Figure Setup
   |  Players alternate until each has placed 3 figures
   v
Attack Phase
   |  Players alternate throwing up to 5 balls each
   |  Resolve each shot before switching turns
   |
   +---- If one player loses all figures ----> Results
   |
   +---- If both players use all 5 shots ----> Compare remaining figures
                                                   |
                                                   v
                                                Results
                                                   |
                             +---------------------+------------------+
                             |                                        |
                             v                                        v
                         Play Again                               Main Menu
```

## 2. Screen and State Definitions

The first version should keep the number of screens small. A screen may
be implemented as a UI panel within a Unity scene; it does not need to
be a separate Unity scene.

### 2.1 Main Menu

**Purpose:** Let the user start a match.

**Display:** - Game title: Sticky Battle AR. - `Play` button. - Optional
`How to Play` button with short instructions.

**Actions:** - `Play` begins a new match and opens AR scanning. -
`How to Play` opens a simple instruction panel. - Returning to the menu
must not leave old figures, balls, or match state behind.

**Next state:** `Scanning`.

### 2.2 Scanning / Find Surface

**Purpose:** Establish a usable AR environment before figure placement.

**Display:** - Live camera background. - Instruction: "Move your phone
slowly to find a surface." - Surface indicator when a suitable plane is
detected. - `Continue` or `Start Setup` control once a valid surface is
available. - A clear tracking-lost message when tracking is unavailable.

**Actions:** - The player moves the phone to scan. - The app detects a
supported surface using AR Foundation/ARCore. - Once a valid surface is
available, the player starts setup.

**Rules:** - Do not allow figure placement before a valid placement pose
is available. - If tracking is lost, temporarily disable placement and
throwing. - Keep the instructions simple; do not require users to
configure an AR session manually.

**Next state:** `FigureSetup`.

### 2.3 Figure Setup

**Purpose:** Each player places three sticky figures into the real
environment.

**Display:** - Camera background and detected surface. - Current player:
Player 1 or Player 2. - Figures placed by each player,
e.g. `Player 1: 1/3`. - A preview/aim indicator for the figure being
placed. - A short instruction explaining drag-and-release placement.

**Turn order:** 1. Player 1 places one figure. 2. Player 2 places one
figure. 3. Continue alternating until both players have placed three
figures.

**Placement interaction:** 1. The current player touches the screen and
drags backward to aim. 2. Releasing launches the virtual figure. 3. The
figure travels using the configured prototype physics. 4. If it reaches
a valid surface and settles, it attaches to that surface. 5. The game
confirms successful placement and advances to the other player. 6. If it
does not land on a valid surface, the placement does not count and the
current player can retry.

**Rules:** - A successfully attached figure remains at its landing
position for the rest of the match unless knocked off. - Figures cannot
be recalled or repositioned in version 0.1. - Setup continues until each
player has three attached figures. - A failed placement does not consume
one of the three successful placements.

**Next state:** `Attack` after all six figures have been placed.

### 2.4 Attack

**Purpose:** Players take turns throwing balls to knock off the opposing
figures.

**Display:** - Camera background and anchored figures. - Current player
label. - Active figures remaining for each player. - Shots
used/remaining for each player. - A simple drag-and-release aiming
interaction. - A brief instruction such as "Drag back, then release to
throw."

**Attack interaction:** 1. The current player aims by dragging backward
from the launch point. 2. Releasing launches one virtual ball. 3. The
game consumes one shot for that player. 4. The game enters
`ResolvingShot` and disables further throws. 5. The ball moves and
collides with surfaces and figures. 6. The shot resolves when the ball
stops, leaves the playable area, or reaches a timeout. 7. The game
checks whether a figure was eliminated and whether the match has ended.
8. If the match is not over, the turn changes to the other player.

**Rules:** - Only the current player may throw. - A shot is consumed on
a valid release, even if it misses. - Only active opposing figures count
as valid targets for elimination. - A qualifying impact detaches the
target figure and marks it eliminated once. - The ball cannot trigger
additional shots or be reused after its shot resolves. - The next turn
cannot begin while the previous shot is still resolving.

**Next state:** `ResolvingShot`, then either `Results` or `Attack` for
the next player.

### 2.5 Resolving Shot

**Purpose:** Finish projectile movement and apply collision outcomes
safely before the next turn.

**Display:** - Continue showing the AR environment and the shot's
animation. - Optionally show a short message such as "Figure knocked
off!" - Do not show active aiming controls.

**Completion conditions:** - The ball has stopped moving. - The ball has
left the playable area. - Or a maximum shot-resolution time has elapsed.

**Actions:** 1. Finalize any hit result. 2. Mark any detached figure as
eliminated. 3. Remove or disable the ball. 4. Check the win condition.
5. If the match is over, enter `Results`; otherwise, switch the current
player and return to `Attack`.

A figure must not be eliminated twice if multiple collision callbacks
occur during the same shot.

### 2.6 Results

**Purpose:** Clearly announce the outcome.

**Display:** - Winner or "Draw". - Remaining figures for Player 1 and
Player 2. - Shots used by each player. - `Play Again` button. -
`Main Menu` button.

**Outcome rules:** - If all of one player's figures are eliminated, the
other player wins immediately. - If both players use all five shots
without either player losing all figures, the player with more remaining
figures wins. - If remaining figure counts are equal at the shot limit,
the result is a draw.

**Actions:** - `Play Again` clears the previous match and starts a fresh
scan/setup sequence. - `Main Menu` clears match state and returns to the
menu.

## 3. Turn Order

### Setup phase

``` text
Player 1 places a figure
        |
        v
Player 2 places a figure
        |
        v
Are both players at 3 placed figures?
        |
     No | Yes
        |   \
        v    v
Next player  Start Attack Phase
```

A failed placement keeps the turn with the same player until a valid
figure is placed.

### Attack phase

``` text
Current player aims and releases
        |
        v
Consume one shot
        |
        v
Resolve ball and collisions
        |
        v
Update eliminated figures
        |
        v
Is either player's figure count zero?
        |                 |
       Yes                No
        |                 |
        v                 v
      Results       Are both shot limits reached?
                           |             |
                          Yes            No
                           |             |
                           v             v
                        Results     Switch player
                                         |
                                         v
                                  Next attack turn
```

## 4. Input Rules

-   Drag backward and release to launch a figure or ball.
-   Clamp drag distance to a maximum value.
-   Ignore extremely short or invalid drags rather than launching a
    zero-strength projectile.
-   Only accept setup input during `FigureSetup`.
-   Only accept attack input during `Attack`.
-   Ignore input while `ResolvingShot`, `Results`, or when AR tracking
    is not usable.
-   Provide visible feedback for invalid actions when practical.

## 5. AR Tracking and Recovery

-   If AR tracking becomes temporarily limited or unavailable, disable
    placement and attack input.
-   Display a short instruction such as "Move your phone slowly to
    restore tracking."
-   Resume the current phase when tracking becomes usable again.
-   Do not change a figure's attachment state just because tracking is
    temporarily lost.
-   If the AR session cannot recover, provide an option to restart the
    match.

## 6. State Management Guidance

Use one explicit game-state controller as the source of truth for the
current phase, current player, figure counts, and shot counts.

Suggested states: - `MainMenu` - `Scanning` - `FigureSetup` - `Attack` -
`ResolvingShot` - `Results`

Suggested player data: - Player ID. - Number of figures successfully
placed. - Number of active figures. - Number of shots used.

Suggested figure data: - Owner player ID. - Attached/active or
eliminated state. - Surface-relative pose or stable anchor reference.

Suggested shot data: - Throwing player ID. - Whether the shot has
resolved. - Whether a qualifying hit occurred.

Avoid letting UI buttons, physics callbacks, and AR scripts
independently change turns. They should notify the game-state
controller, which validates the current state and performs transitions.

## 7. Flow Acceptance Checklist

-   [ ] Play opens the scanning state.
-   [ ] Placement is unavailable until a valid surface is detected.
-   [ ] Players alternate figure placement.
-   [ ] Failed placements allow retry without counting as a successful
    placement.
-   [ ] Attack starts only after all six figures are placed.
-   [ ] Only the current player can throw.
-   [ ] One valid throw consumes exactly one shot.
-   [ ] No new throw is accepted while a shot is resolving.
-   [ ] The game changes turns after the shot has resolved.
-   [ ] A qualifying hit eliminates the correct figure exactly once.
-   [ ] Eliminating all opposing figures ends the match immediately.
-   [ ] Reaching both shot limits produces a winner or draw based on
    remaining figures.
-   [ ] Play Again resets all match objects and counters.
-   [ ] Main Menu clears match state.
-   [ ] Temporary tracking loss disables gameplay input and provides
    recovery instructions.

------------------------------------------------------------------------

**Guiding principle:** Keep the flow predictable and the number of
states small. The player should always know whose turn it is, what
action is allowed, and what happens next.
