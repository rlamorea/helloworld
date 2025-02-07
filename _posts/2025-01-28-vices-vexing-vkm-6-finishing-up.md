---
layout: post
title:  "Vice's Vexing VKM Part 6: Finishing Up"
date:   2025-01-28 14:02:00 -0800
categories: challenge vice-vkm
tags: retro-computing vice emulators commodore
---

# VICE’s Vexing VKM: Part 6

## Finishing Up

*Finally*, the effort to map my MacBook keyboard to use in the VICE Commodore 64 emulator comes to an end. 
[Last time](/challenge/vice-vkm/2025/01/27/vices-vexing-vkm-5-breaking-down-the-option-wall) we got over the final hurdle, 
finding an effective mapping for the Commodore’s CTRL and “Commodore” (C=) keys.

This time we map the remaining two rows of keys, and look back on what was learned.

## The Fifth Row: Getting Easy Now

The fifth row of the mac was planned to be mapped like this:

<span class="eimg">
![Planned mapping of macbook keyboard row 5 to VICE emulator](https://github.com/user-attachments/assets/df46dc44-fe76-4df5-8fe4-4f4bd5cbeeb5)
</span>

Which looks overall pretty easy, based on all we’ve learned.

### Left **`SHIFT`**, and Doing the CharSet Toggle

Mapping the left SHIFT key on the C64 requires yet another `SHIFTFLAGS` value:

`# 0x0002      2  key is left shift on emulated machine`

Making the mapping:

```
Shift_L         1 7 2       # shift (left)
```

However, the Commodore 64 has a special sequence where pressing the Commodore (**`C=`**) and **`SHIFT`** keys at the 
same time toggles the display between the uppercase/graphics character set and the lowercase/uppercase set. We learned 
this to our detriment when we tried to map **`SHIFT`**ed keys to use the mapped **`C=`** key (option on the mac). 
We might have worked around that problem for the mapping, but I still want to keep this functionality available.

Luckily, because we are using the mac’s `control` key for the C64’s **`C=`** key, we can create a mapping for 
this action. All we need to do is add 32 (another definition available) to the above mapping’s `SHIFTFLAGS` value, 
and then add one more mapping:

```
Shift_L         1 7 34      # shift (left)
Shift_L         1 7 3073    # control shift = c= shift
```

Do you see the trick there? The `SHIFTFLAGS` value for the second mapping *combines* three flags:

`# 0x0001      1  key is combined with shift for this keysym/scancode`  
`# 0x0400   1024  ctrl modifier required on host`  
`# 0x0800   2048  key is combined with cbm for this keysym/scancode`

We’re actually forcing both the “cbm” (**`C=`**) and **`SHIFT`** keys to be active when this mapping fires. We took 
the annoying inadvertent behavior that forced us to not directly map the Commodore key earlier and intentionally 
used it here to force the character set toggle to happen.

### The Letters

The letter keys on the fifth line map just like they did on the third and fourth. Three mappings per key.

```
z               1 4 32      # Z 
z               1 4 3072    # control Z = c= Z 
Z               1 4 1       # shift Z 
x               2 7 32      # X 
x               2 7 3072    # control X = c= X 
X               2 7 1       # shift X 
c               2 4 32      # C 
c               2 4 3072    # control C = c= C 
C               2 4 1       # shift C 
v               3 7 32      # V 
v               3 7 3072    # control V = c= V 
V               3 7 1       # shift V 
b               3 4 32      # B 
b               3 4 3072    # control B = c= B 
B               3 4 1       # shift B 
n               4 7 32      # N 
n               4 7 3072    # control N = c= N 
N               4 7 1       # shift N 
m               4 4 32      # M 
m               4 4 3072    # control M = c= M 
M               4 4 1       # shift M
```

### The Symbols

The symbol keys for the fifth row are as simple as it gets (well, not that I understand things):

```
comma           5 7 0       # , 
less            5 7 8       # < 
period          5 4 32      # . 
greater         5 4 8       # > 
slash           6 7 0       # / 
question        6 7 8       # ?
```

### One Last Shift

The last key on the fifth row is the right **`SHIFT`** key, which, like the left **`SHIFT`** key, has its own `SHIFTFLAGS` value:

`# 0x0004      4  key is right shift on emulated machine (use only this one`

And we’ll set up to be able to toggle the character set.

```Shift_R         6 4 36      # shift (right) 
hift_R         6 4 3073    # control shift = c= shift
```

## The Last Row: Modifiers and Actions (and Space)

The sixth row of the mac keyboard is mostly modifiers, a big ol’ space bar, and the cursor keys.

<span class="eimg">
![Planned mapping of macbook keyboard row 6 to VICE emulator](https://github.com/user-attachments/assets/dc9dc390-4531-4c3f-a361-484cdff8d99e)
</span>

Of course, this was the *planned* mapping. We now know that the CTRL and C= keys needed to be swapped, and that the left command key has been mapped to act as a “standalone” Commodore (C=) key, just in case.

## The Modifiers

The mapping of the modifier keys looks like this:

```
#fn - ignored 
Control_L       7 5 0       # C= key (not really) 
Alt_L           7 2 16384   # CTRL key (kinda) 
Meta_L          7 5 8192    # “standalone” C= key # 
Meta_R - ignored 
# Alt_R - works just like Alt_L
```

### Spaaaaaaaaace\!

<span class="eimg sml">
![Portal 2 Space Core shouting Spaaaaaaaaaace](https://github.com/user-attachments/assets/8c8e4f09-00c8-44ac-a399-aa0956ff297a)
</span>

```
space           7 4 0       # space
```

### The Cursor Keys

The (terrible) cursor keys on the Commodore 64 can be properly “flattened” into the vastly superior cursor keys of the mac:

```
Left            0 2 1       # shift-CRSR LEFT/right 
Up              0 7 1       # shift-CRSR UP/down 
Down            0 7 0       # CRSR up/DOWN 
Right           0 2 0       # CRSR left/RIGHT
```

And that should do it. Except…

# More Control?

The Commodore 64 has a few more keys that do special things when combined with CTRL. Most of these are simply duplicates 
of action keys, but there are a few that are unique and a few others that are worth mapping “just because”.

The [Commodore 128 System Guide](https://www.commodore.ca/manuals/128_system_guide/toc.htm) has 
an [excellent table](https://www.commodore.ca/manuals/128_system_guide/app-i.htm) listing all of the possible control 
codes, along with key mappings for those that have them (and whether those mappings work on the C64 or the C128, or both).

The key information from this are the following combinations:

| `CTRL key` | Purpose | Map? |
| :---: | :---- |:----:|
| **`CTRL C`** | STOP |  no  |
| **`CTRL E`** | Set text color to white |  no  |
| **`CTRL H`** | Disable SHIFT/C= toggle | YES  |
| **`CTRL I`** | Enable SHIFT/C= toggle | YES  |
| **`CTRL J`** | Carriage return |  no  |
| **`CTRL M`** | RETURN |  no  |
| **`CTRL N`** | Go to lowercase | YES  |
| **`CTRL Q`** | Cursor down |  no  |
| **`CTRL S`** | HOME |  no  |
| **`CTRL T`** | DEL |  no  |
| **`CTRL /`** | Set text color to red |  no  |
| **`CTRL ]`** | Cursor right |  no  |
| **`CTRL ↑`** | Set text color to green |  no  |

I will admit my choices as to which of these to map and which to ignore are arbitrary, but it is my mapping, and here’s what 
I’ll add to my vkm:

```
dead_circumflex 4 1 4096    # option I = CTRL I 
abovedot        3 5 4096    # option H = CTRL H 
dead_perispomeni 4 7 4096   # option N = CTRL N
```

And *now* I’m done with my keyboard map.

## The Final Mapping

The final mapping looks like this:

<span class="eimg">
![Final mapping of macbook keyboard to VICE emulator Commodore 64 keyboard](https://github.com/user-attachments/assets/1c9baa95-931a-4afd-a826-bd0deec1ad9f)
</span>

You can find the final VKM file [hosted on Github](https://example.com/todo).

## In Summary

With the journey to create the VKM done, in the next episode I'll summarize everything into a general how-to guide.
