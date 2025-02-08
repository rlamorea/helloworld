---
layout: post
title:  "Vice's Vexing VKM - How to Create a VICE Keyboard Mapping File"
date:   2025-02-03 18:59:00 -0800
categories: challenge vice-vkm
tags: retro-computing vice emulators commodore
---

# VICE’s Vexing VKM (Part 7)

## A How-To Guide for Creating and Editing VICE Keyboard Mapping Files 

[VICE](https://vice-emu.sourceforge.io/) is an emulator for Commodore 8-bit computers, from the first PETs through to 
the Commodore 128, and everything in between.

<span class="eimg">
![VICE Emulator Logo](https://github.com/user-attachments/assets/871f44fb-a7a9-4643-bd06-20fcfd305906)
</span>

While first experimenting with VICE, I found that I was having trouble typing using my macbook, and after 
getting very frustrated, I set out to put together a keyboard mapping that would let me type efficiently 
on the emulated computers without having to do any significant re-training of my fingers.

> Technically this is part 7 in a [series](/series/vice-vkm.html) that started with 
> [making a plan](/challenge/vice-vkm/2024/12/06/vices-vexing-vkm-1-planning-a-keymap.html).
>
> Then I went through a long and involved process to learn how to make that mapping and find what did and didn’t work.
> In the end I got what I wanted, and enough understanding that I think I can create almost any mapping I might need in the future.
>
> In this article I will summarize how you can do this as well.

## STEP 1: Copy an Existing VKM File

Of course, in order to copy the file, you’re going to have to find out where it is.

You should be able to do this opening preferences.

<span class="eimg">
![VICE preferences menu](https://github.com/user-attachments/assets/8f96104c-126c-4f23-b005-68e14719d785)
</span>

And then selecting input devices.

<span class="eimg">
![VICE preferences panel](https://github.com/user-attachments/assets/a1a76a8e-40d9-46db-a3fc-fb2da9bcdef7)
</span>

Under input devices you'll see an option for keyboard settings.

<span class="eimg">
![VICE Keyboard Settings Configuration](https://github.com/user-attachments/assets/bc0a4f40-03a5-4720-a5b5-91d67780a3fe)
</span>

Here there are choices for the type of mapping — positional or symbolic, or custom files.

You should be able to click the “file viewer” option for the custom files and that 
file selection window should tell you where the file is stored.

<span class="eimg">
![VICE keyboard file selector](https://github.com/user-attachments/assets/c141e775-79d2-476d-9c25-5e9b4a17feec)
</span>

Note that each emulator variant has its own files, so be sure you are in the folder appropriate 
to the emulated machine you want, be it a PET, or a Commodore 64.

The most likely file you want to copy is the `gtk3_sym.vkm` file. There is one of these for every emulated machine.

Though you may find that some simply include another vkm file in the folder, and in those cases, it is that 
included vkm that you want to copy.

### VKM File Tour 

Each vkm file is going to have a long header block of comments. Comment lines start with `#` signs.

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
# Joystick direction values:
# 0      Fire
# 1      South/West
# 2      South
# 3      South/East
# 4      West
# 5      East
# 6      North/West
# 7      North
# 8      North/East
#
# Joyport keypad key layout:
# --------------------------
# |  0 |  1 |  2 |  3 |  4 |
# --------------------------
# |  5 |  6 |  7 |  8 |  9 |
# --------------------------
# | 10 | 11 | 12 | 13 | 14 |
# --------------------------
# | 15 | 16 | 17 | 18 | 19 |
# --------------------------
#
# When a bigger spaced key is used,
# it uses the upper left most key value.

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

And then a short set of directive lines starting with exclamation points. You need to make sure these lines 
remain unchanged and are not deleted in your copy.

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

And eventually coming to the actual mapping lines themselves.

```
#####################
# |Bit 0| DEL |Retrn|C_L/R|  F7 |  F1 |  F3 |  F5 |C_U/D|

BackSpace       0 0 8
Delete          0 0 8
Insert          0 0 1
Return          0 1 8
Right           0 2 8
KP_F4           0 2 8
Left            0 2 1
F7              0 3 8
F8              0 3 1
F1              0 4 8
KP_F1           0 4 8
F2              0 4 1
KP_F2           0 5 8
F3              0 5 8
F4              0 5 1
F5              0 6 8
KP_F3           0 6 8
F6              0 6 1
Down            0 7 8
Up              0 7 1
```

If you want to make small changes, you can edit these lines directly. If you plan on making an entirely new map, 
then it might be better to delete these lines and then methodically recreate them — as I did for my map.

### Map Line Breakdown

```big
Q 7 6 8
```

Each mapping line has four parts:
1. A keycode (Q)
2. A row (7)
3. And column (6) 
4. And a shift-flags value (8)

### Getting Keycodes

The keycode is a name for the key on the host computer — in my case that would be my macbook.

The name of the key, however, is not always obvious — perhaps even when it seems like it is.

This is where the keyboard debugger comes in. Back in the keyboard preferences there is an option 
to enable keyboard debugging on the statusbar.

<span class="eimg">
![VICE Keyboard Settings Configuration](https://github.com/user-attachments/assets/bc0a4f40-03a5-4720-a5b5-91d67780a3fe)
</span>

Now when you are in VICE, every key you press is going to show up in the keyboard debugger.

<span class="eimg">
![VICE Keyboard Debugger](https://github.com/user-attachments/assets/1df4612f-bf83-4ce2-bbe2-6fa2be62d7c7)
</span>

Some examples are:

So if I press the minus key, we see minus in the debugger.

<span class="eimg">
![VICE Keyboard Debugger - minus key](https://github.com/user-attachments/assets/26bd965e-9c84-4f8b-bbce-e6fc240456d8)
</span>

If I press the letter q, we see q. Notice that it is lowercase. That is important. Keycodes are case sensitive.

<span class="eimg">
![VICE Keyboard Debugger - q key](https://github.com/user-attachments/assets/c1c287cc-1853-43d9-919d-82d719f04f7e)
</span>

And you can see why if I press shift and q together. See that the keyboard debugger is showing Shift_L for the 
shift key pressed, and also a capital Q.

<span class="eimg">
![VICE Keyboard Debugger - capital or shifted Q key](https://github.com/user-attachments/assets/50b7b6e3-7cfb-49e7-ac5f-ac9a4bbd5c1c)
</span>

When it comes to keycodes, a capital Q is not the same as a lowercase q — and even though the debugger shows that 
the left shift key is pressed, the capital Q keycode already implies that shift is pressed, so in the VKM file, 
a map to capital Q means “shift and q together”.

Sometimes the keycode is obvious, like if I do shift-2 to get an at symbol, nicely named at.

<span class="eimg">
![VICE Keyboard Debugger - shift 2, or at, key](https://github.com/user-attachments/assets/1794a202-8d8e-4a73-9c3d-56081b055d78)
</span>

But shift 1, the exclamation point, has a keycode of exclam.

<span class="eimg">
![VICE Keyboard Debugger - shift 1, or exclamation mark, key](https://github.com/user-attachments/assets/91772daa-68f7-4a4f-b86c-41fcedf696c6)
</span>

It isn’t only shift that can create different keycodes. On the mac, the option key will create new keycodes, 
like option 3 which is a british pound sign with a keycode of sterling.

<span class="eimg">
![VICE Keyboard Debugger - option 3, or pound sterling, key](https://github.com/user-attachments/assets/becf8e3c-1425-4ade-b44e-c924935c1ffa)
</span>

As with shift, even though the keyboard debugger shows Alt_L — which is the keycode for the left option key — 
the keycode name sterling implies the option key is pressed.

This isn’t always the case though — if I press the return key, getting a keycode of Return, 
with a capital R.

<span class="eimg">
![VICE Keyboard Debugger - option Return key](https://github.com/user-attachments/assets/83c9e89f-1551-4251-9fb0-ab667cd429ca)
</span>

If I press shift and return at the same time, the debugger shows the Shift_L and Return keycodes. 
This time the shift isn’t generating a new keycode.

<span class="eimg">
![VICE Keyboard Debugger - shift Return key](https://github.com/user-attachments/assets/abde6f18-b696-4203-b706-9d66045d671c)
</span>

In cases like this, you’ll need to handle things a little differently, and we’ll get to that shortly.

Finally, you should know that some keys are unmappable. One example of this is the mac’s 
function — or `fn` — key, which even though I am pressing it doesn’t show up in the keyboard debugger.

However, other keys are modified by the fn key, so if I press the “dim screen” or F1 key when the fn key is pressed, 
the keyboard debugger shows F1.

<span class="eimg">
![VICE Keyboard Debugger - F1 key](https://github.com/user-attachments/assets/595dfbcf-1a85-455f-b44d-66d065c997fa)
</span>

And a warning — some of the option-key combinations on the mac produce an unmappable code, like option-W which 
shows a code of U+2211, but as far as I can tell there is no way to map this.

<span class="eimg">
![VICE Keyboard Debugger - option w key](https://github.com/user-attachments/assets/8b410e69-c36d-40a9-a3b9-e032f9c2f508)
</span>

### Row & Column

Now we need to understand where the row and column come from.

In each vkm file there is a keyboard matrix.

```
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
```

This matrix is different for each type of emulated machine — which is one reason why it is important to get copy 
your VKM file from the right place. Almost every key on the emulated — in this case the Commodore 64’s — keyboard 
is in this matrix.

So you find the key you want to map to, let’s say 0, which is row 3 snd column 4.

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

I did say almost every key. A few keys are handled specially.

These use a negative row value. Like the RESTORE key.

```
# Negative row values:
# 'keysym -1 n' joystick keymap A, direction n
# 'keysym -2 n' joystick keymap B, direction n
# 'keysym -3 0' first RESTORE key
# 'keysym -3 1' second RESTORE key
# 'keysym -4 0' 40/80 column key (x128)
# 'keysym -4 1' CAPS (ASCII/DIN) key (x128)
# 'keysym -5 n' joyport keypad, key n (not supported in x128)
```

Which uses a row of -3 and a column of 0.

### SHIFTFLAGS

Finally we need to understand how to assign SHIFTFLAGs for each mapping.

The VKM documents these as well, though somewhat confusingly.

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

These flags are used to handle all the special cases needed when it comes to adding or removing keys like 
SHIFT or option on the host keyboard, or SHIFT and Commodore on the emulated Commodore computer.

These combine together as needed.

From the experience I’ve gained, I can offer this series of steps.

#### Common Keys

For common keys — that is ones that aren’t modifying other keys like SHIFT or option — we can follow this,
starting with a ShiftFlags value of 0.

##### Step 1: Check for multiple mappings

The first step is to check whether there will be more than one mapping for this key.

For example, the key might be mapped for a regular keypress, but have an additional mapping when combined with control.

If there is going to be another mapping to come later in the file, add `32`.

> And from my experience, it is important to map the simplest thing first — in order this 
> should be the unmodified key, then the shifted key if any, then any further key combinations. 
> I don’t know why this is.

##### Step 2: Host Key Conditions

Next we look at the key or keys being pressed on the host — again macbook for me — keyboard.

###### 2.1 Check for shifted/unshifted case

If the host key can be both unmodified and shifted AND will map to the same emulated key either 
unmodified or shifted, then add `8` — and in this case no other SHIFTFLAG values should be added,
so proceed to the end (step 4).

> This is a little tricky. Think of the case above where the Return key had the same keycode whether 
> the shift was pressed or not. If you map return to return, and shift return to shift return, then 
> this is the flag to use.

###### 2.2 Check for unmodified

Otherwise, If the key is being pressed alone on the host, do nothing. Proceed to Step 3.

###### 2.3 Check for Shift

If the key is combined with the shift key, add `128`.

> Note that this is not commonly used since most keys have a different keycode when shifted.

###### 2.4. Check for Alt (option)

If the key is combined with the alt — or option on the mac — key, add `512`.

> Remember these can be combined, so a mapping of a key combined with both alt and shift 
> would add both 128 and 512 (or 640).*

###### 2.5 Check for Control

If the key is combined with the control key, add `1024`.

That’s all our options for the host side of things.

#### Step 3: Emulated Key Combinations

Finally we look at what is going to happen with the key on the emulator. Remember that the 
emulated key is determined not by the keycode, but by the row and column from the keyboard matrix.

##### 3.1 Check for de-SHIFT-ing

If the emulated key is not shifted, but the host key is, add `16`.

> This means that if you mapped shift 2 — or the @ symbol — to the unmodified @ key on 
> the emulator you need this flag added in to “de-shift” the key.

##### 3.2 Check for unmodified

If the emulated key is unmodified, do nothing further. Proceed to Step 4.

##### 3.3 Check for SHIFT

If the emulated key is shifted, add `1`

> It is important to remember that for many keycodes, the shift key is implied. In our example, the 
> keycode is “at”, you just have to know that means the shift key is pressed on the host.

##### 3.4 Check for Commodore

If the emulated key is combined with the Commodore key, add `2048`.

> One thing to be careful of is if you combine this flag with either 1 (or shifted on the emulator), 
> 8 (carry shift over from host), or mapped to a shifted key on the host you can cause the Commodore to 
> change character sets from its normal uppercase/graphics set to upper/lower case. You may need to add 
> in the “de-shift” flag of 16 to fix this.

##### 3.5 Check for CTRL

If the emulated key is combined with the CTRL key, add `4096`.

#### Step 4: All Done

And that should handle most cases.

### Special Keys

Another use of the SHIFTFLAGs value is to tell VICE about certain special keys. These flag values are:

- `2` if the key is the left SHIFT key
- `4` if the key is the right SHIFT key 
- `64` if the key is the SHIFT LOCK key, which is typically mapped to the row and column of the left SHIFT key but also has this flag.
- `8192` if the key is mapped to is the Commodore key.
- `16384` if the key is mapped to The CTRL key.

### Good to Go

And that’s pretty much everything you need to know to create or edit your own mappings.

If you are going to create a full map, I would recommend going in with a plan. You can check out my planning 
phase in the first episode of this series.

## My Mapping

Here is what my final mapping to use VICE on a macbook looks like.  It should also work fine for the mac if you 
ignore keys outside the core keyboard.

The VKM file for this mapping is available to [download from GitHub](https://github.com/RickSaysHelloWorld/VICE-vkm/blob/main/C64/gtk3_sym_c64_macbook.vkm):

### Row by Row

Let’s take a quick tour. If you want to understand better why the mapping is the way it is, there’s six prior 
episodes to watch if you wish.

#### Row 1

<span class="eimg">
![Macbook top row of keys](https://github.com/user-attachments/assets/2428a20d-1371-46de-bc67-615ea9985575)
</span>

We’ll start with the top row on the macbook. This is mostly mapping function keys, and a few extra keys 
from the Commodore 64 keyboard.

`F9` note the special negative row mapping for the RESTORE key.

```
## Macbook Row 1

Escape          7 7 8         # RUN/STOP
F1              0 4 0
F2              0 4 1
F3              0 5 0
F4              0 5 1
F5              0 6 0
F6              0 6 1
F7              0 3 0
F8              0 3 1
F9             -3 0           # RESTORE
#F11 - DO NOT USE! (reserved for mac)
F12             6 3 8         # HOME / CLEAR
```

#### Row 2

<span class="eimg">
![Macbook second row mapping plan for VICE](https://github.com/user-attachments/assets/20f1f271-7df5-4e6f-85e0-ff6911f9a4ea)
</span>

The second row is numbers and some symbols.

Due to differences in number of keys and what is on them, some of the graphic characters have been shuffled around.

```
## Machbook Row 2

grave           7 1 0         # ` = left arrow
asciitilde      6 6 1         # ~ = pi

1               7 0 32        # 1
1               7 0 3072      # control 1 = c= 1 (orange)
exclam          7 0 1         # !
exclamdown      7 0 4096      # option 1 = ctrl 1 (black)

2               7 3 32        # 2
2               7 3 3072      # control 2 = c= 2 (brown)
at              5 6 16        # @
trademark       7 3 4096      # option 2 = ctrl 2 (white)

3               1 0 32        # 3
3               1 0 3072      # control 3 = c= 3 (pink)
numbersign      1 0 1         # #
sterling        1 0 4096      # option 3 = ctrl 3 (red)

4               1 3 32        # 4
4               1 3 3072      # control 4 = c= 4 (dark gray)
dollar          1 3 1         # $
cent            1 3 4096      # option 4 = ctrl 4 (cyan)

5               2 0 32        # 5
5               2 0 3072      # control 5 = c= 5 (medium gray)
percent         2 0 1         # %
infinity        2 0 4096      # option 5 = ctrl 5 (purple)

6               2 3 32        # 6
6               2 3 3072      # control 6 = c= 6 (light green)
asciicircum     6 6 16        # ^ = up arrow
section         2 3 4096      # option 6 = ctrl 6 (green)

7               3 0 32        # 7
7               3 0 3072      # control 7 = c= 7 (light blue)
ampersand       2 3 1         # &
paragraph       3 0 4096      # option 7 = ctrl 7 (blue)

8               3 3 32        # 8
8               3 3 3072      # control 8 = c= 8 (light gray)
asterisk        6 1 16        # *
enfilledcircbullet 3 3 4096   # option 8 = ctrl 8 (yellow)

9               4 0 0         # 9
parenleft       3 3 1         # (
ordfeminine     4 0 4096      # option 9 = ctrl 9 (rvs on)

0               4 3 0         # 0
parenright      4 0 1         # )
masculine       4 3 4096      # option 0 = ctrl 0 (rvs off)

minus           5 3 32         # -
minus           5 3 3072       # control - = c= -
underscore      5 6 2064       # c= @

equal           6 5 32         # =
equal           5 0 1025       # control = = shift +
plus            5 0 16         # +

BackSpace       0 0 8          # inst/del
```

#### Row 3, 4, and 5

<span class="eimg">
![Planned mapping of macbook row 3 to VICE emulator](https://github.com/user-attachments/assets/aa28fce6-9044-4d90-8ed6-1cb09a57f9de)
</span>

The mac’s third row is mostly letters with some symbols.

```
## Macbook Row 3

Tab             7 4 0         # space
ISO_Left_Tab    0 2 1         # shift CRSR LEFT/right

q               7 6 32        # Q
q               7 6 3072      # control Q = c= Q
Q               7 6 1         # shift Q

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
dead_circumflex 4 1 4096      # option I = ctrl I (enable upper/lower switch)

o               4 6 32        # O
o               4 6 3072      # control O = c= O
O               4 6 1         # shift O

p               5 1 32        # P
p               5 1 3072      # control P = c= P
P               5 1 1         # shift P

bracketleft     5 5 33        # [
bracketleft     5 0 3072      # control [ = c= +
braceleft       6 0 2064      # c= pound

bracketright    6 2 33        # ]
bracketright    6 1 3072      # control ] = c= *
braceright      6 0 1         # shift pound

backslash       4 4 33         # shift M
backslash       6 0 1024       # control \ = pound
bar             5 3 8          # shift -
```

Same with the fourth row.

<span class="eimg">
![Planned mapping of macbook fourth row to VICE emulator](https://github.com/user-attachments/assets/a610506c-8cc3-436f-9ee0-ce3ce9cf24a2)
</span>

```
# Macbook Row 4

Caps_Lock       1 7 64         # shift lock

a               1 2 32         # A
a               1 2 3072       # control A = c= A
A               1 2 1          # shift A

s               1 5 32         # S
s               1 5 3072       # control S = c= S
S               1 5 1          # shift S
ssharp          1 5 4096       # option S = ctrl S (HOME)

d               2 2 32         # D
d               2 2 3072       # control D = c= D
D               2 2 1          # shift D
partialderivative 2 2 4096     # option D = control D

f               2 5 32         # F
f               2 5 3072       # control F = c= F
F               2 5 1          # shift F
function        2 5 4096       # option F = control F

g               3 2 32         # G
g               3 2 3072       # control G = c= G
G               3 2 1          # shift G

h               3 5 32         # H
h               3 5 3072       # control H = c= H
H               3 5 1          # shift H
abovedot        3 5 4096       # option H = ctrl H (disable upper/lower switch)

j               4 2 32         # J
j               4 2 3072       # control J = c= J
J               4 2 1          # shift J

k               4 5 32         # K
k               4 5 3072       # control K = c= K
K               4 5 1          # shift K

l               5 2 32         # L
l               5 2 3072       # control L = c= L
L               5 2 1          # shift L

semicolon       6 2 32         # ;
semicolon       5 6 1025       # control ; = shift @
colon           5 5 16         # :

apostrophe      3 0 33         # '
apostrophe      6 1 1025       # control ' = shift *
quotedbl        7 3 1          # "

Return          0 1 8          # Return
```

And the fifth row.

<span class="eimg">
![Planned mapping of macbook keyboard row 5 to VICE emulator](https://github.com/user-attachments/assets/df46dc44-fe76-4df5-8fe4-4f4bd5cbeeb5)
</span>

```
# Macbook Row 5

Shift_L         1 7 34         # shift (left)
Shift_L         1 7 3073       # control shift = c= shift (toggle upper/lower)

z               1 4 32         # Z
z               1 4 3072       # control Z = c= Z
Z               1 4 1          # shift Z
Greek_OMEGA     2 7 4096       # option Z = control X

x               2 7 32         # X
x               2 7 3072       # control X = c= X
X               2 7 1          # shift X

c               2 4 32         # C
c               2 4 3072       # control C = c= C
C               2 4 1          # shift C
ccedilla        2 4 4096       # option C = ctrl C (STOP)

v               3 7 32         # V
v               3 7 3072       # control V = c= V
V               3 7 1          # shift V

b               3 4 32         # B
b               3 4 3072       # control B = c= B
B               3 4 1          # shift B

n               4 7 32         # N
n               4 7 3072       # control N = c= N
N               4 7 1          # shift N
dead_perispomeni 4 7 4096      # option N = ctrl N (to lower)

m               4 4 32         # M
m               4 4 3072       # control M = c= M
M               4 4 1          # shift M
mu              4 4 4096       # option M = ctrl M (RETURN)

comma           5 7 0          # ,
less            5 7 8          # <

period          5 4 32         # .
greater         5 4 8          # >

slash           6 7 0          # /
question        6 7 8          # ?

Shift_R         6 4 36         # shift (right)
Shift_R         6 4 3073       # control shift = c= shift - toggle upper/lower
```

#### Row 6

<span class="eimg">
![Planned mapping of macbook keyboard row 6 to VICE emulator](https://github.com/user-attachments/assets/dc9dc390-4531-4c3f-a361-484cdff8d99e)
</span>

The bottom row is mostly those keys that modify the other keys.

Note though that the mac’s control key acts like the Commodore key, but doesn’t actually register as that key on the Commodore 64.

If you need to actually send the Commodore keypress itself to VICE, use the left command key.

That leaves the option key acting as CTRL. Both of them, in fact.  Then the space bar and the cursor keys

```
# Macbook Row 6

# fn - used to select function / special key actions
Control_L       7 5 0          # acts as C= key in terms of most of the key mappings,
                               # but all are explicitly defined above because it will
                               # interfere with some complicated mappings otherwise
                               # if you need the C= key specifically use left command
Alt_L           7 2 16384      # acts as CTRL key, but generates special keycodes
                               # all mapped above
Meta_L          7 5 8192       # acts as c= key if absolutely necessary, but
                               # not recommended to use (use left control)
space           7 4 0          # space
# Meta_R - ignored
# Alt_R - acts as CTRL key as well
Left            0 2 1          # shift-CRSR LEFT/right
Up              0 7 1          # shift-CRSR UP/down
Down            0 7 0          # CRSR up/DOWN
Right           0 2 0          # CRSR left/RIGHT
```

### Wrapping Up

That’s my mapping. I find it works very well — for me at least. I can type almost as fast in VICE as I can 
normally on my macbook.

You can get it here:

[https://github.com/RickSaysHelloWorld/VICE-vkm/blob/main/C64/gtk3_sym_c64_macbook.vkm](https://github.com/RickSaysHelloWorld/VICE-vkm/blob/main/C64/gtk3_sym_c64_macbook.vkm)

In fact, the only reason I saw “almost” is because VICE can lag a little, so sometimes I can outtype the emulator.

But that’s honestly a good problem to have.

I hope you got something useful out of this, and I hope you’ll join me when I come back to the VKM series.

Because we still need to look at how to take my plan and what’s been learned and apply it to build map files 
for all the other emulated Commodore computers.

And — small teaser — that will mean dealing with some really wild keyboards.
