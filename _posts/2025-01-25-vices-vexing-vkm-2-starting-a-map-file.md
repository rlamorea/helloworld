---
layout: post
title:  "Vice's Vexing VKM Part 2: Starting a Map File"
date:   2025-01-25 19:48:00 -0800
categories: challenge vice-vkm
tags: retro-computing vice emulators commodore
---

[Last time](/challenge/vice-vkm/2024/12/06/vices-vexing-vkm-1-planning-a-keymap.html),
I described my plan for creating a useful (to me) map of my Macbook’s keyboard to a Commodore 64 keyboard to
use in [VICE](https://vice-emu.sourceforge.io/) — the emulator for all of Commodore’s 8-bit computers.

The mapping I came up with was this:

<span class="eimg">
![Planned Mapping of Commodore 64 Keyboard to Macbook Keyboard](https://github.com/user-attachments/assets/ca628552-a225-40e1-9490-5a972a434264)
My proposed mapping for the Commodore 64
</span>

Every key and PETSCII graphic character available on the C64 keyboard maps to somewhere on the mac.  Since the mapping very
much keeps things in their “proper” place on the mac keyboard, I should be able to type efficiently using this mapping.

Now if I only knew how to actually make that mapping.

## Understanding the VKM

The VKM files have all their documentation in the file itself:

```
# VICE keyboard mapping file
#
# A Keyboard map is read in as patch to the current map.
#
# File format:
# - comment lines start with '#'
# - keyword lines start with '!keyword'
# - normal lines have 'keysym/scancode row column shiftflag'
#
# Keywords and their lines are:
# '!CLEAR'               clear whole table
# '!INCLUDE filename'    read file as mapping file
# '!LSHIFT row col'      left shift keyboard row/column
# '!RSHIFT row col'      right shift keyboard row/column
# '!VSHIFT shiftkey'     virtual shift key (RSHIFT or LSHIFT)
# '!SHIFTL shiftkey'     shift lock key (RSHIFT or LSHIFT)
#  for emulated keyboards that have only one shift key, set both LSHIFT
#  and RSHIFT to the same row/col and use RSHIFT for VSHIFT and SHIFTL.
# '!LCTRL row col'       left control keyboard row/column
# '!VCTRL ctrlkey'       virtual control key (LCTRL)
# '!LCBM row col'        left CBM keyboard row/column
# '!VCBM cbmkey'         virtual CBM key (LCBM)
# '!UNDEF keysym'        remove keysym from table
#
# Shiftflag can have these values, flags can be ORed to combine them:
# 0x0000      0  key is not shifted for this keysym/scancode
# 0x0001      1  key is combined with shift for this keysym/scancode
# 0x0002      2  key is left shift on emulated machine
# 0x0004      4  key is right shift on emulated machine (use only this one
#                for emulated keyboards that have only one shift key)
# 0x0008      8  key can be shifted or not with this keysym/scancode
# 0x0010     16  deshift key for this keysym/scancode
# 0x0020     32  another definition for this keysym/scancode follows
# 0x0040     64  key is shift-lock on emulated machine
# 0x0080    128  shift modifier required on host
# 0x0100    256  key is used for an alternative keyboard mapping, e.g. C64 mode in x128
# 0x0200    512  alt-r (alt-gr) modifier required on host
# 0x0400   1024  ctrl modifier required on host
# 0x0800   2048  key is combined with cbm for this keysym/scancode
# 0x1000   4096  key is combined with ctrl for this keysym/scancode
# 0x2000   8192  key is (left) cbm on emulated machine
# 0x4000  16384  key is (left) ctrl on emulated machine
#
# Negative row values:
# 'keysym -1 n' joystick keymap A, direction n
# 'keysym -2 n' joystick keymap B, direction n
# 'keysym -3 0' first RESTORE key
# 'keysym -3 1' second RESTORE key
# 'keysym -4 0' 40/80 column key (x128)
# 'keysym -4 1' CAPS (ASCII/DIN) key (x128)
# 'keysym -5 n' joyport keypad, key n (not supported in x128)
#

<snip>

# Symbolic Mapping, US Layout, C64, GTK

# C64 keyboard matrix:
#
#       +-----+-----+-----+-----+-----+-----+-----+-----+
#       |Bit 0|Bit 1|Bit 2|Bit 3|Bit 4|Bit 5|Bit 6|Bit 7|
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 0| DEL |Retrn|C_L/R|  F7 |  F1 |  F3 |  F5 |C_U/D|
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 1| 3 # |  W  |  A  | 4 $ |  Z  |  S  |  E  | S_L |
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 2| 5 % |  R  |  D  | 6 & |  C  |  F  |  T  |  X  |
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 3| 7 ' |  Y  |  G  | 8 ( |  B  |  H  |  U  |  V  |
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 4| 9 ) |  I  |  J  |  0  |  M  |  K  |  O  |  N  |
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 5|  +  |  P  |  L  |  -  | . > | : [ |  @  | , < |
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 6|POUND|  *  | ; ] | HOME| S_R |  =  | A_UP| / ? |
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+
# |Bit 7| 1 ! |A_LFT| CTRL| 2 " |SPACE|  C= |  Q  | R/S |
# +-----+-----+-----+-----+-----+-----+-----+-----+-----+

# C64 Keyboard layout:
#
#   arrow 1!  2"  3#  4$  5%  6&  7'  8(  9)  0   +   -   pound clr del     f1/f2
#    ctrl   q   w   e   r   t   y   u   i   o   p   @   *   ^   restore     f3/f4
#   r/s SL    a   s   d   f   g   h   j   k   l   :[  ;]  =   return        f5/f6
#   CBM LS      z   x   c   v   b   n   m   ,<  .>  /?  RS  u/d l/r         f7/f8
#                               space
```

The rest of the file was mostly mapping lines that looked like:

```
numbersign      1 0 1
dollar          1 3 1
Shift_L         1 7 2 # shift lock
Caps_Lock       1 7 64
```

It was pretty cryptic and despite reading through the upper comment block, and even reading
some of the documentation and various forum posts, it just wasn’t making sense to me.
I can’t really say why.

Then I stumbled across [this video](https://www.youtube.com/watch?v=zMyCyO7c0hY) from
[GroundhogGrafix Retro](https://www.youtube.com/@groundhoggrafixretro4095).

<span class="eimg">
![GroundhogGrafix Retro's video about remapping VICE](https://github.com/user-attachments/assets/d1e718d3-3a36-42ab-941b-62d406316a95)
</span>

Suddenly things clicked for me and I realized what I needed to do.

## Understanding the Lines

The fact is that *most* of what I needed was sitting right in front of me in that comment header block of the vkm.  There are really three things that must be understood to make sense of the key mapping definitions in the file:

1. `KEYCODE` \- The keys on the “host” keyboard (meaning the keyboard you are typing on) you can map from — which are all named.
2. `ROW & COLUMN` \- The matrix grid location of the key on the emulated (meaning the original Commodore keyboard) that maps to the `KEYCODE`.  The keyboard matrix, which is different for each emulated keyboard, is in the comment header.
3. `SHIFTFLAGS` \- The flags to apply to this mapping, which are listed in the comment header.

All of that is documented in that comment header, but I still had some questions:

1. **How do I know the `KEYCODE`s?**
   I mean *yes*, there are obvious keycodes listed in the file itself, but what if the key I’m looking for isn’t listed? Since the default vkm file is intended to work with a standard PC keyboard, there are bound to be keys on my Macbook that aren’t listed in the file.
2. **Which `SHIFTFLAG`**s **do I apply?**
   The video above gave me the clue as to how to apply `SHIFTFLAGS`, but it was going to take some experimentation to really get them straight.

I did recognize that the block of definitions at the start of the file (just under the comment header) were effectively “reserved” code. No changes needed (or, indeed, recommended) there.  I could leave them be.

```
!CLEAR
!LSHIFT 1 7
!RSHIFT 6 4
!VSHIFT RSHIFT
!SHIFTL LSHIFT
!LCBM 7 5
!VCBM LCBM
!LCTRL 7 2
!VCTRL LCTRL
```

This is basically VICE telling VICE how the Commodore 64 keyboard works.

## A Better Organization

One thing I immediately saw was that the organization of the default mapping file was not helping me. 
It is organized around the keyboard matrix. To me that made it unhelpful for visualizing how my mappings 
were going to work against my target keyboard.

Rather than try to fight against a file organization I wasn’t fond of, I chose another route: 
everything below that “reserved” section must go.  I’ll start the mapping from scratch.

I want to organize *my* mapping file by the keyboard I was mapping from (i.e. the Macbook if you somehow forgot).  
So first I added in my own comment block showing that keyboard:

```
# Macbook keyboard
#
#   esc    F1   F2   F3   F4   F5   F6   F7   F8   F9  F10  F11  F12  [pwr]
#   ~`   !1   @2   #3   $4   %5   ^6   &7   *8   (9   )0   _-   +=   delete
#   tab    Q    W    E    R    T    Y    U    I    O    P    {[   ]}   |\
#   capslock A    S    D    F    G    H    J    K    L    ;:    "'   return
#   shift     Z    X    C    V    B    N    M    <,   >.   ?/         shift
#   fn  ctrl opt cmd [         space          ] cmd  opt    <-   ^/v   ->
```

Then I began specifying mappings, row by row of that keyboard.

### Entering the Matrix

VICE, being an emulator, matches the way [the underlying hardware works](https://c64os.com/post/howthekeyboardworks).  For the Commodore 64, the keyboard is handled by a set of wires that wrap around in such a way that each key is at the intersection of two wires.  This forms a matrix of `ROW`s and `COLUMN`s, and it is these that are used to map the “host” keyboard to the emulated one.

<span class="eimg">
![Commodore 64 Keyboard Matrix](https://github.com/user-attachments/assets/21d6ba5a-411d-4a11-a586-f44eb148065a)
Keyboard Matrix from the Commodore 64 Service Manual
</span>

This mapping is reproduced in the vkm file’s comment header:

```keygrid
      +-----+-----+-----+-----+-----+-----+-----+-----+
      |Bit 0|Bit 1|Bit 2|Bit 3|Bit 4|Bit 5|Bit 6|Bit 7|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 0| DEL |Retrn|C_L/R|  F7 |  F1 |  F3 |  F5 |C_U/D|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 1| 3 # |  W  |  A  | 4 $ |  Z  |  S  |  E  | S_L |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 2| 5 % |  R  |  D  | 6 & |  C  |  F  |  T  |  X  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 3| 7 ' |  Y  |  G  | 8 ( |  B  |  H  |  U  |  V  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 4| 9 ) |  I  |  J  |  0  |  M  |  K  |  O  |  N  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 5|  +  |  P  |  L  |  -  | . > | : [ |  @  | , < |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 6|POUND|  *  | ; ] | HOME| S_R |  =  | A_UP| / ? |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 7| 1 ! |A_LFT| CTRL| 2 " |SPACE|  C= |  Q  | R/S |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
```

So to map the 0 key on the emulated keyboard the ROW and COLUMN will be: 4 3

<pre><code class="language-keygrid">      +-----+-----+-----+-----+-----+-----+-----+-----+
      |Bit 0|Bit 1|Bit 2|Bit 3|Bit 4|Bit 5|Bit 6|Bit 7|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 0| DEL |Retrn|C_L/R|  F7 |  F1 |  F3 |  F5 |C_U/D|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 1| 3 # |  W  |  A  | 4 $ |  Z  |  S  |  E  | S_L |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 2| 5 % |  R  |  D  | 6 & |  C  |  F  |  T  |  X  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 3| 7 ' |  Y  |  G  | 8 ( |  B  |  H  |  U  |  V  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 4| 9 ) |  I  |  J  |  0  |  M  |  K  |  O  |  N  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 5|  +  |  P  |  L  |  -  | . > | : [ |  @  | , < |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 6|POUND|  *  | ; ] | HOME| S_R |  =  | A_UP| / ? |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 7| 1 ! |A_LFT| CTRL| 2 " |SPACE|  C= |  Q  | R/S |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
<hbar class="b4"></hbar><vbar class="b3"></vbar></code></pre>

### What *are* those `KEYCODE`s?

Ok, so I know what to map *to*, but what can I map *from*? Looking at the provided mapping file, I can see lines like:

```
A 1 2 8
B 3 4 8
C 2 4 8
```

But also lines like:

```
numbersign      1 0 1
dollar          1 3 1
Shift_L         1 7 2
```

How was I going to know all the possible key names from my mac keyboard?

The answer was in the settings:

<span class="eimg">
![VICE keyboard settings](https://github.com/user-attachments/assets/bc0a4f40-03a5-4720-a5b5-91d67780a3fe)
</span>

Turning on keyboard debugging showed me the name of the “host” key I was pressing in the emulator:

<span class="eimg">
![VICE Keyboard Debugger Display](https://github.com/user-attachments/assets/a76f1093-529c-4c20-8a04-ddcf74a67215)
</span>

Those key names were what I needed.  Now let’s get mapping.

## Mapping My First Row

The top row of keys on the Macbook are escape and the function keys.  It also has the power key, but we can’t map that to anything.

<span class="eimg">
![Macbook top row of keys](https://github.com/user-attachments/assets/2428a20d-1371-46de-bc67-615ea9985575)
</span>

### STOP is Easy, What About RUN?

Start by finding the name of that key.  The key label might say “esc”, but the keyboard debugger says:

<span class="eimg">
![VICE Keyboard Debugger for esc key](https://github.com/user-attachments/assets/2b0d0429-de1c-4d15-9930-0987ccdbb94e)
</span>

The STOP key is at `ROW` 7, `COLUMN` 7:

<pre><code class="language-keygrid">      +-----+-----+-----+-----+-----+-----+-----+-----+
      |Bit 0|Bit 1|Bit 2|Bit 3|Bit 4|Bit 5|Bit 6|Bit 7|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 0| DEL |Retrn|C_L/R|  F7 |  F1 |  F3 |  F5 |C_U/D|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 1| 3 # |  W  |  A  | 4 $ |  Z  |  S  |  E  | S_L |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 2| 5 % |  R  |  D  | 6 & |  C  |  F  |  T  |  X  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 3| 7 ' |  Y  |  G  | 8 ( |  B  |  H  |  U  |  V  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 4| 9 ) |  I  |  J  |  0  |  M  |  K  |  O  |  N  |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 5|  +  |  P  |  L  |  -  | . > | : [ |  @  | , < |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 6|POUND|  *  | ; ] | HOME| S_R |  =  | A_UP| / ? |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 7| 1 ! |A_LFT| CTRL| 2 " |SPACE|  C= |  Q  | R/S |
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
<hbar class="b7"></hbar><vbar class="b7"></vbar></code></pre>

So mapping the esc(ape) key to the C64’s STOP key:

```
Escape      7 7 ????
```

At this point I realized I really didn’t know *what* to put in for that `SHIFTFLAGS` value.
I saw that most of the keys mapped in the default vkm file used a value of 8, and I saw
that this mapped into the list of flags in the header content:

```
# Shiftflag can have these values, flags can be ORed to combine them:
# 0x0000      0  key is not shifted for this keysym/scancode
# 0x0001      1  key is combined with shift for this keysym/scancode
# 0x0002      2  key is left shift on emulated machine
# 0x0004      4  key is right shift on emulated machine (use only this one
#                for emulated keyboards that have only one shift key)
# 0x0008      8  key can be shifted or not with this keysym/scancode
# 0x0010     16  deshift key for this keysym/scancode
# 0x0020     32  another definition for this keysym/scancode follows
# 0x0040     64  key is shift-lock on emulated machine
# 0x0080    128  shift modifier required on host
# 0x0100    256  key is used for an alternative keyboard mapping, e.g. C64 mode in x128
# 0x0200    512  alt-r (alt-gr) modifier required on host
# 0x0400   1024  ctrl modifier required on host
# 0x0800   2048  key is combined with cbm for this keysym/scancode
# 0x1000   4096  key is combined with ctrl for this keysym/scancode
# 0x2000   8192  key is (left) cbm on emulated machine
# 0x4000  16384  key is (left) ctrl on emulated machine
```

So an 8 means “key can be shifted or not with this keysym.”

> I think “keysym” might stand for “key symbol”, but I prefer to think of it as
> “simulated key” (wrong “sim”, I know), as this makes it clearer that “key” means
> a key on the “host” (mac) keyboard, and “keysym” means one on the simulated
> Commodore 64 keyboard.

What that means is that the host (macbook) key, `esc`, will map to the C64 **`RUN/STOP`** *keysym*
(acting as **`STOP`**), *and* the combination of shift and esc will map to **`SHIFT`**
and the **`RUN/STOP`** key (otherwise known as **`RUN`**) on the C64.  So 8 is the
correct `SHIFTFLAGS` value for this key, and the mapping is:

```
Escape      7 7 8
```

First key mapped. Move on.

### F1: No SHIFT for you\!

Mapping F1 had a small challenge on the mac: the function keys aren’t function keys unless you also
press the `fn` key in the lower left of the keyboard. But the keyboard debugger shows that this
is something happening behind the scenes. Pressing the `F1` key on the keyboard will not even
show up in the debugger — it will just dim the screen (it’s primary action).  But pressing
`fn` and the `F1` key together shows as:

<span class="eimg">
![VICE Keyboard Debugger for fn and F1 key](https://github.com/user-attachments/assets/d5359df0-af64-480d-8e3f-7f4a2b74ddc1)
</span>

Ok, so F1 is my key to map.  That is on ROW 0, COLUMN 4:

<pre><code class="language-keygrid">      +-----+-----+-----+-----+-----+-----+-----+-----+
      |Bit 0|Bit 1|Bit 2|Bit 3|Bit 4|Bit 5|Bit 6|Bit 7|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
|Bit 0| DEL |Retrn|C_L/R|  F7 |  F1 |  F3 |  F5 |C_U/D|
+-----+-----+-----+-----+-----+-----+-----+-----+-----+
<hbar class="b0"></hbar><vbar class="b4"></vbar></code></pre>

So our map will be:

```
F1          0 4 8
```

But wait, using a `SHIFTFLAGS` value of 8 means that combining `shift`, `fn`, and `F1` would be
interpreted as the Commodore 64’s **`F2`** key.  And the macbook *has* an `F2` key.  So I don’t
want to support both unshifted and shifted versions of that key. This means I want to use
a `SHIFTFLAGS` value of 0 (“key is not shifted”).

```
F1          0 4 0
```

Now pressing fn and F1 on the mac keyboard will *always* map to the C64’s F1 key.

### F2: Keep it SHIFTing

Which brings us to how to map `F2`. As I said, the mac *has* an `F2` key.  The Commodore 64 does not.
**`F2`** on the C64 is obtained by pressing **`SHIFT`** and the **`F1`** key. We know how to map a the
macbook key to the C64 key:

```
F2          0 4 ???
```

But what `SHIFTFLAGS` value should be used? In this case, a value of 1 works: “key is combined with shift for this keysym”.

> At this point I admit that the descriptions of these flags was not easy to parse for me.
> The video I cited above gave me a clue how to read these, and subsequent experimentation
> (detailed here) got me comfortable with them. But the descriptions provided in the content
> header documentation is really not all that good.

But 1 is the correct value, and the mapping for `F2` is:

```
F2          0 4 1
```

In this case, pressing `F2` on the mac will *always* map to the C64’s **`F1`** key *and* **`SHIFT`** key simultaneously.

This pattern follows through the remaining function keys:

```
F3      0 5 0
F4      0 5 1
F5      0 6 0
F6      0 6 1
F7      0 3 0
F8      0 3 1
```

### F9: RESTORE me

The “spare” four function keys on the mac were a good place to map a few other of the
Commodore 64’s special keys.  I planned on mapping `F9` to the **`RESTORE`** key.
But the **`RESTORE`** key isn’t on the keyboard matrix.

This is where a few lines in the header come into play:

```
# Negative row values:
# 'keysym -1 n' joystick keymap A, direction n
# 'keysym -2 n' joystick keymap B, direction n
# 'keysym -3 0' first RESTORE key
<b># 'keysym -3 1' second RESTORE key</b>
# 'keysym -4 0' 40/80 column key (x128)
# 'keysym -4 1' CAPS (ASCII/DIN) key (x128)
# 'keysym -5 n' joyport keypad, key n (not supported in x128)
```

These are “extra” row/column pairings that match up to the cases where the standard keyboard
matrix does not cover the key in question. If you look at that service manual diagram, you see
that the **`RESTORE`** key is outside the keyboard matrix:

<span class="eimg">
![Commodore 64 Keyboard Matrix zoom on Restore key circuit](https://github.com/user-attachments/assets/666bb298-90fb-48b6-a8a8-6083feb8b082)
</span>

So the mapping will need to be:

```
F9     -3 0
```

Although there is no direct documentation, looking at the default vkm file showed that these
special “negative” row mappings have no `SHIFTFLAGS` value.

Testing this out, it works perfectly. Pressing the `fn`, `esc`, and `F9` keys at the same time
produces the soft reset of a **`RUN/STOP`** \+ **`RESTORE`** combination in the emulated Commodore 64\.

### F11: Whatever It Is Doing, I Can’t Stop It

The plan was to “flatten” the **`CLR/HOME`** key, mapping `F11` to **`CLR`** and `F12` to **`HOME`**.  But, at
least on my macbook, F11 does *something* to the screen, and there’s no stopping it.

<span class="eimg">
![Mac F11 screen effect](https://github.com/user-attachments/assets/5160c72d-871c-4716-a17f-22733cf2eb35)
</span>

So I need to find an alternative approach.  I *could* map F10 to CLR and keep F12 mapped to HOME,
but it would be weird having that skipped key in between.  So instead I will…

### F12: HOME and CLR

…map `F12` to the **`CLR/HOME`** key (at `ROW` 6, `COLUMN` 3\) using the “key can be shifted or not”
`SHIFTFLAGS` value (8).  That will make pressing `fn` and `F12` work as **`HOME`**, and `fn`, `shift`,
and `F12` act as **`CLR`**.

```
F12     6 3 8
```

And with that, the first line of the macbook keyboard is mapped.

## Next Up

Next time we'll tackle mapping the second row of numbers and symbols.
