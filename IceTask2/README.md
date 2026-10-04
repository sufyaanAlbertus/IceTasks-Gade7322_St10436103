# The Hollow Return – Procedural Generation Prototype

**Student:** Sufyaan Albertus (ST10436103)
**Module:** GADE7322 – Game Development (ICE Task 2)
**Engine:** Unreal Engine 5.8 (C++)
**Theme:** "End Where You Started"

## What this is

This is a playable prototype of my Part 1 game concept, *The Hollow Return*. Mira returns to her family house the night before it gets sold. Each loop, a spirit (Grandmother, Mother, then her Younger Self) asks her to find keepsakes hidden around the house. Once she finds them all, the front door unlocks. Walking through it takes her back to where she started, and the house rebuilds itself into a new layout for the next spirit.

Nothing in the house is placed by hand. The walls, furniture, lamps and keepsakes are all generated at runtime when the game starts, and again on every loop.

## What I built

| Brief requirement | What I did |
| --- | --- |
| 1. Procedural environment | The floor, outer walls and random inner partition walls of the house are spawned at runtime, so the rooms change shape every loop. Warm ceiling lamps are also placed randomly, so the lighting changes too |
| 2. Random object selection | Furniture meshes are stored in an array and one is picked at random for every spawn (Starter Content furniture if it is in the project, otherwise the engine's basic shapes) |
| 3. Random placement | The house is split into a 10 × 10 grid of 4 m cells. Each object goes into a random free cell with a random offset inside it |
| 4. Random rotation | Every prop gets a random 0–360° turn plus a slight lean, so the house feels a bit "off" |
| 5. Random scale | Every prop is scaled randomly between 0.8 and 1.3 |
| 6. Collision prevention | One object per grid cell, a distance check based on each mesh's size, and a sphere overlap test against anything already in the level |
| 7. Procedural gameplay elements | 3–5 glowing keepsakes spawn in random places at least 10 m from the start, so the player has to explore. The front door locks and unlocks based on them |
| Loops and arrays | `for` loops spawn every prop, keepsake and lamp. Arrays store the meshes, colours, free cells, occupied spots and every spawned actor |
| Seeds | Every layout comes from a random seed shown on the HUD, so a layout can be repeated for testing |

## How it runs

Open `IceTask2-HollowReturn_St10436103.uproject` with Unreal Engine 5.8 and click **Yes** when it asks to build the modules. Then press **Play**.

1. **The house builds itself.** The game mode spawns Mira, the HUD, the night lighting, a light fog (a nod to the Hush) and the generator. The generator builds the house around the starting point.
2. **Explore.** The HUD shows the loop number, which spirit's room it is, the keepsakes found and the seed. The spirit's message appears at the bottom of the screen.
3. **Collect keepsakes.** The blue glowing keepsakes bob and spin. Walking into one collects it and updates the count.
4. **The door unlocks.** When the last keepsake is found, the Hush lifts and the front door behind the start point changes from a red light to a warm gold light.
5. **Return to where you started.** Walking into the door fades to black, sends Mira back to the start and generates a brand new house for the next spirit. The house gets a bit more cluttered each loop.
6. **Ending.** After the third spirit, Mira walks out of the door and the story starts again from the first loop.

### Controls

| Key | Action |
| --- | --- |
| WASD / Arrow keys | Move |
| Space | Jump |
| Q / E | Turn the camera |
| Tab | Zoomed-out overview of the whole house |
| R | Regenerate the house instantly |
| G | Show/hide the debug grid and collision circles |
| H | Hide the HUD (for clean screenshots) |

### Code

| File | Purpose |
| --- | --- |
| `HollowGenerator` | The procedural generator: house shell, partitions, props, lamps, keepsakes, collision checks and the loop |
| `MemoryFragment` | A keepsake: bobs, spins and glows, and tells the generator when it is collected |
| `FrontDoor` | The front door: locked/unlocked light and the trigger that starts the next loop |
| `MiraCharacter` | The player, built from simple shapes, with an angled top-down camera like the Part 1 concept |
| `HollowHUD` | Draws the loop, spirit, keepsake count, seed, story messages and controls |
| `HollowGameMode` | Sets everything up at runtime, so the prototype runs without any hand-built level |

Each time `Generate()` runs it:

1. Clears the last layout and starts a random stream from a new seed
2. Builds the floor and outer walls
3. Fills an array with every free grid cell (skipping the area around the start)
4. Adds random partition walls, never on the edge or next to each other so no area gets sealed off
5. Spawns the keepsakes far from the start
6. Loops through and spawns the furniture with a random mesh, rotation, scale and colour, checking each spot is free first
7. Places the lamps

## Connection to the Game Concept

In Part 1, The Hollow Return was about Mira going back to her family house the night before it gets sold. Every time she listens to a spirit and does what they ask, she ends up back at the same front door. For this prototype I moved the idea into Unreal Engine 5 as a 3D version, but the core loop is the same, and procedural generation is what makes that loop work.

The biggest problem with a game built around returning to the same place is that it can get boring fast. If the house looked exactly the same every loop, the player would just be walking the same path three times. With procedural generation, the house rebuilds itself every time Mira walks back through the door. The partition walls move, so the rooms change shape, the furniture is swapped around and rotated, the lamps light different corners and the keepsakes the spirit asks for are hidden in new places. Mira ends where she started, but the house around her is never quite the same, which is exactly the feeling I wanted from the theme.

It also fits the story. In my concept, the house feels "slightly wrong", like it is holding on to people who should have left. Furniture that leans a little, rooms that rearrange themselves and a house that gets more cluttered with each loop all make it feel like the house is reacting to Mira, not just sitting there as a background.

For replayability, each run uses a different random seed, so no two playthroughs have the same layout. The player cannot just memorise where the keepsakes are, they have to explore again, which keeps the focus on looking around the house like the original concept wanted. Because the system uses seeds, I can also repeat a layout when testing.

Procedural generation is a good choice for a small student project like this as well. Instead of building three separate rooms by hand, one generator can produce many layouts from a few meshes. That keeps the scope realistic, which was one of the main risks I identified in Part 1.

## Testing

I ran the prototype multiple times to check that the house, furniture and keepsakes change between playthroughs. The screenshots of each run can be found in the `Screenshots` folder.

**What I checked while testing:**

- No furniture spawned inside walls or other furniture
- At least 3 different meshes appeared in every run
- Props had different rotations and sizes every time
- Keepsakes were always reachable and not right next to the start
- Mira never got stuck after being sent back to the start
- The game still worked after more than 5 loops

## Reflection

Building the procedural generation system for The Hollow Return was the first time I made a level that builds itself instead of placing everything by hand, and it changed how I think about level design.

**What worked well.** The grid-based placement worked better than I expected. Splitting the house into 4 m cells and only allowing one object per cell made the layout feel organised but still random, and it made collision prevention a lot easier. Using a random seed also worked well, because I could show it on the HUD and use it to repeat a layout when something looked wrong.

**Challenges.** The biggest problem was objects overlapping. At first I only used random X and Y positions, and furniture kept spawning inside walls and inside each other. Another problem was that different meshes have different sizes and pivot points, so some props floated or sank into the floor. I also had partition walls that sometimes blocked off parts of the house completely.

**How I solved them.** For overlapping, I added a distance check that uses each mesh's bounds, so bigger objects get more space than smaller ones, plus a sphere overlap test against the level. For the floating props, I worked out the bottom of each mesh from its bounds and moved it down so it always sits on the floor. For the blocked areas, I stopped partition walls from spawning on the edges or next to each other.

**Useful features.** Arrays were the most useful part, for storing the meshes, the free cells and every object that was spawned so they could be cleared each loop. `FRandomStream` made the randomness repeatable with a seed. Exposing variables to the editor with `UPROPERTY` meant values like the number of props, the scale range and the grid size could be tweaked without changing code, and the Blueprint events (`On Generated`, `On Door Unlocked`) mean sounds or effects can be added in Blueprints later. The debug drawing helped a lot to actually see the grid and collision circles.

**Future improvements.** I would generate proper rooms with doorways instead of single partition walls, turn the Hush into a procedural fog that blocks random doorways, add rules so furniture is placed against walls, and spawn the spirits themselves in random rooms with visual-novel style dialogue.

**What I learned.** Procedural generation is not just "make it random". Pure randomness looks messy, so you need rules like spacing, grids and limits to make it look designed. I also learned that it can support a story, because the changing house is what makes returning to the same door feel meaningful instead of repetitive.

## Video Demonstration

The video demonstration can be found in the `VideoDemo` folder.

## References

Anthropic (2026) *Claude (Opus 5.5)* [Large language model]. Available at: https://claude.ai (Accessed: 5 October 2026).

Epic Games (2026) *Unreal Engine 5 documentation*. Available at: https://dev.epicgames.com/documentation/en-us/unreal-engine (Accessed: 5 October 2026).
