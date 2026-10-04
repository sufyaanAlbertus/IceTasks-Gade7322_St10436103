# ICE Task 4 – Boid Flock: "Don't Lose the Flock!"

**Sufyaan Albertus – ST10436103**
Unreal Engine 5.8 (C++)

## The challenge

> *Can your group explore the world and still find its way back to where it started?*

For this task I made a small flock of creatures in Unreal Engine using C++. The creatures fly together as a group, travel to a target area, explore it, and then find their way back to where they started. None of them follow a fixed path. Each creature only reacts to the creatures around it and to a pull toward where the flock needs to go.

---

## What I did

### 1. Created the agents
I made a `BoidAgent` class for one creature. Each creature is a simple cone that points in the direction it is flying, so you can see where it is heading. The flock has 8 creatures by default, and this can be set anywhere from 5 to 10.

### 2. Gave each agent a starting position
When the game starts, the `BoidFlockManager` places the creatures in a ring around itself with a bit of randomness, so they don't all start in a perfect circle. Each creature saves its own `StartLocation` so it knows exactly where to go back to at the end.

### 3. Implemented the three Boid behaviours
Every frame, each creature looks at the other creatures near it and works out three forces:

- **Separation:** if another creature is too close, it pushes away. The closer they are, the harder it pushes.
- **Alignment:** it turns to match the average direction of its neighbours.
- **Cohesion:** it steers toward the average position of its neighbours, so it stays with the group.

Each force has its own weight, and they all get added together with a fourth force that pulls the creature toward the current goal:

```
acceleration = Separation × SeparationWeight
             + Alignment  × AlignmentWeight
             + Cohesion   × CohesionWeight
             + Goal       × GoalWeight
```

I used Reynolds steering for each force, which is *desired velocity minus current velocity*, capped at a maximum force. I also copy all the positions and velocities at the start of every frame. That way every creature reacts to the same moment, and creatures updated later in the loop don't react to ones that have already moved.

### 4. Gave the flock a target area to explore
The flock first flies toward the target area (the yellow circle). Once the centre of the flock is inside it, the flock starts exploring. It moves between random points inside the circle for a few seconds, as one group.

### 5. Made the flock return to its starting area
After exploring, the flock heads home. Each creature steers toward its own start spot and slows down as it gets close, so it stops gently instead of overshooting. The mission is complete when every creature is back inside the start area (the cyan circle).

### 6. Made the values easy to experiment with
I added keyboard controls, so the Boid values can be changed while the game is running and the effect shows up straight away. I also added presets that change only one value at a time for the Quick Challenge.

---

## How it runs

**Just open the project and press Play.** Nothing needs to be set up first. The flock, floor, lighting and camera are all created automatically when the game starts.

When you press Play, the camera looks down at the start area and the target area. The flock goes through four stages, and the creatures change colour so you can see which stage they are in:

| Stage | Colour | What happens |
|---|---|---|
| Going to target | Blue | The flock flies together toward the yellow circle |
| Exploring target | Blue | The flock moves around inside the target area for a few seconds |
| Returning home | Orange | Each creature heads back to its own start spot |
| Home | Green | The creatures slow down and settle where they started |

A creature turns **red** whenever another creature is too close to it. This made it easy to spot crashes while testing.

The scene also shows some debug drawings:

- **Cyan circle:** the start area. The small dots are each creature's own start spot.
- **Yellow circle:** the target area.
- **Orange sphere:** the point the flock is currently exploring toward.
- **White sphere:** the centre of the flock.

### Controls

| Key | Action |
|---|---|
| 1 / 2 / 3 / 4 | Balanced / High Separation / High Cohesion / High Alignment |
| Q / A | Separation up / down |
| W / S | Alignment up / down |
| E / D | Cohesion up / down |
| T / G | Goal pull up / down |
| R | Restart the mission |
| N | Show lines between neighbours |
| F | Follow the flock with the camera |
| Mouse wheel, Z / X | Zoom |
| H | Hide or show the panel |

---

## Fun Twist – "Don't Lose the Flock!"

The panel in the top-left corner checks each goal live while the flock is flying:

| Goal | How it is checked |
|---|---|
| Stay together | No creature ever gets too far from the flock centre |
| Avoid crashing | No two creatures ever get closer than the crash distance |
| Move naturally | The creatures' directions match by more than 70% |
| Reach the target | The flock centre gets inside the yellow circle |
| Return to start | Every creature ends up back inside the cyan circle |

Each goal turns green when it is met. During the 3 minutes I changed the values, pressed **R** to restart, and watched which goals passed or failed. The balanced settings passed all five goals:

| Setting | Value |
|---|---|
| Separation | 1.5 |
| Alignment | 1.0 |
| Cohesion | 1.0 |
| Goal | 0.8 |

---

## Quick Challenge – changing one value at a time

For each test I started from the balanced settings and raised only one value.

| Change | What I observed | Answer |
|---|---|---|
| **Separation ↑** | The creatures pushed each other away a lot more, so the gaps got bigger and the flock became wide and loose. Crashes almost stopped, but creatures on the edges sometimes drifted too far away and got "lost". | **A. Spread out** |
| **Cohesion ↑** | Every creature pulled hard toward the middle, so the flock squashed into a tight ball. Lots of creatures turned red and the crash count went up. | **B. Clump together** |
| **Alignment ↑** | The creatures copied each other's direction, and heading match went close to 100%. They turned together like a school of fish, but their turns became wider. | **C. Move more like a group** |

### Other things I noticed

- **Separation at 0:** the creatures overlapped and crashed all the time.
- **Cohesion at 0:** the flock slowly drifted apart, and only the goal kept them in the same area.
- **Alignment at 0:** the movement looked jittery because every creature pointed a slightly different way.
- **Goal very high:** the creatures mostly ignored each other and rushed to the target in a messy line.
- **Small neighbour radius:** each creature could barely see the others, so the flock broke into small groups.

## Conclusion

Getting a flock to look natural depends on balancing the three rules. Separation keeps the creatures from crashing, cohesion keeps them together, and alignment makes them move as one group. If any one of them is too strong, the flock either spreads out, clumps up, or loses its shape. With balanced values, the flock was able to explore the target area and still find its way back to where it started.
