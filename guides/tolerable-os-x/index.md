---
layout: page
title: Making OS X Tolerable
---

OS X is in many ways a lovely UNIX-ish operating system, but those of us
who come from any other OS find some of its behaviors rather annoying. A
proponent of tiling window managers like me can find *many* things to
hate. Circumstance, however, has forced me to use it and therefore I've
found a great variety of tweaks to make it a little more bearable.


## Software

### [BetterTouchTool]
<p class="subhead">Freeware</p>

OS X uses very different keyboard shortcuts from other OSes and provides
only very limited options for configuring them. Many applications don't
provide any way to customize their shortcuts at all.

BetterTouchTool is an extremely versatile utility for binding keyboard
shortcuts and mouse gestures to actions. It supports emitting key and
mouse events, plus a wide variety of system and window management
actions. There are also some bonus window management features like edge
snapping.

[BetterTouchTool]: http://www.bettertouchtool.net/



### [TotalSpaces2]
<p class="subhead">Commercial &bull; $18 &bull; 14-day Trial</p>

When switching spaces by swiping on the trackpad the slide animation
used in the transition feels natural. When switching with a keyboard
shorcut, on the other hand, it's jarring and even causes motion sicknes
for some people. It used to be possible to disable it via `defaults`,
but in Mac OS 10.7 "Lion" the animation was updated so its speed is
determined by how fast you swipe and the workaround no longer works.
There doesn't seem to be any other way in stock OS X to disable it.

TotalSpaces adds several features to Spaces by injecting code into the
Dock application at runtime. It lets you choose from several animations
for the transition, most of which are even worse, but in the process
also gives you the option to disable animation entirely. As a bonus, it
adds a status bar icon which displays the number of the current space.
I disable nearly all of its other features.

[TotalSpaces2]: http://totalspaces.binaryage.com/



### [USB Overdrive]
<p class="subhead">Nagware &bull; $20</p>

OS X applies acceleration to all scroll actions performed with a mouse.
For trackpads and the clickless scroll ball on the Mighty Mouse that
makes sense &ndash; those are continuous inputs you can operate at
different speeds. It's infuriating, however, when applied to the scroll
wheels with detents on external mice. On every other platform those
scroll a fixed distance for every click of the wheel.

USB Overdrive is an alternative USB HID (human interface device) driver
which provides a great many options for customizing the behavior of
various inputs. Among many other abilities, it can make each click of
the wheel scroll a configurable distance instead of applying the normal
acceleration algorithm.

[USB Overdrive]: http://www.usboverdrive.com/USBOverdrive/



### [Bartender]
<p class="subhead">Commercial &bull; $15 &bull; 28-day Trial</p>

A great many applications for OS X insist on creating menu bar icons
that serve little to no purpose or are only useful for a subset of
users. Many of those don't let you disable their icons if you don't want
them. Furthermore, the icons are displayed in the order the apps create
them, so the order tends to change every time you reboot. That's
terrible for usability because you can't build muscle memory for the
location of icons you have to click often.

Bartender is a utility that takes over management of the menu bar icons.
It allows you to define per application whether and when the icon should
appear. Icons can be suppressed entirely or moved to a toggleable
overflow bar and optionally shown or promoted to the main bar when they
have activity. It also allows you to drag the icons around and then
persists the order so it remains constant.

[Bartender]: http://www.macbartender.com/
