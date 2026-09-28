---
math: true
title: Vibe Coding Pintos
date: 2026-09-28
---

## Motivation

In my second year as a computer science undergraduate, there were two
major group projects: a compiler and an operating system.
These group projects left some significant memories even though the projects were
almost a decade ago, especially Pintos, where I spent more than a few long
nights with colleagues in the computer lab debugging random race conditions.
The departmental student society gave out ceremonial glasses with "Pintos
Survivor" on them, which underscores the importance of Pintos during the
university years.

Recently[^2], Anthropic claimed to have "vibe coded"[^1] a compiler from
scratch (See [code](https://github.com/anthropics/claudes-c-compiler),
[post](https://www.anthropic.com/engineering/building-c-compiler)).
After I read the article, a natural question came to mind:

{{< callout text="Can we vibe code Pintos too?" >}}

[^2]: At the time of writing, Feb 2026.
[^1]: People would probably disagree with this term, but the engineer literally
    sent the agents to work and walked away.

The premise of this experiment is to figure out whether it is possible for a
hypothetical student to complete the coursework with modern AI technology.
I am assuming a basic level of AI knowledge (not using MCP or skills, for
instance), and will give basic guidance without much course-correcting.

{{< callout type="alert" text="Important Note on Outdatedness" >}}

I started working on the project half a year ago, when I was still trying to
figure out how to integrate AI into my daily workflow. Unfortunately, another
half-year has passed, and I still haven't finished writing this post (and I
don't plan to finish the rest).
A lot of things that might have made sense then sound absurd now.
Nowadays, models have become smarter, and it's possible for LLMs to complete
much more complex tasks without human guidance.
AI systems are working to solve the next hard problems in science, and the
problem described here sounds almost trivial in retrospect.

## What is Pintos?

Pintos is an operating system that is famous enough to have its own [Wikipedia
page](https://en.wikipedia.org/wiki/Pintos).
In short, it's an operating system that is designed for teaching OS concepts to
computer science students, originally created at Stanford.
The students are given a basic OS codebase, with four main tasks to implement:
threading, userland & system calls, virtual memory, and file systems.
Apart from the codebase, a self-contained manual was provided that explains
the existing components of the OS and describes the tasks students are required
to complete.

The coursework requires students to provide implementations and design docs,
and is evaluated by an automatic grader (with a test suite provided) and a
design/code review.
The version of this coursework I did at university had minor abridgements
compared with the original (we didn't have the file system project), and you can
find the coursework via [this
link](https://www.doc.ic.ac.uk/~mjw03/OSLab/pintos.pdf).
In this exercise, I'm also going to complete the first three projects (threading,
user program, and virtual memory).

## Setting up

In this exercise, I downloaded Pintos from the Stanford course
website and used the reference docs
[here](https://www.scs.stanford.edu/25sp-cs212/reference/).
I cloned the repository from http://cs212.scs.stanford.edu/pintos.git and
downloaded the documentation to the `doc` directory.

I'm using Claude Code with Opus 4.6 on a Mac. I have a Pro subscription, which
means I will hit limits pretty often and have to cool down.
One side benefit is that I can number the vibe-coding sessions by when I hit
the usage limit. So let's get into vibe coding.

## Session 0: Setting up dev environment and `CLAUDE.md`

I opened Claude Code, typed `/init`, and patiently waited for Claude to spit out
a nice summary file.
Since I was running on a Mac, a specific cross-compiler was needed to build for
x86 (Pintos runs on an 80x86 CPU, whatever that means).
Claude was able to install that for me, as well as QEMU, the emulator. It was
also able to patch out problematic parts of the `Makefile` and make Pintos build on
my machine.
This was something I didn't manage to achieve during university (partly
because I was too lazy to figure it out),
so I had to SSH into a university Linux machine that had the
environment pre-configured.

Now that Pintos is able to run on my machine, I asked Claude to set up a quick
CI script, to make sure code builds and is well-formatted. Claude was able to
set up `clang-format` config, and spit out a bash script for CI checks.

I asked Claude to identify a short list of tests that can be run quickly, as
this was pointed out in the Anthropic article as something that needs to be
"designed around".
This is because LLMs don't have a sense of time, but we humans do.
Claude identified some tests that were "supposed" to be fast, and they looked
plausible enough to me.

Claude happily wrote them down in the updated `CLAUDE.md`, and we were ready to
do some serious coding.

I turned on plan mode to ask Claude to create a plan for Project 1 (Threading).
I knew there were four subtasks and asked Claude to identify them. Claude
identified them correctly.
At this time, I was running low on usage quota, so I asked Claude to save the
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

Claude split the project into 4 tasks: alarm clock, priority scheduling,
priority donation, and MLFQS scheduler.
Since this is the first project, the coursework provided ample guidance for
this project to be a warm-up task for students, although the
[requirement](https://www.scs.stanford.edu/25sp-cs212/pintos/pintos_2.html#SEC23)
divided the project into 3 parts instead of 4.

Claude hinted to me that I should install Clang LSP, which I happily did, and
then Claude started to generate code for the tasks.
Claude completed the alarm clock task in only two minutes and verified the
implementation by running the test.
Then Claude quickly went on to implement priority scheduling. I had to
interrupt Claude and ask it to commit the previous task.

After that, Claude continued with the implementation and ran the tests for
priority scheduling.
Unfortunately, the tests did not pass on the first try: Claude implemented
the priority ordering the other way around. It quickly identified the error and
inverted the sorting order.

At this point, Claude was quite happy to continue pumping out code. It quickly
completed the third task, committed it, and moved on to the final task.
Again, it only took a few minutes for Claude to finish. I remember taking quite
a long time at university, having to go through corner cases and
drawing diagrams.

The final task, MLFQS (Multi-Level Feedback Queue Scheduler), was relatively
easy to implement, but the tests took quite a long time to run.
Unfortunately, running them in parallel used too many resources, and the
Claude Code process was killed by `pkill`.
However, Claude recovered and completed the task on the second try.

At this point, the last thing was the design document. I had forgotten to
download the design document template and place it in the git repository, so
Claude looked everywhere for it. I realized it was probably my
fault, interrupted Claude, downloaded the template, and prompted it to
continue. Claude produced a plausible [design
doc](https://github.com/fangyi-zhou/vibe-coded-pintos/blob/master/doc/threads.tmpl).

As we neared the completion of the project, I asked Claude to update the
`CLAUDE.md` file and checked my usage. We had used around two-thirds of the
session quota.

As in the previous session, I used a plan-mode prompt to ask Claude to plan for
the next project. This time, I reminded Claude to commit after each task and
write the design docs. Claude then explored the codebase and requirements.
I was surprised by the token usage in plan mode, as
it did not manage to complete within the remaining 1/3 session usage limit.
I turned on some extra usage and invited Claude to finish. Here is the
[plan](https://github.com/fangyi-zhou/vibe-coded-pintos/blob/master/plans/project2-plan.md).

## Thoughts and Reflections (Sep 2026)

I never finished writing about the other tasks, but they followed the same
playbook: Claude came up with a plausible plan and implemented it reasonably
well. Claude had some issues making small commits, but the result should be
sufficient for computer science coursework, for a student who can't be bothered
to do the coursework themself.

In the half-year since my initial experiment, I have developed some skill at
identifying code generated by Claude, with its ever-increasing verbosity and
use of particular "load-bearing" vocabulary.
I would assume that people who use AI frequently would also be able to do the
same, which may or may not be the case for teaching assistants at universities
(probably PhD students who have to pay for AI subscriptions out of their own
pockets).
I would argue that the use of AI is still quite likely to impact computer
science education a lot.

When I was in university, this coursework was not graded by a teaching
assistant alone checking the implementation of the code, but in combination
with an interactive code review where the teaching assistant discusses the
design with the students, asking the students to demonstrate the correctness of
the implementation w.r.t. concurrency and safety. Arguably, such a solution
doesn't scale, but I believe it was a good solution.
Just as software engineering has shifted in the age of AI, computer
science education will probably have to shift to adopt the AI trend too.
In the old days, "Talk is cheap, show me the code" worked. But now, one might
say, "Code is cheap; let's talk it through."
