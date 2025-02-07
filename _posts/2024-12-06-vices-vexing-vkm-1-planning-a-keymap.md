---
layout: post
title:  "Vice's Vexing VKM Part 1: Planning a Keymap"
date:   2024-12-06 22:56:00 -0800
categories: challenge vice-vkm
tags: retro-computing vice emulators commodore
---

A while back I fell into the rabbit hole of YouTube channels dedicated to old computers, 
particularly the early personal computers of the '80s. And particularly 
[/my-journey/2024/12/04/my-journey-1-the-commodore-64.html](my first computer: the Commodore 64). 
Channels like "[The 8-bit Guy](https://www.youtube.com/@The8BitGuy)" 
and "[8-bit Show and Tell](https://www.youtube.com/c/8BitShowAndTell)” sparked nostalgia and 
not a little regret that I'd let my Commodore 128 go to the landfill long ago.

<span class="eimg dbl">
![The 8-Bit Guy](https://github.com/user-attachments/assets/790c8a25-b4cc-412f-9881-e45086a098f7)
![8-Bit Show and Tell](https://github.com/user-attachments/assets/68460131-212f-4f9d-b3a0-d63fe3b735fc)
</span>

Enter [VICE](https://vice-emu.sourceforge.io/), the emulator for the entire range of 8-bit Commodore 
computers. Launching it for the first time was like entering a time warp, throwing me back to my youth. 
A fond memory of that time was of typing in programs from books and magazines. And that was the 
moment VICE started to really annoy me.

<span class="eimg">
![VICE Emulator Logo](https://github.com/user-attachments/assets/871f44fb-a7a9-4643-bd06-20fcfd305906)
</span>

VICE is a brilliant piece of software, but like so many open source projects, it serves the needs 
of its development community.  And if the community doesn't share your interests in what their 
software can do, you aren't going to convince anyone to change for your sake.

Most people remember the Commodore 64 as a games machine, and that is not at all an unfair 
categorization.  But my nostalgia comes from it being the machine where I first learned about 
how to write programs. And to use VICE to relive that time I was going to need to type. On, and 
this turned out to be important, my preferred laptop: a MacBook.

<span class="eimg">
![Commodore 64 Keyboard](https://github.com/user-attachments/assets/881239bf-ae0c-42d8-ae7d-850182768ba9)
![MacBook Keyboard](https://github.com/user-attachments/assets/ab81f191-cb71-4fb4-9a24-93f6e7379420)
How do I make the top keyboard match up to the bottom one?
</span>

The mapping of my Mac keyboard to that of a C64 in VICE was fairly good. But it had some quirks, 
and some keys didn't really work where they were placed, and some keys simply didn't exist at all! 
I spent some time hunting for anyone who might have implemented a virtual on-screen keyboard that 
I could use as a supplement for when my Mac keys failed me.

No luck on that.

In fact, what I found was a [thread on the VICE bug list](https://sourceforge.net/p/vice-emu/feature-requests/294/) 
that basically came down to "we (the developers) don't care about this and have no interest in doing it."

Fair enough. I seriously contemplated if maybe this was something I could write up. But I've got 
enough to do keeping on top of work projects and I had little to no interest in trying to remember 
how to get a C/C++ project going again. If they can be disdainful of someone wanting to use VICE 
for a purpose they don't care about, I can be disdainful of their choice of technology stack.

> THIS IS A JOKE - The VICE Developers are doing an amazing job creating and maintaining a wonderful product!

Not that any of this got me closer to being able to efficiently type on my ersatz Commodore 64.

## Enter the VKM

<span class="eimg">
![VICE Keyboard Settings Configuration](https://github.com/user-attachments/assets/bc0a4f40-03a5-4720-a5b5-91d67780a3fe)
</span>

VICE does have key mapping files, and that seemed like a path to pursue. The VICE keymap (or VKM) 
files are dense and cryptic, and the in-file documentation didn't really make much sense to me on 
first read-through. But it was obvious there was some way to map a modern keyboard to these old 
Commodore keyboards.  I was determined to make a useful mapping, and to make things harder on myself, 
I set myself the following requirements:

 - Keep as much of my "muscle memory" for typing on a MacBook keyboard intact.
 - Support all of the keyboards provided by the various VICE emulators.
 - Make the keymapping for these various keyboards consistent as much as possible.

## My Process

I started by finding pictures of all the Commodore 8-bit computer keyboards.  One thing I didn't 
concern myself about was finding a mapping for every key on the original keyboards.  
The Commodore 128, PET business, and CBM-II keyboards all have a numeric keypad, but I didn't feel 
it necessary to find a place to map that onto a keyboard that had none.

<span class="eimg dbl">
![Commodore PET Original Chicklet Keyboard](https://github.com/user-attachments/assets/f8a35bcd-a2cf-4364-a9ad-6f01f1edbcc0)
![Commodore PET Keyboard](https://github.com/user-attachments/assets/8a9c59e4-8129-497b-a5a9-ca54b158fad2)
![Commodore PET Business Keyboard](https://github.com/user-attachments/assets/e1b9beb3-95d4-4c84-82d9-0b1e43fefc34)
![Commodore VIC20 Keyboard](https://github.com/user-attachments/assets/c716b14f-74f6-4038-89d3-d9846ac522a3)
![Commodore 64 Keyboard](https://github.com/user-attachments/assets/881239bf-ae0c-42d8-ae7d-850182768ba9)
![Commodore 16 Keyboard](https://github.com/user-attachments/assets/d879e085-9da8-46af-ad5c-f1f7ef124434)
![Commodore Plus-4 Keyboard](https://github.com/user-attachments/assets/ac6b5cad-57bc-45b6-907e-861be407be13)
![Commodore 128 Keyboard](https://github.com/user-attachments/assets/acdb8e4a-966a-485c-bd82-1449ae3f1b29)
![CBM-II B128 Keyboard](https://github.com/user-attachments/assets/e3a2776d-6c5d-4441-9bb5-9fb0403cbc04)
<br/>Quite the collection of keyboards! The PET Graphics "Chicklet", PET Graphics, PET Business, VIC20, Commodore 64, Commodore 16, Plus/4, Commodore 128, and CBM-II B128
</span>

My goal for the mapping is all about typing, and duplicate keys don't produce different characters 
on the screen.  Of course this could be a problem if I encounter a program that is looking for a 
certain key by its key scan (like more than a few programs I recall used the keypad as an 8-direction 
controller).  But that is a limitation I can live with in exchange for being able to type in programs 
easily, and with a minimum of rewiring touch-typing honed for over a decade.

## Looking at the Commodore 64 Keyboard

I thought of starting with the keyboard I remembered best (and most wanted to emulate), which was 
the Commodore 128. But it felt like the Commodore 64 was the better baseline. So I drew up a 
mapping onto my MacBook keyboard. I didn't know, or care, how easy it might be to achieve that 
mapping, or even if it was possible.

<span class="eimg">
![Commodore 64 Keyboard with Key Groupings Overlaid](https://github.com/user-attachments/assets/415fd06b-846e-42f4-b01b-fec24e387fbd)
</span>

The Commodore 64 keyboard can be broken down into six groups:

 1. <b style="color:#38761d;">The Core Keyboard</b> - consisting of the letter keys and the space bar.  These keys are a match to the MacBook.
 1. <b style="color:#0000ff;">The Number Keys</b> - which are a near match to the MacBook, but some of the shifted symbols have to be moved around.
 1. <b style="color:#cc0000;">The Symbol Keys</b> - symbols like colon, comma, period, brackets, etc. that are standard on the MacBook, and "oddball" symbols like the left arrow and British pound sign unique to Commodore keyboards.
 1. <b style="color:#ff9900;">The Function Keys</b> - which are a pretty easy match to the MacBook, albeit using the special fn key.
 1. <b style="color:#f1c232;">The Action Keys</b> - the classic RUN STOP and INST DEL keys of the 64 as well as others that show up on other keyboards.
 1. <b style="color:#9900ff;">The "Transforming" Keys</b> - SHIFT, CTRL, and the classic C= (Commodore) key that provide access to alternate characters and key actions.

These are pretty much listed in terms of the difficulty of mapping them.  The first four groups of 
keys have a pretty obvious way to be mapped: if the number/letter/symbol is on the key, map it to 
its C64 equivalent.  For those keys, such as the back-tick (`) that have no equivalent on the C64, 
map them to similarly unique keys there, like the left arrow (←). Likewise, the function keys are 
a straightforward mapping, although the Mac requires pressing the "fn" key simultaneously with the 
function key. Conveniently, the Mac has 12 function keys, more than enough to "flatten" the C64's 
eight (packed into four keys).

<span class="eimg">
![MacBook Keyboard with Key Groupings Overlaid](https://github.com/user-attachments/assets/aa9c212a-b7d3-4dd8-8099-f1216b69ed44)
A Rough Mapping of Commodore Key Groups onto the MacBook Keyboard
</span>

For the action keys, the "delete" key is a perfect mapping for INST/DEL. The cursor movement keys 
map just fine, and the mac's arrow key layout is vastly superior to the old C64. For CLR/HOME, 
why not use some of those unused function keys. I can even "flatten" those, having one key for CLR, 
and one for HOME, and use another function key for RESTORE.

Finally, the "transforming" keys would make sense to map out as SHIFT keys to (obviously) the 
shift keys. SHIFT LOCK is a reasonable map to caps lock. The Mac keyboard has a "control" key for 
CTRL, and that pretty much leaves the "option" key to map to the C= key since the mac's "command" 
key will almost always end up issuing commands or selecting menu entries.

## PETSCII Graphics

A unique feature of Commodore computers and their keyboards was access to a graphic character 
set known as [PETSCII](https://en.wikipedia.org/wiki/PETSCII).  PETSCII graphic characters were 
useful in creating simple (and sometimes not-so-simple) graphics on a text screen grid.  Over the 
years and through various keyboard designs, accessing these extended characters was achieved by 
different means.  On the Commodore 64, most keys had two graphic characters shown on front (not top) 
of the key.  The character on the left was obtained by holding the “Commodore” (C=) key down and 
pressing that key.  The one on the right was obtained by pressing the SHIFT key along with that key.

<span class="eimg vsml">
![Commodore Q Key](https://github.com/user-attachments/assets/3b3b6e51-24ae-4c96-8d5c-48f963316e6d)
</span>

For most keys this can be mapped directly — a “Commodore” Q (whatever ends up mapping to the Commodore 
key) will produce a character like ⊢.  A SHIFT Q will produce a solid circle (⚫).  But some of the 
key mappings aren’t so simple.

On the MacBook, the plus sign (+) is obtained with a shift-equals-sign (=).  The Commodore 64 
keyboard has equals and plus as separate keys.  The plus key has two graphic characters 
(a “fill” checkerboard like somewhat like ▩) and a cross (➕).  One of these will need to be 
relocated.

## Making a Plan

In the end, I was able to make a plan for my mapping.

The <b style="color:#38761d;">Core Keyboard</b> will map one-to-one onto the MacBook, with one 
exception described below.

The <b style="color:#0000ff;">Number Keys</b> would map mostly one-to-one, but some of the 
Commodore 64 keys will map so they match what the mac’s keyboard shows:

| Number Key  | Shifted MacBook Symbol  | Commodore 64 Key        |
|:-----------:|:------------------------|:------------------------|
|      1      | **!** - exclamation     | **SHIFT 1** (unchanged) |
|      2      | **@** - at              | **@** key               |
| 3 | **#** - hash | **SHIFT 3** (unchanged) |
| 4 | **$** - dollar | **SHIFT 4** (unchanged) |
| 5 | **%** - percent | **SHIFT 5** (unchanged) |
| 6 | **^** - caret | **↑** (up arrow) key |
| 7 | **&** - ampersand | **SHIFT 6** |
| 8 | <b>*</b> - asterisk | <b>*</b> (asterisk) key |
| 9 | **(** - left parenthesis | **SHIFT 8** |
| 0 | **)** - right parenthesis | **SHIFT 9** |

The <b style="color:#cc0000;">Symbol Keys</b>, at least those not already mapped to number keys, 
would be shuffled around to match the MacBook keyboard:

| MacBook Key           | Obtained by  | Commodore 64 Key      |
|:----------------------|:------------:|:----------------------|
| **-** (minus)         |              | **-** (minus) key     |
| **_** (underscore)    | shift **-**  | **C= @**              |
| **=** (equals)        |              | **=** (equals) key    |
| **+** (plus)          | shift **=**  | **+** (plus) key      |
| **[** (left bracket)  |              | **SHIFT :**           |
| **]** (right bracket) |              | **SHIFT ;**           |
| **\\** (backslash)    |              | **SHIFT M**           |
| **&#124;** (vertical bar)   | shift **\\** | **SHIFT -**           |
| **;** (semicolon)     |              | **;** (semicolon) key |
| **:** (colon)         | shift **;**  | **:** (colon) key     |
| **'** (single quote)  |              | **SHIFT 7**           |
| **"** (double quote)  | shift **'**  | **SHIFT 2**           |
| **,** (comma)         |              | **,** (comma) key     |
| **<** (less than)     | shift **,**  | **SHIFT ,**           |
| **.** (period)        |              | **.** (period) key    |
| **>** (greater than)  | shift **.**  | **SHIFT .**           |
| **/** (slash)         |              | **/** (slash) key     |
| **?** (question mark) | shift **/**  | **SHIFT /**           |

The underscore, vertical bar, and backslash keys are mapped to PETSCII graphic characters 
that most resemble these symbols.

The <b style="color:#ff9900;">Function Keys</b> map very simply.  The Mac has twelve function keys, 
the Commodore 64 has 8.  So an easy mapping of those C64 keys to the first eight function keys, 
leaving four available for other purposes.

The <b style="color:#f1c232;">Action Keys</b> were a bit of a challenge to map out.  I chose the 
following:

| MacBook Key | Obtained by | Commodore 64 Key             |
|:------------|:-----------:|:-----------------------------|
| **return**  |   | **RETURN**                   |
| **F9** | fn **F9** | **RESTORE**                  |
| **F11** | fn **F11** | **CLR**                      |
| **F12** | fn **F12** | **HOME**                     |
| **esc** |    | **STOP**                     |
|           | shift **esc** | **RUN**                      |
| **delete** |   | **DEL**                      |
|             | shift **delete** | **INST**                     |
| **▲** (up arrow) |    | **SHIFT CRSR ⇑⇓**            |
| **▼** (down arrow) |   | **CRSR ⇑⇓**                  |
| **◀** (left arrow) |   | **SHIFT CRSR ⇐<rar>⇒</rar>** |
| **▶** (right arrow) |   | **CRSR ⇐<rar>⇒</rar>**       |

Although there are some Commodore keyboards that have an ESC key, it felt like the Mac’s esc key 
was more akin to the STOP key. Typically you use esc on the Mac to interrupt actions, so this was 
a good fit. When we come to mapping a keyboard with ESC, we’ll find some other key to map it to.

The <b style="color:#9900ff;">"Transforming" Keys</b> are mapped out as:

| MacBook Key   | Obtained by | Commodore 64 Key |
|:--------------|:-----------:|:-----------------|
| **shift**     |    | **SHIFT**        |
| **caps lock** |    | **SHIFT LOCK**   |
| **control**   |     | **CTRL**         |
| **option**    | (left) **option** | **C=**           |

## The Mac “Commodore” Key and C64 Graphic Characters

In order to provide a place for all of the graphic characters available on the C64 keyboard, 
some had to be shuffled around.  This requires using the mapping of the C= key onto the Mac’s 
option key to make these work.

I didn't want this to be wholly random, so I thought I'd do the following:

 - Put the "graphic plus" (Commodore SHIFT-Plus) as the "option" variant of the equals/plus key.
 - Put the "graphic minus" (Commodore SHIFT-Asterisk) as the "option" variant of the minus key.
 - The "reverse L" graphic (Shift-at) can be the "option" variant of the semicolon key, which at least keeps it in the right place with the other three similar graphics (Shift-O, P, and L).
 - The two diagonal half-fill graphics (SHIFT-pound and Commodore-Asterisk) can go on the right bracket key (keeping the Shift/Commodore part)
 - The two half-checkerboards (Commodore-minus and Commodore-pound) can go on the left bracket key.
 - The full checkerboard can be the "option" variant of the apostrophe key.
 - Finally, the pi symbol (Shift-up arrow) can get mapped to the "option" variant of the backslash key.

| MacBook Key   |              Graphic Character              | Commodore 64 Key |
|:--------------|:-------------------------------------------:|:-----------------|
| option **+**  | <span class="petscii c64f">&#xe05b;</span> | **SHIFT +**      |
| option **-**  | <span class="petscii c64f">&#xe040;</span>  | <b>SHIFT *</b>   |
| option **;**  | <span class="petscii c64f">&#xe07a;</span> | **SHIFT @**      |
| shift **]**   | <span class="petscii c64f">&#xe069;</span> | **SHIFT £**      |
| option **]**  | <span class="petscii c64f">&#xe05f;</span>  | <b>C= *</b>      |
| shift **[**   | <span class="petscii c64f">&#xe05c;</span>  | **C= -**         |
| option **[**  | <span class="petscii c64f">&#xe068;</span> | **C= £**         |
| option **'**  | <span class="petscii c64f">&#xe066;</span> | **C= +**         |
| option **\\** | &pi; | **SHIFT &uarr;** |

## The “Missing” Keys

Two keys on the 64 remain to be mapped: the left arrow (&larr;) key and the british pound (£) key.
We can stick these on the unused backtick/tilde key on the mac.

And on the Mac side, we have a tab key that has to equivalent on the Commodore 64.
That key is mapped to the Commodore key in the default VICE symbolic mapping, and this makes some
sense since there is no TAB key on a Commodore 64.  But there is a TAB key on a Commodore 128’s
keyboard, a PET Business keyboard, as on the keyboard of a CBM-II B128.  And my muscle memory is
going to have me hitting the tab key and expecting it to move the cursor.  So I thought having tab
match to a space, and shift-tab (which means “back one tab”) move the cursor back one space would be
a reasonable representation.

| MacBook Key  |    Obtained by     | Commodore 64 Key        |
|:-------------|:------------------:|:------------------------|
| <b>&#96;</b> |    <b>&#96;</b>    | &larr; (left arrow) key |
| **~**        | shift <b>&#96;</b> | **£** (british pound) key |
| **tab**      |                    | (space bar)             |
|              |   shift **tab**    | **SHIFT CRSR ⇐⇒**       |

## The Result:

<span class="eimg">
![Planned Mapping of Commodore 64 Keyboard to MacBook Keyboard](https://github.com/user-attachments/assets/ca628552-a225-40e1-9490-5a972a434264)
My proposed mapping for the Commodore 64
</span>

So that was my plan. Time to figure out that VICE mapping file. That will be in the next part of the series.
