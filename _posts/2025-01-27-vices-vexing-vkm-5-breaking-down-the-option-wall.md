---
layout: post
title:  "Vice's Vexing VKM Part 5: Breaking Down the Option Wall"
date:   2025-01-27 09:42:00 -0800
categories: challenge vice-vkm
tags: retro-computing vice emulators commodore
---

#VICE's Vexing VKM: Part 5

## Breaking Down the Option Wall

[When we left off](/challenge/vice-vkm/2025/01/26/vices-vexing-vkm-4-colors-control-and-commodore.html), I had hit a wall. 

Using the mac’s (left) option key to map to the Commodore 64’s C= key couldn’t work.

There are two reasons for this:

1. The `option` key generates a unique `KEYCODE` for each `option`\-key combination, *BUT* not every key on the keyboard (not even every letter on the keyboard) has such a `KEYCODE`.
2. VICE has no way (that I was able to discover) of mapping these “missing” keycodes.

<span class="eimg">
![VICE Keyboard Debugger for option w](https://github.com/user-attachments/assets/4d691e73-93c1-4b3f-90e1-34ebec2c68b3)
</span>

Here is what the option `KEYCODE`s are for the macbook keyboard's letter keys:

- **`A`** - aring
- **`B`** - integral
- **`C`** - ccedilla
- **`D`** - partialderivative
- **`E`** - dead\_acute
- **`F`** - function
- **`G`** - copyright
- **`H`** - abovedot
- **`I`** - dead \_circumflex
- **`J`** - <span class="red">n/a</span>
- **`K`** - <span class="red">n/a</span>
- **`L`** - notsign
- **`M`** - mu
- **`N`** - dead\_perispomeni
- **`O`** - ooblique
- **`P`** - Greek\_pi
- **`Q`** - oe
- **`R`** - registered
- **`S`** - ssharp
- **`T`** - dagger
- **`U`** - dead\_diaeresis
- **`V`** - radical
- **`W`** - <span class="red">n/a</span> 
- **`X`** - <span class="red">n/a</span>
- **`Y`** - yen
- **`Z`** - Greek\_OMEGA

A total of four keys don’t have a mapping, but that’s enough to make using the `option` key to act as the 
Commodore (**`C=`**) key impossible.

### Swapping **`CTRL`** and **`C=`**

Since I am still determined not to use the tab key or a function key to map to the C= key, the best option I have is to swap 
the mappings of the control and option keys.

<span class="eimg vsml">
![Proposed mapping of Commodore and CTRL keys to mac control and option keys](https://github.com/user-attachments/assets/b7bdf488-33eb-47c2-9109-25d8cb89fa71)
</span>

This makes the mapping of the **`CTRL`**\-key combinations for colors and enabling/disabling the REVERSE mode easy 
enough, as we basically already did that for the “Commodore” colors before.

The only difference is that we need to use a different SHIFTFLAGS value:

`# 0x1000   4096  key is combined with ctrl for this keysym/scancode`

And we'll need to add mappings for `C=` 9 and 0 (RVS ON and RVS OFF).  That makes the mappings:

```
exclamdown      7 0 4096      # CTRL 1 (black) 
trademark       7 3 4096      # CTRL 2 (white) 
sterling        1 0 4096      # CTRL 3 (red) 
cent            1 3 4096      # CTRL 4 (cyan) 
infinity        2 0 4096      # CTRL 5 (purple) 
section         2 3 4096      # CTRL 6 (green) 
paragraph       3 0 4096      # CTRL 7 (blue) 
enfilledcircbullet 3 3 4096   # CTRL 8 (yellow) 
ordfeminine     4 0 4096      # CTRL 9 (reverse on) 
masculine       4 3 4096      # CTRL 0 (reverse off)
```

And we’ll need to map the `option` key to **`CTRL`**:

```
Alt_L           7 2 16384
```

Note that unlike the mapping of `option` to the **`C=`** key before, we *can* tell VICE that this key is the **`CTRL`** key 
using the `SHIFTFLAGS` value of 16384\.

### Making `control` act like **`C=`**

Mapping in the other direction is trickier. As we mentioned before, when using the mac’s `control` key, 
the `KEYCODE` for the key pressed along with `control` is the same as the “normal” `KEYCODE` for that key.

<span class="eimg">
![VICE Keyboard debugger for Control 1](https://github.com/user-attachments/assets/d12a66e6-2ba7-412c-b307-cdf99f59a2aa)
</span>

We’ve also established that due to some tricky mappings (that I have stubbornly refused to abandon), we can’t simply 
map the `control` key to the C64’s **`C=`** key, because in some cases it will create a **`C= SHIFT`** combination and 
toggle the character set between uppercase/graphics and lowercase/uppercase.

So that means the `control` key mapping needs to be:

```
Control_L       7 5 0
```

But how do we map the keys themselves?

Walking through the [VICE documentation](https://vice-emu.sourceforge.io/vice_4.html#SEC53) means:

* For each “normal” key we are going to have two definitions:
    * One for the unmodified key
    * One for the **`C=`** variant of the key (i.e. pressed along with the `control` key)
* For the C= variant:
    * The `control` key on the “host” (the macbook) will be depressed (0x0400 or 1024)
    * In *most* cases, the mapped (C64) key will need to be combined with the “cbm” (**`C=`**) key (0x0800 or 2048)
    * In *some* cases, the mapped (C64) key will need to be combined with the **`SHIFT`** key (0x0001 or 1)

This means that:

* If I am mapping a `control`\-key combination on the mac to a **`C=`**\-key combination on the C64, the `SHIFTFLAGS` value will be 1024 \+ 2048 \= 3072\.
* If I am mapping a `control`\-key combination to a **`SHIFT`**\-key combination on the C64, the `SHIFTFLAGS` value will be 1024 \+ 1 \= 1025\.

But I also must set the `SHIFTFLAGS` value for the unmodified key to 32 (0x0020), which means there are other definitions for this key to follow.

> The first time I tried mapping the same key, I assumed it made the most sense to define 
> the most restrictive mapping first and then the more general one. So I mapped the*** 
> **`control`*\-key combination first, adding 32 to the `SHIFTFLAGS` (so 3104 instead of 3072). 
> This did not work. For whatever reason, VICE prefers the most general mapping to show up 
> first in the vkm file.

So to map the Commodore color for the 1 key (orange), I need the following pair of lines:

```
1               7 0 32        # 1 
1               7 0 3072      # control 1 to C= 1 (orange)
```

This maps the unmodified 1 key to the C64 **`1`** key, indicating another definition of this key will follow. 
That mapping indicates that it applies to when the `control` key is pressed along with the 1 key, and this 
will map to the **`1`** key combined with the **`C=`** key on the C64.

### Remapping the Second Row

With this approach, the second row of keys will now map like:

```
grave           7 1 0      # ` = left arrow 
asciitilde      6 6 1      # ~ = pi 
1               7 0 32     # 1 
1               7 0 3072   # control 1 = c= 1 (orange) 
exclam          7 0 1      # ! 
exclamdown      7 0 4096   # option 1 = ctrl 1 (black) 
2               7 3 32     # 2 
2               7 3 3072   # control 2 = c= 2 (brown) 
at              5 6 16     # @ 
trademark       7 3 4096   # option 2 = ctrl 2 (white) 
3               1 0 32     # 3 
3               1 0 3072   # control 3 = c= 3 (light red) 
numbersign      1 0 1      # # 
sterling        1 0 4096   # option 3 = ctrl 3 (red) 
4               1 3 32     # 4 
4               1 3 3072   # control 4 = c= 4 (dark gray) 
dollar          1 3 1      # $ 
cent            1 3 4096   # option 4 = ctrl 4 (cyan) 
5               2 0 32     # 5 
5               2 0 3072   # control 5 = c= 5 (medium gray) 
percent         2 0 1      # % 
infinity        2 0 4096   # option 5 = ctrl 5 (purple) 
6               2 3 32     # 6 
6               2 3 3072   # control 6 = c= 6 (light green) 
asciicircum     6 6 16     # up arrow 
section         2 3 4096   # option 6 = ctrl 6 (green) 
7               3 0 32     # 7 
7               3 0 3072   # control 7 = c= 7 (light blue) 
ampersand       2 3 1      # & 
paragraph       3 0 4096   # option 7 = ctrl 7 (blue) 
8               3 3 32     # 8 
8               3 3 3072   # control 8 = c= 8 (light gray) 
asterisk        6 1 16     # * 
enfilledcircbullet 3 3 4096 # option 8 = ctrl 8 (yellow) 
9               4 0 0      # 9 
parenleft       3 3 1      # ( 
ordfeminine     4 0 4096   # option 9 = ctrl 9 (rvs on) 
0               4 3 0      # 0 
parenright      4 0 1      # ) 
masculine       4 3 4096   # option 0 = ctrl 0 (rvs off) 
minus           5 3 32     # - 
minus           6 1 1025   # control - = shift * 
underscore      5 6 2064   # option - = c= @ 
equal           6 5 32      # = 
equal           5 0 1025    # control = = shift + 
plus            5 0 16      # +  
BackSpace       0 0 8       # inst/del
```

## The Third Row: Piece of Cake!

The third row on the macbook should be easy, peasy…*now*. A little shuffling of graphic characters on the last three keys, but we’ve figured out how to do that. And the rest is trivially straightforward mapping.

<span class="eimg">
![Planned mapping of macbook row 3 to VICE emulator](https://github.com/user-attachments/assets/aa28fce6-9044-4d90-8ed6-1cb09a57f9de)
</span>

The mapping for the `tab` key doesn’t change:

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
q               7 6 32        # Q 
q               7 6 3072      # control Q = c= Q 
Q               7 6 1         # shift Q`
```

And on through the rest of the letters:

```
w               1 1 32        # W 
w               1 1 3072      # control W = c= W 
W               1 1 1         # shift W 
e               1 6 32        # E 
e               1 6 3072      # control E = c= E 
E               1 6 1         # shift E 
r               2 1 32        # R 
r               2 1 3072      # control R = c= R 
R               2 1 1         # shift R 
t               2 6 32        # T 
t               2 6 3072      # control T = c= T 
T               2 6 1         # shift T 
y               3 1 32        # Y 
y               3 1 3072      # control Y = c= Y 
Y               3 1 1         # shift Y 
u               3 6 32        # U 
u               3 6 3072      # control U = c= U 
U               3 6 1         # shift U 
i               4 1 32        # I 
i               4 1 3072      # control I = c= I 
I               4 1 1         # shift I 
o               4 6 32        # O 
o               4 6 3072      # control O = c= O 
O               4 6 1         # shift O 
p               5 1 32        # P 
p               5 1 3072      # control P = c= P 
P               5 1 1         # shift P
```

### The Symbol Keys

The last three keys in the third row of the mac are mostly places to put some extra graphic characters.

<span class="eimg sml">
![Mapping plan for last three keys of macbook row 3](https://github.com/user-attachments/assets/43347010-7858-4514-9ae9-4770ebfe21da)
</span>

These all have some new `SHIFTFLAGS` values:

```
bracketleft     5 5 33     # [ = SHIFT : 
bracketleft     5 3 3072   # control [ = c= - 
braceleft       6 0 2064   # shift [ = c= pound 
bracketright    6 2 33     # ] = SHIFT ; 
bracketright    6 1 3072   # control ] = c= * 
braceright      6 0 1      # shift ] = shift pound 
backslash       4 4 33     # \ = shift M 
backslash       6 6 1025   # control \ = shift ^ 
bar             5 3 8      # shift \ = shift -` |
```

Let’s look at what each of these means:

* 33 \- combining 1 (map to keysym with SHIFT) and 32 (another mapping available)
* 2064 \- combining 2048 (map to keysym with C=) and 16 (de-shift this key)

## The Fourth Row: Straightforward

The fourth row on the mac should map pretty much just like the third.

<span class="eimg">
![Planned mapping of macbook fourth row to VICE emulator](https://github.com/user-attachments/assets/a610506c-8cc3-436f-9ee0-ce3ce9cf24a2)
</span>

Mostly letters, with a few symbol keys, an action key, and one somewhat unique key…

### How to Map Caps Lock

The obvious mapping for the macbook’s `caps lock` key is the Commodore 64’s **`SHIFT LOCK`** key. They do *almost* the same 
thing, except that **`SHIFT LOCK`** applies to *every* key on the keyboard, not just the letter keys. But close enough to map.

But what is the mapping? The keyboard debugger tells me the `KEYCODE` is `Caps_Lock`. But there is no **`SHIFT LOCK`** key 
mappable in the keyboard matrix.

Enter that block of definitions at the start of the file I ignored 
[back in Part 2](/challenge/vice-vkm/2025/01/25/vices-vexing-vkm-2-starting-a-map-file.html).

```
!CLEAR !LSHIFT 1 7 
!RSHIFT 6 4 
!VSHIFT RSHIFT 
!SHIFTL LSHIFT 
!LCBM 7 5 
!VCBM LCBM 
!LCTRL 7 2 
!VCTRL LCTRL
```

The `!SHIFTL` definition tells me that **`SHIFT LOCK`** is mapped to the same matrix location as the 
left **`SHIFT`** (`LSHIFT`) key (which we can see is `ROW` 1, `COLUMN` 7).

So we’ve got the `KEYCODE`, and the `ROW` and `COLUMN`.  What about the `SHIFTFLAGS` value? This looks promising:

`# 0x0040     64  key is shift-lock on emulated machine`

All that put together makes for the following mapping:

```
Caps_Lock       1 7 64         # shift lock
```

### The Letters

As with the third line, each letter gets three mappings: unmodified, `control`/**`C=`**, and shifted.

```
a               1 2 32      # A 
a               1 2 3072    # control A = c= A 
A               1 2 1       # shift A 
s               1 5 32      # S 
s               1 5 3072    # control S = c= S 
S               1 5 1       # shift S 
d               2 2 32      # D 
d               2 2 3072    # control D = c= D 
D               2 2 1       # shift D 
f               2 5 32      # F 
f               2 5 3072    # control F = c= F 
F               2 5 1       # shift F 
g               3 2 32      # G 
g               3 2 3072    # control G = c= G 
G               3 2 1       # shift G 
h               3 5 32      # H 
h               3 5 3072    # control H = c= H 
H               3 5 1       # shift H 
j               4 2 32      # J 
j               4 2 3072    # control J = c= J 
J               4 2 1       # shift J 
k               4 5 32      # K 
k               4 5 3072    # control K = c= K 
K               4 5 1       # shift K 
l               5 2 32      # L 
l               5 2 3072    # control L = c= L 
L               5 2 1       # shift L
```

### The Symbols

There are two symbol keys, and only one of them (;) gets a graphic character mapping (to **`SHIFT @`**):

```
semicolon       6 2 32      # ; 
semicolon       5 6 1025    # control ; = shift @ 
colon           5 5 16      # : 
apostrophe      3 0 33      # ' = shift 7
apostrophe      5 0 3072    # control ' = c= + 
quotedbl        7 3 1       # “ = shift 2
```

### The Action

The **`RETURN`** key on the Commodore 64 can work either unmodified, where it means 
“process this line” or **`SHIFT`**ed where it simply does a carriage return (putting the cursor at the 
start of the next line). That means a `SHIFTFLAGS` value of 8 is appropriate.

```
Return          0 1 8       # Return
```

We’ll do the last two lines and finish up the mapping of the Commodore 64 in our next installment in this series.
