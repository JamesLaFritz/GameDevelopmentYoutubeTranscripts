# Dreamscaper: Killer Combat on an Indie Budget  
**GDC 2022 – Aug 26, 2022**  
🎥 [Watch on YouTube](https://www.youtube.com/watch?v=3Omb5exWpd4)

## Summary  
In this GDC session, Afterburner Studios’ Ian Cofino shares the process of creating a satisfying combat system on a small indie team—focusing on the design, feel, and workflow needed to make a great player experience.

---

# Transcript (Cleaned Up)

## Introduction  
Hi, my name is Ian Cofino. I'm one of the co-founders of Afterburner Studios. On *Dreamscaper*, I handled design, animation, VFX, and UI—among the 100 other things you juggle as an indie developer.

Before that, I spent 10 years in games, with a background in graphic design, motion graphics, and film. I’ve shipped AAA, AA, and now a successful indie game.

Back in summer 2018, I left my job in traditional development to start a studio with two friends—Paul Szymbota and Robert Taylor. This was us at DreamHack in 2019. Fast forward three years: we launched *Dreamscaper 1.0* on August 5, 2021, for PC and Switch. It launched on Xbox and Game Pass in 2022.

Since launch:  
- Over 250,000 players  
- 91% positive rating on Steam  
- 1,800+ reviews  

## What is Dreamscaper?  
*Dreamscaper* is an action roguelike with a waking/dreaming gameplay cycle.  
- **By Day:** Live Cassidy’s waking life, explore Red Haven, meet people.  
- **By Night:** Enter a roguelike dream loop, fighting off nightmares.

## Talk Structure  
This talk is structured into three parts:  
1. **Design** – Key elements to focus on when creating a combat system with limited resources.  
2. **Feel** – Best practices for creating visceral and satisfying combat.  
3. **Keys to Success** – Personal lessons for maximizing efficiency on a small team.

---

## Part 1: Design

### Establishing Combat Pillars  
Create **actionable**, **specific**, and **aligned** combat pillars:
- **Purposeful Action** – Actions should have consequences. Tactical play.
- **Improvisational Combat** – Player freedom and flexibility.
- **Tough But Fair** – Responsive controls, balanced enemy behaviors.
- **Dynamic Interactions** – Overlapping systems create unique situations.
- **Strong Feedback** – Satisfying audiovisual and animation cues.

### Player Options as Verbs  
Verbs = Core building blocks (run, dodge, attack). More verbs = more depth, but also more complexity.  
Balance depth vs. cognitive load by targeting your core audience.

Dreamscaper players always had:
- Melee combo  
- Alternative attack (status effect: burn, freeze, etc.)  
- Ranged + 2 magic attacks  

Each weapon retained familiar mechanics but added new twists—keeping depth without increasing base complexity.

### Scope Considerations  
- Ask: Can you realistically support this many verbs/options?  
- We cut **jumping** entirely. It added too much complexity to animation, art, and traversal systems—without directly serving our design pillars.

---

## Camera & Framing  
Camera impacts genre, feel, and scope.

- **Close/Player-controlled:** High fidelity, heavy scope.
- **Far/Fixed (Dreamscaper):** Lower fidelity, easier to manage, more strategic overview.

We used a **fixed far-back camera**, sacrificing impact for clarity and strategic play. To compensate:
- Dynamic framing (pull in during combat clusters).
- Strong visual and audio feedback elsewhere.

---

## Stacking Systems  
Layer systems gradually:
- Adds depth without immediate complexity.
- Easier to test, iterate, and adjust scope.

**Example:**  
- Early systems: item modifiers + status effects.  
- Mid-project: added elemental system (fire, water, ice).  
- Then: rewarded combining elements with crits.  
- Finally: layered passives to boost crits and effects.

Result:  
- Players explored build synergies.  
- High engagement and replayability.  
- Reused systems instead of ballooning content scope.

---

## Enemy Hit States  
Critical, often overlooked. Hit reactions give structure and pacing to combat.

### Common States:  
- **Stun / Stagger** – Temporarily stop enemies; used for priority.  
- **Flyback** – Big impact, resets situation.  
- **Pop-up** – Sets up juggles.  
- **Knockdown** – Emphasizes downward power, quick counter-opportunities.

Each added reactions like:
- Wall collisions deal extra damage.  
- Splash damage from knockdowns.  
- Extra damage from pop-up height.

Despite a tiny team (1 animator, 1 engineer), we prioritized hit reactions—they touched every pillar.

---

## Part 2: Feel

### Responsiveness  
- Input delay hurts feel.  
- Rob cut latency in half on Switch using low-latency frame sync.  
- Parry/dodge tuned per-platform to feel fair.

### Animation  
Apply the **12 Principles of Animation**, especially:
- **Anticipation & Follow-Through** – Adds weight and believability.  
- **Secondary Motion** – Trails, floaty scarf, etc.  
- **Slow In/Out** – More natural movement.  
- **Exaggeration** – Strong, readable poses even at small sizes.

### Aim Assist & Hit Correction  
- Generous aim correction for melee and ranged.  
- **Translation Magnetism:** enemy is subtly moved to line up the hit.  
- **Data-driven hit translation** sets up the enemy for clean follow-up attacks.

### Input Buffering  
- Generous combo chaining windows.  
- Early inputs stored and executed when valid.  
- Prevents dropped inputs and unwanted repeats.

### Hitboxes  
- Generous hitboxes for player; precise for enemies.  
- Separate hitboxes for enemies, breakables, and environment.

---

## Game Feel Enhancements

### Visual & Audio Feedback  
- **Hit flash**: short color flash to show contact.  
- **Hit stop**: freeze attacker and enemy on impact (~115ms).  
- **Procedural Enemy Shake**: root-level shake reinforces physicality.  
- **Camera Shake**: scaled based on action (less when player is hit).  
- **Force Feedback**: gamepad rumbles match attack impact.  
- **Juicy Sound Design**: sound conveys strength and connects viscerally with hits.

---

## Part 3: Keys to Success

### Ruthless Scope Management  
- Don't get too attached.  
- Think from the **player’s perspective**, not the creator’s.  
- Cut anything that doesn’t move you closer to your pillars.

**Example:**  
- Cut a complex "chat minigame" despite weeks of work.  
- Replaced with simpler dialogue + deep meta-progression.

### Iterate Cheaply  
- Early feedback loop was crucial.  
- One tester sped up the whole game by 25%—turned out to be a huge improvement.  
- Test hypotheses fast, even if it breaks things.

### Build on What Exists  
- Use external assets (e.g., controller icons, VFX packs).  
- Use your existing skillset to break into new areas (motion graphics → animation).  
- Repurpose and stylize purchased content.

### Leverage Tools  
- Custom combat animation sequencer using UE4’s cinematic tool.  
- Visual timeline of attacks; preview instantly; no deep coding.

### Know When to Stop  
- Don’t chase 120% when 100% is good enough.  
- Skip animating transitions back to idle—players rarely notice.
  
### Emotional Energy Management  
- Ask yourself: “How much do I really care about this?”  
- Let go of small conflicts to focus on what matters most.  
- Flat teams need to manage emotional resources carefully.

---

## Final Takeaways

- Create **specific pillars** to guide design.  
- Layer systems **gradually** for low-complexity depth.  
- Prioritize **responsiveness** and **feedback**.  
- Cut scope mercilessly.  
- Use existing tools, assets, and knowledge.  
- Protect your energy—don’t burn out over 5% gains.

---

Thank you for attending!  
If you’d like to continue the conversation or connect, reach out through the links below.

👏 *Q&A followed the talk.*
