# Sticky Battle AR --- Testing Checklist

**Document status:** Draft v0.1\
**Related documents:** `GAME_DESIGN_DOCUMENT.md`, `GAME_FLOW.md`,
`TECHNICAL_PLAN.md`, `DEVELOPMENT_ROADMAP.md`\
**Purpose:** Track whether the game works as intended as features are
implemented.

------------------------------------------------------------------------

## How to Use This Checklist

-   Mark an item `[x]` only after testing it.
-   Use `[ ]` for not tested or not yet passing.
-   If a test fails, record the issue in the Notes section with steps to
    reproduce it.
-   Retest relevant items after making a fix.
-   Test the non-AR prototype in the Unity Editor and AR behavior on a
    real, supported Android device.
-   Do not mark a milestone complete merely because the code compiles.

**Test build/version:**\
**Unity version:**\
**AR Foundation version:**\
**ARCore XR Plug-in version:**\
**Android device:**\
**Android version:**\
**Tester:**\
**Date:**

------------------------------------------------------------------------

## 1. Project and Build Setup

-   [ ] Unity project opens without blocking errors.
-   [ ] Scripts compile successfully.
-   [ ] Main development scene opens and runs in the Editor.
-   [ ] Android is selected as the build platform.
-   [ ] Android build installs on the test phone.
-   [ ] App launches without crashing.
-   [ ] App name and package identifier are set appropriately for
    testing.
-   [ ] Git repository contains the project and documentation, without
    generated build folders or temporary files that should be excluded.

**Notes / issues:**

------------------------------------------------------------------------

## 2. Main Menu and Navigation

-   [ ] Main menu displays the game title.
-   [ ] Play starts a new match.
-   [ ] How to Play, if implemented, opens and closes correctly.
-   [ ] Returning to the main menu clears the previous match.
-   [ ] Play Again starts a clean match.
-   [ ] Repeated restarts do not duplicate figures, balls, UI panels, or
    event handlers.
-   [ ] Back navigation behaves sensibly and does not leave the game in
    a broken state.

**Notes / issues:**

------------------------------------------------------------------------

## 3. AR Camera and Surface Detection

*Run these tests on a real ARCore-supported Android device.*

-   [ ] Camera permission is requested when needed.
-   [ ] If camera permission is denied, the app shows a useful message
    instead of failing silently.
-   [ ] AR camera view starts successfully.
-   [ ] A suitable horizontal surface is detected in a well-lit room.
-   [ ] A suitable vertical surface is detected where supported by the
    device and configuration.
-   [ ] Placement indicator appears only when a valid surface pose is
    available.
-   [ ] Placement indicator follows the current valid raycast hit.
-   [ ] Moving the phone slowly improves or maintains tracking.
-   [ ] A simple test object remains aligned with the surface while
    tracking is valid.
-   [ ] Poor lighting or an undetectable surface does not allow invalid
    placement.
-   [ ] Temporary tracking loss disables gameplay input and displays a
    recovery instruction.
-   [ ] Gameplay can resume after tracking recovers.
-   [ ] If tracking cannot recover, the user can restart safely.

**Test environments:**\
- \[ \] Textured wall\
- \[ \] Table or desk\
- \[ \] Cupboard or similar solid surface\
- \[ \] Low-light environment (record result; not necessarily
supported)\
- \[ \] Glass or reflective monitor (record result; not necessarily
supported)

**Notes / issues:**

------------------------------------------------------------------------

## 4. Figure Placement and Attachment

-   [ ] Player 1 can launch a figure during setup.
-   [ ] Player 2 can launch a figure during setup.
-   [ ] A figure that reaches a valid surface and settles becomes
    attached.
-   [ ] Attached figures do not continue falling due to gravity.
-   [ ] Attached figures remain aligned as the phone moves while
    tracking is valid.
-   [ ] Figures remain in their landed positions for the rest of the
    match unless knocked off.
