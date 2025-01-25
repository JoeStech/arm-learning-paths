---
title: Command line tools for performance analysis
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Let's look at some useful command line tools that can help you get a feel for what's happening on your system. If your application is misbehaving, it could be due to many different types of resource constraints -- memory, cpu, disk i/o, or network bandwidth. These are tools to help you narrow down the problem.

We'll assume that you've already looked at your application logs and haven't found sufficient clues there.

## dmseg

To get an initial look at what kinds of errors might be occuring in the kernel log, run

`sudo dmseg | tail`

`tail` defaults to the ten most recent lines, but you can change this arbitrarily by adding the number of lines you'd like to see, e.g. to view the latest 100 lines you'd run `sudo dmseg | tail -100`

The most common types of errors you'll see in the kernel log are likely Out Of Memory (OOM) errors. If you see these, you'll know to start looking at RAM use in your application.

## vmstat

To identify what's happening from a memory/cpu standpoint in real-time, you can run

`vmstat 1`

This will give you a good idea if anything is swapping to disk (if you have swap space configured). If swap is happening, your application is likely running with insufficient memory.

## mpstat

To see what's going on with all the cores in your system, run

`mpstat 1`

This will let you know if one core has contention over the others, etc.

mpstat may not be pre-installed on your Ubuntu image. If mpstat is not found, you can run

```bash 
sudo apt install sysstat
```

## pidstat

`top` is a very full-featured way to examine per-process resource use, but it is curses-based and very information dense. A clearer, no-frills utility for those that don't use `top` frequently is `pidstat`. If you run

```bash
pidstat 1
```

You'll get real-time info on which commands are using the most resources along with process IDs. Because its not curses-based, you'll also be able to more easily copy/paste values compared to `top`.

## iostat

So far we've been primarily looking at CPU/