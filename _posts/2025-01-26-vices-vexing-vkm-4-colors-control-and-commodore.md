---
layout: post
title:  "Vice's Vexing VKM Part 4: Colors, Control, and the Commodore Key"
date:   2025-01-26 14:46:00 -0800
categories: challenge vice-vkm
tags: retro-computing vice emulators commodore
---
# VICE’s Vexing VKM: Part 4

## Colors, Control, and the Commodore Key

When [we left off](/challenge/vice-vkm/2025/01/26/vices-vexing-vkm-3-numbers-and-symbols.html), we’d been able to map the first two rows of the macbook keyboard to 
keys on the Commodore 64\. Well, *mostly* map. The first row was all set, but the second 
row had some work left to do.

Here is the mapping of that row so far:

```
grave           7 1 0 
asciitilde      6 6 1 
1               7 0 0 
exclam          7 0 1 
2               7 3 0 
at              5 6 16 
3               1 0 0 
numbersign      1 0 1 
4               1 3 0 
dollar          1 3 1 
5               2 0 0 
percent         2 0 1 
6               2 3 0 
asciicircum     6 6 16        # up arrow key 
7               3 0 0 
ampersand       2 3 1		   # SHIFT 6 
8               3 3 0 
asterisk        6 1 16        # * key 
9               4 0 0 
parenleft       3 3 1         # SHIFT 8 
0               4 3 0 
parenright      4 0 1         # SHIFT 9 
minus           5 3 0 
underscore      5 6 2064      # C= @ 
equal           6 5 0 
plus            5 0 16        # + key 
BackSpace       0 0 8
```

The plan for mapping this row is:

