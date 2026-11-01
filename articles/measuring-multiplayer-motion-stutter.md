# Measuring Multiplayer Motion Stutter Beyond FPS

[Article index](../README.md)

A stable render frame rate does not guarantee smooth movement in a networked game. Frame delivery, state update cadence, interpolation timestamps, and the position actually drawn on screen can disagree.

## Measure separate signals

Record render frame time, network state arrival intervals, interpolation target time, and frame-to-frame position displacement. Distinguish pauses, large corrections, and inconsistent movement from low FPS. Capture device, build identifier, network conditions, package version, and test duration with every trace.

## Compare changes without overstating evidence

A package update can correct an interpolation timing bug while leaving visible pauses. Compare like-for-like traces and retain the residual failure. One run per condition is a diagnostic observation, not a statistically reliable improvement estimate. If a capture lacks a reliable build identifier, label its attribution uncertain rather than presenting the numbers as definitive.

## Acceptance

Require both a measurable improvement in the chosen motion metric and an acceptable user-visible result on representative devices. Do not infer smoothness from package release notes or average FPS alone.

The reusable lesson is to measure **the motion the player sees**, not just the rate at which the renderer draws frames.