-   [ ] Figures cannot be recalled or repositioned during the match.
-   [ ] A failed placement does not count as a successful placement.
-   [ ] A failed placement lets the same player retry.
-   [ ] Each player can place exactly three successful figures.
-   [ ] Setup does not end before all six figures are placed.
-   [ ] Each figure is assigned to the correct player.
-   [ ] Player 1 and Player 2 figures are visually distinguishable.
-   [ ] Temporary tracking loss does not mark attached figures as
    eliminated.

**Notes / issues:**

------------------------------------------------------------------------

## 5. Ball Throwing and Input

-   [ ] Dragging backward and releasing launches a ball.
-   [ ] A valid drag produces a predictable launch direction.
-   [ ] Drag distance affects launch strength as configured.
-   [ ] Launch strength is clamped to the maximum.
-   [ ] Very short or invalid drags do not produce unintended throws.
-   [ ] Releasing the touch launches at most one projectile.
-   [ ] A ball follows a plausible trajectory.
-   [ ] The ball collides with arena surfaces and configured colliders.
-   [ ] The ball can miss all figures without breaking the game.
-   [ ] The ball is removed or disabled after the shot resolves.
-   [ ] The ball cannot score additional hits after its shot has
    resolved.
-   [ ] A ball that leaves the playable area resolves after the
    configured condition or timeout.
-   [ ] A ball that never settles cannot permanently block the next
    turn.

**Notes / issues:**

------------------------------------------------------------------------

## 6. Hit Detection and Figure Elimination

-   [ ] A ball can hit an active opposing figure.
-   [ ] A qualifying impact detaches the target figure.
-   [ ] A weak impact below the threshold does not detach the figure, if
    weak-hit filtering is enabled.
-   [ ] A figure hit by its owner's ball is handled according to the
    documented rule; in version 0.1, it must not eliminate an opponent's
    figure.
-   [ ] A missed shot does not eliminate any figure.
-   [ ] An eliminated figure falls away or otherwise clearly shows that
    it has been knocked off.
-   [ ] An eliminated figure is marked eliminated only once.
-   [ ] Repeated collision callbacks cannot decrement the figure count
    multiple times.
-   [ ] An eliminated figure cannot be counted as an active target.
-   [ ] Figure counts update correctly after a successful hit.
-   [ ] The next turn waits until the shot has resolved.

**Notes / issues:**

------------------------------------------------------------------------

## 7. Turn Management and Match Rules

-   [ ] Player 1 begins figure setup.
-   [ ] Players alternate figure placements.
-   [ ] Failed placement keeps the turn with the same player.
-   [ ] Attack phase begins after all six figures are successfully
    placed.
-   [ ] Player 1 begins the attack phase.
-   [ ] Only the current player can throw.
-   [ ] Each valid attack consumes exactly one shot.
-   [ ] A missed shot still consumes one shot.
-   [ ] Invalid input does not consume a shot.
-   [ ] No new shot is accepted while the previous shot is resolving.
-   [ ] The next player receives the turn after shot resolution.
-   [ ] Both players start with five attack shots.
-   [ ] Shot counts never become negative.
-   [ ] Active figure counts never become negative.
-   [ ] All three opposing figures being eliminated ends the match
    immediately.
-   [ ] At the shot limit, the player with more active figures wins.
-   [ ] Equal active figure counts at the shot limit produce a draw.
-   [ ] The results screen displays the correct outcome and counts.
-   [ ] Restart resets players, figures, shots, projectiles, and game
    state.

**Notes / issues:**

------------------------------------------------------------------------

## 8. User Interface and Feedback

-   [ ] Current game phase is understandable.
-   [ ] Current player is clearly indicated.
-   [ ] Figure counts are visible and accurate.
-   [ ] Shots remaining are visible and accurate.
-   [ ] Invalid placement gives clear feedback.
-   [ ] Missed shots and successful eliminations give understandable
    feedback.
