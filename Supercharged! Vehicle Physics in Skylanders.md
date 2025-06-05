# Supercharged! Vehicle Physics in Skylanders  
**2016 GDC Talk – Jul 27, 2018**  
🎥 [Watch on YouTube](https://www.youtube.com/watch?v=Db1AgGavL8E&t=3527s)

## Summary

In this 2016 GDC talk, Jan Erik Steel and Patrick Donnelly of Vicarious Visions explore how they implemented vehicle physics for *Skylanders: Superchargers*. Covering land, sea, and air handling, the team shares their process of balancing physically accurate simulation with simplicity for designers. The talk dives deep into system architecture, tool interfaces, and the technical challenges of collisions, suspension, and air control.

---

## Key Topics

### 🚗 Physics Simulation Philosophy

- Started with physically-accurate wheel-based simulation using raycast springs and ground forces.
- Pivoted to "character vehicle" approach with animation-driven motion — but found it problematic.
- Eventually merged both ideas: accurate under-the-hood physics with a simplified designer-facing interface.

### ⚙️ Design Pillars

1. **Simplify the physics system when possible.**
2. **Expose parameters the way designers think, not just physics equations.**
3. **Keep tunable values independent.**  
   Changing mass shouldn’t affect resting height, for example.

---

## Land Vehicle Prototyping

- Initial prototype used spring-damper suspension with raycasts.
- Applied engine/brake/side forces at each wheel’s contact point.
- Resulted in realistic drift, chassis lean, and responsive feel.
- Problem: collisions produced rotational chaos, suspension tuning was fragile, and unintuitive for designers.

### ❌ Attempted Alternative: Animation-Based Driving

- Used state machines to control vehicle velocity and orientation.
- Designers liked the familiarity of character tools.
- Issues:
  - Glitchy contact with uneven terrain.
  - Poor interaction with external forces (e.g. collisions).
  - Effectively built a custom physics system—duplicated effort.

### ✅ Final Approach: Physics Core + Designer Controls

- Reintroduced physical simulation.
- Removed forces that created instability (e.g. wheel-applied torque).
- Applied engine/braking forces at center of mass.
- Defined motion using **velocity curves** rather than raw forces.

---

## 🎛️ Velocity Curves

- Designers provided curves defining time-to-speed behavior.
- Reverse-evaluated the curve each frame using current speed to find target acceleration.
- Applied acceleration via forces, not by setting velocity directly.
- Allowed consistent behavior and responsiveness to environmental changes.

---

## 🛞 Suspension System

- All vehicles used **4-wheel suspension**, regardless of visuals (treads, wings, etc.).
- Used **shape casts** (not raycasts) to detect ground and handle uneven surfaces.
- Connected suspension laterally across the chassis for "Flintstone car" behavior, improving edge-case stability.

### 🧮 Substepping Suspension

- Euler integration caused jitter at low framerates.
- Introduced sub-stepping (e.g. 200Hz) to stabilize spring behavior.
- Extended raycasts by projected velocity to improve air-to-ground transition.
- Solved landing instability.

---

## 🌊 Water Vehicle Buoyancy

- Simplified volume to a box.
- Applied a single upward buoyant force at the center of mass.
- Used surface normal to tilt vehicle.
- **Nesting height** exposed as a single tunable 0–1 parameter.
- Calculated derived values (mass, volume) automatically to keep height independent.

---

## 🎨 Artist-Driven Suspension Animation

- Animators created full suspension ranges.
- System interpolated between top and bottom visual poses.
- Enabled stylized suspension (e.g. angled or squashed wheels) without breaking physics.

---

## 💥 Collision Rule System

- **Data-driven rule system** filtered collisions based on context (speed, angle, vehicle type).
- Two-phase system:
  - **Phase 1**: Modify or ignore collisions before they are resolved.
  - **Phase 2**: Apply effects (damage, sound, bounce) after physics resolves.
- Scope-based:
  - Global, per game mode, per vehicle type, per entity.
- Rules were **bidirectional-aware**: handled “vehicle hits crate” or “crate hits vehicle.”

### Example: Breaking Destructibles

- Rule: If vehicle hits destructible with ≥20% max speed
  - Ignore vehicle collision.
  - Apply damage and force to destructible.
- Scoped only to destructibles; did not affect global behavior.

---

## 🧠 Complexity Management

- Complexity centralized in engineering.
- Designers used intuitive sliders, toggles, and velocity curves.
- Engineers debugged rules using visual tools.
- Independent parameters reduced unintended side effects.

---

## ✈️ Predictive Landing System

- Wanted all vehicles to **land wheels-first** after jumps.
- At airtime:
  - Predicted landing position using a convex hull and trajectory cast.
  - Compared current angular momentum to required orientation.
  - Computed correction torque and applied a rotation.
- Simplified by faking rotation along a single axis, hiding the transition visually.

---

## Team Structure & Production

- ~3 dedicated engineers (land/air/sea) at peak.
- 20 vehicles shipped (10 land, ~5 sea, ~5 air).
- Patrick Donnelly supported physics core as needed.

---

## Closing Advice

- Don’t shy away from physical simulation—**it can be controlled**.
- Define your **design pillars** early and stick to them.
- Focus on stability, clarity, and cross-discipline collaboration.

---

_“Guide the complexity into engineering, and expose the fun to designers.”_  
— Patrick Donnelly

