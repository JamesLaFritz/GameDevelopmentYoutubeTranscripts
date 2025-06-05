# Procedural Level Design in Eldritch  
**2015 GDC talk – Jun 25, 2017**  
🎥 [Watch on YouTube](https://www.youtube.com/watch?v=BYN0PJOdvzs)

## Summary  
In this 2015 GDC talk, Minor Key Games' David Pittman explores how and why procedural level generation was used in the creation of *Eldritch*. He discusses the development philosophy behind building a modular, replayable first-person roguelike on a tight budget and timeline. Pittman emphasizes simple, flexible tools, thoughtful content reuse, and the importance of design sensibility even in automated systems.

---

# Transcript

### Background and Motivation

David Pittman introduces himself as a former AI programmer on BioShock 2, who later co-founded Minor Key Games. With a background in engineering rather than level design, he handled every aspect of Eldritch's development—design, art, audio, business, and marketing—in under eight months. He estimates about 1,500 hours were spent on the game, contrasting it with the hundreds of thousands of hours typical in AAA development. The tight scope required finding "the simplest thing that could possibly work," which became a core principle throughout development.

### Why Procedural Levels?

Pittman outlines several reasons for using procedural generation:

- **Perennial Discovery**: Random maps encourage learning systems over space.
- **Time Constraints**: Hand-building maps wasn’t feasible with limited resources.
- **Reuse of Content**: Modular systems ensure that assets appear frequently, maximizing development value.
- **Play to Strengths**: His programming background made procedural systems more natural than traditional level design.
- **Design, Not Just Code**: He argues that procedural generation is a design activity. A programmer without design sensibilities can't judge level quality effectively.

### Design Goals for Eldritch Levels

Pittman wanted:

- Small, compact levels with clear start and end points (2–5 minutes to complete).
- Environments that felt synthetic and built—sunken cities, not noise-generated caves.
- Seamless spatial continuity instead of disconnected rooms.
- Semi-coherent world logic inspired by Lovecraftian wrongness, not total randomness.

### Visual and Art Style References

Visual inspiration came from Giovanni Battista Piranesi’s architectural etchings and Lovecraftian fiction. Pittman aimed for verticality, atmosphere, and a warped sense of place. Although verticality became problematic in gameplay, it was pursued to reinforce the surreal tone.

### Maze Generation Using Prim’s Algorithm

Eldritch’s levels began as 3D mazes, generated via a modified version of Prim’s algorithm:

- **Base Maze**: Randomly expands from one room to neighboring cells.
- **No Loops or Open Spaces**: A natural constraint of the algorithm.
- **Modifications**:
  - Biased expansion to favor recent rooms (produces long winding mazes vs. branched ones).
  - Biased on the horizontal plane (vertical travel was less enjoyable).
  - Randomly removed walls after generation to add loops, sight lines, and open spaces.

### Prescribed Rooms and Seeding

Rather than hoping generated mazes would contain valid locations for shops or exits, Pittman seeded those rooms before generation. Specific rooms were locked in, and surrounding cells were opened first to guarantee geometry and placement. This ensured structural integrity and feature availability, regardless of random generation results.

### Modular Room Construction

Each cell of the maze was filled with a 12×12×8m voxel-based module:

- **Safe Regions**: Outer buffer zones on module edges ensured proper connectivity.
- **Exterior Variations**: Openings outside safe zones allowed serendipitous visual results, like balconies or dead-ends turned loot closets.
- **Rotation and Mirroring**: Reduced required modules from 64 to 24 unique configurations, allowing reuse with variation.

### Frequency-Based Module Planning

To avoid repetition and determine how many variants were needed per module type:

- A tool simulated 10,000 mazes, recording how often each room configuration appeared (dead ends, L-junctions, T-junctions, etc.).
- More frequent types had more module variants built.
- Eldritch shipped with ~300 modules total, with ~60 per world.

### Theming and Visual Noise Reduction

Each module was tagged with a theme (e.g., Warehouse, Cave, Ruins). During generation, a dominant theme was chosen, with fallbacks used only when necessary. This reduced visual noise and made levels feel more coherent while maintaining variety.

### Feature Rooms and Landmarks

In addition to modular rooms that could appear anywhere, Eldritch featured special "prescribed rooms" that required specific geometry and placement—entrances, exits, shops, shrines, bank vaults, and more. These were declared explicitly in the generation system and seeded into the maze before Prim’s algorithm ran. By locking down these cells and their neighbors, the maze algorithm would respect their structure and never override them—even when wall removal logic was applied later.

These rooms served two main purposes:

1. **Functional Needs**: Ensured gameplay-critical elements like shops or exits were always accessible and properly placed.
2. **Rare Setpieces**: Delivered standout, hand-crafted moments—memorable encounters that contrasted with the procedural chaos.

For example, a throne room guarded by two Doctor Who-inspired “weeping angel” statues could appear once per level. These statues would only move when the player wasn’t looking, creating a unique, high-stakes moment. Because it used handcrafted logic, it didn’t rely on enemy AI randomness and stood out as a highlight.

Feature rooms also enabled **large-scale modular assemblies**, like the Ziggurat at the end of World 1. It was composed of **eight adjacent modules** forming a massive landmark—something impossible to create with the base maze logic alone. By prescribing it as a feature room, Eldritch could include grand structures that broke the usual visual scale and spatial logic.

These rooms helped achieve one-off spectacle, visual anchors, and carefully paced moments of surprise within a procedurally generated world.

### Entity Spawning and Placement

Once the maze was generated and rooms were filled with geometry, the game needed content: enemies, traps, loot, and key objects. Pittman established four core **entity placement goals** that guided the procedural system:

1. **Challenge the player** with enemies and traps.  
2. **Reward exploration** with loot and upgrades.  
3. **Ensure spatial logic**, so items appear in believable, sensible places.  
4. **Control pacing and difficulty**, scaling enemy count and trap frequency over time.  

Because procedural levels lack a designer’s hand on each instance, the generator had to support systems that fulfilled these goals reliably and flexibly.

#### Declarative Spawner System

Instead of hardcoding entity placements into modules, Eldritch used **abstract spawner objects**. These acted as "opportunities" where something might appear, with rules determining the final outcome at runtime.

Spawner behaviors included:

- **Fixed spawns**: Always generate a specific item or actor (e.g., player start point).
- **Chance-based spawns**: Spawn based on probability (e.g., a spike trap appears 1-in-3 times).
- **Weighted random tables**: Choose among multiple items with assigned weights (e.g., different enemy types or coin piles).

For example, a bank vault might have a 75% chance to spawn coins. If it triggered, it might randomly choose between a single coin or a large coin pile, giving variance even in specific feature rooms.

Enemy spawners used weighted tables to balance difficulty—basic enemies would dominate early levels, with rare tougher foes introduced sparingly.

#### Resolve Groups and Population Control

To maintain **game balance and pacing**, Eldritch grouped spawners using tags (`Enemy`, `Trap`, `Loot`, etc.) and ran a resolution step after room placement:

- **Max caps**: Only X enemies or healing fountains per level.
- **Filtering logic**: Remove spawns too close to the player spawn point.
- **Selective activation**: Choose a subset from all candidates to use.

This let Pittman scale enemy counts as the game progressed (e.g., from 12 enemies in World 1 to 24 in later levels) and prevent unfair situations like spawning enemies beside the player on entry.

#### Strategic Placement Philosophy

Beyond functional placement, Pittman used **level structure** to reinforce purpose and pacing:

- **Loot** was placed in dead ends, signaling reward and motivating exploration.
- **Traps** were placed in linear corridors or vertical shafts—places where traversal was the only focus.
- **Enemies** were ideally placed near junctions, enabling multiple paths for engagement or stealth. Though their AI could undermine this by wandering randomly, the design intent remained.

These ideas emerged late in development, but Pittman emphasized that **placement philosophy**—even in procedural levels—makes a massive difference. Treating spawn rules as a form of level design turned randomized spaces into directed, learnable experiences.

> “This stuff made the game way better. And it’s not mutually exclusive with procedural generation. If I had known what level designers know earlier, I would’ve done it from the start.”

### Final Thoughts and Key Takeaway

Pittman concludes that many principles of hand-built level design still apply to procedural systems. The biggest improvement came from applying level designer knowledge to guide placement, pacing, and structure within generated levels.

> "I hope level designers can be as excited about procedural generation as I am—but bring your expert knowledge to it and make levels better than Eldritch."

---

### Q&A

#### Q: Can you expand on your issues with verticality in level design?

**A:** Vertical movement was difficult to implement cleanly. While Eldritch had advanced movement options—mantling, rope guns, and jump powers—I couldn’t guarantee players would have them at any time. That meant I had to build long, awkward staircases to connect vertical modules. These spaces were harder to design, often less interesting, and consumed more room than they were worth. Falling between levels would’ve been much easier to support than climbing up.

#### Q: How long did it take before your tools were generating good levels?

**A:** It’s hard to pinpoint. Development was fast-paced, and I continued tweaking things up to the end. The first world was also the first one I built, so it reflects early experimentation. Over time, the levels improved as I learned what made them feel good. The tools themselves didn’t change much—most of the improvement came from learning how to guide the generator better.

#### Q: Do you have any advice for non-programmers who want to build procedural systems?

**A:** I didn’t use off-the-shelf procedural tools like Houdini, but they could work if they fit your project. For Eldritch, the generator was closely tied to the game’s needs, and it didn’t take long to build—maybe two weeks for the system, two more for content. If your requirements are bigger or more visual, a dedicated tool might help. But for me, writing custom code was faster and more flexible.

#### Q: Was building your own engine worth it?

**A:** I didn’t build the engine during Eldritch’s development—it’s something I had already worked on as a hobby for 8–9 years. At this point, I’m highly efficient in it. For me, it’s like using a licensed engine. That said, I wouldn’t recommend it to most people. Tools like Unity and Unreal are powerful and accessible unless you already have a custom workflow you’re deeply familiar with.

#### Q: If you made a sequel, how would you improve the system?

**A:** I’d support variable-sized modules. In Eldritch, every module is 12×12 meters, which limited layout variety. I originally planned to detect larger openings in the maze and insert bigger setpieces like a full mine shaft. That would allow for more visual variety and structured features. I’d also explore smarter feature blending and less rigid grid alignment.

#### Q: How did you handle pacing in a procedural game?

**A:** I couldn’t control moment-to-moment pacing like in handcrafted games. Eldritch encouraged player freedom—players could build any combination of powers and weapons, so I couldn’t predict exact behaviors. My only levers were population density and item timing. I increased enemy count and item availability between levels, and ensured more powerful abilities only appeared in later stages. But within each level, pacing was mostly emergent.

---
