# Will You Find Your Way Home?
**Project:** Icetask3_ST10436103 (Unreal Engine 5.8, C++)

## What I made
I made a procedurally generated world using Perlin noise. An enemy spawns on the edge of the map, travels through the terrain, and finds its way back to where it started. Everything is handled by one C++ actor called `APerlinPathGenerator`.

## How it works

### 1. Generating the terrain with Perlin noise
I wrote my own seeded Perlin noise function. The seed shuffles the noise's permutation table and also shifts where the noise is sampled. This means every seed gives a different map, but the same seed always gives the same map.

To make the terrain look more natural, I layered a few octaves of noise together (fractal noise). The first layer makes the big hills and the extra layers add smaller details.

### 2. Turning noise into terrain heights
The noise values are normalised and split into 6 height levels. Each level is drawn as a coloured column of blocks:

| Level | Terrain | Walkable? |
|---|---|---|
| Lowest | Water (blue) | No |
| | Sand (yellow) | Yes |
| | Grass (green) | Yes |
| | Rock (grey) | Yes |
| Highest | Peak (white) | No |

I used stepped levels instead of a smooth landscape because it suits a tower-defence game better. The tiles are clear, and towers can be placed on them.

### 3. Choosing a starting point
The game finds every walkable tile on the edge of the map and picks one at random, using the seed. This becomes the enemy's spawn point and home.

### 4. Creating a path that loops back home
The path goes from the start to 3 waypoints placed around the centre of the map, then back to the start. Each part of the route is found using the **A\*** pathfinding algorithm. While pathfinding, the enemy:
- can't walk on water or peaks
- can't climb more than 1 height level between tiles, so it avoids steep areas
- prefers flat ground, because climbing costs more
- avoids tiles it has already used, so the path makes a proper loop instead of just going back the same way

If a starting tile can't make a full loop, the game tries another edge tile.

### 5. Marking the start/end point
A yellow pillar with a **HOME** label marks the start/end tile. The path is shown as orange tiles. If no loop could be found, the label turns red and says **LOST**.

### 6. The enemy
When the game runs, a red sphere (the enemy) leaves its spawn point, walks the whole path, and returns home. Each time it gets back, the message *"The enemy found its way home!"* appears on screen, and then it does another lap.

## Running it
1. Open the `Icetask3_ST10436103.uproject` project.
2. Press **Play**.
3. Click inside the game view, then press **R** to run all 3 attempts.

The overhead camera shows the whole map, and the scores appear in the top-left corner.

| Key | What it does |
|---|---|
| `N` | Runs the next attempt (next seed) and scores it |
| `R` | Runs all 3 attempts and shows the total score out of 15 |
| `G` | Generates a world with a random seed (not scored) |

The same options are also buttons in the Details panel, so the terrain can be generated in the editor without pressing Play. The scores are shown on screen, in the Output Log, and under **Home Run → Results** in the Details panel.

## Scoring ("Will You Find Your Way Home?")
I made the game score each attempt automatically, out of 5:

| Point | How it is checked |
|---|---|
| +1 Terrain changes with the seed | At least 25% of the tiles are a different height from the previous attempt |
| +1 Terrain looks naturally varied | At least 4 height levels are used, no single level covers more than 55% of the map, and neighbouring tiles change smoothly instead of looking random |
| +1 Path loops back home | The path starts and ends on the same edge tile |
| +1 Path avoids impossible terrain | Every step is to a walkable tile next to it, with a climb of no more than 1 level |
| +1 Usable in a tower-defence game | The path is long enough, doesn't keep repeating tiles, and has at least 30 free tiles next to it for placing towers |

## My 3 attempts

| Attempt | Seed | Score | Tiles changed | Path length | Notes |
|---|---|---|---|---|---|
| 1 | 1337 | 5/5 | 75% | 116 tiles | Started on the left edge (tile 0,12). 214 build slots for towers |
| 2 | 4242 | 5/5 | 78% | 116 tiles | Started on the right edge (tile 39,11). 207 build slots |
| 3 | 98765 | 5/5 | 74% | 110 tiles | Started on the left edge (tile 0,35). 183 build slots |
| **Total** | | **15/15** | | | |

All 3 attempts got full marks. Each new seed changed about three quarters of the map (74–78% of the tiles), which shows the seed really does create a new world. All 6 height levels were used every time, and the path always looped back to the starting tile without crossing water, peaks or steep climbs. The enemy also completed its lap and the game showed *"The enemy found its way home!"*.

## What changed and what stayed the same

**What changed each time I changed the seed:**
- where the hills, water and peaks were
- which edge tile the enemy spawned on
- the shape and length of the path
- how many tiles were free for towers

**What stayed the same:**
- the size of the map and the tiles
- the height levels and colours
- the rules for where the enemy can walk (no water, no peaks, no steep climbs)
- the enemy always spawned on the edge and always ended up back home

I also noticed that using the same seed again always gave exactly the same world. This shows the generation is random-looking but still controlled.

## Takeaway
Even though the world is generated differently every time, the rules I set make sure the enemy can always find its way home. This is what makes procedural generation useful for a tower-defence game: every map feels new, but it still works and is fair to play.
