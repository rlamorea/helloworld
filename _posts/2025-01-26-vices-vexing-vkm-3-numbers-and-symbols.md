---
layout: post
title:  "Vice's Vexing VKM Part 3: Numbers and Symbols"
date:   2025-01-26 09:39:00 -0800
categories: challenge vice-vkm
tags: retro-computing vice emulators commodore
---

# VICE’s Vexing VKM: Part 3

## Numbers and Symbols

[Last time](/challenge/vice-vkm/2025/01/25/vices-vexing-vkm-2-starting-a-map-file.html), I started building my
VICE keyboard mapping — or VKM — file by creating the mappings for the top row of keys on my macbook.

Working from my plan, I mapped the top row of the macbook keyboard:

<span class="eimg">
![Macbook top row of keys](https://github.com/user-attachments/assets/2428a20d-1371-46de-bc67-615ea9985575)
</span>

That gave me the following mappings:

```
Escape      7 7 8
F1          0 4 0
F2          0 4 1
F3          0 5 0
F4          0 5 1
F5          0 6 0
F6          0 6 1
F7          0 3 0
F8          0 3 1
F9         -3 0
F12         6 3 8
```

## The Second Row: Numbers and Symbols

The second row was going to be trickier. Although the number keys are the same between the two keyboards,
the symbols you get when shifting these numbers differ for a bunch of keys.  And the keys following the
numbers are quite different — there’s not even the same *number* of keys. The Commodore 64 has 5 keys
following the 0, the macbook has 3.

<span class="eimg">
![Macbook second row mapping plan for VICE](https://github.com/user-attachments/assets/20f1f271-7df5-4e6f-85e0-ff6911f9a4ea)
</span>

So, let’s get to work.

## What’s in a (Key) Name?

Since the Commodore 64 has no key (or even character) matching either the backtick (\`) or tilde (\~), that key
on the mac was “open” to whatever I needed.

The key in that position on a C64 is a left arrow (**`←`**), a key with no equivalent on the mac.  So it seems
reasonable to map the backtick to that arrow. Another unique key symbol to the Commodore is pi (**`𝝿`**),
and mapping that to the shifted backtick key (or tilde) is as good a place as any.

First, though, we need to find out what the name of this key is:

<span class="eimg">
![Keyboard Debugger output for backtick](https://github.com/user-attachments/assets/7cd5bdd3-1480-41d0-8449-13582f250655)
</span>

Ok, so grave maps to the left arrow key at `ROW` 7, `COLUMN` 1\.  The left arrow key has no **`SHIFT`** symbol,
and I have plans for what I will do with `tilde`, so setting the `SHIFTFLAGS` to 0 will do what I need.

```
grave   7 1 0
```

And now I wasn’t sure what to do next. I hadn’t mapped a shifted key yet. Both **`RUN`** and **`CLR`** were maps
to keys on the Commodore that would respond to whether the **`SHIFT`** key was pressed or not (`SHIFTFLAGS` value 8).
But now I wanted to map the `tilde`, which is `shift backtick` on the mac, to pi, which is **`SHIFT`**
up arrow (**`↑`**) on the C64 (`ROW` 6, `COLUMN` 6).

Using the keyboard debugger, I see that typing tilde results in:

<span class="eimg">
![Keyboard Debugger output for tilde](https://github.com/user-attachments/assets/ed40a6e7-a132-407b-9871-5e5cfb53240a)
</span>

So I can see that I pressed the shift key (the left one, in fact) but the key is not `grave` as I had expected,
but `asciitilde`. The fact that it is shifted changes the key, and when mapping, that name effectively
“brings in” the shift keypress. This makes the mapping:

```
asciitilde      6 6 1
```

Note the value of 1 for `SHIFTFLAGS`. This ensures that the simulated key (up arrow) has the **`SHIFT`** applied
to it to produce the pi symbol. I *could* use a value of 8, and this would work as well, since the `shift` key
is depressed for both `tilde` and pi, so keeping the current `shift` key state would work for the mapping.

Having worked that out for the backtick/tilde key, mapping the 1 key was straightforward. The key names
are `1` and `exclam`, and these map to the **`1`** key at `ROW` 7, `COLUMN` 0, “natural” and **`SHIFT`**ed.

```
1               7 0 0
exclam          7 0 1
```

Next key.

## De-Shifting

The `2` key presents a new challenge. On the mac, `shift 2` is the at (`@`) symbol. On the Commodore 64, **`@`** is its own key (to the right of **`P`**). The keys to map are `2` and `at`. For the C64, the **`2`** key is located at `ROW` 7, `COLUMN` 3, and the **`@`** key is at `ROW` 5, `COLUMN` 6\.  That maps out as:

```
2               7 3 0
at              5 6 0
```

But testing that turned up something unexpected. Here’s what I got by typing **`2`** and **`SHIFT 2`**:

<span class="eimg vsml">
![VICE Emulator output for shift-2 with bad mapping](https://github.com/user-attachments/assets/ae00dfed-9174-4dd4-b37e-fe35079f726d)
</span>

That isn’t an @ symbol. In fact, going back to the Commodore 64 keyboard, I see that is, in fact, the symbol that SHIFT @ will generate.

<span class="eimg vsml">
![Commodore 64 at key and symbols](https://github.com/user-attachments/assets/4b735609-43ed-468c-8fb5-5c713b8a3239)
</span>

What’s happening here? I set the SHIFTFLAGS value to 0, meaning “key is not shifted”, but clearly it is. It turns
out that “key is not shifted” does not override the fact that I am holding the shift key down to get that @. So
enter a new SHIFTFLAGS value: 16, or “de-shift this key for this keysym.”  Change the mapping to:

```
at              5 6 16
```

And now I get:

<span class="eimg vsml">
![VICE Emulator output for shift-2 with correct mapping](https://github.com/user-attachments/assets/f0237fc0-e9ed-4374-8515-2081e0ecea4a)
</span>

Perfect.  With this knowledge, the mapping the rest of the numbers was easy:

```
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
```

The mac’s \=/+ key was also straightforward.

```
equal           6 5 0
plus            5 0 16         # + key
```

## Underscore: Mapping to a C= symbol

The minus key was easy enough to map as well:

```
minus           5 3 0
```

But I had chosen to map the mac’s `underscore` (`_` or `shift minus`) to a similar PETSCII graphics character that
is obtained by pressing the **`C=`** key and the **`@`** key on the Commodore keyboard.  So I need to map a shifted
key on the mac to an unshifted, but **`C=`** key modified, key on the C64.  How do I do that?

I fought with this for a while, and then I found that in [the VICE documentation was an extremely
useful explanation](https://vice-emu.sourceforge.io/vice_4.html#SEC53) for helping choose the right
`SHIFTFLAGS` “bits” to set.

So following this sequence, I work out that I want `SHIFTFLAGS` values of:

* 16 (de-shift key for this keysym)
* 2048 (key is combined with CBM — **`C=`** — for this keysym)

Add these together to get 2064, and that’s my mapping:

```
underscore      5 6 2064       # C= @
```

## One last key: delete

The last key in this row is `delete`, which shows as `BackSpace` in the keyboard debugger. I’m mapping this
(obviously) to the Commodore 64’s **`INST/DEL`** key. The `SHIFTFLAGS` value of 8 works perfectly here to
map that key’s functions:

```
BackSpace       0 0 8
```

The row is done…or *is* it?

Next time: Things getting colorful.
