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
