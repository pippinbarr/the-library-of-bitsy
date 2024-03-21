# Process journal

## Beginning -- Thursday, 21 March 2024

Just started thinking about this one again today, I've thought about it before.

Here's the Things entry from a while back when I thought of it for the first time:

> Bitsy: Everything / The Bitsy of Babel 00 can you generate Bitsy dynamically?

> Could attempt to use colour cycling to avoid having to have separate rooms based on color?

Here is the Things entry I wrote when I re-thought of this while writing about v r 5:

> The Library of Bitsy

> Is this possible? It would generate a single screen Bitsy which could involve any combination at all of pixels, tiles, etc.
< Could also include links actually I think that would work
< I mean so long as there are limits on everything and they can be represented by numbers/symbols that I can decode into generating the map then… theoretically? Also colliding or not…
> Would have to see…
> I like it as an idea.

Here is what I wrote into a *Typora* document while I tried to understand if I should try to make it:

> > Bitsy
>
> Avatar is 8x8 1 bit = 64 pixels = 2^64 images (right) = 1.844674407e19
>
> That's a pretty big number. They can also be any color represented by a hex color, which is a colorspace of 16^6 which is...16,777,216 colors.
>
> The largest INT in JavaScript is 9007199254740991 which means you can only get up to e15 which means you can't actually capture even the basic pixel space of the avatar (or tiles). Well not in a single number.
>
> But you could encode it in different ways and generate that encoding right?
>
> Like you could spell out the avatar as a string of 64 digits and just randomly generate that string and thus have access to the total space. And if you prefix it/postfix it with a generated color then you're all good to generate it accurately.
>
> So randomly generating a specific Bitsy screen and also encoding it as a string ought to be possible by just not addressing the combinatorial SIZE of it.
>
> Can you similarly reliably generate the same links for a specific generated room? Yes you can encode them too of course.
>
> But at some point what you're doing it just encoding the data for a Bitsy room no? And if you can potentially relevantly have a new tile for every space on the room for instance then that gives you 16x16 = 256 individual tiles, plus as many items and sprites as would fit per tile (including none).
>
> What is the longst possible string? What is the longest possible URL string? Looks like a URL can be at most **2,083 characters** for compatibility.
>
> It's also possible that you simple don't generate these as URL parameters but rather than a page that takes you into the space I suppose at random. Can you bookmark a room? I suppose you can... a string can be **2^53 - 1 characters** which is pretty big. Probably enough to encode a single possibility space for a Bitsy level? Is it?
>
> Lest we forget, Bitsy ALREADY encodes all this stuff, so could one just piggyback on that and fit its encoding system into a string and just "slap it in" when going to a room?
>
> It's still the case though here that you wouldn't be able to encode links? Or... well a link has a name in Bitsy but the name doesn't encode the room it goes to only the reference to the data, ...
>
> Would the library include broken Bitsy levels that link to non-existent other rooms? It doesn't *seem* right... but if were to try to generate the data such that every link is valid you would run into the situation of an borderline infinite amount of data that you couldn't store. So... no it can't happen that way...
>
> But if you can't predictably generate a single space, knowing where it links then you don't have a stable representation of the possibility space, just the ability to do a random crawl of the bitsy-space... which isn't the same as a library...
>
> Unless you disallow linking in favour of an external navigation that finds you "adjacent" rooms (though that doesn't make sense because the space is more dimensional than that would allow for).
>
> Almost every room would look like static.
>
> I shouldn't make this game.
>
> Well I shouldn't make it unless it's traversable in some meaningful way and... it kind of cannot be because the nature of the possibility space is that the vast majority of stuff will be non-navigable static... which is... well, it's what you get when you don't have design.
>
> Which is also why design is worthwhile and interesting and now I'm like maybe I *should* make it. FUCK.
>
> So what it should probably be in just a random level generator down to the level of tiles and so on. And we just see what we see.
>
> And maybe we just jettison the idea of multiple rooms. There are just no links outward. Just singular rooms. Even though of course that's axing a key concept. But also you could have rooms but they just lead you to a different randomly generated space?
>
> Can I intervene at the level of wherever it is that bitsy loads data and just generate into that space so there's no need to spell out the data? A hacked Bitsy that spits out random rooms and ever door leads to another random room and you will probably never find your way back (well, essentially certainly).
>
> Okay maybe I'm convinced to at least try.

So then I am starting to try by making a repository first. And in doing so I am writing this journal entry. And my next step is to eyeball the output of Bitsy and to see where in fact I can intervene.

This project (assuming it even lasts) I hope to commit a screenshot with every commit that is somehow reflective of that commit.

## Initial Code Review -- Thursday, 21 March 2024

The next logical step (so obvious I don't think I need to think in terms of to-dos just yet) is to take a look at Bitsy's code to understand where the data goes in and thus where I could reasonable intervene in generating a room. I *can* generate the basic string version, and maybe that's ultimately the easiest, but I could conceivably intervene later in the process if that turns out to make sense as well.

Even as I write these words my brain keeps going "what about" over and over, so we'll see. It's the kind of project that may in fact be kind of impossible/unmanageable, but for now let's just look at the code.

One nice thing is that Bitsy exports as a single HTML file that includes the Bitsy engine bit right there.

There's a script tag

```html
<script type="text/bitsyGameData" id="exportedGameData">
...
</script>
```

that contains the game data. At least in the default version (I exported exactly what you get when you start a new proejct) it contains representations of:

- Version stuff
  - Includes some stuff like ROOM_FORMAT that worry me but I think I just stick hard to defaults as much as I can? Maybe I don't address something like this though I suppose I should find out what it is?
- The palette
  - Very small amount of data here because the game only has three colors
  - It has a *name* but that's not exposed to a player so I wouldn't look at generating/worrying about names here
- Room(s)
  - Here I see ROOM 0 followed by a grid mimicking the layout of 16x16 tiles with tile characters per space that refer into the tileset (including I assume 0 for no tile)
  - And some naming stuff below
  - Includes a selectable TUNE! This seems novel to me. Do I want to try to cope with tunes? It's pretty funny to think about it so probably the answer is yeah. More noise.
- Tiles
  - Named tiles and their layout in 8x8 with references to their palette (since all tiles/sprites/etc. are 1-bit drawing from the main palette which has background/tile/sprite colors)
- Sprites
  - Same thing for sprites
  - Includes a position which confusingly has a 0 and then what I assume is an x,y into the room, so I'll have to learn what that first digit means.
  - Sprites also have other possibilities like dialog. DIA.
  - And... BLIP? What is BLIP?
- Items
  - Classic image
  - a NAME
  - a DLG (with a number after it)
- - an optional BLIP...
- Dialog
  - Listed as DLG then an integer
  - Which is how the dialog is linked
  - Where would I make the decision on the presence of dialog?
  - Is dialog always singular?
  - Has a NAME
- Variables
  - Oh fuck
  - That's a whooooole other kettle of fucking fish I hadn't thought about
  - at all...
- Tunes
  - They look pretty intelligible. Enough to generate them on the fly and hit the possibility space
- Blips
  - Ah, here it blip
  - Looks like it's a mini soundeffect
  - Notes, an envelope, a beat, other stuff...
  - I mean... fuck... this is getting pretty overwhelming, but it's worth remembering we only see one at a time and they will all be totally fucked anyway

Well shit... that was a lot more complex than I thought. Bitsy has been changing while I've been away from it.

The variable might be the most scary bit because it seems to be suggest some underlying code stuff I would LOVE not to engage with. This default level also does not have multiple rooms in it so doesn't show the idea of linking outward at all. Is that a good thing? Should I pursue that I just place a single exit per level

Each time I allow myself to think about the whole space I feel like this isn't worth doing... that I am just going to be generating fields on noise and that that is not worthwhile. But then sometimes I snap into focus on a single experience and think... well that might be of interest, to see the noise-field of Bitsy specifically. I don't know *for sure* what it would look like or how it would feel to try to navigate...

And again just this whole thing of visualizing the absence of design is fun to me a bit.

I looked at this: [https://ayolland.itch.io/trevor/devlog/29520/bitsy-variables-a-tutorial](https://ayolland.itch.io/trevor/devlog/29520/bitsy-variables-a-tutorial) to understand conditionals better. It seems like it really only affects dialog and... I don't know how important it is to represent this in the possibility space, even though it *is* part of the possibility space for sure. I could leave it out without feeling like it was a harrowing and terrible exception.

Having looked at the basic data (sans doors which I should think about) the question starts to be... where in the code does it use that data and would it be easier to intervene there instead of out at the level of human-legible data? And... well also just what would *that* even mean. ESPECIALLY since data refers to itself... like you can't have a tile in your room that doesn't exist in the tileset. So do you generate the tileset first? But then how many tiles should you generate? I suppose a random number up to the maximum? And then the room generator just uses an arbitrary number of them so that you end up with redundancy? Probably.

There's a bunch of nice input stuff that I specifically *don't* need to interface with.

There is an audio system I might need to look at pending TUNE and BLIP stuff. But hopefully NOT.

There is a graphics system. Similarly, will I need to look at this or only generate the stuff that goes into it?

There is a logging system. I will also not investigate unless I have to.

Some global variables about tile size and map size and stuff.

And we get this:

```javascript
function loadGame(canvas, gameData, defaultFontData) {
 bitsyLog("load!", "system");
 // initialize bitsy system
 bitsy._attachCanvas(canvas);
 bitsy._write(bitsy._gameDataBlock, gameData);
 bitsy._write(bitsy._fontDataBlock, defaultFontData);
 bitsy._start();
}
```

Which is relevant because gameData is, I think, the stuff in the data script. So this is the moment of data-goes-in. So one intervention is that I generate that data (a string) here and send it in here. The font stuff is kind of... I dunno what to think about the font stuff. Another thing I don't touch?

Then we get to the BitsySystem:

```javascript
function BitsySystem(name) {
```

Lots of funny very human commenting in here

```javascript
// too hacky???
  if (self._injectPreLoop) {
   self._injectPreLoop();
  }
```

Some pretty serious memory allocation code which... I hope I don't run afoul of it.

Far out there's a lot in there. Now I'm down at line 1556 wherein lives the "engine".

There are pieces of information in here that are important for setting ranges on randomization, e.g.:

```javascript
/* TUNE CONSTANTS */
var barLength = 16; // sixteenth notes
var minTuneLength = 1;
var maxTuneLength = 16;
```

This seems rather important structurally:

```javascript
function createWorldData() {
 return {
  room : {},
  tile : {},
  sprite : {},
  item : {},
  dialog : {},
  end : {}, // pre-7.0 ending data for backwards compatibility
  palette : { // start off with a default palette
   "default" : {
    name : "default",
    colors : [[0,0,0],[255,255,255],[255,255,255]]
   }
  },
  variable : {},
  tune : {},
  blip : {},
  versionNumberFromComment : -1, // -1 indicates no version information found
  fontName : defaultFontName,
  textDirection : TextDirection.LeftToRight,
  flags : createDefaultFlags(),
  names : {},
  // source data for all drawings (todo: better name?)
  drawings : {},
 };
}
```

That's a lot to take in and this function is only creating the STRUCTURE for that data, not the thing itself.

Okay this is a juicy area because it also includes functions

```javascript
function createDrawingData(type, id)
function createTuneData(id)
function createTuneBarData()
function createTuneKeyData()
function createBlipData(id)
function createDialogData(id)
```

All of which are helpful in exploring what is possible in these different areas. And then perhaps the most important one:

```javascript
function parseWorld(file)
```

This SEEMS to be the one that goes through the data representation and translates it into the language of the inner systems. So we could imagine replacing it with a `generateWorld()` instead.

It basically hands off to a series of `parseX()` helpers that deal with individual datasets within a file. It treats it literally as a line-by-line file reading process.

As I continue reading I see that the fonts are data based as well and COULD be generated... that would add a strange and hellish layer to language.

... and then suddenly we're out of parse world.

So... is the right thing to make a `generateWorld()` and subsidiary `generateX()` helpers that mimic the parse versions but instead of reading data in, populate every possible element with random data?

And what are the implications, as above, for situations (there are plenty) where different things refer to each other (the map refers to the tiles etc.). Where do you generate first? Where is the redundancy? How do you make sure that at least theoretically "any" Bitsy could emerge?

I leave this up to you, later-me...?
