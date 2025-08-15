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

## Next Steps
- Lives and level reset
- Brick durability, power-ups
- Sound effects and UI polish
