---
layout: post
title: Games for Learning Linux and the Command Line
---

This post is a survey of existing games people have made for learning Linux or just messing around on the command line.

The command line presents an interactive environment which can be adapted to games quite easily. A lot of old-school text adventure games like Zork and MUDs were based on the command line. This post focusses rather on games which teach you UNIX/Linux utilities in the process of play.


## The Command Line Murders (2013)

[Link](https://github.com/veltman/clmystery)

![Command line murders](/public/commandlinemurders.png)

There's been a murder, and you're a detective. The evidence is spread out among multiple text files. You have to write commands to narrow down the culprit. There's also a similar [murder mystery for SQL](https://mystery.knightlab.com/).

*Pros:* Great theme, fun premise. Succeeds in implementing learning by exploring. Hints and a cheatsheet are provided for less experienced players.

*Cons:* Too short, can be solved in less than 10 minutes if you know your `grep`. Could have been designed so that unveiling the final answer was more satisfying.


## Command Challenge (2017)

[Link](https://cmdchallenge.com/)

![Command challenge](/public/cmdchallenge.png)

This is a series of mini command line tasks that you have to perform, e.g. `Print the relative file paths, one path per line for all filenames that start with "access.log" in the current directory.`

*Pros:* Fast UI and it's addictive. Alternative solutions are offered which are interesting to read.

*Cons:* Uses some kind of non-interactive shell with one-shot Docker images rather than providing a realistic environment. This also means that some correct solutions don't work! Most challenges are easy and it doesn't scale up to a substantial level of difficulty by the end. The tasks are fairly mundane.


## Bashcrawl (2019)

[Link](https://gitlab.com/slackermedia/bashcrawl)

![Bashcrawl](/public/bashcrawl.png)

This is a dungeon crawler game where each room is a directory, objects inside the room are files, and interactive objects are shell scripts. So you naturally have to use command line tools to explore the dungeon.

*Pros:* The idea of mapping a filesystem to a game environment is a strong one. This feels more like an actual game than any of the others here, and simple commands are learned quickly by repetition.

*Cons:* Some of the instructions, in trying overly to be noob-friendly ("drag and drop this folder into your terminal") manage to cause more confusion. Tracking game state is done manually perhaps to teach environment variables but this gets tedious. The game is only for beginners, if you know too much it's easy to break.


## SadServers (2022)

[Link](https://sadservers.com/)

![Sadservers](/public/sadservers.png)

Billed as "like LeetCode for Linux", this contains a number of individual timed troubleshooting challenges that are similar to hands-on SRE interviews.

*Pros:* Realistic scenarios that make debugging actual accidents that could happen with servers fun. Not aimed at beginners, the challenges touch on advanced topics. Some challenges get you using tools that help understand Linux at a deeper level.

*Cons:* The UI isn't great and you have to wait a while for instances to launch, which spoils the immersion. Some of the challenges are obscure and don't clearly communicate a teachable idea.

