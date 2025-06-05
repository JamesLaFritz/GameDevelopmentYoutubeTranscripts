# Title

**GDC Talk – Dec 12, 2016**

[![Watch on YouTube](https://img.youtube.com/vi/hG9SzQxaCm8/0.jpg)](https://www.youtube.com/watch?v=hG9SzQxaCm8)
 
## Summary

In this GDC 2016 talk, Kyle Pittman breaks down how to design jumping mechanics in games by starting with intuitive, player-focused parameters like jump height and distance, then deriving physical constants like gravity and initial velocity using basic physics equations. He explores how to shape jump arcs for feel, rather than realism, and explains how different integration methods like Euler and Verlet can model jump trajectories accurately in code. The talk concludes with strategies for building double jumps, variable height jumps, and achieving consistency and uniqueness in jump feel.

---

## Transcript

### Introduction 0:00

Hi I'm Kyle Pitman I'm a graduate of the Guildhall SMU.
I worked at gearbox software for six years and in 2013 my brother and I founded minor-key games and now we do indie development.

---

### Motivation 0:15

> - Avoid hardcoding, guessing in Games.
> - Design jump trajectory on paper.
> - Derive constants to model jump in code.

Today I'm going to be talking about jumping in video games.
The principles I'll be discussing can apply to platformers both 2d and 3d, but they can also apply to first-person shooters, endless runners, or really any sort of game that has a jumping mechanic.

Our goal is going to be to move away from the trial and error that occurs when we start by defining physical constants upfront.

I've had this experience in my own development, maybe some of y'all had a similar experience, where you're working on a new project and you say alright let's start by defining gravity.  
We'll say gravity is 9.8 meters per second squared, real-world physics, that's a good place to start.  
We'll choose a jump velocity relative to that, maybe we'll tweak these numbers a little bit to get something that feels sort of in the ballpark of what we expect our character's jump to look and feel like.

But then maybe we send the build to friends and get some feedback that says:
 - jumping feels floaty
 - jumping feels heavy

Okay, how do we respond to this?  
How do we tune the feel of our game without potentially breaking things—you know—five levels down the road because suddenly your character can't jump high enough to reach a platform or can't jump far enough to clear a gap?

What we would like to do instead is start by describing a jump trajectory in terms that are familiar to us as players and as designers.  
I know that I need to jump this high.  
I know that I need to jump this far.  
From that description, derive the mathematical parameters that we need to model this in code.

---

### Assumptions

We'll start with a few assumptions:
 - We're going to be modeling our player character as a massless, frictionless particle in vacuum—game programmers love those, right?
We're not going to worry about air friction or drag yet, we could bring this in later if we want to. We're going to keep things simple for right now.
 - Collision bounds don't matter—whether you're using a box, sphere, capsule, or whatever—the math is going to be exactly the same.
 - We're going to be representing our player character's state as a position in space, a velocity that they're currently traveling, and a constant downward acceleration due to gravity.

So let's look at gravity. 
> `f''(t) = g`  

Gravity is the one and only external force that's acting on our player character.
It's constant over time, so if we graph this out it's just a flat line, and we can write this as an equation `f''(t) = g`, where `g` is our gravitational constant.

Now from physics we know that acceleration, velocity, and position are all related to each other in that they are derivatives and integrals of each other.  
So if we know acceleration at any time, we can integrate with respect to time to find velocity.  
So we'll go ahead and do that.

---

### Integration 2:49

This gives us a function for velocity:  
`f'(t) = gt + V₀`—we have to add an initial condition any time we integrate—and we call this `V₀`. This is our initial velocity.

When we graph this it's a descending line.
In fact the slope of that line is our gravitational constant `g`.

We can integrate once more with respect to time to find a function for position, we'll do that.

This gives us a function for position:

`f(t) = (1/2)gt² + V₀t + P₀`

We add another initial condition when we integrate `P₀`, and this is our initial position.

Throughout the rest of this talk, I'll be assuming that our initial position is zero—that we're jumping from a ground height of zero—and that term just sort of goes away, but it's important to understand why that's there.

When we graph this one out you can see it sort of has a nice smooth curve to it.  
It's sort of what you would expect to see if you plotted your player character's jump over time.

---

### Projectile motion 3:43

In fact this equation is just the textbook physics 101 projectile motion formula:

`f(t) = (1/2)gt² + V₀t + P₀`

If you were asked to solve a physics problem about:
you know how high in the air is a baseball five seconds after being hit by a batter.
This is probably the equation you would be using.

All that we did to get there is we started by saying gravity is the one thing that affects us and we integrated twice with respect to time.

---

### Parabolas 4:06

Now this equation has another cool property which is that this is a parabola.
Algebraically we can define a parabola as 
`f(x) = ax² + bx + c` 

Substituting these values you can see that our projectile motion formula does fit that description.
 - `x = t`
 - `a = (1/2)g`
 - `b = V₀`
 - `c = P₀`

`f(t) = (1/2)gt² + V₀t + P₀`

What that tells us is that the position and the path our player character follows as they are traveling through the air under the influence of gravity is a parabola.

Necessarily any properties of parabolas that we can identify would also hold true for that path.

---

#### Properties of Parabolas 4:42

let's look at some properties of parabolas next.

 - Every parabola is symmetric about an axis of symmetry that intersects its vertex.
What we would think of as the peak of the jump.

What that tells us is that the duration of time for when we leave the ground to when we reach the peak of a jump is going to be the same as the time from the peak of jump back down to the ground again.

 - Every parabola is geometrically self similar to every other parabola.

What that tells us sort of intuitively is that if we can find a sufficient set of constraints to describe a parabolic jump trajectory.
For instance a vertical axis of symmetry, desired height and distance.
There's only one parabola that could pass through that set of points, that could fulfill those constraints.

 - The the shape of a parabola—whether it's very wide and flat or very tall and narrow—is entirely dependent on the quadratic coefficient, which in our projectile motion formula is `(1/2)g`.
 
Hopefully that intuitively makes sense as well, that this would depend on gravity.  
As gravity gets stronger, we expect to see a steeper, sharper falloff on that curve.  

For reference, the `b` and `c` terms of this parabola would be the initial velocity (what we could think of as the tangent to the jump) and the initial position (which is sort of just a translational offset on that graph).

---

### Design on paper 6:06

Understanding that our players jump trajectory is a parabola and understand some of these properties of parabolas, what we can do next is start designing a jump on paper.

For right now we're going to be talking about standing in place jumping straight up and falling straight down, we aren't jumping sideways through space yet but we'll get there.

For right now our x axis is time.
We'll draw a graph of what we want our players jump trajectory look like.

I'll say we have some height that we want, we'll call this height `h`, maybe you have a platform that's 50 units off the ground, so you want your jump height to get a little bit above that.

Next we need to answer the question how long does it take us to reach that height, we'll call this duration `tₕ`.

Now because we know every parabola is symmetric we know that the time at which we land back on the ground again will be `2tₕ`.

Now from this description what we would like to do is calculate the mathematical properties that we need to model this jump in code.

We need two things:
 - we need to know gravity
 - we need to know our initial jump velocity

---

### Initial velocity 7:10

So we'll start by looking at velocity.

> Solve for `V₀`
> 
> `f'(t) = gt + V₀`

We go back to our graph from earlier and we say at time zero we have some initial velocity `V₀`.
We don't know what that is yet, that's one of the things we want to find.

At time `tₕ` our velocity is zero.
This is the peak of the jump.
This is when gravity has pulled down hard enough that we are momentarily weightless before we fall back to earth.

> `f'(tₕ) = 0`
> 
> `0 = gtₕ + V₀`

Then at time `2tₕ`, this is just as we're landing on the ground again, our velocity is equal in magnitude and opposite in direction to our initial velocity.
So this is `-V₀`.

> `V₀ = -gtₕ`

We'll look at this point in the middle here where time `t`<sub>`h`</sub> and velocity is zero.
We'll substitute these values into our function for velocity and solve for `V`<sub>`0`</sub>.

Skipping to the end there what this gives us is a solution for our initial velocity `V`<sub>`0`</sub> in terms of gravity (`g`) which we don't yet know and the duration to the peak of the jump which we've defined in advance.

---

### Gravity 8:11

So next we need to find a solution for gravity.

> Knowen values:
>  - `V₀ = -gtₕ`
>  - `P₀ = 0`

> Solve for `g`
> 
> `f(t) = (1/2)gt² + V₀t + P₀`

To solve for gravity we'll go to the graph of position that we just drew.
We start at a height of zero at time zero.
At time `tₕ` we have some desired height `h`
> `f(tₕ) = h`

Then at time `2tₕ` we've land on the ground again our height is zero.

From the previous slide we know that we can express our initial velocity as a function of gravity and time.
We'll substitute these values into our equation for position—and again—we'll look at this point in the middle where our height is `h` and our time is `tₕ`
> `h = (1/2)gtₕ² + V₀tₕ + P₀`
> 
> `h = (1/2)gtₕ² + (-gtₕ)tₕ + 0`
> 
> `h = -(1/2)gtₕ²

We'll solve this for g and again sort of skipping through all the math there what this gives us is a solution our gravitational constant `g` in terms of the height and the duration of the jump.
> `g = -2h / tₕ²`

So that's one of the two things we were wanting to find

---

### Back To Initial velocity 9:01

> Solve for `V₀`
> 
> `V₀ = -gtₕ`

Now that we have a solution for gravity we can go back to the previous slide where we wrote initial velocity in terms of gravity and we can substitute this in.
> `g = -2h / tₕ²`
> 
> `V₀ = -(-2h/tₕ²)tₕ`

This gives us a solution for initial velocity also in terms of the height and the duration of the jump.
> `V₀ = 2h/tₕ`

---

### Review 9:18

To review, I know I'm skipping through a little bit of the math here quickly, but if you're taking notes or taking pictures in your cell phone this would be probably one of the slides you'd want to remember.
> `V₀ = 2h/tₕ`
> 
> `g = -2h/tₕ²`

What we've done here is we've described a jump trajectory in terms of the height and in terms of the duration that takes to get us to that height.

From that description we can calculate the gravity and the initial velocity that we need to model this jump accurately in code.

This is cool, this gets us some of the way to our goal.

---

### Time -> Vertical Space 9:48

As long as we're speaking about the height of the jump it would also be useful to speak about the distance of the jump and we can do that but we need to introduce another parameter which is how fast we move side-to-side.

I'll be calling this **laetrile speed** or **foot speed**.
Depending on your game/application this might not be a constant value.
You might have some ramp up and ramp down time in your foot speed.
You might have a different horizontal speed when you're running on the ground versus jumping through the air but for the purposes of this talk whatever the maximum horizontal speed that you could reach as you are moving through the air, that is that's the number we want to use.

It's also important to know here that you need to keep the horizontal and vertical components of your player characters velocity separate.
If you're making a 2d platformer this means keeping your x and y values separate.
If you're making a 3d platformer whatever you're up axis is, whether that's Y or Z, you need to keep that separate from movement in the horizontal plane.

When I say keep those separate what I mean is:
If your player is running really fast sideways and they press the jump button you don't want to kill that horizontal movement and jump straight up.
You want to keep the horizontal going and just add a vertical component to that.

---

### Parameters 11:05

We also have this relationship among three parameters now.
Previously we were speaking about the duration of the jump, the time to reach the peak of the jump `tₕ`

> Time to peak `tₕ`
> 
> Distance to peak `xₕ`
> 
> Foot speed `vₓ`

Now we're going to start talking about the horizontal distance that we cover on the way to the peak of the jump and we're also going to be talking about our foot speed `vₓ`.

You can pick **two** of these, but the third one necessarily has to be a function of the other two.

So for my money I think it makes the most sense to define:
 - the horizontal distance to the peak of the jump, and
 - to find the foot speed
 
Allowing the duration of the jump to be a function of those.  
Just because when we're talking about the duration of a jump, usually we're talking about fractions of a second.  
And that's not necessarily the most intuitive thing to speak about, or the most intuitive thing to design upfront.

---

### Time -> Horizontal Space11:50

So just as before we're going to draw a graph of what we would like our jump trajectory to look like only now we're jumping through space, so our x-axis is distance rather than time 

In fact you could draw this graph over a representative scene from your game, especially if you're making a 2d platformer.
You could say well I have a platform this high or I have a pit of spikes that's this long, I know that I need to jump that high ort hat far to cross those.

We're defining our jump in terms of:
 - the height,
 - the distance horizontally that we cover on the way to the peak of that jump

We call that `xₕ`.
And then we have our lateral foot speed, we call that `vₓ`.

Just as before, we want to find:
 - gravity
 - initial velocity

Because those are the things we need to model this in code.

To do this we're going to take the equations we found previously for those values and make some substitutions.

On the left here we have a substitution for `tₕ`—the duration to the peak of the jump.
We can rewrite this as:
`xₕ` (the horizontal distance to the peak of the jump) divided by `vₓ` (our lateral foot speed)
> `tₕ = xₕ / vₓ`

on the top row we have the equations we found previously for:
 - initial velocity
> `V₀ = (2h * vₓ) / xₕ`
> `V₀ = 2h/tₕ`

 - gravity.
> `g = -2h / tₕ²`

On the bottom row we've substituted that `tₕ` term with the substitution from the side:
 - `V₀ = (2h * vₓ) / xₕ`
 - `g = (-2h * vₓ²) / xₕ²`

So again, to review—this would probably be one of the slides you'd want to get a picture of:
> `V₀ = (2h * vₓ) / xₕ`
> `g = (-2h * vₓ²) / xₕ²`

What we've done is we've started by describing an ideal desire to jump trajectory:
 - in terms of height
 - in terms of distance
 - in terms of how fast our player character moves side to side.

From that description we can calculate gravity and we can calculate initial velocity.
We have everything that we need to model this jump in code.

This is pretty cool.
This gets us a little bit further.
but at this point, we're still talking about very perfect parabolic jumps.
While that might be true to real-world physics, in the case of this massless frictionless particle in a vacuum, it's not necessarily the most fun, interesting or good feeling thing for a video game.

---

### Breaking the rules

What we'd like to do next is start looking for ways that we can break the rules a little bit while still relying on these properties.
Knowing that we can design our jump trusting that we're going to reach a certain height and distance.

To do this we're going to start thinking about our jump trajectory not as a single parabola but as multiple augments of partial parabolas.

To give you a real world example of a game that does this sort of rule breaking.
In the original *Super Mario Brothers* I think this was mentioned in Steve slinks book **game feel**.
Mario runs, jumps and flies through the air.
He reaches the peak of his jump and suddenly gravity triples.
Mario falls like a brick.

Now there's nothing real world physics about a sudden increase in gravity at the peak of a jump, but that treatment is part of what gives Mario his characteristic trademark sense of weight.
Along with a slow ramp up and ramp down time on his sideways movement.
It's what makes him feel different than other videogame characters.
We'd like to look for ways that we can bring those sorts of unique characteristics to our own game.

---

### Fast falling 15:06

Let's look at an example.
We'll take the same example of falling faster beyond the peak of a jump.

We can start by drawing a jump trajectory exactly as we have in the past in terms of height, distance, and lateral foot speed.
That's not shown here, but once we get to the peak of the jump we'll break it there.
We'll draw a different curve on the other side a different part of a diffrent parabola.
One with the same height but a shorter distance.

Now from the materials that we've already covered we have everything that we need to calculate an
initial velocity and a gravity for each part of this trajectory.
We only need the initial velocity for the first part as our character's taking off from the ground
What we do then is we would apply gravity over time for the first half of the curve.
When we reach the peak of the jump in code when the vertical component of our velocity goes from positive to negative, we would switch over and start applying the heavier gravity corresponding to the second half of this trajectory.

When you play this back in real time, you'd see your character follow a stretch-out hand trajectory just like this.
They would fly through the air, have sort of a nice smooth descent, they would reach the peak of their jump, and fall much faster.

---

### Variable height jumping 16:21

We can do a similar thing for variable height jumping.
Variable height jumping tends to be communicated to the player in terms of "hold the button down longer to jump higher".

The way we would think about this in code is:
 - We would treat the maximum height that the player could ever reach as the default.
 - That's where we get our initial velocity from.
 - If the player releases the jump button early—before they’ve reached the peak of the jump—we switch over to a heavier gravity value to reach the peak sooner.

In this graph here where the line goes from blue to red that's the moment where the player has released the jump button.
You can see another outline of blue in the back, that represents sort of that maximum jump height that they could reach if they kept the button held down.
You can see another smaller outline in red and that's a minimum jump height if you were
to just feather the button and off again for a split second.

You would still want to cover some distance so that's where this minimum jump height comes from and that corresponds to the gravity that we use to bring the player character to the peak of their jump sooner.

Now once we reach the peak of the jump we have a few options.
In this graph I show us reverting back to the original gravity, so we have sort of a nice smooth descent on the other side, but you could also remain at a heavier gravity value if the player had released the button sooner.
You could even combine this technique with the one from the previous slide and say regardless of whether the player released the button early, we always switch to a different falling gravity beyond the peak of the jump.

---

### Double jumping 17:55

If you're implementing double jumping or any number of mulit-jumps, you could define each stage of the jump as a separate parabola.
To find these in terms of height and distance again just as we have in the past.
Calculate an initial velocity and a gravity for each stage of this jump.

In this case you would need to know the initial velocity for that second part because you would be treating that just like a jump from a standstill in that case.

---

### Applying in Code

Throughout this talk, I’ve been mentioning that we need two things to model a jump in code:
- We need gravity  
- We need initial velocity

So let’s talk a little bit about how this works.

With **initial velocity**, as soon as the player presses the jump button, we want to apply that to the player character’s current state.  
Again, it’s important to note that you want to decouple your horizontal movement from your vertical.  
You don’t want to kill horizontal movement and just jump straight up—you want to keep horizontal movement going and apply a vertical component to that.

**Gravity** needs to be applied over time. So for that one, we need to talk about integration.  
Generally speaking, integration solves the problem of:
 - We have some previous game state  
 - Then there’s been some passage of time  
 - We want to predict with as much accuracy as possible what the next game state is going to be

In any engine or any framework, you’re going to have the same sort of integration with this passage of time.  
This might be a variable amount of time, it might be constant, it might be sort of obscured a little bit, but that’s always there, and your math does need to account 

---

### Euler 19:25

So the easiest way to do this is with Euler integration.
 - `position += velocity * Δtime`
 - `velocity += acceleration * Δtime`

This is easy, right, but it's not very stable.
In the worst-case scenario, the player might perceive a difference in the way their character handles—for instance, if a lot of stuff is happening on screen and the framerate drops.  
Those are the sorts of inconsistencies that we would like to avoid.

---

### Runge-Kutta (RK4) 19:53

So on the far end of the spectrum we have the Runge-Kutta methods RK4 in particular.
I don't have pseudocode for this one because it tends to be very wordy but I would point to the Gaffer on games article: *Integration Basics* as a really great overview of what this might look like in an actual game development scenario.

However my takeaway from the article and from some of the comments on that article is that:
If all we're doing is dealing with this sort of projectile motion the Runge-Kutta methods are way more complex than what we need.
We can actually find an integration method that is every bit as accurate but much much simpler to write.

---

### Velocity Verlet 20:31

Let's take a look at Velocity Verlet next.
The wave velocity verlet is written you start a similar to Euler integration.
You increment position based on velocity but then we have this term one-half acceleration times the
square of the Delta time.
> `position += velocity * Δtime + 1/2 * acceleration * Δtime²`

Then you calculate a new acceleration value as a function of position.
> `new_acc = f(pos)`

You increment your velocity by an interpolation halfway between the previous and new acceleration value.
> `velocity += 1/2 * (acceleration + new_acc) * Δtime`

Then you commit the change to acceleration
> `acceleration = new_acc`

So this is interesting.
It's definitely you know simpler to write than Runge-Kutta methods, but it's not necessarily applicable to our situation because in our case gravity is mostly constant, except for those few places where we've chosen to break the rules and increase or decrease gravity.

So maybe we can simplify this a little bit further.
What's also interesting to notice here:
We have this 1/2 acceleration times delta time squared term—that sort of recalls our original projectile motion formula.

---

### Assuming constant acceleration 21:31

In fact if we think about in those terms we have this projectile motion formula.
That already answers the question of:
Given any set of initial conditions, for instance the previous game state,
We know with 100% accuracy where our player is going to be at any time in the future.
For instance after this delta time

If we were to simplify the Velocity Verlet, assuming a constant acceleration, this would actually correspond directly to our projection and projectile motion formula
We could trust that we are getting 100% accurate results.

---

### Near-constant acceleration 22:10

What about those times when we do deliberately break the rules a little bit?
We say we reach to the peak of the jump, we increase the gravity, how much error do we accumulate in those cases if we were to just naively integrate this way assuming a constant acceleration?

That error would be on the order of the change in acceleration from
the previous frame to the new one multiplied by the square of the Delta time.
> `Δacceleration * Δtime²`

Now when I ran these numbers on one of my own games this error was about half a pixel.
This isn't something that would accumulate over an entire session and eventually throw things out of whack.
This is a one time offset that gets zeroed out the next time your player character lands on the ground.
I would argue this is a perfectly acceptable amount of error.

If you've been using Euler integration and if you're dealing with projectile motion I would recommend giving this simplified Velocity Verlet integration method to try.
I think you'll be really happy with the results.

---

### The takeaway

To review our goal has been to move away from trial and error.
Move away from defining physical constants upfront and move in a direction where we can describe a jump trajectory in terms are familiar to us as players and as designers.

I know that I want to jump this high and this far.
Then we want to lean on those abilities to craft a unique individually characteristic jump trajectory that can give our particular game its own unique feel.
We still can trust that it's going to feel rooted in the truth of gravity constantly pulling our player character down

---

### Closing Comments 23:45

I'm plug my own game here real quick.
I have a game on Steam.
It's called *You Have to Win the Game*.
It's free—like actually free—not in-app purchases free.
It uses most of the principles I've talked about here today.

If you have a chance to try that, hopefully you'll like the way it feels and be convinced that this is a good method to follow.

You can find me on Twitter as well:  
 - [@PirateHearts](https://twitter.com/piratehearts)

My websites are:  
 - [http://minorkeygames.com](http://minorkeygames.com)  
 - [http://gunmetalarcadia.com](http://gunmetalarcadia.com)
 
Other platformers I'm making that uses all these same principles.

But that's it for me. Thank you for your time.

---

### Questions? 24:20

All right, I guess we do have time for some questions.  
So if anyone wants to come up to the mic there…

---

**Q:**  
24:38  
I guess I wanted to ask real quickly—  
I know you briefly mentioned *Super Mario Bros.* as a way of—as an example of good jumping.  
Are there any other games you might mention to have examples of good or bad jumping?

**A:**  
24:48  
I haven’t analyzed others as specifically as that one,  
but I would point to *Super Meat Boy* as a really good example of having a unique, characteristic sort of floatiness.  
But, you know, floatiness necessarily doesn’t always feel good,  
but they managed to make it feel good in that way.  
I think it was a combination of, you know, the way the character moves,  
but also interacts with walls and has—you know—wall jumping and all those sorts of abilities.

---