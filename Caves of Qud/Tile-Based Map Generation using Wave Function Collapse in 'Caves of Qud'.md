# Tile-Based Map Generation using Wave Function Collapse in 'Caves of Qud'

**GDC Talk – Apr 11, 2022**  

[![Watch on YouTube](https://img.youtube.com/vi/AdCgi9E90jw/0.jpg)](https://www.youtube.com/watch?v=AdCgi9E90jw)

## Summary

In this 2022 GDC talk, Freehold Games’ Brian Bucklew shares how *Caves of Qud* uses Wave Function Collapse (WFC) to procedurally generate tile-based maps with a wide variety of results from small training images. He walks through intuitive examples, explains the algorithm's mechanics, discusses its limitations (homogeneity and overfitting), and presents practical solutions for applying WFC in real-world game development.

---

## Transcript

### Introduction 0:00

[Music]  
Why are we here?

*Caves of Qud* is a game that I develop.  
I'm Brian Bucklew. I'm a co-founder at Freehold Games.

*Caves of Qud* has a ton of procedural generation.  
We use a bunch of normal techniques that you've heard of—Perlin noise, etc.—  
and we use a few novel techniques. Today we're going to talk about a specific one: Wave Function Collapse,  
which sounds scary, but I hope by the end you'll realize it's not particularly terrifying.

We're going to first start by looking at some final results that we get by utilizing this technique,  
and then we'll go through some slides to talk about how we get there.

---

### Map Overview 0:41

Here's a map in *Caves of Qud*.  
If you're not familiar with it, it's a tile-based game.

Each of the maps is 80 by 25 and it's very discrete.  
You can see walls and creatures and plants here, and they're represented in a sort of abstract way.

Here we have a sort of rectilinear palace.  
It has sort of an outer wall, inner buildings, and they're populated by various things—blood splatters, centipedes, and gardens.

Here we have an underground map where we have some very narrow corridors, goalies full of water, little rooms full of water—this is very sort of mazy, narrow map.

Here we have a circular tower embedded in some rock salt.  
It's filled with bears I guess, asphalt, and a little statuary on the right.  
It's got sort of curvy, labyrinthine halls.

Here we have a little town.  
We've got maybe a big ruined civic structure up north and in the south we have a bunch of rectilinear village buildings adjacent, laid out along a nice forested path.

---

### Wave Function Collapse 1:46

So these are a wide variety of results, but the result of a single algorithm.  
These each had a very different character—you had little mazy corridors, you had a fairly dispersed village with well-ordered buildings, you had water-filled goalies.

And instead of using an individual technique to generate each of these, we use a powerful new texture synthesis technique called Wave Function Collapse, alongside a few other tools that we use to supplement its weaknesses when applied to maps.

So Wave Function Collapse was developed by a Russian mathematician named Maxim Gumin, released open source, MIT licensed.  
The repository's out here—it’s in 20 different languages. You can go out there and download it now and use it.  
Hopefully after this talk you’ll be able to go out there and build it into your projects.

*Caves of Qud* was its first commercial use. A bunch of others quickly followed because it's such a powerful technique.

---

### Tile-Based Map Generation 2:39

Wave Function Collapse has two primary modes:
 - It operates on tiles.
 - It operates on textures.

If you do procedural generation, you’ve probably encountered Wang tile maps, which are based on edge-colored adjacencies.  
The tile map generation is sort of similar to that, but the adjacency constraints are much more powerful than edge coloring.  
You can get a much more powerful result.

So if you’re interested in tile-based map design, this is great—we’re not going to talk about it.

Instead, we use texture mode.

Texture mode uses small training inputs—they can be big, but it’s most powerful when they’re small.  
Maybe 8x8 or 16x16 training images.  
And they can produce arbitrarily large outputs with the character of that input training image.

This is pretty amazing.

On the left you have three separate inputs.

On the top, you see a sort of rectilinear room/hall and you can see seven sample outputs here—just bigger.  
You can see that each of them has a pretty different layout but shares structurally the same character as the input.

On the second row you see some caves.  
These are caves that you would typically generate via a cellular automata system or something, or box filters—something very different from that first rectilinear room/hall arrangement.

And then third, you see an input texture that’s 5x5—simple couple dots in a line—and you get some very complex quilted patterns.  
This would be some custom line drawing technique.

So instead of having to have three very different algorithms, you’re able to have one single algorithm that’s fed different, very simple training inputs.  
This is something you can pop open MS Paint, write any training input, and throw it through the algorithm.

---

### Algorithm Overview 4:20

Very cool.  
So I'm going to run quickly through this text slide.  
Don't feel like you have to load this into your brain.  
We're going to walk through it more carefully.  
I just want to prime your system for what we're going to talk about.

I'm actually not going to go way down to the nuts and bolts of this algorithm—there are good papers to do that.  
I want to give you an intuitive understanding of how this algorithm is making decisions when it's painting its output.

1. What it does is it chops it up into NxN tiles. N is something you pick.  
Three is a very typical size. It's going to operate on individual 3x3 tiles.  
Chop up your input image into those. It's going to operate on those as its basic atom.  
You can choose bigger or smaller atoms depending on the character you want out of your output.

2. We set up the output as a superposition of all those possible tiles.  
So you can imagine each element of the output being superimposed of each of the tiles that are pulled out of the input.  
And this is the character it shares with quantum wave function collapse—it’s sort of a snide relationship, it’s not anything complicated.

3. It picks one at random to start with and says, "Well, of all these possible tiles, we're going to select the lowest entropy area and pick one at random and collapse it down to a single tile."

4. It's got some new information:  
"I know that this tile exists. These adjacencies are removed from the list."  
And then it continues until every output has only a single selection.  
And that's pretty much it.

If you didn’t catch that—we’ll walk through that in a little more detail here.

---

### Identify all the NxN patterns 5:48

So the first thing it does is it identifies all the NxN patterns.

The example here I'm going to use is a very simple three-color training input.  
It's got red on the outside, a green box, and a blue inside.

So it's going to chop it up into all these 3x3 patterns.  
I'm showing you five here, but there's obviously a bunch in a 9x9 box.  
Without wrapping, it's going to be like 49.  
If you allow wrapping, it's like, what, 81.  
If you allow symmetries and rotations, it's much more.

---

### Identify tiles that overlap 6:14

Then it goes through and for each tile, it figures out where do the other tiles overlap.  
And it's going to use this information in order to do the propagation.

Here I'm just showing an example: you've got two tiles.  
You can see that the first tile on the left—it overlaps with the two tiles below it sharing those six red pixels.

---

### Example Output 6:33

So this is an example output of a WFC run.  
Let’s unpack what we're looking at here a little bit.

On the very left, you see the training input.  
What you're seeing is the first four steps of the WFC collapse.

In the very first frame, the white outline box is the 3x3 tile it's decided to collapse.  
Then it steps through one 3x3 section at a time and selects an output tile that correctly overlaps the existing tiles that it knows about.  
And it steps through the image until all of them are collapsed from the existing input data.

So to give you a real idea what's going on:  
If you look at the area in the box, it's very clearly green-blue.  
That's a single non-superimposed result.

The rest of the blending pixels is a visualization of the output tiles that remain available for that area, superimposed on one another.  
So pixels that have sort of a green-blue box—they can still be either green or blue depending on what tiles remain to be chosen.

---

### Intuition 7:33

So let's look very specifically at this first step and try to build an intuition for how it makes these decisions.

Let's look at this strip of three pixels here.  
Unlike the pixels to the left, you can see these blue pixels are a little green,  
which means they still have some possibility to become green pixels.

So why is this?

Well, we look to the left of this pattern and we see to the left of this pattern in the input is a strip of blue-blue-green.

And where can we find this in the input pattern?  
We can essentially find it in these three places.

Each of these magenta boxes is what the output column we're thinking about might be.

In this case, in the most leftmost case, you can see left of that is blue-blue-green.  
And the second case—left of that is blue-blue-green.  
And the third case—left of that is blue-blue-green.

So these could all slot in there.

Two of these outputs—that column is blue-blue-green.  
And the third possible output—the column is green-green-green.

So we can see—here we go—  
if we look at two blue-blue-green inputs and one green-green-green input,  
you can see that the probability is two blues and one green for each of those top two pixels,  
and it's always going to be green in the output.

So you can see that though we've selected that initial 3x3 box,  
we know quite a bit about what possible adjacencies exist based on the input image.

And so again, looking at this—you should have a better idea sort of intuitively what it's doing.  
Each of these white boxes is selecting for sure what these pixels are.  
In each case, it knows a little bit more about the possibilities that remain,  
and it simply sort of flood-fills around the area to make it work.

---

### Training Inputs 9:15

So let's watch this work—it's fun.

We have five training inputs on the left:
 - A little white box  
 - A little red box  
 - A sort of more complex temple  

You can watch in real time as it decides—usually from the edges—what the possibilities are,  
and then folds in those overlapping potentials until we've got a final image.

And you can obviously run this many times and get a different output each time.

Pretty fun.

So you can get big complex outputs from little inputs.

---

### First Experiment 10:00

So that gives you an idea of how the technique works.

Again—you don't have to understand it.  
When I started experimenting with it, I had no clue what was going on.  
I'm sort of a hands-on learner, so I just tried to give it input and see what happened.

This was my first experiment in texture mode in respect to building tile maps.  
I wanted to build structured output.

In this case I was like, "Well, can I make it build little square buildings for a village? That would be neat."

I gave it a black box on a white background.

And I did not get a bunch of little square buildings.

It turns out that's because—  
what is the image telling it?  
It's telling it basically, on the order of 3x3 pixels, that a line can turn left, or a line can turn right, or a line can go straight.

You'll notice that there’s no overlaps, but there’s also no training in that image that tells that there's an inside and outside of the square.  
There's no spatial relationship between those turns.

And so—you get the output there.

I said, “Well, what if I add a third color?”

So now I've got white, and I've got black, and I've got red inside.

This suddenly produces the kind of spatial coherence that I want across the map.  
You've got little red-filled boxes—that's awesome.

Because not only do you get the spatial coherence,  
but you also get some structural hinting.

The red pixels tell me: "Hey, that's the inside of a place."  
And when you're building a map, that's a pretty interesting distinction to be able to pull out of a real simple texture.

"Okay, well, something I can put furniture inside and plants outside."

And that looks like already a coherent village with almost nothing going on.  
We've got a three-color texture going into it.

---

### Additional Training Examples 11:00

Here's another example. As I get more comfortable playing with it:  
"Hey, what if I feed a bigger training image? Do I get noise or do I get something interesting?"

I get something interesting. That's cool.

Another example: I said, "Well, can I produce sort of naturalistic gardens?"  
Sure! I mean, these are like eight-second experiments in MS Paint.  
I'm not spending 20 hours learning to do high-level math.

So maybe I'm in the wrong lecture also.

Here's an introductory shape—and keep this in mind because we're going to return to the star shape.  
I added an additional color here and you can see you get star-like shapes.

Not identical stars, but some variation—and that's pretty interesting.

---

### Code 12:11

How to use this in your code—probably the most interesting slide for people.

It’s not complicated. It’s three lines of code.

You initialize the model.  
You tell it to run.  
And you get the output image.

And that’s it.

Let’s take a look at the inputs, because sometimes the complexity is hidden in the parameterization.  
For WFC, it's not.

There’s not even any complexity there.

You give it:
 - the training image
 - the value of N (i.e., 2 or 3 or 4)
 - how big do you want the output to be
 - do you want the input to wrap across edges
 - do you want the output to wrap across edges
 - and do you want it to sample with symmetry

Symmetry is a little bit interesting—not very complicated.

If you’re, for instance, putting in something where orientation matters (e.g., ground at the bottom, sky at the top),  
you probably don’t want symmetry—unless you want to end up with sky at the bottom.

If you’ve got buildings where you don’t care about the orientation,  
you probably want to crank that symmetry up to 8,  
where you’ve got full reflection and rotational symmetry.

---

### Problems 13:04

So, applied to map design—these are not problems with the algorithm per se,  
but domain-specific problems.

What problems did we run into when we tried to use this?

---

#### Problem 1: Homogeneity 13:13

Problem one was homogeneity.

The outputs are, as you get bigger and bigger, just completely homogeneous in all directions.

They’re interesting at the small scale, but you don’t really want to walk across an infinite homogeneous plane of these buildings.

---

#### Problem 2: Overfitting 13:26

Problem two is overfitting.

And so—we’ll return to the star shape.

One of the things you want to do in maps is have more interesting details than walls.  
You want to have:
 - doors  
 - where the monsters are  
 - where the furniture is, etc. etc.

And you might think, “Well, let me add more colors to the WFC input.”  
And that, of course, was the first thing I tried.

The reality is: as soon as you start adding more and more colors, fewer of these tiles tend to overlap.  
And so you tend to get very non-varied outputs because the tiles can fit together only in one particular way.  
And so instead, you just reflect and rotate the shape across space.

---

### Homogeneity Solution 14:05

The solution to homogeneity was very simple.

We segmented the output space and then used WFC to fill in details.

In this case—this very simple example—we’ve segmented this map into three high-level chunks,  
and each of them is filled with a WFC input with different character.

So this produces a high-level graph that’s more interesting than a homogeneous output.  
And then the interiors—that interior detailing, which can be quite difficult to get with standard procedural techniques—  
can be highly varied because you can pick from a big library of 500 templates you made in MS Paint  
and get some really interesting, complex output.

---

### Overfitting Solution 14:41

How do we deal with overfitting?

The solution to overfitting was pretty simple:  
Use the output of WFC to generate high-level wall structure  
and then use a post-processing pass to generate the more detailed things like:
 - doors  
 - furniture  

You can imagine a bunch of ways to do this.

I'm going to show you one of the full run-throughs that we did to get a level in *Caves of Qud*.

---

### Segmentation Example 15:04

So here’s a real simple example.

First we pick some segmentation.  
In this case, we’re going to pick a big circle in the middle of the map.

---

### WFC Example 15:11

We’re then going to run a WFC template to fill the whole map up.  
In this case, we’re just going to run a set of buildings.

We’re going to cookie-cutter that circle out, and we are left with some walls.

You can see that this has created some disjoint rooms.  
So what we do is we simply segment this map,  
we figure out which are the disconnected spaces,  
and then we A* from segment to segment.

Anywhere we pop through a wall—we’re going to place a door.

So you can see in this case: we’re A*ing here.  
You’ve got a little red cross where the door is punched.

And we do it again.  
And again.  
And again.  
And again.

---

### Final Output 15:51

And once that’s done, we are able to build a whole map which looks pretty complicated.  
But you can see now that there’s very little going on.

We’ve done a top-level segmentation,  
we’ve filled in the details with WFC,  
and we’ve punched some doors with a pretty simple A*,  
and the output is pretty complex.

So let’s revisit the initial maps.

---

### Additional Training Examples 11:00

Here's another example. As I get more comfortable playing with it:  
"Hey, what if I feed a bigger training image? Do I get noise or do I get something interesting?"

I get something interesting. That's cool.

Another example: I said, "Well, can I produce sort of naturalistic gardens?"  
Sure! I mean, these are like eight-second experiments in MS Paint.  
I'm not spending 20 hours learning to do high-level math.

So maybe I'm in the wrong lecture also.

Here's an introductory shape—and keep this in mind because we're going to return to the star shape.  
I added an additional color here and you can see you get star-like shapes.

Not identical stars, but some variation—and that's pretty interesting.

---

### Code 12:11

How to use this in your code—probably the most interesting slide for people.

It’s not complicated. It’s three lines of code.

You initialize the model.  
You tell it to run.  
And you get the output image.

And that’s it.

Let’s take a look at the inputs, because sometimes the complexity is hidden in the parameterization.  
For WFC, it's not.

There’s not even any complexity there.

You give it:
- the training image
- the value of N (i.e., 2 or 3 or 4)
- how big do you want the output to be
- do you want the input to wrap across edges
- do you want the output to wrap across edges
- and do you want it to sample with symmetry

Symmetry is a little bit interesting—not very complicated.

If you’re, for instance, putting in something where orientation matters (e.g., ground at the bottom, sky at the top),  
you probably don’t want symmetry—unless you want to end up with sky at the bottom.

If you’ve got buildings where you don’t care about the orientation,  
you probably want to crank that symmetry up to 8,  
where you’ve got full reflection and rotational symmetry.

---

### Problems 13:04

So, applied to map design—these are not problems with the algorithm per se,  
but domain-specific problems.

What problems did we run into when we tried to use this?

---

#### Problem 1: Homogeneity 13:13

Problem one was homogeneity.

The outputs are, as you get bigger and bigger, just completely homogeneous in all directions.

They’re interesting at the small scale, but you don’t really want to walk across an infinite homogeneous plane of these buildings.

---

#### Problem 2: Overfitting 13:26

Problem two is overfitting.

And so—we’ll return to the star shape.

One of the things you want to do in maps is have more interesting details than walls.  
You want to have:
- doors  
- where the monsters are  
- where the furniture is, etc. etc.

And you might think, “Well, let me add more colors to the WFC input.”  
And that, of course, was the first thing I tried.

The reality is: as soon as you start adding more and more colors, fewer of these tiles tend to overlap.  
And so you tend to get very non-varied outputs because the tiles can fit together only in one particular way.  
And so instead, you just reflect and rotate the shape across space.

---

### Homogeneity Solution 14:05

The solution to homogeneity was very simple.

We segmented the output space and then used WFC to fill in details.

In this case—this very simple example—we’ve segmented this map into three high-level chunks,  
and each of them is filled with a WFC input with different character.

So this produces a high-level graph that’s more interesting than a homogeneous output.  
And then the interiors—that interior detailing, which can be quite difficult to get with standard procedural techniques—  
can be highly varied because you can pick from a big library of 500 templates you made in MS Paint  
and get some really interesting, complex output.

---

### Overfitting Solution 14:41

How do we deal with overfitting?

The solution to overfitting was pretty simple:  
Use the output of WFC to generate high-level wall structure  
and then use a post-processing pass to generate the more detailed things like:
- doors  
- furniture  

You can imagine a bunch of ways to do this.

I'm going to show you one of the full run-throughs that we did to get a level in *Caves of Qud*.

---

### Segmentation Example 15:04

So here’s a real simple example.

First we pick some segmentation.  
In this case, we’re going to pick a big circle in the middle of the map.

---

### WFC Example 15:11

We’re then going to run a WFC template to fill the whole map up.  
In this case, we’re just going to run a set of buildings.

We’re going to cookie-cutter that circle out, and we are left with some walls.

You can see that this has created some disjoint rooms.  
So what we do is we simply segment this map,  
we figure out which are the disconnected spaces,  
and then we A* from segment to segment.

Anywhere we pop through a wall—we’re going to place a door.

So you can see in this case: we’re A*ing here.  
You’ve got a little red cross where the door is punched.

And we do it again.  
And again.  
And again.  
And again.

---

### Final Output 15:51

And once that’s done, we are able to build a whole map which looks pretty complicated.  
But you can see now that there’s very little going on.

We’ve done a top-level segmentation,  
we’ve filled in the details with WFC,  
and we’ve punched some doors with a pretty simple A*,  
and the output is pretty complex.

So let’s revisit the initial maps.

---

### Map Review 16:11

So let’s revisit the initial maps, which looked complex before.  
But let’s think about how we did these.  

These are each accomplished through the exact same technique.

In this case, we’ve got a couple square areas that were filled with rectilinear WFC output—that’s it.  
You get this cool-looking palace.

Here we have a full map that’s been allowed to run.  
We had a tiny little corridor WFC—it’s maybe 8×8.  
We let it just run out across the whole map.  
Then we flood-fill in some of the edges with dirt.  
So this looks like a buried set of goalies.

---

### Burial Fill 16:40

Here we’ve just got a circle and a square output that have been filled with real simple labyrinthine WFC.

---

### Square Fill 16:47

And here we have a big square at the top that’s been filled with WFC output,  
and a big square at the bottom which has been filled with WFC output, and that’s it.  
And you get this cool little village, right?

Pretty simple.

---

### Additional Reading 17:00

[![A Brief Introduction to Wave Function Collapse](https://imgur.com/h6nVCmW)](https://www.youtube.com/watch?v=pcZQILKxo_M&t=449s)

There are some really good papers in the wild.

Isaac Karth has written a good one called  
[“WFC is Constraint Solving in the Wild”](https://isaackarth.com/papers/wfc_is_constraint_solving_in_the_wild/).

If you want to go really deep down into how the algorithm works, you can go grab that.

The [WFC repository](https://github.com/mxgmn/WaveFunctionCollapse) is a really excellent resource.  
Unlike a lot of repositories, it’s really excellently documented.  
It’s got source code in 30 different languages, because as you can see,  
it’s a super easy technique and a super powerful one, and so it’s very easy to plug into your project and go.

---

### Questions 17:28

And that is it. I’m Brian Bucklew. That was WFC.

[Applause]

---

**Q:**  
17:55  
So how did you deal with communicating the algorithm to people who are more used to traditional level design tools,  
or more traditional procedural generation approaches,  
which tend to work on like room chunks or things like that,  
where there’s discrete combat spaces or discrete spaces for different gameplay styles?

**A:**  
Yeah, the question is how would I communicate that to them—or how did you...

**Q:**  
Yeah, how did working with design people from that background work out for you?

**A:**  
Yeah. Well, our team is—  
So the question is, how did it work out working with people who are more interested in discrete level design?

And the answer is:  
We have none of those people on the team.

[Laughter]

How did it go with you?

**Q:**  
Well, I mean, this was a big challenge for *CounterSpy*, because we ended up doing room-by-room chunking  
because that was easy to build out spaces and have combat AI work in it, but yeah...

**A:**  
If I can take that question to an adjacent space:  
I think there’s a tendency to want to have a little bit more controllability from a designer’s perspective.

These—in *Caves of Qud*—we let our generative systems run really wild,  
and it’s part of the aesthetic of the game, and we can lean into it.

And you can see, we do things like—we just smash outputs on top of each other.  
But because our game is about ruins that have been smashed out on top of each other, that’s great.

I think that when you get into the design space where you want more control,  
the tendency is to move more toward prefabs, which designers have control over.  
So you want to lean into the tile-based techniques in order to fit those prefabs together,  
and then allow the designers to design spaces that can work within a particular bound.

So, within this prefab, this area can be filled in—this knuckle...

**Q:**  
So if I’m following you, you’re suggesting maybe when you’re doing the spatial partitioning step,  
you can do some of that WFC step to do the kind of broad strokes and then chunk down prefabs into the appropriate size spaces?

**A:**  
Yeah, I would actually say probably the tile-based portion of WFC—which I didn’t talk about—  
is probably more interesting for those teams.  
That gets a little bit more controllability to that output.

**Q:**  
Thank you.

---

**Q:**  
20:05  
Did you try building any constraints into the Wave Function Collapse algorithm itself,  
like no repeating tiles in this region or anything like that?

Because I’ve experimented with that with some other implementations of this and ran into quite a few problems.

**A:**  
Yeah, the question was: Did we build any additional constraints on top of WFC?

The answer is no—because I tried it and I ran into a bunch of problems.

Max is a cool guy and very knowledgeable—obviously, since he created it.

And if you have a specific question about constraint building, I would actually hit him up on Twitter and ask,  
because he’s helpful.

But I didn’t have time to push past those problems in this case,  
and we were able to get really good output without any kind of complex constraint additions.

**Q:**  
I would say there’s a great C# library on GitHub by a guy—“Boris the Brave” or whatever.  
I think it’s linked within that repository—works great inside Unity.

**A:**  
Yeah, awesome.

---

**Q:**  
21:00  
Great talk!  
I had a question: Did you ever try having a more detailed training set  
to be able to have more permutations of different adjacent materials?

**A:**  
Yeah, it’s really tricky.  
Because the reality is that those bigger training sets—like the temple—  
really, you can reduce those quite a bit and still get similar output.

Those bigger sets, unless they’re very complicated, tend to just start repeating the same patterns.

And so a good training set—because as your training set gets bigger,  
the memory impact and the CPU impact of the collapse gets bigger—  
it tends to be best just to try to figure out exactly what the character is  
and pack it into as small a space as possible for actual production.

**Q:**  
So I meant—sort of—when you had multiple colors, you got the repeating patterns, right?

So by having maybe a bigger training set, maybe you could have more permutations of colors adjacent to each other  
to sort of still be able to get the detail, but...

**A:**  
Oh! Yeah—could you work around overfitting by having a bunch of them with different colors?

Yeah, I think you probably could.  
I haven’t really experimented with it.  
But I mean, that would certainly be an effective use of it.  
I think that’s something to experiment with.

Any other questions?

[Silence]

Okay, well, thank you.

[Applause]

---

# 🛠️ Recommended Tools & Libraries

## 📦 Official Repository (Reference Implementation)

- **[Max Gumin’s Wave Function Collapse (GitHub)](https://github.com/mxgmn/WaveFunctionCollapse)**  
  - Language: C#  
  - License: MIT  
  - Includes both **overlapping (texture)** and **simple tiled (tile)** modes  
  - Well-documented with image examples  
  - Excellent foundation for learning or porting to other languages

---

## 🧰 C# Libraries (Great for Unity)

- **[Unity-WFC by BorisTheBrave](https://github.com/BorisTheBrave/Unity-WFC)**  
  - Unity-ready WFC implementation  
  - Modular, extensible, supports prefab integration  
  - Good for 3D environments and real-time generation

- **[wfc-unity by nykac](https://github.com/nykac/wfc-unity)**  
  - Lightweight and performance-focused  
  - Designed for 2D maps  
  - Includes sample tilesets and generation workflows

---

## 🔬 Educational & Visualization Tools

- **[WFC Interactive Explorer (Oskar Stålberg)](https://oskarstalberg.com/game/wave/wave.html)**  
  - Try WFC texture mode in-browser  
  - Visualizes constraint solving interactively  
  - Great for hands-on experimentation

- **[Wave Function Collapse is Constraint Solving in the Wild (Isaac Karth)](https://isaackarth.com/papers/wfc_is_constraint_solving_in_the_wild/)**  
  - In-depth analysis of WFC in games  
  - Discusses design patterns and pitfalls  
  - Highly recommended companion to implementation work

---

## 🎨 Sample Tilesets for Training

- **[Kenney’s Free Game Assets](https://kenney.nl/assets)**  
  - Modular tilesets perfect for tile-based WFC  
  - Ideal for level building, towns, dungeons, etc.

- **[RogueBasin](https://roguebasin.com/index.php?title=Main_Page)**  
  - Classic procedural map templates  
  - Use cave, maze, or BSP layouts as WFC training data

---

## 📚 Tutorials & Deep Dives

- **[Red Blob Games – WFC Explanation](https://www.redblobgames.com/articles/constraints/wave-function-collapse.html)**  
  - Interactive diagrams  
  - Explains entropy, collapse, and constraint propagation  
  - Great for beginners and intermediate devs

- **[Sebastian Lague’s YouTube Series on Procedural Generation](https://www.youtube.com/playlist?list=PLFt_AvWsXl0eZgMK_DT5_biRkWXftAOf9)**  
  - Covers foundational procedural systems  
  - Useful for combining WFC with other techniques (e.g., pathfinding, tile logic)

---

## 🧪 Bonus Tools & Workflows

- **Tiled2WFC (conceptual workflow)**  
  - Convert `.tmx` files from Tiled Map Editor into WFC training data  
  - Handy for modular room-based games

- **MS Paint, GIMP, or Photoshop**  
  - Draw your own 8×8 or 16×16 training samples  
  - Use 3–4 colors to encode structure: walls, floors, decorations  
  - Keep inputs simple for best generative variety