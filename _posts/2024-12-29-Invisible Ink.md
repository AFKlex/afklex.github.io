---
title: "Invisible Ink"
categories: [CTF, Wargame2024]
tags: [misc]
date: 2024-12-29 09:56:00 +0100
media_subpath: /assets/img/posts/wargame2024/
description: Writup of "Invisible Ink" challange in Wargame 2024.
---

## Challange 
The flag is hidden somewhere in this GIF. You can't see it? Must be written in transparent ink.

![Challange Gif](Wargame2024Orginal.gif)

## Solution 
I obtained the GIF and began analyzing it. 

First, I split it into multiple files using a website, and then I ran the `strings` command on each of them. In one of the files, I found the string "netscape." 

However, this didn't provide any useful hints. Next, I installed [[Stegsolve]] and examined the frames. Here, I discovered two images that appeared to be random noise, which might potentially contain the flag.
![Noise 1](Wargame2024Noise1.bmp)

![Noise 2](Wargame2024Noise2.bmp)

Then i used Stegsolver to change the colors and changed the colors of the image. This showed some interesting information: 

![Flag Part 1](Wargame2024FlagPart1.bmp)

![Flag Part 2](Wargame2024FlagPart2.bmp)

If we examine the files, they combine to form a single word, which represents the flag.

These two files together generate the final result, but I was unable to merge them manually, as I couldn't read the content. Instead, I placed both images into GIMP as separate layers.

I then added both images as layers in GIMP and adjusted the alpha channel to remove the background colors of the upper layer.
![Gimp Changes](Wargame2024Gimp.png)

We can easily read the flag, and I have obtained the Flag.
![Gimp Flag](Wargame2024Gimp2.png)

Flag: `wgmy{d41d8cd98f00b204e9800998ecf8427e}`