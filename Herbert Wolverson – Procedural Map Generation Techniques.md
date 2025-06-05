# Herbert Wolverson – Procedural Map Generation Techniques

**[2020 Virtual Roguelike Celebration – Oct 15, 2020](https://roguelike.club/event2020.html)**

[![Watch on YouTube](https://img.youtube.com/vi/TlLIOgWYVpI/0.jpg)](https://www.youtube.com/watch?v=TlLIOgWYVpI)

## Summary  
Herbert Wolverson is the developer of *Nox Futura*, *One Knight in the Dungeon*, and several 7DRL projects. He authored the *Rust Roguelike Tutorial* and is writing *Learn Rust by Making a Game* (Pragmatic Bookshelf).  He's a regular contributor to the subreddit r/roguelikedev.
 
This talk is a detailed survey of procedural map generation, including integration of hand-crafted "prefabs"/vaults. While the source material is written in Rust, it's readily abstracted to psuedocode and presented in a language agnostic fashion. Techniques include: Simple room placement, BSP room placement, BSP interiors, cellular automata, drunkard's walk, mazes, diffusion-limited aggregation, voronoi, wave function collapse, and prefabs. Sprinkle in a little spice by adding symmetry, layering/combining generators, different corridor algorithms, and doors placement.

---

## Covered Techniques

- Simple Room Placement
- BSP Rooms & Interiors
- Cellular Automata
- Drunkard’s Walk
- Mazes
- Diffusion-Limited Aggregation (DLA)
- Voronoi Diagrams
- Perlin/Simplex Noise
- Wave Function Collapse (brief mention)
- Prefabs/Vaults
- Symmetry and Layering
- Dijkstra Maps for post-processing

---

## Highlights

### 🎮 Rogue (1980)
One of the earliest uses of procedural generation. Rooms were randomly connected, promoting replay through system mastery rather than memorization.

### 🏰 Dwarf Fortress
A poster child for procedural complexity: overworlds, terrain, civilizations, taverns, personalities—even socks—all procedurally generated.

---

## Room-Based Techniques

### 🔲 Simple Room Placement
- Fill the map with solid tiles.
- Try placing random rectangles (rooms).
- Reject overlapping rooms.
- Connect with dog-leg corridors (either vertical or horizontal first).

### 🪓 BSP (Binary Space Partition)
- Recursively split the map into smaller areas.
- Place rooms inside the partitions.
- Prevent rooms from touching via a 1-tile gutter.
- Ensures good spacing with zero overlap.

---

## Cave-Like Generation

### 🧬 Cellular Automata
- Start with random walls/floors.
- Apply rules like:
  - 5+ neighbors → wall
  - ≤4 neighbors → floor
- Iterate for natural-looking caverns.

### 🍺 Drunkard's Walk
- Start solid.
- Place a “drunken” actor (Umber Hulk) who randomly carves tiles.
- Repeat until X% of the map is open.
- Guarantees contiguity.

### 🌊 Diffusion-Limited Aggregation (DLA)
- Start with a seed.
- Randomly shoot “particles” toward it.
- When it hits, carve the last tile passed.
- Result: organic, winding, contiguous structures.
- Supports symmetry for mirrored maps.

---

## Spatial Partitioning

### 🐝 Voronoi Diagrams
- Place seed points.
- Each tile belongs to the nearest seed.
- Can use:
  - Pythagorean distance → smooth edges
  - Manhattan → blocky/urban feel
  - Chebyshev → in between

**Uses:**
- Region-based monster spawning
- Road layout
- City zoning

---

## Terrain Generation

### ⛰️ Perlin & Simplex Noise
- Generate smooth, coherent gradients.
- Use for:
  - Elevation maps
  - Biomes
  - Weather
  - Cloud/wood texture

**Enhance by:**
- Mixing coarse (continent) and fine (local detail) layers.
- Adjusting octaves, gain, lacunarity.

---

## Combining Techniques

### ⚔️ Hybrid Maps
- Mix BSP + Cellular Automata (e.g. dwarven fortress meets underworld).
- Add prefab content like fortifications, traps, or treasure rooms.
- Composite storytelling through layout.

### 💥 Erosion with DLA
- Post-process an existing map.
- “Erode” tiles with DLA particles for decay/naturalism.

---

## 🏗️ Prefabs/Vaults
- Hand-designed tiles inserted into procgen maps.
- Great for narrative beats, puzzles, traps.
- Use placement checks to ensure they fit.
- Don’t insert every prefab—preserves variability.

---

## Pathing & Validation with Dijkstra Maps

### 📊 Dijkstra Maps
- Start with a goal.
- Expand outward marking each tile with distance.
- Use for:
  - Culling unreachable areas
  - Ensuring connectivity
  - Placing start/end points

### 🔥 Hot Path
- Use Dijkstra to find A→B optimal route.
- Mark adjacent tiles (within N distance) as "hot path".
- Use for:
  - Progression tuning
  - Story pacing
  - Bonus content off the main route

---

## 📖 Narrative Integration

- Place key items (like a locked door or boss) along the hot path.
- Ensure required keys/items appear **before** blocked progression.
- Use progression to structure dialogue/story events.

---

## Q&A Highlights

**Q: How to avoid "samey" maps?**  
A: Mix noise layers, use erosion, vary algorithms per region.

**Q: How to generate vertical/Z-level maps?**  
A: Herbert admits this is not his strength but suggests Wave Function Collapse has promise.

---

## Resources

- 📘 [Rust Roguelike Tutorial](http://bfnightly.bracketproductions.com/rustbook/)
- 🧠 [Dijkstra Map Article on RogueBasin](https://roguebasin.com/index.php?title=The_Incredible_Power_of_Dijkstra_Maps)
- 🛠️ GitHub code for this talk (URL given on final slide in original video)
- 📝 Book: *Hands-on Rust* (Pragmatic Bookshelf)

---

## Final Takeaways

- Guide your randomness.
- Check your results with post-process algorithms.
- Mix procedural systems to achieve both variation and control.
- Tell stories through layout, not just writing.

---

_“Guide your randomness, then use algorithms to check your randomness, to make sure it matches what you want.”_  
— Herbert Wolverson
