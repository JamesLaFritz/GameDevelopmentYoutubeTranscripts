# End to End Procedural Generation in Caves of Qud

**GDC Talk – May 13, 2019**  

[![Watch on YouTube](https://img.youtube.com/vi/jV-DZqdKlnE/0.jpg)](https://www.youtube.com/watch?v=jV-DZqdKlnE)
 
## Summary

Freehold Games’ Jason Grinblat and Brian Buckley explain how they procedurally generate villages in *Caves of Qud* from high-level abstract history and culture down to concrete game objects like NPCs, buildings, and quests. They walk through the full system architecture step by step, focusing on design context, modularity, storytelling traditions, population tables, and more—offering a rare, comprehensive look at a deeply simulative PCG pipeline.

---

## Transcript

### Introduction

[Music]  
**Jason**  
I'm Jason Grinblat, and this is Brian Buckley. We're co-founders at a small indie studio called Freehold Games, and we're going to end the day of math for game developers with a talk with basically no math in it. So I apologize in advance.  

We're going to be walking you through the architecture for one of our big procedural generation systems. The talk is called *End-to-End Procedural Generation in Caves of Qud*.  

---

### Problem Statement

Let’s say that you have some familiarity with procedural generation—some various techniques. You’ve used L-systems to generate some plants or maybe this weird horseshoe creature. You’ve used noise maps to generate terrain, maybe you’ve used a Markov chain to generate text.  

But we have a problem here. Here’s our problem statement, which is the reason why we’re here:  
**How do you combine procedural generation techniques to build large-scale generative systems?**

Large-scale is relative, of course, but what we mean is any system that requires you to compose several of these techniques in some novel way. Something you can’t just go and look up a tutorial for.

---

### Who Are We

So who are we? Why are we here to talk about this today? Again, we’re Jason and Brian from Freehold Games.  

We’ve made some roguelikes—*Sproggiwood* and *Caves of Qud*. A couple years ago, Tanya Short and Tarn Adams edited a book called *Procedural Generation in Game Design*. We have a few chapters in there.  

And then coming out today or something—this *Procedural Storytelling in Game Design* book is available here at GDC, and I have a chapter in there about procedurally generating history, which is one of the modules we’re going to touch on today.  

*Caves of Qud* is an open-world science fantasy RPG and roguelike. It’s a tile-based game. The tagline is "chisel through a layer cake of thousand-year-old civilizations."  

We’ve woven a handcrafted narrative—the kind you might find in an *Elder Scrolls* RPG—around deeply simulative systems.  

As I said earlier, and as Brian said in his last talk, we make extensive use of procedural generation. That’s kind of why we’re here.    

---

### Outline

So we’re going to walk you through a little case study of how we procedurally generated a large-scale output—a whole village in *Caves of Qud*.  

Where we can, we’ll try to generalize into some wisdom. But for the most part, we’re just going to walk you through the system and you can experience that and take notes and see how you might apply it to your own generative systems.  

Here’s what we’re going to do:
 - First, establish design context.
 - Then, describe our broad approach.
 - Define what a village is.
 - Show some of the techniques we use.
 - Deep dive into “Village Generation”—this is where most of the talk lives.
 - Try to distill takeaways at the end. 

---

### Design Context

Design context is very important.

Any time you’re building a large-scale system—or any system—it’s important to frame it with your design context. That’s what’s going to guide you when making decisions.  

A few bullet points:
 - **We’re making an RPG**, so we’re taking a **simulative approach to design**. A village exists in the world. We’re trying to reproduce its dynamics—not perfectly, but we heighten certain aspects.
 - **Perspective is limited to a player avatar.** Players walk through the village, talk to NPCs, experience the space firsthand.
 - This is different from *SimCity*, where you’d engage with a village over centuries through stats.
 - **We value novelty in output.** We let our generators run wild and build a context where that’s okay. 

---

### Modularity

Modularity is good.  

From a software perspective:
 - Decoupled modules are tractable.
 - They’re flexible.
 - They can be recombined.

From a game design perspective:
 - Decoupled modules dovetail with the idea that we let our generators run wild.
 - Modules composed into novel output = fun.

Silly metaphor time:  
This is Mount Fuji, but today we’ll call it **Abstraction Mountain**.

Our approach:  
 - Model the village abstractly first.
 - Transform that through multiple steps into concrete data.
 - At the bottom: Concrete Valley—where we fabricate game objects.

---

### Village

Here’s a village in *Caves of Qud*. It’s a tile map. A lot is going on here:
- Inner circular building.
- Outer circular building.
- Multiple doors.
- Bottom-right: a library with a bookshelf.
- Top-right: a kitchen or food storage building with an oven and vessels.

This is the final result we’re unpacking. Let’s understand what got us here.

---

### Village Concept

Let’s get on the same page: what is a “village”?  

A village is a world concept—one we can form consensus around. Since *Caves of Qud* is far-future but styled after ancient-world aesthetics, we borrowed ideas from history.

We’ve also been working on the game for over a decade. We had static, hand-authored villages. So we unpacked those and identified design principles to proceduralize.

Villages include:
 - **Villagers** – Represented as NPCs.
 - **History** – The village’s relationship with its past.
 - **Geography** – Where it is and how it's embedded in the world.
 - **Architecture** – How it’s constructed.
 - **Government, religion, culture** – A whole culture is generated.
 - **Storytelling tradition** – How the village passes on its history.
 - **Resource relationships** – What ingredients they use, what they cook.
 - **Furniture styles** – For every building.
 - **Food and drink** – Signature meals.
 - **Dialogue and proverbs** – Procedurally generated from culture/history.
 - **Quests** – Villager needs, modeled as RPG-style quests.

---

### Techniques

***Techniques we use
 - **Population tables**
 - **Noise maps**
 - **Wave Function Collapse**
 - **State machines**
 - **Replacement grammars**
 - **Dijkstra mapping**
 - **Dynamic image manipulation**
 - **Pathfinding**
 - **ECA with hand-defined prefabs**
 - **Inversion of control**
***

We’ll touch on these as we go through the pipeline.  

---

### Village Generation

Village generation happens in three distinct **phases** during gameplay:

#### 1. Design Phase (compile time)

 - Static, handwritten templates.
 - Pre-baked materials.
 - Happens once, built into the game.

#### 2. World Generation

 - When you hit “New Game,” we generate:
   - Village history.
   - Village neighbors and their relationships.

#### 3. Village Fabrication (on-demand)

 - Only triggered when you **enter** the map tile where the village exists.
 - This is when we:
   - Generate the culture.
   - Generate the architecture and layout.
   - Fabricate game objects—NPCs, walls, furniture.

This design is efficient. It delays expensive work until it’s needed.

---

### Abstraction Mountain

Back to our metaphor.

Five steps down abstraction mountain:
 1. Generate history (abstract)
 2. Resolve neighbors (still abstract)
 3. Generate culture (getting more specific)
 4. Generate architecture (concrete map)
 5. Fabricate game objects (what the player sees)

The player only interacts with the final output. That means:
 - History? We can be abstract.
 - Fabrication? Needs concrete data.

Each step consumes input, processes it, and outputs data for the next step.

Sometimes we don’t have the needed input. That’s fine—we use defaults.

That’s the power of **partial modeling**. We don’t need to model everything to generate something coherent. Humans perceive patterns. We rely on **apophenia**—you’ll fill in the gaps.

We focus on the **plumbing** between steps—how data flows and transforms. We won’t deep dive into individual module internals unless they’re interesting or novel.

---

### Monument Hall

Here's a monument in one of these villages we're going to be using it as a sort of touchstone like sort of a nice consistent thread that we're going to be able to follow as we do this walk through of this big system.

It's a little building, some sort of **monument hall**.
We see there's some torch posts in the corners and then there's in the center of these yellow and cyan  monuments.
Everything in *Caves of Qud* is an object you can interact with.
It it has a little description here which reads:

> “In 790, the villagers of Nawan demanded that the mayor abdicate and all forms of hierarchy be abolished from their village the two pillars of civic life and now on thus became anarchy and valatara legendary giant dragonfly”

*Caves of Qud* is a game where there's a lot of uplifted plants and animals.
So it's very common to see a lot of like sapient dragonflies for instance.
If you see on the right side of this village there's a little statue of a dragonfly, which also is part of the village.

We’ll trace how that got generated.  

---

### Generate History

**Step 1: Generate History**

Here’s how we structure each step:
 - **Inputs** from the left (dynamic) and bottom (design).
 - **Outputs** go to the next step.
 - A **right pane** tracks which of the 5 steps we’re in.
 - Labels indicate whether it’s happening in world generation or fabrication.

#### Inputs:

 - **Dynamic input:** World seed
 - **Design inputs (bottom):**
   - Factions
   - Regions
   - Cultural relationship graph

The **world seed** lets us regenerate the world deterministically.

#### Design Inputs Explained:

**Factions**  
These are pre-authored. You can see them on the faction screen—apes, antelopes, arachnids, etc. You have a reputation score with each.

**Cultural Relationships**  
This is a graph modeled as a replacement grammar. It's also used to generate procedural text.  
We map culture concepts (e.g., what’s sacred, what’s profane) and how they relate.

---

#### Output:

What do we generate at this step?
 - Village name
 - Founding faction
 - Color palette
 - Historical events
 - Sacred/profane elements
 - Signature items and dishes
 - Government type
 - Preferred architectural features

These are **abstract** outputs. They’re not implemented as full systems, just data tags.

Internally, this is stored as a simple object with key-value pairs like:
 - `"name": "Nawan"`
 - `"governor": "abdicated"`
 - `"signature_dish": "electric schnitzel with goulash"`

These are used later to generate descriptions, architecture, and behavior.

#### How it works:

We use a state machine and a library of historical event templates.

Sometimes we **start from the outcome** and reverse-engineer the justification.  
For example:
 - Outcome: “The village worships a creature.”
 - Then: Pick a nearby creature and generate rationale.

---

### Historical Text and Monuments

That quote from the monument:
> “In 790, the villagers of Nawan demanded that the mayor abdicate...”

It was generated here in Step 1. It gets attached to a monument later in Step 5.

We store it in the history, and reuse it when generating the monument object.  

---

### Resolve Neighbors

**Brian**

Now that we've generated abstract villages, we need to start grounding them in the world. Right now, they don't exist in space. They just know things like “we worship a creature,” but not which one.  

So we take a step down **Abstraction Mountain**: we **resolve neighborhood relationships**.

#### World Location

We use these inputs:
- **World seed** (for deterministic randomness)
- **Village history** (from Step 1)
- **World map** (static)

In *Caves of Qud*, the **world map** is largely static:
 - Tile types (jungle, desert, mountain) are fixed.
 - Certain important locations are static.

But **villages** are placed dynamically.  

The world map includes metadata about:
 - Surface features (e.g. “jungle with ruins”)
 - Encounter types
 - Music to play
 - Subterranean features

#### Placing Villages

Each village has a tag like “mountain village.” We find a tile that matches, then place it.  
Example:
> “Shubal” is placed in a mountain, honoring Susuraya the legendary goat.  

Goats live in mountains—this makes sense.  

We also place other world objects here:
 - Legendary creatures
 - Shoemakers
 - Distilleries

Villages are just one kind of object in this placement pass.

#### Reifying Abstract Relationships

Now that we have actual village locations, we can reify abstract tags.

Example:
> A village worships a “legendary monster.”  
→ We find a nearby legendary monster.  
→ Assign that monster as the one they worship.  
→ Retrofit the history with this new info.

So:
 - Abstract: “worships legendary creature”
 - Becomes: “worships Kellyufat the legendary boar”

We do this for all relationships:
 - Enemies
 - Sacred relics
 - Trading partners

We match based on spatial proximity and tag compatibility.

#### Factions

Villages also become **factions**.

Remember those static factions like “apes,” “bears,” etc.?  
We create a new **dynamic faction** for each village, and link it to the static ones.  
This allows the village to have:
 - Reputation
 - Goals
 - Enemies
 - Allies

This completes **world generation**.

---

### Generate Culture

**Jason**

We’re in **Step 3**: generate culture.

Now the player enters the village. We need to generate all cultural output. It’s still abstract but much more specific.

#### Inputs:

 - **Seed** (implied by random calls)
 - **Village history** (updated)
 - **Village region** (affects food, building style)

#### Design Inputs:

 - **Population tables**
 - **Cultural relationship graph**

We use these to derive:
 - Storytelling tradition
 - Furniture style
 - Religion
 - Foods
 - Cultural practices  

---

### Population Tables

Population tables are weighted decision tables with metadata.  

They help pick:  
 - Storytelling traditions  
 - Encounter types  
 - Furniture for specific rooms  
 - Placement rules (like “inside corner,” or “radius 2 from door”)  

---

### Signature Dishes

Every village has a signature dish.  

Example:  
“Fungus and electric schnitzel with goulash”  
 - Imbues resistance to fungal infection and electrical damage  
 - Can be learned and cooked by the player  

Generation uses:
 - Village history (chef arrived?)  
 - Region (local ingredients)  
 - Cultural graph (for naming)  
 - Population tables (to pick ingredients and effects)  


---

### Storytelling Traditions

Let’s return to our monument hall. This village’s **storytelling tradition** is:  
→ Build monuments to record their history.

Other traditions:
 - Engrave stories into furniture (e.g. ovens)
 - Inscribe stories into books (libraries)

Example:
> “History of Toppable, Vol. 2” — a procedurally generated book object.

---

### Population Tables

How do we pick a storytelling tradition?  

We roll on a **population table**. Each option has a weight.

Example:
 - Doors (weight 20)
 - Monuments (weight 40)

So: Monuments are twice as likely as doors.

#### What is a population table?

It’s a tool we wrote to encapsulate **discrete procedural logic**:
 - Weighted choices
 - Conditional logic
 - Metadata for hints

Example:
```xml
"torch_post": {
  "count": "1-2",
  "location_hint": "radius 2 from door"
}
```

The consumer of the table (e.g. map builder) interprets those hints.

We nest population tables too:
 - One table might return a “goatfolk encounter” that itself rolls on a subtable for:
   - Goatfolk Savages
   - Yurt Wardens
   - Shaman

We use these tables for:
 - Creatures
 - Furniture
 - Foods
 - Storytelling traditions

---

### Signature Dishes

So back to our steps here—we’re in step three of our five steps on our journey down Abstraction Mountain into Concrete Valley. We just picked a storytelling tradition. Let’s look at another output: **signature dishes**.

We really wanted to make food important in *Caves of Qud*. We wanted to replicate the idea that food is a major part of culture that's often overlooked in games.

Each village has a **signature dish** that they’re known for cooking. You can walk up to their oven and eat it.  

Example:
> This village is known for their **fungus and electric schnitzel with goulash**.

Each of these dishes has a **procedurally generated status effect** it grants the player.

In this case:
 - The dish provides **protection against fungal infection** (which can happen in the game),
 - And **protection against electrical attacks**.

You can also:
 - Walk up to the villagers and engage in the **Water Ritual** (a cultural exchange system),
 - Spend **reputation** to **learn how to cook** the dish,
 - Use the **cooking system** at a campfire with the right ingredients to make it yourself,
 - And get the same effects.

#### Inputs touched to generate a signature dish:

 - **World Seed**: for randomness in selecting ingredients and combinations.
 - **Village History**: sometimes villages have events that involve food—like a traveling chef arriving, or a new crop being introduced. These become part of the history and inform food culture.
 - **Village Region**: each biome has its own set of local ingredients—dishes differ between jungles and deserts.
 - **Population Tables**: map regions to available ingredients (plants, animals, fungi).
 - **Cultural Relationship Graph**: used to generate a *sensible name* for the dish by stitching together text snippets and cultural tags.

#### Output:

A data object representing the signature dish.  
When we reach the **Fabricate Game Objects** step, we use this data:
 - To stock ovens with the dish,
 - And to allow villagers to offer it in dialogue and rituals.

So by the time the player encounters it in the world, it feels like a fully authored piece of culture—despite being generated at runtime from multiple abstract layers.

---

### Generate Architecture

**Brian**

Okay, so we've gone through three entire steps—big ones—and we haven’t built any game objects yet.

Now, in Step 4, we’re finally going to generate the **architecture** of the village.

We need to make concrete decisions from all the abstract data we’ve built up so far.

#### Inputs at this stage:

 - Storytelling tradition (determines important buildings)
 - Cultural preferences
 - Region (affects style)
 - Population tables (for everything)

We need to decide:
 - **Building style** (round, square, tented, etc.)
 - **Important buildings** (e.g., a monument hall or library)
 - **What plants to grow** (agriculture, decoration)
 - **Liquid types, door types, wall materials**

We’re about to **build a map**.

---

#### Building Style

We make this decision using population tables again.

##### Example:

 - **Default style**: fallback
 - **Region-specific table**: e.g., Salt Dunes → Tents are weighted higher
 - **Faction-specific table**: Birds → aeries, Fish → ponds, Mollusks → ponds

We use a **priority system**:
 1. Check faction-based table (highest priority)
 2. If missing, fall back to region-based
 3. If still missing, use default

This is flexible and expressive. We don’t have to define styles for every faction—just the important ones.

---

#### Important Buildings

Storytelling tradition determines which buildings matter.

If the tradition is **monuments**, we build a monument hall.  
If it’s **books**, we build a library.  
If it’s **tattoos**, no special building—tattoos go on villagers.

Again, population tables drive this.

---

#### Map Layout

Let’s generate a map!

 1. We start with a **zone**, say a canyon.
 2. We generate the base terrain (with monsters, plants, etc.)
 3. Then we **strip it clean**—remove everything to make room for the village.

Now we need to place buildings.

This is non-trivial. How do we fit buildings into arbitrary terrain?

---

#### Dijkstra Maps for Placement

We use **Dijkstra maps** (Dijkstra flood-fill algorithm) to find regions.

Each color in the debug screenshot is a distinct region:
 - We drop random seeds into the map.
 - We grow regions via Dijkstra expansion.
 - Any unfilled areas get new seeds.
 - We keep adding seeds until each region is small enough.

Then:
 - We compute the **largest rectangle** inside each region.
 - Those become our **building plots**.

It’s a great solution for distributing space while maintaining walkability.

---

#### Building Generation

Each plot becomes:
 - A square building
 - A round building
 - Or a Wave Function Collapse (WFC) generated structure

We use **WFC templates** for stylistic buildings:
 - Small templates for huts
 - Large templates for public buildings

These are dropped into the plots.

We also build **tent villages**, **carved caves**, or **multi-room homes** depending on culture.

---

#### Interior vs Exterior

Once buildings are placed, we run a second Dijkstra pass:

 - Place seeds inside each building.
 - Flood-fill to mark **interior space**.
 - Close off narrow openings.

Now we know:
 - Where is “inside” vs “outside”?
 - Where can we put **furniture**?
 - Where do **villagers spawn**?

Now we’ve got a complete map layout, ready to be populated.

Here’s an example:
 - Lower right: tents
 - Upper left: green building
 - Right side: WFC stone building
 - Cave dwellings along the cliff

Everything is derived from prior steps and region context. 

---

### Fabricate Game Objects

**Jason**

Now we’re in **Concrete Valley**—Step 5: Fabricate Game Objects.

Finally, we’re generating what the player actually sees.

#### Design Inputs:
- **Object blueprints** – pre-authored XML files

Each game object (e.g., torch post) is defined as a blueprint:
- `RenderPart` – how to draw it (ASCII or tile, colors)
- `PhysicsPart` – is it solid? does it block movement?
- `LightSourcePart` – radius and behavior

Example: Torch post blueprint
```xml
<RenderPart tile="torch" color="red" />
<PhysicsPart solid="true" weight="1" />
<LightSourcePart radius="6" />.
```
Each component encapsulates logic.

---

#### Output

A **furnished and populated village map** is the output of this final step.

Let’s return to the example village we showed earlier—the one with multiple buildings, NPCs, and cultural landmarks. Every object in this space is generated or modified in this step.

---

##### Monument

- This came in from the previous step, **Generate Architecture**, as a planned structure.
- Now we instantiate a **monument object** from its blueprint.
- We **dynamically attach** a snippet of history generated in Step 1.

For example:
> “In 790, the villagers of Nawan demanded that the mayor abdicate and all forms of hierarchy be abolished…”

This snippet becomes the **description text** for the monument object.

---

##### Desecrated Statue

- During **Generate Culture**, the village developed a hatred of a legendary creature.
- During **Resolve Neighbors**, we found a nearby candidate: Liramir, a bloated leech.
- During **Generate Architecture**, the village style included monuments or statues.
- During **Fabricate Game Objects**, we:
  - Instantiated the statue
  - Marked it as **desecrated** because it represents something hated

So now we have:
> “A desecrated marble statue of Liramir, the legendary bloated leech”

This entire narrative is constructed over multiple steps and only assembled visually now.

---

##### Goat Villager

- Villagers can be animals or plants.
- In this case, a **goat** is a villager.
- The goat object is instantiated from a prewritten blueprint (just like the torch post).
- Before placing it, we **modify it**.

In this village, the storytelling tradition is **tattoos**, so:
- We add a tattoo part
- The tattoo includes a snippet of procedurally generated text, pulled from the village’s history or cultural graph

This way, even animals can wear their village’s stories.

---

##### Dragonfly Queen

- The village converted to a monarchy.
- We spawn a **dragonfly NPC** to serve as queen.
- She receives special dialogue lines reflecting her role.

Here’s a line:
> “Resorbing matter is no way to bless the queen.”

This is a **proverb**, procedurally generated by:
- Pulling cultural values
- Replacing tokens in a grammar template

In this case:
- “Resorbing matter” refers to gelatinous cubes (a known game entity)
- It implies disdain for them, supporting the queen’s values

---

##### Plant Villager with Quest

 - This villager is a **shrub**.
 - The game has a dynamic quest system driven by village history and culture.

This shrub asks:
> “What pearls might this place contain? Carbon dioxide, ample sunlight, a shrine to the practice of shield slamming… We must know.”

How did this happen?
 - Plant-based villagers **like** CO₂ and sunlight (from cultural graph)
 - “Shield slamming” is a **cultural practice** picked earlier in the pipeline
 - The **quest system** uses these tags and fills a grammar template

This shrub offers:
 - A quest to visit a location with those properties
 - A **skill reward**: If you complete the quest, they teach you **shield slamming**

So even a quest-giving NPC is the result of five connected, layered systems—designed, generated, and finalized by this point.

---

Each of these objects—the monument, the goat, the queen, the statue, the shrub—exists independently, but also harmonizes as part of a single cohesive village.  

Each has:
 - A history
 - Cultural context
 - Relationships to other entities
 - Dialogue or behaviors that reflect procedural decisions

They all came together here—in **Step 5: Fabricate Game Objects**.

---

### Takeaways

**Jason**

This is a complex system, and your takeaways might vary. But we’ve found a few patterns that repeatedly helped us build this architecture.

### 1. Think carefully about your design context

Everything in our system—what gets generated, how detailed it is, where it shows up—was driven by design.

 - We’re making an RPG.
 - The player has a single perspective.
 - They’re not running simulations over centuries.
 - They’re walking through a village, talking to people.

So, design decisions informed every technical decision.

---

### 2. Lean into modularity

There’s a strong temptation in procedural generation to couple everything together—to make every choice feel meaningful, connected, and justified.

But: you don’t have to.

 - You can decouple modules.
 - Let them run wild.
 - Use partial inputs or defaults when necessary.
 - Accept that some decisions are made randomly or heuristically.

Often, the result still feels coherent. Players infer meaning. Surprising outputs emerge. That’s part of the joy.

---

### 3. Use abstractions to your advantage

You don’t have to model everything in full detail.

Example:
 - Village history is deeply abstract.
 - It’s only surfaced through:
   - Monuments
   - Books
   - Dialogue

So we don’t need to simulate full historical causality.  
We generate **snippets**, and **players fill in the gaps**.

Ask yourself:
 - What parts of your system are visible to the player?
 - What needs to be real, and what can be smoke and mirrors?

---

### 4. Parameterize your modules

Build modules that:
 - Take input from the previous step
 - Use it where relevant
 - Default where it’s missing

This keeps your system:
 - Maintainable
 - Flexible
 - Extendable

---

### 5. Build expressive tools

In our case: **population tables**.

We were constantly making weighted, discrete procedural decisions. So we built a tool to handle that cleanly.

This let us:
 - Define tables in data
 - Nest them
 - Add metadata (like placement hints)
 - Share logic across systems (creatures, furniture, storytelling, quests)

Any time you find yourself copy-pasting logic across content, you probably want a tool.


---

### Questions

**Jason**  
That’s it. Thank you very much. I’m Jason, this is Brian. I think we have a little time for questions.

**[Applause]**

---

#### Q1: ECS and procedural modules

**Audience Member:**  
Brian, I’m a fan of your IRDC talk on data-driven design. Could you compare the componentization of your blueprint system with the architecture used for world generation? Is it composed of discrete components?

**Brian:**  
The question is how related is the entity-component system to these procgen modules?

There’s *some* relationship. We’re inspired by ECS-style modularity. But in reality, we sort of backed into these modules over time.

The idea of discrete procedural modules feeding each other input and output—we didn’t see that modeled anywhere. We had to invent architecture to make it work.

So no, it’s not pristine ECS. But we like ECS for game object definitions.

The unevenness of abstraction levels—some modules are extremely abstract, others are concrete—makes it hard to unify them under one architecture. Maybe in 25 years we’ll know how to do it. Right now we’re just peering in the door.

**Jason:**  
And because the game’s had a long dev cycle—10+ years—we keep adding modules we didn’t anticipate. So by necessity, it’s become a modular system bolted together over time.

---

#### Q2: Will you release population tables?

**Audience Member:**  
Population tables look great. I see they’re in XML. Have you considered releasing that tool for the community?

**Jason:**  
We’re considering it *right now* that you mentioned it!

**Audience Member:**  
Awesome. I'd love to see it.

---

#### Q3: Visualizing generation

**Audience Member:**  
You showed some ASCII and buggy Visual Studio screenshots—was that how you visualized the generation? Or did you build more tools?

**Brian:**  
Those visualizations weren’t made for GDC. They’re my actual debug tools. ASCII mode + Visual Studio debugger—that’s it.

**Audience Member:**  
Hardcore. Did you ever consider making something more user-friendly?

**Brian:**  
Honestly, I built the tools I needed to get the system working. Were they pleasant to use? Maybe not. But they were sufficient.

Individual techniques—WFC, noise, pathfinding—are straightforward. The hard part is **designing the generative space**, and mapping simple tools to complex outcomes.

**Audience Member:**  
Did you ever encounter weird, broken output you couldn’t understand?

**Brian:**  
In *Caves of Qud*, when something strange happens we often say: *“That’s cool, let’s leave it in.”*

But when debugging is needed, we can:
 - Load the player’s save file,
 - Rebuild that zone using the same seed,
 - Step through it in the debugger,
 - And see exactly how that weird village got generated.

Because it’s deterministic, we can always reproduce it.

**Jason:**  
We do have a few other tools:
 - Noise map visualizer
 - Grammar previewer
 - Modding tools

But yeah—mostly it’s raw code and debugger.

---

#### Q4: User-driven generation?

**Audience Member:**  
Do you do any generation based on user input?

**Jason:**  
A little. The player’s actions get logged in a **chronology**, and we sometimes seed things from that.

But mostly, generation happens during world gen and fabrication. Player input influences state more than structure.

---

#### Q5: Save game format?

**Audience Member:**  
Do you save the full world, or just seeds?

**Brian:**  
Serialization is awful. We actually serialize the entire world state in chunks. Each screen-sized zone gets saved.

We use .NET binary serialization. We just dump the structure to disk. It causes problems, but mostly works.

---

#### Q6: Functional programming?

**Audience Member:**  
The pipeline looks functional—composition of input/output. Do you use functional programming or persistent data structures?

**Brian:**  
No, not really. It’s all C#. We generate data, put it in a structure, then the next module uses it.

We **think** about it functionally to keep things clean. But the implementation is messier. There’s global state. Modules reach into it.

Some abstraction helps us **explain** the system, but the code itself is layered, messy, and pragmatic.

**Jason:**  
Yeah, the code’s like 15 years old. It has a bit of everything at this point.

---

#### Q7: Starting abstract or concrete?

**Audience Member:**  
If you were building a system like this today, would you start abstract or concrete?

**Jason:**  
Start by visualizing your output. Maybe prototype some concrete results by hand. Figure out what you want.

Then, in your actual system—start abstract. Especially if the concrete format is easy.

**Brian:**  
I agree. Start by hand-authoring what you want your output to look like.

Then figure out where the *risk* is:
 - If the risky part is abstract modeling, start there.
 - If the risky part is the concrete output format, start there.

Flush out unknowns early. Context matters.

---