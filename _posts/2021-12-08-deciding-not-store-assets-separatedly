---
layout: post
title:  "Azure static web app and content delivery"
date:   2021-12-08 14:00:00 +0100
categories: [Azure, static web app]
tags: [azure, static web app, content delivery network, cdn]
image:
  src: /assets/images/2021/december/Azure_static_web_app_and_content_delivery/1024px-NCDN_-_CDN.png
  width: 400   # in pixels
  alt: Cryptocurrency Logo
---

I have been wrapping my head around whether or not an Azure static web app would benefit from a content delivery network. I know this blog uses [Font Awesome](https://fontawesome.com/), and it is configured using a CDN. That's is what forst got me thinking,
because I don't want to depend on a third party cdn, even when that might be standard.

I was also worried about the 250MB size limit for free Azure static web apps. There is another host plan that increases that qouta to 500MB. So, should I host my images anywhere else? 

So, first question. Do I need a CDN for my static web app? The answer is no, Azure does that for you automatically when you use static web apps. On the other hand, there is another way to host static content on Azure. You can create an storage account an 
there host static web site.

![Storage account on Azure panel showing create Static website button](/assets/images/2021/december/Azure_static_web_app_and_content_delivery/storageaccount_website.png)

In this case, you should use a CDN if you want your content to be retrieved fast from anywhere around the globe. My guess is that static web apps on azure bundle this two things together so you don't have to worry about content distribution.

Second question, should I store my images elsewhere? At this point, I was thinking about uploading my images to an storage account with a web site container, and then set a CDN for that container. Finally, I decided to keep things simple. I don't upload high resolution
images to my blog. It will take me a while to reach 250MB. By that time, maybe that quota has increased and, in any case, I can upgrade to the host plan with 500MB while I find a solution for the lack of space.

Coming next, what's this thing about API functions? Hmmmmm.....

Header image credit:  Kanoha, [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0). [Via Wikimedia Commons](https://commons.wikimedia.org/wiki/File:NCDN_-_CDN.png) 