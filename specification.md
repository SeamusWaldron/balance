Balance — Full Spec (Web Version, From Scratch)
A minimalist equilibrium game inspired by Mondrian where the player keeps a composition in balance by moving lines, splitting, and merging rectangles. No scores on screen; balance is felt through visuals.

Core Loop

Board drifts slowly out of balance on its own (line creep, slight growth/shrink, colour fatigue).
Player makes small corrections: move a line, split a rectangle, merge rectangles.
Balance returns briefly, then drifts again; drift rate increases over time.
Lose when imbalance persists past a threshold; restart.
Data Model

Canvas: fixed square (e.g., 600×600), scales responsively with CSS.
Rectangle: { x, y, w, h, color } where color ∈ {RED, BLUE, YELLOW, WHITE}.
Lines are implicit from rectangles (derived at render): vertical at rectangle x-edges, horizontal at rectangle y-edges. If useful, store as {pos, type}.
Colour weights (for balance math): RED 1.3, BLUE 1.1, YELLOW 0.9, WHITE 0.6.
Actions

Move Line
Drag near a line (vertical/horizontal).
Clamp to keep all resulting rectangles ≥ MIN_SIZE (e.g., 20 px).
Movement applies proportional to cursor delta; optionally reduced when highly imbalanced to feel “heavy.”
Split Rectangle
Trigger via key (Space) or UI button.
Allowed if rectangle area ≥ 12% of canvas and resulting children ≥ 6% each.
Orientation: along long axis (vertical if w>h else horizontal).
Children inherit colour; optionally set one child to WHITE for variety.
Adds temporary local stress (decays over ~2s).
Merge Rectangles
Trigger via double-click on a rectangle.
Valid if: same colour; share an edge within tolerance; sufficient overlap along shared edge; combined area ≤ cap.
After merge: remove internal line; new rectangle spans both; reduce stress.
Tolerances scale with imbalance (see “Merge Rules”).
Balance System (Invisible Score)
Compute balance_score each frame; lower is better. Components (weights tunable):

Colour area imbalance (0.35): for each colour, deviation from equal share weighted by colour weight, normalized by total area.
Centre of mass tension (0.25): weighted COM distance from canvas centre; slightly off-centre is good, dead-centre is bad.
Edge pressure (0.20): larger/heavier rectangles near edges increase penalty (area * colour weight / edge distance).
Complexity (0.20): penalty for being away from ideal rectangle count ideal = 9 + floor(time / T).
State bands:

STABLE: 0.35–0.55
TENSION: 0.55–0.65
WARNING: 0.65–0.75
COLLAPSE: >0.75 for >X seconds (e.g., 5s) triggers loss.
Imbalance mapping:  (0.75 - 0.55), 0..1) for effects.

Destabilisation (Passive Drift)

Every N seconds, apply tiny nudges: move one line by a few px, adjust one rectangle’s size by <5%, or slightly shift saturation.
Drift strength scales with elapsed time and imbalance.
Merge Rules (Overlap-First, Human-Readable)

Stress bias: stress = balance_score,  0.6, 0..1).
Edge tolerance: edgeTol = BASE_EDGE_TOL + stressBias * 24 (e.g., BASE_EDGE_TOL 18–22 px).
Minimum overlap along shared edge: overlapFrac = 0.45 - stressBias * 0.20 (floor at ~0.15). Require overlap ≥ overlapFrac * min(span).
Max merged area: maxArea = TOTAL_AREA * (BASE_MAX_MERGE_AREA + stressBias * 0.30) (BASE_MAX_MERGE_AREA ≈ 0.32–0.36; rises under stress).
Same colour required. If conditions met, merge is allowed; internal line must be removed.
Rendering (Canvas 2D + Optional WebGL Post)
Geometry:

Draw rectangles as fills; draw lines as filled rects (not strokes) for consistent thickness.
Line thickness: base 6–10 px * (1 + localStress * k); hover lines tinted.
Lines derive from rectangle edges each frame (dedupe to unique x/y).
Visual feedback (by imbalance band):

Line weight: thicken with stress; optional 0.5–2 px bow in WARNING.
Saturation drift: overrepresented colours oversaturate, underrepresented desaturate; WHITE warms slightly in tension.
Micro-motion: sub-pixel offset of the whole scene or regions with slow sin waves; freeze in collapse.
Texture/vignette/grain: subtle canvas grain; vignette and stress shading increase with imbalance; flatten on collapse.
Highlight mergeable rects: stroke inset rect when a candidate exists.
Optional WebGL post (single quad sampling canvas texture): saturation, vignette, grain, micro-motion offset, stress shading map (low-res).

Input & Interaction

Mouse/touch drag for lines.
Space to split selected/largest eligible rect.
Double-click/tap to merge highlighted pair.
Selection: track hovered rect/line; show merge highlight only when a valid partner exists.
Prevent actions when game over.
Loss & Restart

If balance_score > 0.75 continuously for MAX_IMBALANCE_TIME (e.g., 5s): play collapse sequence (lines thicken, colours dull, motion stops), fade, show restart button.
Restart resets rectangles to starting layout, clears stress, hides failure screen.
Starting Layout (Merge-Friendly)

Provide at least one obvious pair per colour to teach merging: two adjacent reds on top, two adjacent whites on top, two yellows on bottom, one blue on bottom right, one white on bottom left. Example (600×600):
Top h=280: red 230w, red 130w, white 120w, white 120w.
Bottom h=320: white 200w, yellow 160w, yellow 120w, blue 120w.
Onboarding (First 30s, No Menus)

0–5s: calm board, text: “This structure holds itself.”
5–10s: slight drift in one area, text near stress: “Something is getting heavy.”
10–15s: enable line move, text: “Try moving a line.” Immediate relief feedback.
15–20s: introduce a stubborn block, line move insufficient, text: “Some weight can’t be shifted.”
20–25s: enable split on that block, auto-oriented, text: “Divide space to make it flexible.”
25–30s: let player move a line to feel recovery; no explicit “tutorial complete.”
Audio (optional)

Drone pad when stable; introduce subtle dissonance as imbalance grows. Silence on collapse.
Performance & Technical Notes

Use requestAnimationFrame loop: update state, compute balance, draw scene, apply post.
For hit tests, use logical rects (x,y,w,h), not renderX/Y.
Rebuild line list after split/merge/move.
Keep MIN_RECT_DIM check on line moves and splits.
Prefer pointermove/pointerdown/pointerup for touch compatibility.
Balancing Knobs (to expose for tuning)

BASE_EDGE_TOL, BASE_MAX_MERGE_AREA, overlapFrac floor.
Split area thresholds and stress spike.
Balance component weights (w1..w4).
Drift start time, frequency, magnitude.
Collapse threshold duration.
Acceptance Criteria

At start, at least one same-colour pair highlights on hover and merges on double-click (internal line disappears).
Line drag is smooth and respects MIN_RECT_DIM.
Balance visuals react (line weight, subtle saturation, micro-motion) as score rises.
Passive drift nudges the board over time without abrupt jumps.
Loss triggers collapse sequence and restart works.