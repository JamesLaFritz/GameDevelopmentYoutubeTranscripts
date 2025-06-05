# Procedural meaning: Pragmatic procgen in Voyageur

Voyageur, which I am currently working on, is a literary space exploration RPG with procedural worlds. This blog post talks about its approach to procedurally generating prose, and lessons learned from it that apply to the broader field of procgen.

Voyageur is my project currently in development, a literary space-exploration RPG set in a procedurally-generated universe. It uses mainly prose to communicate with players as they travel from one planet to another, always going forward towards the center of the galaxy. Those planets are procedurally generated, with a prose description reveals some of their features, which determine what kinds of content can be found in the planet.

Voyageur is somewhat different from most games with procedural elements; it's procedurally generating for meaning, not just systemic content. Planet descriptions are glimpses into the game's setting, and they're supposed to be a source of delight for players. They have to be interesting and coherent. Trying to generate content that communicates sophisticated ideas to players isn't exclusive to text generation, but it doesn't have very many antecedents elsewhere. It's possible to imagine, for instance, a roguelike that tries to convey narrative through the levels it generates, but for the most part, roguelikes aren't very interested in narrative. Often the narrative elements are largely fixed and statically wrapped around mechanical elements that are systemic.

This is a pragmatic approach to procedurally generating content with narrative and meaning in mind. It's the one I'm adopting as an independent developer with relatively limited resources.

## How procedural generation works in Voyageur

Voyageur uses what I call a tagged grammar approach. Phrases and words are arranged in a grammar that categorizes their role within the text; such as "statement about a planet" or "type of Earth animal". Those phrases can contain nested references to other phrases: "A [type of person] was arrested today for [a crime]". The text generator works by starting with a very general top-level phrase and picking random phrases that match the grammar.

This is, so far, identical to Kate Compton's Tracery. But what Voyageur does that Tracery doesn't is use tags. Most of the phrases have a set of tags associated with them. The generator uses that metadata to pick phrases that are as appropriate as possible to a model of what is being described. This is done according to several criteria: Phrases are supposed to not contradict the model, but they are also scored more highly if they bring up an aspect of the model that hasn't been mentioned yet, or if they correspond to a randomly-chosen focal point for the description.

Procedural text generation has often been done by AI/machine learning approaches such as markov chains and neural networks. Those approaches can be powerful, but they're also outside the technical capabilities of most developers. A neural network needs a specialized lot of care and feeding, and mobile devices are still not necessarily powerful enough to run them locally.

The tagged grammar approach is a good balance between developer-friendliness and power; it can generate relatively large chunks of text that is narratively coherent and interesting, but it doesn't require a lot of specialized knowledge to use.

Tagged grammars aren't useful just for generating text; any kind of content can be expressed this way. A lot of useful techniques emerge after using this approach for a while.

## Pick the right level of granularity

Ultimately, any procedural generator is going to be assembling content out of elements that were hand-picked or designed by a human. Minecraft doesn't procedurally generate blocks, just the way they're arranged.

Voyageur's text generator works mostly at the sentence level, with many phrases having specific variations within them that ask the generator to dip down into the word level. It is extremely difficult to operate exclusively at the word level without creating word salad; but operating only at the sentence level has very little possibility for delight and surprise.

Too much granularity, and you end up without enough meaning. Too little, and you lose the ability to surprise players. The hybrid approach, where you start from not quite enough granularity but you include underlying levels of variation, works well. Many of the phrases in Voyageur show up relatively often in planet descriptions, but are in themselves variable. An example of how that approach could be adopted in a different kind of game would be a narrative roguelike that assembles levels out of hand-designed rooms, but where the rooms can have individual variations, both in their specific shape and content. So a room might fit the "temple" role and be pulled out of a list of prebuilt temples, but it might have random alcoves or details inserted (such as a ruined wall section).

## Shape your mechanics to be forgiving

Look at Spelunky: You have an uncommonly high jump height for a platformer; you have access to terrain-destroying bombs; you have a fiction and structure where you always enter a level through the top and exit at the bottom. Spelunky is designed to be forgiving of its own procedural level design, relying on delight and surprise rather than the tight flow of a more conventional platformer. In fact, the fragility of the player character and the danger posed by most in-game obstacles all contribute to this. In an a traditional Mario-style platformer, you're usually expecting to traverse the space very fast, carried along on a trail of carefully-placed platforms, items, and enemies. Spelunky is designed so that you want to play methodically and look before you leap. It's therefore easier to verify that the level generator can't make unsolvable levels, and the looser flow of the level designs serves the high-risk mechanics well.

Look at roguelikes more generally: Their entire design is built around creating a rapport with the player where the player accepts unfairness as part of the game. So level generators "accidentally" creating unbeatable mobs of enemies or unfairly placed traps is regarded as a component part of the experience.

