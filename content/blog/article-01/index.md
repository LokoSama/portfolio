+++
title = "Why you should build a starter project to enjoy your side-projects"
description = ""
featured = "kermit-tea.jpg"

type = ["blog","post"]
tags = [
    "starter",
    "project",
    "development",
]
date = "2020-04-16"
categories = [
    "productivity",
    "architecture",
]
[ author ]
  name = "Jérémy Basso"
+++

## Background story

When you are working as a developer for a company, and you want to keep working on side-projects at the same time, you will probably struggle finding a well-known and desirable resource : **time**.  Directly linked to **time**, another important resource is **energy**. It is necessary, in my opinion, to save free **time** for non work-related tasks, in order to relax and maintain enough **energy**. Finally, the third resource is **attention**, when a big task is ahead of you, it gets harder to maintain focus.

I mentioned **time**, **energy** and **attention** for a reason : those are often quoted as the components of the TEA framework. The idea behind TEA is that if you lack any of the 3 resources, you will never be able to achieve optimal productivity. In the case of side-projects, I think of "productivity" not as the amount of code you can deliver, but as the good balance that will provide pride, fulfillment and blooming from working on your side-projects.

<p align="center">
<img src="/TEA-diagram.png" alt="TEA" style="width:600px"/>
<legend>T.E.A. diagram</legend>

</p>


At the beginning of my carrer, I tried really hard to combine the work projects, and the side-projects. As a result, I lost motivation for my side-projects because :

 - I had less time to invest for the side projects.

 - It was hard for me to start something from scratch because the "motivating" part needed some boring setup first

 - I did not want to spend all my free time working on my side-projects,  i just wanted to jump directly to the fun part

Few weeks ago, an unexpected situation occurred : the COVID19 lockdown. Having to spend all my free time at home, I decided to give my side-projects a try again, and tried to find the tricks that could work for me.
I wanted to share with you the trick that worked best, it is not a miracle solution, yet it worked very well for me : what I call **starter** projects. 

## What is a starter project ?

As a developer, whether you are an expert of a specific language, or more of a Swiss Army knife that likes to try a lot of languages, you probably have 2-3 "go-to" languages and frameworks, that you like and will always choose for a side-project. It is not necessary that the chosen language is something you work with your company, side projects can be a good opportunity to try something different.


<p align="center">
<img src="/languages.jpg" alt="pick" style="width:600px"/>
</p>

As an exemple, in my company I work mostly as backend engineer with Kotlin, but on my side projects I often go for Node.js + Typescript for backend, and Flutter for mobile frontend. It allows me to keep learning outside of my job, and most importantly have a blast developing in languages that seduces me (not that I dont like Kotlin, but I am getting used to it).

So here we are, you picked a language, a framework you like, and you are ready to start !

## Where to start ?

### Functional scope
First of all, try to find out what are the features your starter should have. Pick few of your side-projects ideas or existing applications, and try to define what features are always needed among those. This should help you finding the scope of your starter project. You should think of this as a potential starting point for every of your projects. Your incoming projects should be a fork of this one.

*Example for backend starter : authentication email/password, database connection, account management, admin routes*

### Architecture
Once you decided your functional scope, you **should** spend some time on your architecture. Thats the point of having a starter project : you need a good architecture, that **you** like, and that **you** feel confortable with. Thats an opportunity for you to try something new, this starter is designed by **you** and for **you**, so feel free to try new things, as long as you can handle it.
My tip for architecture is to use a whiteboard to visualize what you want to do, it will help you iterate over your architecture and spot potential issues.

*Example for backend starter: hexagonal architecture*

### Implementation

It is getting real now ! You can now start working on what you planned beforehand. 
Take the time to decide if you want to integrate some libraries into you starter. The idea is to limit as much as possible the number of libraries on your project, only the minimum necessary. 

Some tips I am sharing to you (take it or leave it):

• Comment and document correctly your code. All of your projects using the starter will contain your starter.

• Setup the test environment. Tests are good, make tests

• Have a full implementation of a dummy resource (not a complex one). It allows you to immediately get a complete process to copy paste in your projects. For backend it would be from database to web services, and frontend from the API call to the display screen (even ugly).

• Use a keyword for your project name : it will be helpful for search and replace afterwards

• Setup minimal CI/CD. It could be pipeline to Heroku for backend, Bitrise for mobile dev, anything you like.

• For object-oriented projects, think [SOLID](https://en.wikipedia.org/wiki/SOLID). It is more of a general advice, but still a good one.

## What now ?

### Let's start !

Congratulations, you built your starter ! It took some time but think of all the time you will save on your incoming projects.

To start a new project from the starter, I usually do the following :
```
git clone myrepo/mystarterproject
cd mystarterproject
rm -R .git
git init
```
Then you search for your starter project name and replace occurrences with your new project name. Another approach would be to fork the starter project, which I personally dont like because it means your starter project is public, I rather keep it private.

### Going further
If you want to go further, here are some things you can do to keep improving your starter.

First of all, you are free to refactor, or change things on your starter. This is a good way of trying new things and then iterate on what you learned.

 Secondly, you can add features that you have done in one of your project to your starter, in order to save time for the next project that will need it. 
 To do so, you can branch from the master branch and include the code that you want to save for further projects. This way, the next time you will start a project, before deleting the git directory, you can merge into your local branch the features you want to include into your new project. 
 This approach can also be used to separate different technologic choices, such as database choice.

 *Example for backend starter: my master branch uses MongoDB database, but I have a branch that uses MySQL instead. I have as well a branch where I included full code for Facebook authentication.*

## Why starter projects are cool ?

Building starter projects helped a lot with my involvement in side-projects, and here is why.

First of all, while I used to dislike building the basic features for my side-projects, building the starter became a side-project on itself, and it was easier to maintain **attention** and **energy** knowing that what I was building will save me tremendous **time** later.

<p align="center">
<img src="/kermit-tea.jpg" alt="pick" style="width:400px"/>
<legend>Accurate picture of me and my T.E.A.</legend>
</p>

Then, it allows to summarize all your knowledge around a technology, and to have something practical that shows where you locate yourself. You can see yourself improving when improving the starter, it is setting interesting milestones.

I can finally start projects without spending hours of configurations and boring features, and directly jump to the fun part. The idea that drives me is : the more projects I create (and continue !) from my starter, the more the time spent on building the starter is worth.

Finally, I even started creating starters from my starters, for specific types of applications. Yeah I know, this is pretty meta. 

The idea is that certain types of applications always have the same core features, and the only difference between those is a standout factor that will decide if the product is successful or not. So imagine you have a starter for that kind of product : you can just try out without big investment different standout factors, until you find the one that makes a successful product. 
I am pretty convinced this approach has a potential, maybe it will come within an incoming article.

**Thanks for reading !**
