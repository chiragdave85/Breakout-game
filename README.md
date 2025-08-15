# Breakout (HTML5 Canvas)

A simple Breakout game built step-by-step in a single `index.html` using HTML5 Canvas and vanilla JavaScript.

## How to run
- Open `index.html` directly in a modern browser (Chrome, Firefox, Edge, Safari).
- The game uses `requestAnimationFrame` and scales for devicePixelRatio for crisp rendering.

## Controls
- Mouse: move paddle left/right
- Keyboard: Arrow Left/Right to move paddle
- Space: launch the ball
 - M: mute/unmute sounds
 - Shift+M: reset high score (with confirmation)

## Steps

### Step 1
- Create `index.html` with 800x600 canvas, centered
- Handle devicePixelRatio scaling
- Add RAF game loop that clears background each frame
- Display FPS in top-left

### Step 2
- Add paddle controlled with left/right keys and mouse movement
- Clamp paddle to canvas bounds

### Step 3
- Add ball (radius 8px)
- Ball rests on paddle center until Space is pressed to launch upward at 45°
- Bounces off top/left/right walls
- If it falls below bottom, resets to paddle
- Constant speed with `dt`

### Step 4
- Paddle–ball collision using AABB from above
- Reflect vertical, adjust horizontal based on hit position
- Keep speed constant and resolve overlap

### Step 5
- 10×6 grid of bricks
- Fixed brick size and padding, colored by row
- Track active/inactive bricks
- Ball–brick collision removes brick, flips vertical velocity
- Score +100 per brick, only 1 brick removed per frame

### Step 6
- Game states: START, PLAYING, LEVEL_CLEARED, GAME_OVER
- START: Press Space to Start; ball sits on paddle
- LEVEL_CLEARED: when all bricks gone; Space loads next level, slight speed increase
- GAME_OVER: when lives reach 0; Space resets to START
- HUD shows Score/Lives/Level

### Step 7
- HUD: Score (top-left), Lives (top-center), Level (top-right)
- FPS moved to bottom-left

### Step 8
- Difficulty progression with multiple level patterns/colors
- Ball speed increases slightly each level (capped)
- Score multiplier per level

### Step 9
- Audio feedback via Web Audio API
- Paddle hit: short beep, Brick hit: higher beep, Life lost: lower tone
- M key toggles mute

### Step 10
- Touch controls: drag to move paddle, tap to start/advance level
- Pause toggle: P key pauses/resumes during PLAYING (shows overlay)
- Audio priming: first interaction (click/tap) enables sounds per browser policy

### Step 11
- Visual polish: particle effects when bricks break
- Subtle screen shake plumbing (kept minimal; paddle-hit shake disabled)

### Step 12
- Responsive: canvas scales to fit window with letterboxing; logical size remains 800×600
- Input mapping respects scaling for mouse/touch
- Auto-pause when tab is hidden (document.visibilitychange); resume on return

### Step 13
- Refactor to modules (still single-file): Game, Paddle, Ball, BrickField, Input, AudioManager, Particles, Renderer
- Clear responsibilities and inline comments; behavior preserved

### Step 14: Manual QA Checklist

Use this checklist to validate features after changes. Record actual results as needed.

#### Controls
- [ ] Keyboard Left/Right moves paddle smoothly, clamped within bounds
	- Expected: Paddle moves; ball follows paddle when not launched
- [ ] Mouse move maps correctly across resized canvas
	- Expected: Paddle centers under cursor; ball tracks when resting
- [ ] Touch drag moves paddle; tap starts/advances level
	- Expected: No tap-to-launch during PLAYING

#### Ball and Collisions
- [ ] Walls reflect correctly (left/right/top)
- [ ] Paddle collision reflects upward; angle depends on hit position; speed constant
- [ ] Brick collision removes exactly one brick per frame; score increases with level multiplier; particles spawn

#### Game States
- [ ] START overlay appears on load; Space or tap starts and launches
- [ ] PLAYING updates run; P toggles pause overlay and freezes updates
- [ ] LEVEL_CLEARED shows overlay; Space or tap advances to next level and increases ball speed
- [ ] GAME_OVER after lives reach 0; Space resets score/lives/level

#### HUD and Scoring
- [ ] Score (left), Lives (center), Level (right) update correctly
- [ ] +100 per brick × max(1, level) multiplier applied

#### Lives and Game Over
- [ ] Ball falling below bottom decrements lives; on 0 lives, GAME_OVER
- [ ] Ball resets onto paddle after life loss

#### Progression and Speed
- [ ] Clearing level advances Level and increases ball speed ~10% (capped)

#### Effects and Feedback
- [ ] Brick destroys spawn particles near impact center
- [ ] No shake on paddle-hit; no unintended shakes elsewhere

#### Pause, Audio, and Mute
- [ ] P pauses/resumes only in PLAYING; overlay shows "Paused"
- [ ] M toggles mute; sounds suppressed when muted
- [ ] First click/tap enables audio (autoplay policy)

#### Responsiveness and Visibility
- [ ] Window resize preserves aspect ratio (letterboxed) and crispness on HiDPI
- [ ] Pointer/finger maps correctly at all sizes; paddle aligns with edges
- [ ] Switching tabs auto-pauses; returning resumes (unless manually paused)

#### Performance
- [ ] FPS readout near 60 on typical hardware; no obvious stutters

#### Optional Audio Checks
- [ ] Paddle hit plays short tone; brick hit higher tone; life lost lower tone

---

### Common Issues and Quick Fixes

- Ball tunneling through bricks/paddle (at high speeds)
	- Fix: Substep integration when `dt > 0.016` (e.g., split update into 2 substeps); continue using previous-position checks for paddle crossing
- Ball stuck near-horizontal
	- Fix: Enforce minimum vertical component after reflections (e.g., `|vy| >= speed * 0.2`), or slightly bias angle when `|t|` near edges
- Input scaling off after resize
	- Fix: Ensure `toGameX` uses `getBoundingClientRect()` and divides by `currentScale`; update transform with `dpr * currentScale` on resize
- Blurry canvas on HiDPI
	- Fix: Backing resolution = CSS size × devicePixelRatio; reset and scale context after resize
- Audio not playing
	- Fix: Prime/resume AudioContext on first pointer event; check mute state
- Performance dips during effects
	- Fix: Reduce `MAX_PARTICLES`, per-hit particle count, or shadowBlur values
- Lives or GAME_OVER logic incorrect
	- Fix: Confirm bottom check (`ball.y - radius > HEIGHT`) triggers life loss and transitions at 0 lives

## Next Steps
- Lives and level reset
- Brick durability, power-ups
- Sound effects and UI polish

### Step 15: Power-ups

- Bricks have a chance (~12%) to spawn a falling power-up at their center when destroyed.
- Power-ups fall at a steady speed and render as colored rounded squares with a letter icon.
- Paddle pickup uses AABB collision with the paddle rectangle.
	- On collision: activate the effect, remove the item, and play a short collect sound.

Effects implemented:
- Extra Life (+): Adds one life, capped at 3.
- Expand (E): Temporarily increases paddle width (10s). Center preserved and clamped in bounds.
- Shrink (S): Temporarily reduces paddle width (10s).
- Slow (C): Temporarily slows the ball to 70% speed (8s) while maintaining direction.
- Sticky (G): For 8s, the next paddle hit will stick the ball to the paddle. Press Space or Click/Touch to relaunch.

Notes:
- Multiple effects can overlap; timers refresh if you collect the same effect again.
- Low-graphics mode disables power-up shadows but keeps gameplay intact.
