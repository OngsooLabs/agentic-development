# Consistent AI-Generated 2D Characters Need an Art Contract

Generating a convincing character portrait is easier than producing a set of interchangeable game-ready poses. Consistency is a production constraint, not merely a prompt adjective.

## Define the invariant parts

A reusable art specification should identify proportions, camera angle, outline hierarchy, shading complexity, light direction, color roles, and silhouette readability. Across idle, attack, hit, and defeat poses, preserve character identity, approximate scale, and a shared ground reference.

Keep effects separate from character frames when the animation system needs to control them independently. A visually impressive glow baked into one pose may make it harder to reuse the same art in gameplay.

## Test the asset at game scale

Compare all poses side by side and at the intended display size. Check feet alignment, weapon direction, hand readability, and whether major color blocks remain distinguishable against the actual battlefield background.

A background should support character readability rather than compete for attention. Detail, contrast, and lighting should be assessed in a composite gameplay scene, not only as standalone illustrations.

## Connect visuals to motion and audio

A shared character style does not automatically create convincing combat. Timing, anticipation, impact, recovery, camera movement, and sound still need their own integration checks. Separate approval of the concept sheet, pose consistency, in-engine animation, and final gameplay presentation.

## Limits

Generated pose sheets can still require manual cleanup, rigging, or redraws. This workflow does not establish that a particular image model can guarantee pixel-perfect pose alignment.

## Takeaway

Treat character generation as asset production with explicit invariants and acceptance tests, not as a sequence of unrelated illustrations.