Voyageur's perpetually-forward structure is an important part of making the procedural text work well with the game. If players could revisit places, they would very likely end up laying down roots, and expecting a place they spend a lot of time at to reveal itself to them more and more. Generating more and more detail for those players would strain any world model I could reasonably produce. Forcing the player to keep moving forward works with the grain of the procedural text, not against it.

## Favor apophenia

Apophenia is the psychological phenomenon where people attribute order, intentionality, and humanity to random data. Apophenia is your best friend.

The tagged grammar expansion approach I'm using in Voyageur originates with Emily Short's procedural novel, The Annals of the Parrigues, which contains the following passage, regarded as the first "joke" told by the procedural generator:

Once we were served hound in a nut crust; and goat in a nut crust is not without its merits. The mare in a nut crust was less satisfactory.

The generator never set off to come up with a joke, but readers will draw upon what the know about jokes (repetition, rule of threes structure) to understand this as one. Procedural generation works well with implicit storytelling and stylization; leaving room for players to fill in the blanks is important. It works better to supply a limited number of salient details, because the player will implicitly and subconsciously join those details together into coherent meaning, much better than your software could.

Meaning is always created by the reader, viewer, or player. When designing procedural systems, it's really important to create a space for that construction of meaning.

Voyageur's planet descriptions are phrased as collections of impressions, rather than linearly narrated series of events. Implicit, low-bandwidth storytelling has worked successfully in a lot of games (like Dark Souls, which I am obligated to mention); procedural generation plays really well with it. The human mind has a bias towards meaning, so people will rationalize before they decide something is nonsensical. You do have to get past a threshold of consistency and meaningfulness, of course. But being too zealous about making sure things make sense risks creating not enough variation, or creating an expected level of detail and consistency that isn't forgiving enough of the generated content.

## Generate for theme and tone

Games with purely authored content will often have clear contrasts between different sections of their worlds. In something like Dark Souls, enemy placement, environment design, and level design all work in concert to give a very clear impression of what a place is.

Procedural generation, in contrast, can often create worlds that seem ramshackle and thematically incoherent. You can either start from a very narrow set of elements that you know all work together in concert, or you can incorporate thematic considerations into the generator itself. The latter works better for variety, and is often going to be a necessity in bigger games.

This doesn't have to be overly complicated; many roguelikes have themed areas with specified enemy lists, and Spelunky of course has very clearly-defined themed areas like that. In Voyageur, the alignments provide overarching themes. Planets are also tagged according to mood, which correlates roughly with the state of their economy; some planets have a bright and optimistic tone, while others seem darker and more dystopian. Together, they highlight the conflicts and dangers at the heart of each alignment. Being consistent with theme and tone is *extremely* easy, but it carries a lot of weight when creating the suggestion of intelligence behind the generated content.

Combining different thematic axes helps retain variation. Consider a roguelike that generates a dungeon according to its original use, then applies transformations to that dungeon themed after its later occupants. Having those elements be modular would allow for combinations such as "ancient temple turned orc stronghold" or "castle donjon infested with undead."

## Recombination, not branching

Building content out of elements that are meant to be combined freely is key to achieving variation. Planet descriptions in Voyageur are assembled mostly free-form from lists of statements about a planet; those statements are all meant to mostly stand alone, and therefore can be placed in any order in a paragraph and produce a coherent description. The generator isn't just filtering those statements based on whether or not they apply to the planet in question; it's also selecting them based on whether or not they bring up an aspect of the planet that hasn't been mentioned yet, and every planet has a "focus" that biases its description towards highlighting something about it. This means that statements are often called up early to bring up the most visible aspect of the planet first in the description, or later on to fill a hole in the player's understanding of that planet.

The resulting text doesn't have the specific flow that human-authored text can have, in the same way that a Spelunky level doesn't have the carefully built flow of jumping and running that a Mario level has. But, they all carry the possibility for surprise, contrast, and even irony in their construction.

## Meaning in procedurality

Ultimately, procedural content has to stand on its inherent possibilities and allure; it can't be treated as a cheaper way of generating voluminous amounts of content that is indistinguishable from human-authored content. Because the truth is, unless your game is very simplistic, you will never be able to generate content that is indistinguishable from human-authored content, barring actual strong AI that is probably more valuable doing something other than generating content for a video game.

This is particularly true when procedurally generating for meaning. There are approaches that can approximate human-generated content quite well in making things like platformer levels or rules text on Magic cards. But with narrative content, the fact that it is procedural has to be a core identity of the text, something that in itself is interesting to players. This gets into airier, more experimental territory; but at this stage, I believe that the future for procedural generation lies not in better and better imitation of human aesthetics, but in developing a machine aesthetic that can be appreciated by human audiences for what it is, and not as a weak substitute for authored content.