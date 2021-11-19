---
layout: post
title:  "Better than something: changing the look of this blog"
date:   2021-11-19 19:30:00 +0100
categories: [Personal, blog]
tags: [sass, scss, blog, personal development]
image:
  src: /assets/images/2021/november/better_than_something_styles_changed/changes_sample.png
  width: 700   # in pixels
  height: 476   # in pixels
  alt: Detail of how the sidebar of this blog changed
---

>"I'd rather be half-assed at a hundred things than a virtuoso at one."
>Jimmy Lee Lindsay, aka Jay Reatard

It is probably no coincidence that the documentary _Better than something_ DVD is right now lying on my desktop. I saw it like almost two years ago, on my Mac computer because I don't own a DVD player. I have been too lazy since then to put it back with the vinyl it came with. This documentary is about Jay Reatard, the man I have quoted to start this blog post. 

I have been customizing the style of this blog lately. I want to avoid saying that I have been improving it because it does not necessarily look better. I have no experience with css styles or designing, I know when something looks cool but I don't know how to make something look cool. Nonetheless, I have been working by myself on the sass of this blog, mostly hacking my way through it with some trial and a lot of error. I am not excited about how it looks like now, but I have had some fun along the way. And it certainly is more personal than it was before, which is good because it means I have put a little bit of me into this.


## Visual Studio Code + WSL = love

![Visual Studio Code working on blog files on the Window Subsystem for Linux](/assets/images/2021/november/better_than_something_styles_changed/hackingmywaywithvscode.png)

I'm loving Windows Subsystem for Linux -I need to learn why it is not called Linux Subsystem for Windows-. It is very usefull to have Linux tools at the reach of your mouse on your Windows desktop. When it comes to create a blog with jekyll, you can have your files on WSL, edit them with VS Code and run _bundle exec jekyll serve_ to see the changes locally. _jekyll serve_ will publish your site on your computer, it will detect changes on the blog files and regenerate the contents of your local site automatically. Smooth! Then, in my case, I used web browser dev tools both to see how the site looked like in different resolutions and to kind of reverse engineer the style sheets.

I have been learning ~~not so~~ !important things about CSS. I have changed the color of the side bar, fonts, the size of the avatar, the social icons, I have changed the size of the font of some elements, and I have changed the top bar on small resolutions. In case you don't know, I based this blog on [chirpy](https://chirpy.cotes.info/). At least on the version I cloned, the side bar on small resolutions collapsed and you have a button on the top bar to toggle it. I did not like that. I prefer a top bar with more presence. Something that diferentiates your blog from another blog.

![Top bar of this blog on small screens](/assets/images/2021/november/better_than_something_styles_changed/topbar_smallresolutions.png)

There are still things I would like to improve. Both things that you can see (better styling of the site) and things that you don't see unless you dig a little bit. For instance, I have ignored the dark mode/light mode switch. I have focused on the light mode and I don't even now how my changes look like in dark mode. Anyway, I don't think this feature is so important on a blog or web site in general. In any case, the sass for this site supports it and I don't want to have a mess that ignores dark mode. I would either remove it or support it. I am also afraid that on my way of changing the top bar for small screens I might have created a little mess (not so many lines after all), I just hope it did not affect other parts of the site.

I don't know if I would like to be a virtuoso at something. Maybe the bad thing about being a virtuoso at something is that you tend to do the same thing over and over again, in order not to waste your talent. I am certainly not even half assed at styling a web site, so I can leave it here for now no worries given. I am tired of scrolling through scss files with thousands of lines with just my intuition and a search box. And I am happy with how it looks like now, it is better than something. 