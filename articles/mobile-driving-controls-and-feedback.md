# Designing Mobile Driving Controls as a Feedback System

A driving HUD is not just a layout of buttons. Its controls, vehicle response, camera, and feedback form one interaction loop.

## The design problem

A visually polished accelerator can still feel unresponsive if acceleration takes too long to become noticeable. Likewise, a camera that amplifies speed can mask poor input precision. Separating acceleration, braking, and reversing clarifies intent, but the input combinations must still be tested.

## A practical approach

Start by defining the actions the player must perform simultaneously: steer while accelerating, brake to stop, reverse from rest, and activate a skill while driving. Keep these cases in the acceptance checklist rather than testing each button in isolation.

Treat speedometer labels, camera field of view, follow distance, and speed-line effects as feedback for the same underlying vehicle state. Compare displayed values with telemetry instead of assuming the visual effect proves the physics is correct.

Test in two stages. Editor or Play Mode checks can establish whether input routing and state transitions work. Device testing is still needed for touch reachability, latency perception, accidental presses, and screen aspect ratios.

## What this does not prove

A successful Play Mode input check does not establish that driving feels good on a phone. Neither a wider field of view nor faster-looking effects necessarily improve control. Those are hypotheses to compare under repeatable conditions.

## Takeaway

Evaluate mobile driving as an end-to-end feedback system. Preserve a clear distinction between functional checks, device usability, and user preference.
