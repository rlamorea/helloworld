---
layout: post
title:  "My Journey: The Commodore 64"
date:   2024-12-04 11:21:23 -0800
categories: my-journey
tags: retro-computing, commodore, commodore-64, nostalgia
codestyle: c64
---

#### [My Journey](../../../../series/my-journey.html): Part 1

<span class="eimg">
![The Commodore 64](https://github.com/user-attachments/assets/d0e70b77-8c71-4b46-b9b9-c8f7f2f7b6fa)
[Evan-Amos](https://commons.wikimedia.org/w/index.php?curid=17414881){:target="_blank"}
</span>

My first computer was, like so many of my generation, a Commodore 64.  I got it for Christmas in 1983, 
about a year and a half after it came out.  I also got a small black and white TV for a monitor.

I can remember getting it hooked up to the TV and turning it on.

<span class="eimg">
![The Commodore 64 start screen in monochromatic glory](https://github.com/user-attachments/assets/30dcc55b-6d94-4876-92b2-04c7e32e1d24)
A whole new world awaited...albeit not a colorful one
</span>

With no storage device, and the concept of keeping a computer “always on” decades in the future, 
there was nothing to it but to write some BASIC programs and watch them run, then watch them 
vanish when the power switch went OFF.

At the time, it seemed like everybody knew a little BASIC.  At least everybody I knew.

```basic
10 PRINT "HELLO"
20 GOTO 10
```

If you knew nothing else, you probably knew how this worked.  One day I was walking through SEARS, 
past the Commodore 64 display, likely ogling the 
[Commodore 1702 Color Monitor](http://www.jax184.com/projects/1702/1702.html).  The screen was at a 
`READY.` prompt, someone had killed whatever program was running.  Assuming it would probably start 
up the classic 
[Commodore Christmas Demo](https://www.youtube.com/watch?v=TYJl1EzBs_4&ab_channel=MajorThriftwood), 
I typed `RUN`.

What I got was:

<span class="eimg">
![Commodore 64 screen with Eric White repeating across it](https://github.com/user-attachments/assets/b03ced2f-5fd7-47c5-b072-a61c7c0bf667)
</span>

Eric White was my friend from down the road.  He’d been in the store sometime earlier and decided 
to “tag” the computer in the nerdiest way possible.  He would eventually buy my Commodore 64 from 
me when I upgraded to a Commodore 128.  It was one of the more random coincidences I’ve encountered.

The beauty of BASIC was that, at its most simple, it was easy to comprehend how to tell the computer 
to do something.  The numbered lines gave an easy and obvious point of reference.  `PRINT` did what 
it said, and `GOTO` _line_number_ was self-evident.  I can think of few other languages where a 
“HELLO WORLD” program could be written by anyone without really needing that person to understand 
any programming language theory.

In fact, because in most cases, BASIC was a “live interpreter” (meaning you could execute statements 
directly without needing to RUN a program), “HELLO WORLD” in BASIC is really just this:

```basic
PRINT "HELLO WORLD"
```
What nuance is there to explain here to a novice?

 1. `PRINT` tells the computer you want to print something on the screen.
 1. You need to put what you want to print in quotes.

That’s it.  Two simple ideas, and someone has written their first program.  Add in line numbers and 
`GOTO` and, if your name happens to be Eric White, you can “hack” every computer in town.

Or at least, you could.  Almost all of those 80’s era home computers booted up to some form of BASIC. 
In 2006 the science fiction author and “futurist” [David Brin](https://en.wikipedia.org/wiki/David_Brin) 
wrote an article titled ["Why Johnny Can’t Code."](https://www.salon.com/2006/09/14/basic_2/) 
In it, he argues:

> ...math textbooks all featured little type-it-in-yourself programs at the end of each chapter -- 
> alongside the problem sets -- offering the student a chance to try out some simple algorithm on 
> a computer. Usually, it's an equation or iterative process illustrating the principle that the 
> chapter discussed. These "TRY IT IN BASIC" exercises often take just a dozen or so lines of text. 
> The aim is both to illustrate the chapter's topic (e.g. statistics) and to offer a little taste of 
> programming.

The ubiquity of BASIC on these relatively cheap home computers (most of which were also available in 
every school library) meant, if you were so inclined, you could “TRY IT.”   If you are of my 
generation and didn’t learn at least some of the fundamentals of programming by typing in BASIC 
programs like these, I’d be very surprised.

When I think of it, although I remember seeing them in my textbooks, it is unlikely I ever did type 
them in. Why not? Well, for one thing, they were pretty boring programs. I could tell, because just 
looking at the code I could, effectively, `RUN` them in my head. No point in typing them in.

Going back to that Christmas morning in 1983, all there was to do was type in some BASIC.  My brother, 
completing his masters in robotics, showed me some simple programs to write. And there was the 
[Commodore 64 User’s Guide](https://www.lemon64.com/manual/), 
a typical manual for the time — extensive and detailed.

<span class="eimg sml">
![The Commodore 64 User's Guide Cover](https://github.com/user-attachments/assets/b48a568b-afee-43ec-8acb-591987c822ec)
This is a better read than any owner's manual for a modern laptop by a HUGE margin!
</span>

It is telling that everything in this “User’s Guide” from page 22 onwards is about programming the 
computer. Chapters 2 and 3 are all about BASIC, and the rest goes into some really advanced stuff.  
It was as if the whole point of owning a home computer was to program it to do things. 
How naive was _that_ thinking?

A few weeks later I got a [Commodore Datasette](https://en.wikipedia.org/wiki/Commodore_Datasette) - 
a cassette drive that provided permanent storage. Although there was a market for programs delivered 
on cassette (particularly in Europe, where I wasn’t), I don’t think I ever had a third-party cassette. 
The Datasette was for my programs, a place to save my work so it didn’t vanish with the flick of a 
switch.  It was the point at which I went from playing with BASIC to _programming_.

<span class="eimg">
![Commodore Datasette](https://github.com/user-attachments/assets/04a12952-2f50-4ad7-a9e1-53753ab24b87)
[Evan-Amos](https://commons.wikimedia.org/w/index.php?curid=56287647){:target="_blank"}
So, so, slow. But the media was cheap.
</span>

The Commodore 64 itself was a bit of a tease. For its day, particularly at its price range, it had 
quite good graphics capability, and ridiculously good sound synthesis. The pairing of the VICII 
(graphics) and SID (sound) chips made the C-64 one hell of a games machine, pretty much putting its 
peers to shame — and most dedicated game consoles as well.

The problem was that none of that was accessible through Commodore BASIC. None at all. Want to change 
the background color from its lighter gray on darker gray (remember, I had a black and white TV) 
default?  Here you go:

```basic
POKE 53280,11 : POKE 53281,0 : POKE 646,1
```

Obvious, right?  What the heck is that about?

`POKE` was a way to push a value to some place in memory.  And with the Commodore 64, you needed to 
really understand all the special places in memory.  You had to understand the underlying machine in 
a fundamental way.  Unlike its contemporaries, whose native BASIC let you draw in (often inferior) 
“high resolution” graphics modes and play (vastly inferior) sounds, the Commodore 64 made you really 
work for it.

For what it’s worth, address 53280 (or $D020) is the address where the VICII looks up what color to 
render the border. Address 53281 ($D021) is where it looks for the color to render the background. 
Address 646 ($0286) is where the C-64 stores the current cursor color (meaning all subsequent 
characters will be rendered in that color). The last one is not entirely necessary. You could just 
hold the Control (CTRL) key and 2 simultaneously to get white text. You could even capture that 
CTRL-2 sequence into a quoted string and `PRINT` it to change the color (_what convenience!_).

Those other chapters in the User’s Guide? How to `POKE` your way to cool graphics and sound. 
You’d better be ready to understand binary and bitwise math. You were operating at the hardware 
level every step of the way.  It was a lot for a 12-year old to take in, and I’m sure I didn’t 
understand most of it at the time. But understand or not, I typed in those programs and picked 
things up through osmosis.

The convenience of a late-May birthday meant that I could get major upgrades to my Commodore in a 
pretty regular twice-yearly cadence. So a [1541 floppy disk drive](https://en.wikipedia.org/wiki/Commodore_1541), 
that sought-after color monitor, and a succession of printers rounded out my setup.

<span class="eimg">
![A Complete Commodore 64 Setup](https://github.com/user-attachments/assets/279e5a68-0865-4145-8cad-9cc2a897e055)
(Not my actual setup)
</span>

Of course I built a library of a great deal of software, both purchased and, um, _not_ purchased. 
The Commodore 64 was an amazing games machine, and had some pretty useful utilities and productivity 
software.  And there were programs from books and magazines.  Yes, magazines! Full of programs you 
would have to type in ever so carefully (although many had checksum utilities to reduce the chance 
of mistakes).

<span class="eimg dbl">
![RUN Magazine](https://github.com/user-attachments/assets/bc0b9f6e-4050-4c49-b178-a0cb47eebd17)
![Compute!'s Gazette Magazine](https://github.com/user-attachments/assets/1231870b-1be0-49b7-94e6-bc02e39383d3)
</span>

Typing in those programs, at least the ones that weren’t just an endless list of `DATA` statements, 
was an education in BASIC as good as any formal class.  Perhaps better.  You saw new ways to do 
things, to use the language and its features.

But I also loved writing my own programs, making my computer do what I wanted it to. The BASIC on my 
Commodore 64 was my gateway into making that happen and set me (and so many others) onto a course for 
a career and lifetime in software development and technology.

If you're a software developer, where did you get your start? Leave a comment below and let me know.
