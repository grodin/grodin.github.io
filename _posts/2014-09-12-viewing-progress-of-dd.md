---
layout: post
title: "Viewing progress of dd"
description: ""
category: 
tags: [terminal,useful-tricks]
---

This is a quick note to remind myself how to get the progress of a `dd` command. 

Sending the `USR1` signal to a running `dd` process causes it to output I/O
stats to `stderr` and then continue. 

To send a signal to a process use `pkill -<signal> <process name>`. The options
`-x` and `-n` restrict to exact matches of the process name and the newest
matching process respectively.

Thus for the most recent `dd` to be started the following will work:
    
    pkill -USR1 -n -x dd


