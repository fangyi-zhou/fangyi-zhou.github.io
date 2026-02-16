---
math: true
title: Vibe Coding Pintos
date: 2026-02-07
draft: true
---

## Motivation

In my second year of my computer science undergraduate study, there were two
major group projects: compiler and operating system.
These group projects left some significant memories even though the projects were
almost a decade ago, especially Pintos, where I spent more than a few long
nights with colleagues in the computer lab debugging random race conditions.
The departmental student society gave out ceremonial glasses with "Pintos
Survivor" on them, which underscores the importance of Pintos during the
university life.

We know that recently Anthropic claimed to have "vibe coded"[^1] a compiler from
scratch (See [code](https://github.com/anthropics/claudes-c-compiler),
[post](https://www.anthropic.com/engineering/building-c-compiler)).
After I read the article, the natural question came to my mind:

{{< callout text="Can we vibe code Pintos too?" >}}

[^1]: People would probably disagree with this term, but the engineer literally
    sent the agents to work and walked away.

The premise of this experiment is to figure out whether it is possible to a
hypothetical student to complete the coursework with modern AI technology.
I am assuming a basic level of AI knowledge (not using MCP or skills, for
instance), and will give basic guidances without much course-correcting.

## What is Pintos?

Pintos is an operating system that is famous enough to have its own [Wikipedia
page](https://en.wikipedia.org/wiki/Pintos).
In short, it's an operating system that is designed for teaching OS concepts to
computer science students, originally created at Stanford.
The students are given a basic OS codebase, with 4 main tasks to implement
threading, userland & system calls, virtual memory, and file systems.
Apart from the codebase, a self-contained manual was provided that explains
existing components of the OS, and describes the tasks that are required to
implement.

The coursework requires students to provide implementations and design docs,
and is evaluated by an automatic grader (with testsuite provided), and a
design/code review.
The version of this coursework I did during university had minor abridgements
from the original one (we didn't have the file system project), and you can
find the coursework via [this
link](https://www.doc.ic.ac.uk/~mjw03/OSLab/pintos.pdf).
In this exercise, I'm also going to complete the first 3 projects (threading,
user program, and virtual memory).

## Setting up

In this exercise, I am downloading Pintos from the Stanford course
website, and using the reference docs
[here](https://www.scs.stanford.edu/25sp-cs212/reference/).
So I cloned the repository from http://cs212.scs.stanford.edu/pintos.git, and
downloaded the documentation to `doc` directory.

I'm using Claude Code with Opus 4.6 on mac. I have a Pro subscription, which
means I will hit limits pretty often and have to cool down.
That comes with a side-perk that I can number the vibe coding sessions by when
I hit the usage limit. So let's get into vibe coding.

## Session 0: Setting up dev environment and `CLAUDE.md`

I opened Claude Code and typed `/init`, and patiently waited for Claude to spit out
a nice summary file.
Since I was running on mac, a specific cross-compiler is needed to build for
x86 (Pintos runs on an 80x86 CPU, whatever that means).
Claude was able to install that for me, as well as QEMU the emulator. It was
also able to patch out problematic parts of the `Makefile` and make Pintos build on
my machine.
This was something I didn't manage to achieve during university (partially
because I was lazy to figure out),
so I had to ssh into a Linux machine from the university which had the
environment pre-configured.

Now that Pintos is able to run on my machine, I asked Claude to set up a quick
CI script, to make sure code builds and is well-formatted. Claude was able to
set up `clang-format` config, and spit out a bash script for CI checks.

I asked Claude to identify a short list of tests that can be run quickly, as
this was pointed out in the Anthropic article as something that needs to be
"designed around".
This is because LLMs don't have a sense of time, but we humans do.
Claude identified some tests that are "supposed" to be fast, which looked
plausible enough to me.

Claude happily wrote them down in the updated `CLAUDE.md`, and we're ready to
do some serious coding.

I turned on plan mode to ask Claude to create a plan for Project 1 (Threading).
I know that there are 4 sub-tasks, and asked Claude to identify them (which
Claude was perfectly able to do).
At this time, I am running low on usage quota, so I asked Claude to save the
plan to a file.
Funnily enough, I told Claude to "Commit the plan in the codebase", which
Claude had difficulty understanding and wanted to kick off the execution.

> Interrupted · What should Claude do instead?
>
> Commit the plan in the codebase
>
> ∴ Thinking...
>
> The user wants me to commit the plan into the codebase. Let me first check what's currently in the git status and then create a commit with the implementation plan.
>
> Wait, the user said "Commit the plan in the codebase" - they want me to save the implementation plan as a file in the codebase and commit it. Let me create the plan file and commit it.
>
> Actually, re-reading the conversation, the user had a detailed implementation plan they shared. They want me to commit this plan document to the codebase. Let me create it as a file and commit it.
>
> ...

After a minute of thinking, Claude saved the plan to a
[file](https://github.com/fangyi-zhou/vibe-coded-pintos/blob/master/plans/PROJECT1-PLAN.md),
and it's time to go on a break.

## Session 1: Let's get threading going

Claude splitted the project into 4 tasks: alarm clock, priority scheduling,
priority scheduling, and MLFQS scheduler.
Since this is the first project, the coursework provided ample guidance for
this project to be a warm-up task for students, although the
[requirement](https://www.scs.stanford.edu/25sp-cs212/pintos/pintos_2.html#SEC23)
divided the project into 3 parts instead of 4.

Claude hinted to me that I shall install Clang LSP, which I happily did so, and
then Claude started to generate code for the tasks.
For the alarm clock task, it took only 2 minutes to complete. and verified the
implementation by running test.
Then, Claude quickly went on to implement priority scheduling, which I had to
interrupt Claude and asked it to make a commit for completing the previous
task.

After that, Claude continued with the implemented and ran the tests for
priority scheduling.
Unfortunately, the tests did not pass on the first go --- Claude implemented
the priority ordering the other way around, which Claude quickly identified the
error and inverted the sorting order.

At this point, Claude was quite happy to continue pumping out code. It quickly
completed the third task, made a commit, and continued with the final task.
Again, it look took a few minutes for Claude to finish. I remember taking quite
a long time during university time, having to go through corner cases and
drawing diagrams.

The final task, MLFQS (Multi-Level Feedback Queue System) was relatively
eaasy to implement, but it took quite a long time to run the test.
Unfortunately, running these tests in parallel took too much resources, and the
Claude code process got `pkill`ed.
However, it was able to recover and completed on the second try.

At this point, the last thing is the design document. I actually forgot to
download the design document template and place it in the git repository, so
Claude went everywhere to look for the template. I noticed it was probably my
fault, interrupted Claude, downloaded the templates, and prompted Claude to
continue. Claude produced a plausible [design
doc](https://github.com/fangyi-zhou/vibe-coded-pintos/blob/master/doc/threads.tmpl).

As we are towards the completion of the project, I asked Claude to update the
CLAUDE.md file, and checked usage. For this session, we have used around 2/3 of
the session usage.

Similar to last time, I kicked off a plan mode prompt asking Claude to plan for
the next project. This time, I reminded Claude to commit after each task, and
to write the design docs, then Claude went on exploring the codebase and the
requirements.
I was surprised by the token usage for the planning mode, as
it did not manage to complete within the remaining 1/3 session usage limit.
I turned on some extra usage, and invited Claude to finish, here is the
[plan](https://github.com/fangyi-zhou/vibe-coded-pintos/blob/master/plans/project2-plan.md).