<span class="eimg">
![Macbook second row mapping plan for VICE](https://github.com/user-attachments/assets/20f1f271-7df5-4e6f-85e0-ff6911f9a4ea)
</span>

Notice that we’re missing some mapping here:

* there are colors that map to the number keys, and
* there are some graphic characters that are mapped to the minus and equals keys.

The colors are obtained on a Commodore 64 by pressing either the control (**`CTRL`**) key 
or the Commodore (**`C=`**) key along with that number. The graphic characters on the left 
of the keys (in green) were planned to be obtained by pressing the **`C=`** key 
(technically its MacBook equivalent) along with that key.

### Making Colors 1: The Control Colors

My plan for mapping the special “shifting” keys on the Commodore 64 was to use the 
macbook’s `control` key for **`CTRL`**, and the (left) `option` key for the 
Commodore (**`C=`**) key.

<span class="eimg vsml">
![Mapping Plan for CTRL and Commodore Keys](https://github.com/user-attachments/assets/f0531a3d-4a39-4385-9261-e84cc7113b3a)
</span>

On the C64, using the CTRL key along with a number changes the current text color to the 
color written on the front of the key. So pressing CTRL and 1 would change the text 
color to black (BLK), for example. We want the same thing to happen with the macbook keyboard.

We can do this by mapping the mac’s control key to that on the Commodore 64:

```
Control_L       7 2 0
```

And testing it out, that doesn’t work. I press `control` and `1`, but all I get is a 1 on the screen.

<span class="eimg">
![VICE Keyboard debugger for Control 1](https://github.com/user-attachments/assets/d12a66e6-2ba7-412c-b307-cdf99f59a2aa)
</span>

<span class="eimg sml">
![VICE Emulator showing CTRL 1 with bad mapping](https://github.com/user-attachments/assets/730ad687-9f15-4165-9df7-be9ef797a59d)
</span>

Enter `SHIFTFLAGS` again.

`# 0x4000  16384  key is (left) ctrl on emulated machine`

We not only need to map the key, we also need to tell VICE that this key will act as the **`CTRL`** key using the value 16384\.  So the mapping needs to be:

```
Control_L       7 2 16384
```

And that does the trick. Now using the mac’s `control` key with the number keys from 1 to 8 sets 
the cursor color.  Using it with the 9 and 0 keys enables and disables “REVERSE” mode respectively.

<span class="eimg">
![VICE Emulator showing all CTRL colors](https://github.com/user-attachments/assets/a1495e42-c637-4196-a72f-cb2a91606bdb)
</span>

### Making Colors 2: The Commodore Colors

The Commodore 64 supports a whopping *16* colors.  Keys 1 through 8 along with the **`CTRL`** key set 
half of these, and the other half use the Commodore (**`C=`**) key along with 1 through 8\.

So we’ll need to do the same trick for the mac’s `option` key (which the keyboard debugger shows 
is named `Alt_L`), using the `SHIFTFLAGS` value:

`# 0x2000   8192  key is (left) cbm on emulated machine`

Which leads to a mapping of:

```
Alt_L          7 5 8192
```

That means that pressing the `option` key along with keys 1 through 8 should give us the other 8 colors:

<span class="eimg sml">
![VICE Emulator showing CBM 1 with bad mapping](https://github.com/user-attachments/assets/2bd180a5-d26f-4347-99e7-3677dff1d1c6)
</span>

Well, that didn’t work. Looking at the keyboard debugger shows why:

<span class="eimg">
![VICE Keyboard debugger showing results for Option 1](https://github.com/user-attachments/assets/e9c61c1b-bcc7-4954-b659-aba6f329ff45)
</span>

With the `control` key, pressing 1 returned a `KEYCODE` for 1\. But with the `option` key, 
the `KEYCODE` is `exclamdown`. Similar to how the key codes for shifted keys are different 
from the “normal” key, the combination of a key with `option` returns a different keycode, 
and that needs to be mapped instead.

So let’s map `exclamdown`:

```
exclamdown      7 0 ????
```

What to use for the `SHIFTFLAGS` value? We need to combine the key we are mapping to (1) 
with the **`C=`** key, which is this flag:

`# 0x0800   2048  key is combined with cbm for this keysym/scancode`

That makes the mapping:

```
exclamdown      7 0 2048
```

And that does the trick — for all the keys from 1 to 8:

<span class="eimg">
![VICE Emulator showing all CBM colors](https://github.com/user-attachments/assets/660891af-0814-40a4-b719-a2706cce9803)
</span>

The mappings are:

```
exclamdown      7 0 2048      # C= 1 (orange) 
trademark       7 3 2048      # C= 2 (brown) 
sterling        1 0 2048      # C= 3 (pink) 
cent            1 3 2048      # C= 4 (dark gray) 
infinity        2 0 2048      # C= 5 (medium gray) 
section         2 3 2048      # C= 6 (light green) 
paragraph       3 0 2048      # C= 7 (light blue) 
enfilledcircbullet 3 3 2048   # C= 8 (light gray)
```

### Two More Graphic Characters

The plan was to map in a couple of the PETSCII graphic characters to the minus and equal keys:

<span class="eimg vsml">
![Planned mapping of PETSCII for minus and equal keys](https://github.com/user-attachments/assets/79e0920f-f53e-40d9-8a9e-a803192c04a4) 
</span>

On the Commodore 64 keyboard, these two characters map to the combinations of **`C=`** and 
minus (**`-`**) keys for one, and the **`SHIFT`** and plus (**`+`**) keys for the other. 
I want to map these to be obtained on the mac keyboard using the `option` key.

Using the keyboard debugger we get the `KEYCODE`s for `option` and minus and equals 
are `endash` and `notequal` respectively. Now we need to figure out the `SHIFTFLAGS`. For the 
mapping of `option` minus to **`SHIFT`** asterisk, and `option` equals to **`SHIFT`** plus.
For these, the `SHIFTFLAGS` should be 1 (to indicate the **`SHIFT`**ed version of the key).

```
endash          6 1 1
notequal        5 0 1
```

It turns out this doesn't work. In fact, it does something rather unexpected:

<span class="eimg sml">
![VICE Emulator showing bad mapping causing inadvertent case swapping](https://github.com/user-attachments/assets/e80b1ece-ff5a-4055-ad1f-636562823b5c)
</span>

Typing `option` minus not only doesn’t show the right character, it shifts everything to lowercase!
And then `option` equal also shows the wrong character and shifts it back.

### The Swap-Case Side Effect

What is happening here? It took a bit to realize that because I am assigning a **`SHIFT`**ed key 
to one using the `option` key — which I’ve assigned to the Commodore (**`C=`**) key — I am 
effectively causing a **`SHIFT C=`** sequence, which is a way to toggle the Commodore 64 between 
its uppercase/graphics character set and its lowercase/uppercase character set.  How do I stop that 
from happening?

It turns out that I really *can’t*.  At least not and also keep the `option` key mapped as the 
Commodore key. But I can do a bit of a trick. Because the `KEYCODE`s for keys pressed along 
with `option` are unique, I can treat the `option` key like the **`C=`** key, but not actually 
map it as such.  So if I remove this mapping:

```
Alt_L          7 5 8192
```

Then pressing `option` equals gives me the right characters.

<span class="eimg sml">
![VICE Emulator showing case swapping has been fixed](https://github.com/user-attachments/assets/e2ea45dd-8cbf-4c0d-8e7e-bc12397a864a)
</span>

It is a reasonable compromise. But just in case I actually need to know if the **`C=`** key has 
been pressed, I need to map *some* key to it.  I chose to map the left `command` key on the 
mac to the **`C=`** key, but I will only use it as a “standalone” key, not in combination with 
anything (which is a good thing, because command key combinations tend to open menus, change 
programs, etc.).

```
Meta_L         7 5 8192	# “standalone” C= key
```

### The Complete Row 2

The final mapping for the macbook’s second row of keys is:

```
grave           7 1 0 
asciitilde      6 6 1 
1               7 0 0 
exclam          7 0 1 
exclamdown      7 0 2048      # C= 1 (orange) 
2               7 3 0 
at              5 6 16 
trademark       7 3 2048      # C= 2 (brown) 
3               1 0 0 
numbersign      1 0 1 
sterling        1 0 2048      # C= 3 (pink) 
4               1 3 0 
dollar          1 3 1 
cent            1 3 2048      # C= 4 (dark gray) 
5               2 0 0 
percent         2 0 1 
infinity        2 0 2048      # C= 5 (medium gray) 
6               2 3 0 
asciicircum     6 6 16        # up arrow key 
section         2 3 2048      # C= 6 (light green) 
7               3 0 0 
ampersand       2 3 1		   # SHIFT 6 
paragraph       3 0 2048      # C= 7 (light blue) 
8               3 3 0 
asterisk        6 1 16        # * key 
enfilledcircbullet 3 3 2048   # C= 8 (light gray) 
9               4 0 0 
parenleft       3 3 1         # SHIFT 8 
0               4 3 0 
parenright      4 0 1         # SHIFT 9 
minus           5 3 0 
underscore      5 6 2064      # C= @ 
endash          6 1 1 
equal           6 5 0 
plus            5 0 16        # + key 
notequal        5 0 1 
BackSpace       0 0 8 
## in row 6 
Control_L       7 2 16384 
Meta_L          7 5 8192	# “standalone” C= key`
```

Time to, *finally*, move on.

## The Third Row: Piece of Cake?

The third row on the macbook should be easy, peasy. A little shuffling of graphic characters on the last three keys, but we’ve figured out how to do that. And the rest is trivially straightforward mapping.

<span class="eimg">
![Planned mapping of macbook row 3 to VICE emulator](https://github.com/user-attachments/assets/aa28fce6-9044-4d90-8ed6-1cb09a57f9de)
</span>

### What to do with `tab`?

The mac has a `tab` key, the Commodore 64 does not. The default VICE mapping of this key is to 
the **`C=`** key, but I’m not a fan of that for two reasons:

1. My pinky is going to hit `tab` regularly because I’m just used to it doing the thing that `tab` does: move the cursor to the right some distance.
2. Other Commodore keyboards *do* have a **`TAB`** key, and when it comes time to map them, I want the mac’s `tab` key available.

So for the C64, I’m going to map the `tab` key as an alternate space bar — so I move the cursor to 
the right one space. `Shift`ed `tab`, which usually moves back one tab stop, can map to cursor left (**`SHIFT ⇐CRSR⇒`**) on the C64.

```
Tab             7 4 0         # space 
ISO_Left_Tab    0 2 1         # shift CRSR LEFT/right
```

### Mapping Letters

Mapping each letter means doing three things:

1. Mapping the “normal” letter (which is the lowercase letter `KEYCODE`).
2. Mapping the shifted letter (which is the uppercase letter `KEYCODE`).
3. Mapping the “commodore” (**`C=`**) graphic character, which is the `option` letter combination, which generates a unique `KEYCODE`.

Start with Q:

```
q               7 6 0        # Q 
Q               7 6 1        # shift Q 
oe		        7 6 2048     # C= Q
```

Then on to W…

### The `option` Wall

…and suddenly things fall apart.

<span class="eimg">
![VICE Keyboard Debugger for option w](https://github.com/user-attachments/assets/4d691e73-93c1-4b3f-90e1-34ebec2c68b3)
</span>

It turns out that there is no KEYCODE for the option W combination. I tried many ways to see 
if I could make VICE recognize some form of U+2211 (or u2211, or U2211, or 0x2211, or 
0x1002211, or 16785937).

It is time to rethink things. We’ll pick this up in the next episode.
