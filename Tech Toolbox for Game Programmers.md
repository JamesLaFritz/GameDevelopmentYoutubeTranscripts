# Tech Toolbox for Game Programmers

**GDC Talk – Jan 17, 2017**

[![Watch on YouTube](https://img.youtube.com/vi/W_okgL6HJX8/0.jpg)](https://www.youtube.com/watch?v=W_okgL6HJX8)
 
## Summary

Have you ever bumped into another developer and noticed they're using a code snippet or tool that you feel like you could've used a million times? In this collection of ten-minute technical talks, developers Jonatan Van Hove, Omar Cornut, Keir Miron, Evan Todd, Kate Compton and Alan Hazelden take you through one of their tools, solutions, or hacks that could benefit the average indie developer on a very practical level.

---

## Transcript

### Introduction

**Speaker: Host**

[Music] Hello, thanks so much for coming. This is the second Tech Toolbox. For those of you who weren't here last year, what we try to do here is—when you make video games (I hope that's a thing that sounds familiar)—you end up having this recurring problem that is just really tedious. Maybe you write some code, or you make a little tool or a little app that overcomes that problem.

That's the sort of stuff I always find really interesting—post-mortems and other people's video games. How did you do this? I always have to go through this tedious thing, or I never thought about solving a problem that way.

Often, for most games, there never will be a post-mortem. And for most of those tools, they'll never see the light of day. That's why we put this session together. We did an open call for: what's the thing that you made basically for yourself? What interesting problems did you solve, or did you solve them in an interesting way?

So it's basically interesting solutions or interesting problems. We have five speakers today. They get about ten minutes each. The first one is Evan Todd, who is going to talk to us about his system for node-based dialogue trees. Let's give him a hand. [Applause]

---

### Evan Todd – The Poor Man’s Dialogue Tree: DIY Node-Based Dialogue Tools

**Speaker: Evan**

Hello, I'm Evan. I am nobody, and I'm a poor man. This is the poor man's dialogue tree—how to make a dialogue tree if you don't have any money.

I'm here talking about dialogue, but I'm really here to sell you on my development philosophy and to get you to convert to it. It's this very powerful paradigm that actually extends beyond game development into other aspects of life. In fact, it actually saved my parents' marriage. It's that powerful.

I'm just kidding. They're still divorced.

So, I'm going to talk about the "right" way to do things. "I need a dialogue tree. I'll go to the Unity Asset Store. I'll buy an asset and Frankenstein patch it into my project." I'm not a big fan of this because it's expensive, probably third-party, designed to do a lot of things for a lot of people. It's not exactly what you need. And crucially, it's boring. You're letting other people do your job for you. It's fun to make stuff.

The poor man's way is to write your own—to get 80% of the solution for 20% of the work. And you get exactly what you need.

So, what I needed was an editor that I could use to create a node-based system to hook dialogue nodes together. The "right" way would be to write it in the same language as your game—make a really nice Windows Forms application with all this stuff. It would be performant, it would have the same codebase, and everything. But that takes a lot of work.

So, I did it the poor man's way: HTML5. Cross-platform, easy to do. Performance? I don't care—it’s just a tool for creating things. It doesn't have to run super fast. JavaScript has so many libraries—some sane. And everyone speaks JSON, so you have a really easy-to-read format coming out of HTML5.

This is what I came up with. I call it Dialoger. There's a library called JointJS that I used for the fancy node-connecting stuff. It's really easy to set up. This is actually running in its own little application—it’s not in a browser. I used something called NW.js for that. It's really simple: just take all your JavaScript stuff, zip it up, rename it to a .nw file, and dump it into the NW.js folder. It turns it into an application.

It's the easiest thing in the world to make a completely fake desktop application that feels like a real thing, but it's totally not.

JointJS is great for making diagrams and graphs. NW.js is great for standalone apps. You get the full Node APIs too—file access, networking, all that.

The whole thing was 700 lines of JavaScript. Very small, easy to write, very extensible. You can add widgets to nodes very easily with HTML.

The way it works is: I have three types of files. There's a `.dl` file—it's JSON and the full 2D layout of all the nodes and properties. Then `.dlz`—same JSON, but just what I need for parsing a dialog tree in the game, optimized. And then I use Excel files for the actual strings.

These are IDs, not actual dialogue. I have an Excel file with all these IDs in one column and actual strings in another column. Why Excel? Because when you're a poor man and you want to translate your game without paying anyone, people offer to translate. You ship your strings in Excel, and people come to you saying, "How do I do that?" It's nice to say, "Just open it and change it. Send me the Excel file."

So TL;DR—do it yourself. It's more fun that way. Sorry this wasn't very technical, but if you're interested in dialog trees, it's on GitHub. There's also an article I wrote about it on my website.

We only have nine minutes. Thank you.

**Speaker: Host**

Thanks, Evan.

---

### Kiera – Automating Data Implementation Using IDs in Darkest Dungeon

**Speaker: Host**

Next up we have Kiera, who worked on Darkest Dungeon, and he's going to talk about how they did the data implementation for that game.

**Speaker: Kiera**

Hi, my name is Kiera. I'm a programmer on Darkest Dungeon. I'll be talking about automating data implementation with IDs.

Over this talk, I'll cover syntax, generating and referencing IDs, using the file system hierarchy to automate data linking, connecting code data to file data, and automatically generating data.

Darkest Dungeon is a turn-based RPG about dealing with stress. A lot of the content is procedural. It’s on many platforms. Our team was mostly two programmers. It’s written in C++11 with a custom engine and middleware.

I’ll refer to classes—things that don’t change at runtime. For example, the Jester is a hero class. He has 35 max HP, a skill called "Inspiring Tune," and a ranged attack "Battle Ballad."

We used string-format IDs. Instead of using traditional formatting, I’ll use bold and square bracket syntax for this talk. 

IDs are great for validation messages: missing audio, skills, or effect strings. They’re also useful for generating file paths—if a new ID comes in, a new path gets generated.

When we added a resistance ID (heroes or monsters have 8 types), we automatically generated paths, icons, values, and localization names. 

We grouped files into folders by ID—trinkets in the trinkets folder, gems in the gem folder. We avoided manually assigning icons.

We split data into multiple files per hero—art and info files—edited by different people. IDs linked these files together.

IDs also helped share logic. Two different trinkets could reference the same buff class. If that buff changes, both trinkets reflect it.

Colors were defined as shared ID references. Changing a base color updates all references (e.g., game over screen colors).

Parsing a "trinket" class also generated shared "item" class data. We used the file system to auto-parse folders into hero or monster IDs using regex.

We linked code data to file data using macros. An enum declared at the top would generate string IDs and keep them in sync. 

For options, each enum value had a string ID and associated data. This helped with saving/loading JSON and analytics. New options were automatically added to save files and analytics.

Thanks to Red Hook Studios, our sound, art, and programming team. My Twitter and email are listed, and I’ll post examples of the source on my site.

**Speaker: Host**

Thanks, Kiera. I always find it interesting when people do something counterintuitive—like using file structures for logic. It's cool that it was the right solution for your project.

---

### Kate Compton – Tracery: A Simple Yet Powerful Procedural Text Generation Language

**Speaker: Host**

Next up we have Kate Compton, who wrote a tool called Tracery to generate text—like orcish insults. Give it up for Kate!

**Speaker: Kate**

Hi everyone. I'm Kate Compton, GalaxyKate on all the important things. You might know me from the planet slums or SimCity fire systems. I went back to grad school and now I'm at UC Santa Cruz's Expressive Intelligence Studio, where we use AI to make art and weirdness—and sometimes games.

I'm getting a PhD in PCG. Go slugs!

Tracery started as a little text generation toy. It accidentally turned into a language. Then Twitter bots happened.

On the left is a minimal Tracery grammar in JavaScript. On the right is a generator spitting out bad game pitches.

Tracery uses JSON grammar objects. Input your rules, add modifiers, and generate content. It uses `#hashtag#` syntax for grammar expansion. It works recursively and supports modifiers (pluralization, a/an, etc.), and you can reuse generated text.

This dress generator was written in 40 minutes in response to the dress controversy. Hit a button—it prints endless dress descriptions.

People found Tracery really easy. Someone made Cheap Bots Done Quick, which lets you log in with Twitter, paste a grammar, and run a bot.

Now we have hundreds of bots: hipster cocktail generators, compliment bots, Mexican literary theory bots.

Emojis? They're text too—so we got emoji bots.

Then someone used Tracery to generate SVGs. Turns out HTML parses SVG as graphics. Now Tracery makes graphics. Updated Tracery enabled graphic bots: tartan, circles, ships, landscapes.

In games? It’s used in JavaScript, Ruby, Python, Twine, even Unity. My friend Krank used it in games: surreal greetings, dinner conversations.

It also works for procedural hipster restaurant menus. Generate kale ice cream and kale soda. A boring concept becomes infinitely replayable.

This one is a dumb dating sim using a finite state machine dressed up with Tracery-generated romance titles like “playboy cybernetic cutishes.”

You can generate spaceship names, appearances, and characters. I’ve experimented with music (ABC notation), code generation (JavaScript as text), and even evolving Tracery-generated programs.

TL;DR: I didn’t know what Tracery could do until others showed me. Thanks to everyone who helped evolve it.

Grab a zine, check out Tracery.io. It’s free and open source. I didn’t have a timer. Hope I didn’t go over. Thank you—happy accidents!

---

### Alan Hazelden – Using PuzzleScript for Level Design in “A Good Snowman Is Hard to Build”

**Speaker: Host**

Next up we have Alan Hazelden, creator of *A Good Snowman Is Hard to Build*, talking about using PuzzleScript for level design. Give it up for Alan!

**Speaker: Alan**

Hi there. I'm Alan Hazelden. I made Sokobond and *A Good Snowman Is Hard to Build*—both tricky puzzle games.

Today I'm not talking about puzzle design, but tools.

Most games need a level editor. External editors require reimporting into the game. Built-in editors are faster but harder to build, and might leak into game logic.

We flipped this: we embedded the game in the level editor using PuzzleScript.

PuzzleScript was made by Stephen Lavelle. It’s for 2D tile-based games. It’s web-based, requires no programming, and super accessible.

*A Good Snowman* has a monster pushing snowballs to build snowmen. Only valid snowmen have three balls: large, medium, small. Push snow over snow to grow it. Hug your snowmen—obviously.

PuzzleScript games are a single text file. Our game’s logic fits in 250 lines. Graphics are 5×5 pixel grids—defined in text.

You can edit levels in the browser, instantly test them, and export the level string back into your game.

We pasted new levels into our game and reloaded—done. We made levels in PuzzleScript, tested them live, and imported them into Unity.

I prototyped the game in one day using PuzzleScript.

I also made other games with it: *You’re Pulling My Legs*, *Fabulously*, *Braining*, *Skipping Stones to Lonely Homes*—all prototypes or complete games.

Try it! It’s free online. I’m Alan Hazelden (Twitter: @Draknek). Thank you!

**Speaker: Host**

Thanks, Alan.

---

### Omar Makani – Gear: A Simple Immediate Mode GUI Library for Game Tooling and Debugging

**Speaker: Host**

Last but not least, Omar is going to show us the tool he made to make more tools. Let's hear it for Omar!

**Speaker: Omar**

[Applause] [Music] Hello, I’m Omar Makani. Today I want to inspire you to make your own tools and share the hidden benefits.

At Double Fine, we built many tools from scratch. I helped create a GUI library called Gear (or ImGui)—a simple, portable C++ library for real-time tools.

Most tools focus on content authoring. But we need tools for system creation, debugging, and understanding game state.

Games are interactive stacks—systems layered on systems. Interactive tools help everyone understand what’s going on.

Gear makes UI creation trivial. It’s lightweight, function-based, and always available. You can hit F1 and tweak game properties live. Tools are embedded in your game. They’re not separate editors.

It’s immediate-mode. Call one function, a widget appears. Stop calling it, the widget vanishes. No setup, no layout files—just live code.

You can plot stats, debug variables, edit structures, profile performance—live. All tools are function-based and low-cost to write.

Tools are communication. They expose state and logic to designers and artists. They help people understand the black box of your game.

They’re also documentation. Instead of writing docs, expose real-time data.

Tools help teams share a common vocabulary—everyone sees the same internal state.

Keep it simple. It doesn’t have to be pretty. It just needs to help. Small tools, made easily, make your work and your game better.

No excuse not to start. Learn from others. Gear is one example. You can use it or make your own.

Examples: a 20-line search tool, texture viewer, profiler, tag browser, debug inspectors, etc. Tearaway used tools like these for debugging. You can tailor tools to your needs.

Even the free LÖVE engine uses this library. Don’t be afraid to make your own engine either—you’ll learn a lot.

Game innovation thrives on custom tools. Thanks for listening.

---
