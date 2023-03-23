---
layout: post
title: Announcing DevOpsChops
---

DevOpsChops is a game that I'm building based on the command line. You might need to fullscreen the video to see the demo clearly:

<video autoplay="autoplay" loop="loop" controls style="width:100%;height:auto;">
  <source src="/public/progress.mp4" type="video/mp4">
</video>

There are number of self-contained challenges which rapidly build up fluency in Linux, networking, cloud, databases and related areas. Challenges on high-level DevOps concepts are also on the roadmap.

On the backend, the VMs are running on [Firecracker](https://firecracker-microvm.github.io/), so they launch fast and are isolated from the host system. Much of the work so far has been in creating a VM templating system that enables challenges to be fully specified in a few lines of configuration.

I think that existing DevOps learning resources are pretty dull on average. They tend to overwhelm learners with the massive amount of tools and teach them in a lecture style. What is lost is the idea of _play_, where you are motivated to explore and seek out answers for your own problems. Memorising details is unimportant compared to knowing how to diagnose an issue and how to find a solution. Linux, Kubernetes, AWS are complex but (mostly :D) reasonable systems that you can gain an intuition for after enough hands-on experience.

So that's why I refer to this project as a game. It borrows ideas from [CTFs](https://en.wikipedia.org/wiki/Capture_the_flag_(cybersecurity)), in transforming what would otherwise be learning abstruse material into a competitive, social, engaging experience. While fixing systems may be less exciting than pwning them, I aim to make up for this with a sleek platform and well-written content which slowly escalates in difficulty until players find themselves debugging realistic scenarios.

Software engineers in general say they wish they knew more about these topics. Ultimately, DevOpsChops is an attempt to lower the activation energy and build something which is simply fun to play. 
