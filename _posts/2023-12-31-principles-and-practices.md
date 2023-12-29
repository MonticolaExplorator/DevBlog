---
layout: post
title:  "Principles and practices"
date:   2023-12-31 10:00:00 +0100
permalink: posts/principles-and-practices
description: What are principles and practices and how can they help you
categories: [Software architecture]
image:
  src: /assets/images/2023/december/pracprin.jpg
  width: 200   # in pixels
  alt: ""
tags: [Practices, Principles, Software development, Software architecture]
---

If you are a software developer, you have probably heard the terms _principles_ and _practices_ before. But what are they? And why are they important? Should you care about them? Well, principles and practices are going to be right by your side during your whole career, no matter if you think about them or not, no matter what tecnologies you use, no matter what company you work for. Why? Because at the very least, practices are the way you do things, and principles are the reasons why you do them that way. So they will happen wether you want it or not. Your motivations for caring about principles and practices must be to improve your results from reasoning about them, and to be able to communicate them to others.

## Principles

Principles are the ideas that guide your actions, they align you with a larger goal and help you make decisions. They are not static, they can change as the circumstances chage, but they are not arbitrary either, they are based on your values and beliefs. For example, if one of your goals is to be able to ship more features per week, you might have a principle that states that teams should be autonomous and be able to work in parallel. If another of your strategic goal is to have a consistent user experience, you might have a principle that states that all teams should use the same UI framework. 

You don't need to have a large list of principles. In fact, a reduced set of principles will be more effective. The key is focus: if you have too many principles, you will not be able to focus on the ones that will really improve your results. Furthermore, a team following too many principles will always try to fit to them, leaving no room for creativity and innovation, and might feel like they are just following rules. A good number of principles is around 10. It is easy for people to remember them and you can also print them in a poster and hang it on the wall.

## Practices

Practices are the way we will fulfill our principles. They contain the detailed instructions for performing tasks. In case of software development, many times they will be specific to a technology or a framework. For example, if your organization goal is to have the same technolgy stack for all products, and practice could state that all backend services must use ASP.NET Core, and all frontend applications must use React. Another practice could state that all service must support Oracle, SQL Server and PostgreSQL, or that all products must have a CI/CD pipeline. Another practice could state the license that all products must use, and the license that all third party libraries must use. Practices need to be clear and be expressed in a way that all developers can understand them and follow them. 

Practices support your principles, but they must be reviewed often. They tend to change more often than principles, because they are more specific to the technology you are using, and because they are more related to the current state of the organization. Practices are also more likely to be broken, because they are more specific and more detailed. Keep an eye on them and make sure they are still relevant and that they are being followed.

## Putting it all together

At the end of the day, practices and principles are just a way to improve your work. Start with very few principles and practices, and add more as you need them. Try to keep principles as high level and broad as possible, and practices as specific as possible. Review them often. But also don't get rid of a principle or practice just because it did not work once, after all they probably won't be perfect. In fact, you should see them as _guidance for the wise, and obedience for the fool_. Unless your teams are fools, let them break practices and principles if they have a good reason to do so. That will also make people feel more empowered and more responsible for their work, and more compelled to follow practices. 

Here are some examples of real world principles and practices:

| Principle | Practice |
|----------|----------|
| Easy deployments | Every service must be containerized using docker, and they have to be. Services have to be small and independent |
| Fast and safe flow of new features | Develop directly on main branch. Establish a CI/CD pipelines. All services must have a code coverage of at least 80% |
| Follow security best practices | All services must use HTTPS. Secrets must not be checked into source control. Secrets must be stored in an Azure Key Vault |
| High scalability | All services must be stateless. All services must be able to scale horizontally |
| Resilience and observability | Services must define a set of metrics, logs, and traces to facilitate problem diagnosis and prognosis. These metrics, logs and traces must be exported using OpenTelemetry. Request made to other services must use  exponential backoffs to deal with transient errors.|


Header image generated by [Bing](https://www.bing.com/search?q=Bing+AI&showconv=1&FORM=hpcodx)