---
layout: post
title: Games for Learning Linux and the Command Line
---

This post is a survey of existing games people have made for quickly picking up GNU/Linux-centric command line knowledge.

The command line presents an interactive environment which can be adapted to games quite easily. A lot of old-school text adventure games like Zork and MUDs were based on the command line. This post focusses rather on games which teach you OS skills in the process of playing.


## OverTheWire Bandit (2012?)

[Link](https://overthewire.org/wargames/bandit/)

![Bandit](/public/bandit.png)

A classic series of command line challenges on a server that is accessed over SSH. User permissions are the mechanism used to restrict access to each level. I put the question mark after 2012 as apparently the original game has been around a lot longer.

*Pros:* Fun variety of challenges that escalate in difficulty and encourage self-study and exploration. Additional wargames dive deeper into areas like binary exploitation.

*Cons:* Aimed at absolute beginners but some will find just the SSHing in and challenge conventions confusing. The password mechanism for accessing the next level is somewhat clunky.


## Command Line Murders (2013)

[Link](https://github.com/veltman/clmystery)

![Command line murders](/public/commandlinemurders.png)

There's been a murder, and you're a detective. The evidence is spread out among multiple text files. You have to write text wrangling and filtering commands to narrow down the culprit. There's also a similar [murder mystery for SQL](https://mystery.knightlab.com/).

*Pros:* Great theme, fun premise. Multiple ways to solve. Hints and a cheatsheet are provided for less experienced players.

*Cons:* Too short, can be solved in less than 10 minutes if you know your `grep`. Could have been designed so that unveiling the final answer was more satisfying.


## Command Challenge (2017)

[Link](https://cmdchallenge.com/)

![Command challenge](/public/cmdchallenge.png)

This is a series of mini command line tasks that you have to perform, e.g. `Print the relative file paths, one path per line for all filenames that start with "access.log" in the current directory.`

*Pros:* Fast UI and it's addictive. Alternative solutions are offered which are interesting to read.

*Cons:* Uses a non-interactive shell with one-shot Docker images rather than providing a realistic environment. Some valid solutions don't work. Most challenges are easy and it doesn't scale up to a substantial level of difficulty by the end.


## Bashcrawl (2019)

[Link](https://gitlab.com/slackermedia/bashcrawl)

![Bashcrawl](/public/bashcrawl.png)

This is a dungeon crawler game where each room is a directory, objects inside the room are files, and interactive objects are shell scripts. So you naturally have to use command line tools to explore the dungeon.

*Pros:* The idea of mapping a filesystem to a game environment is a strong one. This feels more like an actual game than any of the others here, and simple commands are learned quickly by repetition.

*Cons:* Some of the instructions, in trying overly to be noob-friendly ("drag and drop this folder into your terminal") manage to cause more confusion. Tracking game state is done manually perhaps to teach environment variables but this gets tedious. The game is only for beginners; if you know too much it's easy to break.


## SadServers (2022)

[Link](https://sadservers.com/)

![Sadservers](/public/sadservers.png)

Billed as "like LeetCode for Linux", this contains a number of individual timed troubleshooting challenges that are similar to hands-on SRE interviews.

*Pros:* Realistic scenarios that make debugging fun. Not aimed at beginners, the challenges touch on advanced topics. Challenges require you to use tools that help you understand Linux at a deeper level, which you rarely get outside of a real-life context.

*Cons:* The UI isn't great and you have to wait a while for instances to launch, which spoils the immersion. Some of the challenges are obscure and don't clearly communicate a teachable idea.

## Suggested order

Overall, there's strong and weak points to each of these games. For complete beginners, Bashcrawl is a great way to start, and after some fluency on the command line has been reached, Command Challenge and Bandit could be used to expand one's repertoire. After that, Command Line Murders could be considered a fun "final boss" to the Command Challenge. Finally, SadServers is less game-like but still exemplifies the more difficult end of the spectrum of acquiring Linux skills through play.