-   [ ] Tracking loss gives a recovery instruction.
-   [ ] Results clearly show the winner or draw.
-   [ ] Play Again and Main Menu work correctly.
-   [ ] Text and controls remain readable over the camera background.
-   [ ] Touch targets are usable on the test phone.
-   [ ] UI controls are disabled when actions are not allowed.

**Notes / issues:**

------------------------------------------------------------------------

## 9. App Lifecycle and Robustness

-   [ ] The app handles Android pause/resume without leaving a shot
    permanently unresolved.
-   [ ] Returning from a brief interruption does not duplicate active
    objects.
-   [ ] Camera or AR session interruptions are handled with a useful
    message.
-   [ ] Restart after tracking failure clears or resets the match
    safely.
-   [ ] Repeated complete matches do not cause visible object
    accumulation.
-   [ ] No common gameplay path produces an unhandled exception.
-   [ ] No common gameplay path leaves the game stuck in setup, attack,
    or resolving state.

**Notes / issues:**

------------------------------------------------------------------------

## 10. Performance and Device Testing

-   [ ] App remains responsive during AR tracking.
-   [ ] Ball movement and collisions remain usable on the target phone.
-   [ ] No severe frame drops occur during ordinary gameplay.
-   [ ] Device does not overheat excessively during a short test
    session.
-   [ ] Battery use is acceptable for a short demonstration.
-   [ ] The app behaves correctly after changing orientation, if
    orientation changes are supported.
-   [ ] Test on at least one real device; use additional devices if the
    intended audience requires broader compatibility.

Record observed issues rather than assuming every Android device
supports the same AR features.

**Notes / issues:**

------------------------------------------------------------------------

## 11. Full Match Smoke Test

Run this sequence after each major integration milestone.

1.  [ ] Open the app.
2.  [ ] Start a new match.
3.  [ ] Scan and find a valid surface.
4.  [ ] Place three figures for Player 1 and three for Player 2,
    alternating turns.
5.  [ ] Confirm all figures remain attached while tracking is valid.
6.  [ ] Throw a ball that misses.
7.  [ ] Confirm the shot count decreases and the turn changes.
8.  [ ] Hit an opposing figure with a qualifying impact.
9.  [ ] Confirm exactly one figure is eliminated.
10. [ ] Continue until a player loses all figures, or both shot limits
    are reached.
11. [ ] Confirm the correct winner or draw.
12. [ ] Select Play Again.
13. [ ] Confirm the new match has no leftover figures, balls, or
    counters.
14. [ ] Repeat the test at least once.

**Smoke test result:** Pass / Fail\
**Notes:**

------------------------------------------------------------------------

## 12. Bug Report Template

For each failure, record:

-   **Issue ID:**
-   **Short title:**
-   **Build/version:**
-   **Device and Android version:**
-   **Unity/editor context:**
-   **Steps to reproduce:**
    1.  
    2.  
    3.  
-   **Expected result:**
-   **Actual result:**
-   **Frequency:** Always / Often / Sometimes / Once
-   **Severity:** Blocker / High / Medium / Low
-   **Screenshot, video, or log:**
-   **Status:** Open / In Progress / Fixed / Retest Needed / Closed
-   **Retest result:**

------------------------------------------------------------------------

## 13. Release Readiness

The first playable prototype is ready for demonstration when:

-   [ ] The documented core game loop works from start to results.
-   [ ] Figure placement, throwing, hit detection, and turn changes pass
    on the target phone.
-   [ ] Win, draw, and restart logic pass.
-   [ ] No known blocker prevents a complete match.
-   [ ] AR tracking limitations are documented honestly.
-   [ ] The debug APK installs and runs on the target phone.
-   [ ] The project documentation reflects any intentional rule or
    implementation changes.

**Final result:** Not Tested / Pass / Fail\
**Known limitations:**

------------------------------------------------------------------------

**Guiding principle:** Test observable behavior, not just code. A
feature is complete only when it works in the actual environment in
which players will use it.
