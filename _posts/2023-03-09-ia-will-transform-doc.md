---
layout: post
title:  "AI will transform the way we document software"
date:   2023-03-12 11:00:00 +0100
categories: [IA]
image:
  src: /assets/images/2023/march/ia-transform-doc/Artificial_Intelligence.jpg
  width: 400   # in pixels
  alt: "A human like machine trying to solve a problem"
tags: [AI, documentation, future]
---

Image via [vpnsrus](http://www.vpnsrus.com).

Mark my words, AI will transform the way we document and the way we consume documentation. To be honest, one could complete the sentence _AI will transform_ with almost anything, and they will be right. But with the recent advent of [ChatGPT](https://openai.com/blog/chatgpt) and some struggles with documentation at work, the idea dawned on me.

## Documents

The documentation I am thinking about is mostly documentation covering the aspects of a machines, software, but it can also be extended to company procedures, contracts or sailing a boat. So when refering to this documentation, we immediately associate it with **text, pictures and diagrams**. But we could also extended it to other formats like video tutorials or even speeches. What all these have in common is that they try to **synthetize, explain or record** systems or events that happened. Some can be tedious like a dicctionary, others can be entertaining like a chess tutorial, and some can be dull like the user manual to your microwave.

## Documenting

Maybe documenting has not changed much since the old greek historians. It just involves going through something that has happened or already exists, and put it in a format that latter can be consulted by a human. There are two key aspects in documenting correctly: **the goal and the target audience**. Because documentation does not, and probably should not, just represent exactly the same thing that is being documented, it is key to know what parts to cover. Let's say you company makes cars, your user manual will contain instructions on how to operate the audio equipment, and your service manual will have instructions on how to replace the display of the audio equipment. 

And since it is intended for humans to consult, and humans can be very different one to the other, the documentation also depends on the audience. Age, language, education, culture and any other human characteristic can affect the documents you generate. You don't prepare a speech about WWII the same way for a high school history class than for a history class in Harvard.

![Ancient historian](/assets/images/2023/march/ia-transform-doc/escriba.jpg)

## Documenting in the software industry

At least as far as my experience goes, documenting software is not an easy task. First and foremost, **is must add value**. Don't just write down every single fine aspect of your software, because it will be useless. It is very easy to fall into this trap, specially as an overshoot coming from a state of poor documentation or no documentation at all. But I think it is also true that **not generating any documentation is not good**. Unless you are producing very ephemeral or very simple software, a little documentation would probably pay off in the long run. And at the very least it will always be a safe bet. 

> The documentation can't be the code itself

You might hear that the code itself is the documentation, but that is just not right. Again, simple software or small isolated parts of a bigger solution might just not need documentation, especially if they are written in a clear way. But bigger solutions solve more complex, more varied problems. They are a solution to many related problems, **they apply strategies, criteria, they have a sense, they make tradeoffs and they have bugs**. In short, they are just a piece of a much bigger picture. There is no way anyone can efficiently make sense of the whole product just by looking at the code. Emphasis on _efficiently_.

But introducing documentation on your working routines can be hard, especially if you don't feel you need it. The way I like to approach this is try to **explain what I did, why I did it and what can go wrong on my pull requests**. And when I work with pull request for small increments, I only do this once a main feature has been completed. Then, instead of just filling the pull request description, I also move this explanation to a markdown in the source code. And boom!!! We are already creating documentation, congratulations to ourselves! And we have only invested the time that it takes to create a pull request.

## Structuring the documentation

We can't resist it, our developer brains live to find systematic solutions to complex problems. So even before deciding we are going to document our software, we are already thinking how are we going to structure the whole documentation. We want the user of our documentation to get all the knowledge they might be looking for as fast as possible. Directory structure, file structure, tags, links, searches,... The problem can become real overwhelming real fast. 

> **Always keep things simple, specially if you don't have your goals and your audience very clear**

The first thing that we can do to start solving this problem is to identify the audience of the documentation. It can be just our future selves, it can be just other people, but most likely it will be anyone that might be involved with the software (development, deployment, maintenance) in the future.

The second thing is identifing our goals. This is a slightly harder task, but I suggest you pick one or more items from the next list:

- What we did
- Why we did it
- When we did it
- What are other possible ways to achieve it
- What can go wrong and how to fix it
- Development requirements and recommendations (IDEs and other tools)
- How to test new development (create a development environment)
- How to deploy it
- How to configure it
- How to maintain it
- How to update it

The important thing to keep in mind is that only you can tell what documentation you need. **Start documenting any aspect that you feel is lacking**.

> **Don't overcomplicate things. More complicated things will have much less chances of adding value**

![A software developer working with many screens](/assets/images/2023/march/ia-transform-doc/doc_software.png)

## Starting to question things: people don't want or need documents

It is time to start re-evaluating everything we have said so far. Even if you are enthusiastic about documenting, you must know that producing text files (or audio or video) is never a end but a means. It is a mean to explain something to someone. It is not even the best way to do it. The best way someone can understand anything is by asking questions to another person with enough knowledge on the matter. Of course, this is expensive and scales poorly if there are few experts and many people trying to ask questions, but otherwise is the best way. That is why many people is reluctant to use documentation, and prefer asking a colleague near in the office. A good human expert will automatically addapt it's anwsers to the listener (**audience**), and with some context they will immediately identify what relevant pieces of information they need to provide (**goal**). Much faster, more concise and more human friendly than a any other way you could imagine so far.

> People want to make questions and get answers

## Don't repeat yourself: if code could transform into documentation 

Except if you are writing down an idea you have, documenting means going over something that already exists. **Repeating things rarely adds value**, and most of the times there are more efficient ways to achieve the same thing. Plus repeating yourself is dreadful. So, is there a way to transform code into documentation? My uneducated guess is that there will be. I am confident in the future we will have systems that will become experts on our software just by reading our code, maybe including our possibly completely unstructured markdown documentation embryo.

> **Machines will be able to become instant experts on anything we produce**

## The revolution

The level of expertise about a product that a human being would acquire in years of work, possibly moving between different jobs in the same company, a computer will be able to achieve it in hours, o maybe minutes. And one key thing on this revolution is that we will be creating something superior to just documentation: we will be creating an expert with much less effort than we needed to create documents no one wanted to read in the first place. Finally, people will be able to find the answers to their questions in an agile way any time, any where.

A machine could go through the pharmacy degree in the blink of an eye compared to what it takes to a human being. And while we probably don't want machines to substitute human chemists, it is very easy to see how they could be a very powerful tool to, for instance, double check the dose of a drug they give to a patient considering their history, age, symptoms, etc.

We can also imagine how many devices could start shipping without instruction manual. These can be the epitome of documentation no one is really willing to read. They might start shipping with linls to apps or websites, where you will be able to immediately get the answer to _how to bake the perfect crispy pizza_, instead of just figuring out how to turn on the oven and set the temperature.

In the software world, machines becoming instant experts on entire software systems would mean any user could have personalized support almost as if they have a dedicated expert for them. Machines might also be able to spot bugs on the software, or detect inefficiencies, weaknesses and vulnerabilities. And with the level of expertise they will have, it is easy to imagine that they will even be able to, at the very least, give hints or tips on how to implement new functionality.

The possibilities are endless, and the implications are huge. The future is exciting, and leaves me wondering how much time is left until machines become smart enough to do my job. I hope by then I am, at least, still funnier than them.



