---
layout: post
title:  "AI Code Autocomplete's Unexpected Flaw"
date:   2025-02-06 16:14:00 -0800
categories: on-the-job
tags: ai coding
---

# AI Code Autocomplete's Unexpected Flaw

Lately, I’ve been using [WebStorm](https://www.jetbrains.com/webstorm/) for my personal projects.

Like all of JetBrains’ latest IDEs, it incorporates an AI-powered 
“[full line autocomplete](https://blog.jetbrains.com/blog/2024/04/04/full-line-code-completion-in-jetbrains-ides-all-you-need-to-know/)” 
feature.

<span class="eimg">
![Jetbrains AI Autocomplete marketing](https://github.com/user-attachments/assets/e1fba3b5-636f-45be-a916-83e2958c1dba)
</span>

I’ve found it pretty useful.

It also worries me a great deal.

## Breaking It Down

You might think my fear comes from the hype around how developers are all going to be replaced by AI.

<span class="eimg">
![AI Hype Headline](https://github.com/user-attachments/assets/2971c1f7-f5ba-41e9-bf90-660c6f61861a)
</span>

That is certainly what the AI hype people want me (and you) to think. It is certainly the narrative the press is pushing.

But no, that isn’t what worries me. In fact, having spent time with AI-assisted coding, I can say that — 
at least for the moment — that really doesn’t even register.

So what worries me so much?

Let me give a breakdown of my experience with AI autocomplete. This is not a scientific study, just an estimate of what I’ve seen.

* 5% of the time: AI does something AMAZING — filling in code that saves me time looking up how to do something, or anticipating what I need to a perfect degree.
* 45% of the time: AI does the obvious, following a pattern I’ve established and saving me *significant* time retyping some common pattern.
* 40% of the time: AI goes off the rails, suggesting code that is *blatantly* wrong or just not applicable to what I need, and leaving me to just type it all in manually like some sort of neanderthal.
* 10% of the time: AI fills in something that *looks* just fine. **But it actually isn’t.**

<span class="eimg sml">
![AI Autocomplete Success Rate Breakdown](https://github.com/user-attachments/assets/8cd3613e-786e-46f7-bb6c-d5de89c648a0)
</span>

It is that 10% that worries me.

## Saved by Unit Tests

The projects I am working on — ones that I will be the first to admit are pretty trivial in the grand scheme of things — are being built with extensive unit testing.

It was these unit tests that started revealing those sneaky AI-assisted failures.

I’d write up some tests, get them all working, then go on and work on new features.

```
myproject.test.js
* * * * * * * * * * * * * * * * * * * * (20/20)
```

Suddenly I’d get test failures.

```
myproject.test.js
* * * x * * * * x * * x * * * * * * * * (17/20)

  FAIL "Ensure func() works on a 4-item array"
    Expected values to be strictly equal: (is)
    
      ++2
      --1 
```

Digging into why revealed the problem: I screwed something up.

## A Troubling Pattern

Of *course* I screwed something up. That’s the whole point of unit testing, right?

The tests catch when you introduce bad code in a way that has an unexpected side effect.

No big deal, right?

But what I started to notice was the *reason* for these unit test failures was often coming from code that, 
once I’d debugged down to the root cause, didn’t quite make sense.

That’s when it hit me:

**This was code added by AI-autocomplete.**

It was code that *looked* correct at a quick glance, and sometimes even at a deeper glance. It was code that 
looked good enough to be quickly accepted when autocomplete suggested it and easy to move on since the AI 
had “done its job”.

## Playing To Our Weaknesses

I want to be absolutely clear that it is completely possible for me to make the same kinds of mistakes. 
Certainly not all of the introduced bugs came from the AI “helping” me. I had my share of mistakes to work through.

But the thing is that AI autocomplete and AI code generation amplify the opportunity to make these kinds of mistakes.

Autocomplete works by autofilling a suggestion for an entire code line, sometimes when you type a single 
letter (sometimes not even that). If you’ve got code that is using consistent patterns (and you will), 
the autocomplete is going to suggest the next line in that pattern, helpfully adjusted to fit the new situation 
(such as by using a different variable name).

You hit the TAB key and that suggestion is now code. It is just so convenient. Seriously, I really like this feature.

But it is also very fast — there is no way you are reading that newly accepted code carefully. If it *looks* good and 
the AI has been doing a good job for you so far, you accept it. And once you accept it, you’re on to the next 
line of code.

<span class="img sml">
![Example of how focus points are very limited](https://github.com/user-attachments/assets/e4c39e7a-f045-4e81-8c28-45968bef1b9c)
</span>

Here’s the irony of it all — your brain has similar processes to that AI autocomplete. Given a brief glimpse at something, 
your brain is going to fill in the gaps. The video clip above is from a [NOVA episode dealing with perception](https://www.youtube.com/watch?v=HU6LfXNeQM4). 
You are seeing a screen where the text is only revealed to a person's eye. The rest is filled with 'X's. To that person
the entire page is visible -- all those X's go away because everywhere they look they see text, and everywhere else their
brain fills in the text they've already seen.

So that AI-generated code is flashing in front of your eyes, and your brain is telling 
you “that looks fine” based on code that it autocompleted itself based on what was already on your mind.

To fully understand this you need to understand something about human vision. The key thing is that what you see 
is a construct of the outside world, not exactly the world. This is why many optical illusions work, and why 
magicians can fool you with slight-of-hand. In a moment’s glance, much of what you “see” is what your brain is 
filling in to complete the picture of the actual tiny bit of focused vision you really are seeing.

Now think about what this means when you are writing code and AI autocomplete throws up a suggestion of, say, 
20 or even 50 characters. If you are like almost all developers, you are going to scan that very quickly — and 
your brain is going to say “looks good\!”.  Except your actual vision might’ve seen only a quarter, or even a tenth, 
of that suggestion. The rest was filled in — filled in with the code your brain already had in ready memory.

If the AI did a good job, you’re golden. If not, then it was completely invisible to you. Worse, it’s already 
“signed off” and you’ve moved on. In your mind there’s good code ready to run — and we all know how hard it 
can be to shake the fallacy of “I know that code was good when I wrote it” line of thinking when hunting down bugs.

Furthermore, when you are typing code you actually have two systems doing on-the-fly error correcting for you. 
The first is vision, but the second is your muscles. You are thinking code and sending signals to your fingers to 
type that in — and how many times have you realized you typed something wrong just by something not “feeling” right 
on the keyboard? It doesn’t have a huge impact, but it does have *some* impact. Vision and muscle signals both 
are helping to keep you typing true.

But AI autocomplete (and in this case, any autocomplete) bypasses your typing muscles, so that’s not going to be of any help.

So let’s summarize this:

> **The time you typically take to accept a line of suggested code is short enough that it is highly likely 
> the code you are “accepting” was at least partially made up in your head. You saw what you wanted to see, 
> and it looked just fine. And there’s no backup mechanism in place that might raise concern.**

The problem isn’t that AI-autocomplete can make mistakes. It is that AI-autocomplete is implemented in a way 
where your ability to spot those mistakes is compromised.

## Worrisome Code

It is in this zone of “good-looking code” that, to me, things get worrisome.

AI autocomplete is a mechanism that is *guaranteed* to sneak bugs into your code. It may not be many, but it will be *some*.

So let’s look at some truths of software development:

* Developers tend to trust “their” code more than they should when committing it
* Test suites are *never* as effective as they should be
* Code reviewers are just as prone to overlooking subtle bugs as anyone else

Ask yourself:

> **How many of these subtle bugs introduced by AI autocomplete and   
> other generated AI coding methods are slipping into production code?**

Are you worried too?
