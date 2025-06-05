# Procedurally Generating History in Caves of Qud

**GDC Talk – Jun 12, 2018**

[![Watch on YouTube](https://img.youtube.com/vi/H0sLa1y3BW4/3.jpg)](https://www.youtube.com/watch?v=H0sLa1y3BW4)
 
## Summary

In this 2018 GDC talk, Freehold Games' Jason Grinblat walks through the novel approach he used to generate histories for Caves of Qud, laying out some commonalities across games that generate histories, then demonstrating how he avoided building a full historical simulation by generating historical events and rationalizing them after the fact. 

---

## Transcript

### Introduction 0:00

Hello, my name is Jason Grinblat. I'm the co-founder of a small indie studio called Freehold Games, and we make a game called *Caves of Qud*, which is a hybrid open-world RPG, science fantasy roguelike. We do a lot of experimentation with procedural generation. Today I'm going to tell you about a recent system we designed for procedurally generating history for our game world.

Imagine you are a lore-seeker in a wild and fractured world. You've just come to a strange land called Qud, where poison jungles strangle rusted archways and chrome steeples to the earth. Your first stop is an oasis village called Joppa. In the northeast corner, there's a vine-swaddled stone shrine. You approach and examine it, and see it's depicting a historical event:

> "Shrine to Ammas II, Mirrorborn. While leading a small army in the forum province of Elpe, Ammas II assassinated the Sultan of Qud over an ordinance prohibiting the practice of staining glass. She won and ascended to the crown. She was six years old."

---

### Overview 1:23

This is an example of procedural history generated for one game. I'm going to tell you how we got here. Here's the path:  
 - Proceduralizing history in general, with examples  
 - Context from *Caves of Qud*
 - Some of our constraints and aesthetics that we followed to arrive at the design 
 - Generated histories and our model  
 - Crash course on replacement grammars and text generation  
 - Causality and narrative coherence for histories
 - Resources and takeaways for your own generative history system

okay so it's kind of corny to start with the dictionary definition in a talk, but when we're talking about something as abstract as history I think it's useful to get on the same page.

The definition that I best liked was from dictionary.com - a continuous systematic narrative of past events relating to a particular people, country, period, person, etc.. 

---

### Procedural Generation 2:29

This subfield of procedural generation is still in its infancy. There's no canonical formulation. The systems that do generate history tend to:
- Model historical entities (people, places, items)
- Model historical events (which modify entity properties)
- Relate events through logic
- Expose events via text generation

---

### Dwarf Fortress 3:27

The canonical example is *Dwarf Fortress* by Tarn and Zach Adams. It simulates individual dwarves and physics deeply to produce emergent history. In Legends Mode, you can read this history.

The system logs “newsworthy” events from a larger pool. These are flagged during simulation—e.g., a dwarf becomes queen, founds a city, is betrayed, and a battle happens. Only the battle might be recorded, even though it's the result of many prior, unrecorded simulated events.

---

### Epitaph 5:21

On the other end of the spectrum is *Epitaph*, an idle game by Max Kreminski. You guide the course of a civilization through coarse-grained events like technological advancements or famines.

Here, the player directs history. Events influence the likelihood of future ones. A weighted table of events gets updated as events occur.

---

### Challenges of Procedural History 6:37

Some challenges we encountered:

- **Immense complexity**: History is a dense network of people, places, and things. Causality is often unclear.
- **History as rhetoric**: Games treat history reductively—as objective. But historical accounts are biased, written by someone in a particular context.
- **Integration**: *Caves of Qud* already had a lot of content. Our system had to fit in with existing lore and gameplay.

---

### Caves of Qud Overview 8:05

*Caves of Qud* is a open world science fantasy roguelike inspired by *Gamma World*. There's a handcrafted narrative, but also systems: - Physics (walls melt, you can dig anywhere)  
 - Factions (60+, with favor systems)  
 - Historical simulation (our focus today)

Tagline: “Chisel through a layer cake of thousand-year-old civilizations.”

---

### Constraints and Aesthetics 9:00

#### History as Artifact

We wanted players to engage with history as filtered through the present: shrines, artifacts, and texts—not as an objective simulation.

#### Resource Constraints

Procedural history is a tertiary system. We didn’t have the time to build a full generative simulation.

#### Voice Consistency

The system had to blend with the existing handcrafted lore and game voice. So we focused history on five mythic rulers: the Sultans.

#### Emergent Narratives

We avoided prescribing arcs. No “hero’s journey” for every Sultan—just emergent, varied stories.

#### Apophenia

Players bring their own interpretations. Our system leans on this pattern-seeking tendency.

#### Archetypal Domains

Each Sultan has a “domain” like Fire, Ice, Might, or Scholarship. Like epithets in epic poetry (e.g., “Wily Odysseus”), these domains characterize them and influence narrative themes.

---

### Generating History 12:53

At game start, we generate:

- Five periods, each centered on a Sultan
- Each period contains about 12 events
- Events generate text snippets—called **Gospels**

---

### Gospels 13:25

Players encounter Gospels:

- Through NPC dialogue  
- In shrine descriptions  
- On painted or engraved objects

Example: A painted pitcher reads:

> "Under a strange and onyx sky, the people of the Hermitage of the Unfamiliar saw an image on the horizon that looked like an atomic clock bathed in onyx. It was Arum. After he came and left, they built a monument to him. Folks called him Clock-in-Onyx."

---

### Player Journal 14:08

As you discover more Gospels, they’re added to your journal in chronological order. Over time, a coherent narrative emerges for each Sultan.

---

### Relics 14:28

Historical relics or locations generated in history become **actual places** in the world. They’re populated by cults worshiping the ancient Sultans. You can visit and interact with them.

---

### Historical Events 15:01

Our model uses:
 - **Entities**: Places, items, Sultans (data buckets with properties)
 - **Events**: Modify those properties  

Examples: sieges, building construction, relic discovery

Each event is a **template** filled in with details at runtime.

---

### How Events Resolve 16:04

Events resolve partly through randomness, partly by checking current entity state.

We:
 1. Initialize Sultan (name, region, domain)
 2. Pick a random event template
 3. Fill in details using Sultan state
 4. Generate a Gospel via grammar
 5. Update Sultan state

Each Sultan gets ~12 events. If they’re still alive at the end, we add a death event—though they can die during their reign.

---

### Causality 20:03

We **don’t** enforce causal chains between events. Events are picked randomly. But the **text** rationalizes them after the fact.

Example Gospel:

> “Acting against the persecution of frogs, O Three Deities led an army to the gates of Upir.”

The system saw the Sultan was friendly with frogs, so it generated a justification (“persecution of frogs”) via a grammar rule.

---

### Narrative Context 21:26

Even though events are disconnected, players weave narratives from them.

Example:
 1. Antix Sipper convinces cats to found a dig site to excavate ancient ice.
 2. Next: She fights to liberate cats with a frosty hammer.

Players infer a deal was made: help me build the site, I’ll fight for you.

But actually, the fight event was chosen randomly. The first event set “friendly with cats” in Sultan state. The second event saw that and used “liberate cats” as a cause.

The hammer name “Frost Kiss Cat’s Friend” reinforces this—because the Sultan’s domain was Ice.

Domains act as thematic glue; state-based grammars tie events into coherent-seeming stories.

---

### Resources 25:33

Resources for designing your own generative history:

- [**Generation of Mythic Biographies in Caves of Qud (PDF)**](Subverting%20Historical%20Cause%20and%20Effect%20-%20Generation%20of%20Mythic%20Biographies%20in%20Caves%20of%20Qud.pdf) – detailed paper
- [**Epitaph Source Code**](https://github.com/mkremins/epitaph) – readable, simple example
- [**Microscope**](https://www.goodreads.com/book/show/12403479-microscope) – tabletop RPG for collaborative history building
- [**Opera Omnia**](https://finalfantasy.fandom.com/wiki/Dissidia_Final_Fantasy_Opera_Omnia) – narrative game exploring subjective history
- [**Kate Compton’s Blog**](https://galaxykate0.tumblr.com/post/139774965871/so-you-want-to-build-a-generator) – overview of generative techniques, including grammars
- [**Procedural Meaning in Voyageur**](https://www.gamedeveloper.com/design/procedural-meaning-pragmatic-procgen-in-voyageur) – Bruno Dias' article on generative storytelling

---

### Summary 27:15

Takeaways for your own procedural history system:

- Consider **narrative context**: Who’s telling the history? What are their biases?
- Break history into **entities and events**
- Design a set of **event templates**
- Choose a **logic for linking events**:
  - Influence future likelihoods (*Epitaph*)
  - Require preconditions
  - Or “cheat” by randomizing and rationalizing later
- Use **replacement grammars** to generate flexible, expressive text

---

### Question 28:59

**Q:** How did you balance how much state you gave to the Sultans?

**A:** State is only set at initialization (name, birthplace, one domain) and during the 12 life events. That’s limited enough that most Sultans only interact with one or two factions. When a Sultan ends up with many allies or spouses, it’s a fun, interesting outlier.

---