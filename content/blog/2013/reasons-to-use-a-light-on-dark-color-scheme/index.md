---
date: "2013-06-23T00:00:00Z"
description: ""
tags:
- color
- design
image: lightvsdark.png
title: Reasons to use a light on dark color scheme
summary: Light on dark, or dark on light? Light on dark may just seem cool, but there's some marginally good objective reasons for it.
---

{{<tip "warning">}}
This article was written long ago and imported to this blog with only minor edits. Some information may no longer be up to date. I'm keeping this mostly just for my own archival purposes.
{{</tip>}}

# It's not just to be cool.

You'll often see developers working with a light-on dark color scheme for their web sites, editors, and other applications. I have dark scheme on this site (with a light scheme switch available), and all my primary desktop editors use a dark color scheme. I use a dark terminal, a dark desktop background, and dark themes on every application that supports theming. You might think this is only due to contemporary media branding dark themes "cool" or "retro," but there's actually some marginally good reasons for it.

## You might save power.

It might seem silly, but having a darker theme can actually save you a small but measurable amount of power. [Blackle](http://www.blackle.com/) claims to save watts by rebranding the Google search homepage with a dark scheme. Google [refuted this](http://googleblog.blogspot.com/2007/08/is-black-new-green.html) in 2007, claiming that black schemes might actually increase power usage because in many monitors, the white is always on and the black requires energy to block the light. PCStats set out to [test the claims](http://www.pcstats.com/articleview.cfm?articleID=2649) in 2012. In their testing, a dark color scheme saved 4 watts on their reference LCD monitor. CRT monitors had an even more drastic difference, though at this point they are all but completely obsolete. As of this writing, Blackle claims to have saved over 3.5 million watt-hours of energy by users visiting their site instead of Google's home page; though exactly how they measure this, I'm unsure.

However, not all monitors are LCD, so I'm sure this is up for a lot of debate. In an LED monitor (or, more drastically, something like a Jumbotron), where the light actually turns off in black pixels, the power difference between a full black and white screen is probably even more prominent.

## You might sleep better.

Our sleep is affected in part by a hormone called melatonin. Melatonin promotes sleep and, in effect, tells the body "It's night now, do night things." Light hitting the retina surpresses the production of melatonin, and can [affect your sleep schedule](http://www.nytimes.com/2011/07/05/health/05light.html?_r=3&pagewanted=all&). According to one study, blue light especially kept subjects awake and alert for up to an hour after exposure. By having a darker scheme, you get less blue light and can get to sleep more normally. There are of course other ways to achieve this - for example, a light red background - but in the abscence of other choices, a black or grey background will affect your sleep far less than a bright white one.

## It is easier to scan.

This part mostly applies to code or terminal windows, but [according to some](http://uxmovement.com/content/when-to-use-white-text-on-a-dark-background/), light text on a darker background is easier to quickly read in short scans. This is because white light, being made of every color combined, stimulates every color receptor in the eye. The extra light stands out in contrast to the background and is easy to scan across. The lighter the text, the more pronounced the effect - notice how easy it is to quickly scan the title of this post. However, brighter text also tends to become blurred and strain the eyes due to scattering, which makes reading a full paragraph of bright white text on a full black background very stressful on the eyes. To mitigate this, light grey/dark grey schemes like this one are generally used for longer text such as this paragraph.

## Accessability concerns

According to the [American Federation for the Blind](http://www.afb.org/section.aspx?TopicID=200&DocumentID=210), "Contrast is one of the most critical factors in enhancing visual functioning." They suggest that text always be printed with the best possible contrast to allow sight impaired users to consume it more easily. They mention that for many older people a light-on-dark scheme is easier to read than black on white. This is the origin of both the "high contrast" and "inverted colors" schemes available in many operating systems. This generally isn't a concern for most code editors or tech sites, but if you're aiming for an older audience, you may want to consider adding these options.

Of course, there's data that goes the other way on this, as well. People with astigmatism [may have a harder time](http://blog.tatham.oddie.com.au/2008/10/13/why-light-text-on-dark-background-is-a-bad-idea/) reading light on dark text. Because the iris must open wider to take in the decreased light, their lens deformation is exaggerated and contributes to fuzzy looking text. That's why it is a good idea now to allow for light or dark schemes - accessability! Why choose one for your users when you can accomodate both?

I'm not suggesting every web site run out and switch to a light on dark theme, obviously. But next time you see someone with a dark color scheme, it might not _just_ be because it looks neat.