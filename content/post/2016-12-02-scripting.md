---
title: Scripting
author: Scott
type: post
date: 2016-12-02T15:48:53+00:00
url: /2016/12/02/scripting/
categories:
  - Technology

---
Scripting languages and command line interfaces is an area I've decided to focus on in 2016. I'm currently a part of an online course on Python with Udemy by [Jose Portilla][1] that I highly recommend. I'm also diving into Powershell at work to become more familiar with remote Azure AD management. Familiarity with Powershell scripting will make me much more powerful in my domain.

Over the weekend I decided to get a little more comfortable with the command line interface with my Mac. I decided to write a couple scripts that I'd find useful on a daily basis. I'd recently brought in the LCD display I use with my home PC to work as I realized I wasn't giving it the attention it deserved at home, but needed a way to remote in and control the PC in order to access Steam and wake the PC to run my Plex server. So I wrote two scripts:

![Icons](/img/ScriptIcons.png)

## WakeUpHomePC.sh

WakeMeUp runs wolcmd, which is a custom Unix executable by [Depicus][2] that imitates the special packet sending process that can be found in Windows in order to wake a sleeping PC on your network.

## LaunchTightVNC.sh

TightVNC is great. I installed TightVNC server on my home PC in order to remote in and launch steam so I could play my games remotely from my living room using my Steam Link. For whatever reason, Steam Big Picture and Steam Link did not want to play well through an RDP session so this was necessary and works great.  This script launches the Java version of the TightVNC client from my Mac so I can quickly jump in (once my PC wakes up) and launch Steam to start gaming.

These are just a couple quick easy examples of how scripting can make your life easier and enable you to do some pretty neat stuff. Now I can shove my home PC away into the cabinet in my desk as a headless server! In both cases the scripts were only 2-3 lines long each but make these two tasks much more convenient.

 [1]: https://www.udemy.com/complete-python-bootcamp/
 [2]: https://www.depicus.com/wake-on-lan/wake-on-lan-cmd