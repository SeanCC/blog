---
title: "Crime Lab New York Intership: Post-Mortem"
date: 2019-09-02T17:51:49-04:00
draft: false	
tags: ["internship", "experience", "post-mortem"]
---
	

I spent this past summer in New York City, interning for an amazing organization: [Crime Lab New York](https://urbanlabs.uchicago.edu/labs/crime-new-york), a member of the University of Chicago's [Urban Labs system](https://urbanlabs.uchicago.edu/). I got to spend a lot of time learning about a lot of things that I had, frankly, never even thought to think about! 

Given just how much I've learned this summer, I thought it might be beneficial to myself (and whomever might come across this blog/portfolio) to do a post-mortem of my time at CLNY. So, without further ado, let's begin!



## Lessons learned: 

**Feature generation is a skill, and a very valuable one**

Prior to this internship, I had worked with a fair bit of data over the course of my Master's as well as at Sagicor Life Insurance. However, almost all of the work that I did used the features already present in the dataset, with maybe a little bit of feature generation. 

Reading numerous professional data science and statistics blogs and articles, you'll undoubtedly read a lot about how important feature generation is. What I didn't read a lot about was how, exactly, to carry out generation of features. 

For me, I came to understand feature generation as another step in the question asking process. Often, you want to have an idea of what sort of overall question you want to answer *before* you seek out a dataset. But once you have the dataset, there's several classes of questions you can ask about your dataset, guided by your original query. Some of these include:

* How many times has an event of this sort happened before? 
* How long has it been since the last time an event of this sort happened?
* How many of this type of object are present in the dataset? 
* How many relationships does this object have with other objects? 
* Does this object have a relationship of type X with another object in the dataset?
* Can this record be classified in a way that isn't built into the dataset?
* What's the minimum/maximum value that this objective achieved in column X?

There's countless questions of this type that can be asked of a dataset. The type of data that you have will very much affect what questions you can ask - obviously, you'd be asking entirely different questions about image data or sound data. The point is, the data we have usually has a great deal more information embedded in it than is immediately apparent from the set of columns presented to us. To access that information, we have to do some work.

**Any one dataset or set of datasets can be used to make predictions at a variety of levels**

This seems like a really simple and obvious idea, but it's not one that I've seen verbalized very much. Another way of putting it is that data is almost never about *just one thing*. A dataset consisting of arrests is also a dataset consisting of victims, offenders, arresting officers, locations, et cetera - and it can be transformed to represent such, often with relative ease. 

At CLNY, analysts frequently work with massive tables of records at varying levels. Tables might be at the victim level (i.e. each row is a victim), the incident level, the arrest level, the offender level, et cetera. One of the first steps in building a model is defining what level we're doing predictions at (e.g. do we want to make predictions on the victim, the offender, the incident, a locale, et cetera), and then generating features for that level from the various tables available to us. 

For instance, say we're working with motor vehicle collision data, where each row is a traffic incident. We might want to make predictions at the street level - e.g. do we expect that there will be an accident on this street in the next year? We might want to make predictions at the person level - e.g. how high of a traffic risk is this person? We might want to make predictions at the vehicle type level - e.g. how dangerous is this vehicle on the road? Deciding what level we're making predictions at will profoundly affect the features we generate and the type of model we build. 

Any time you're working with a dataset, a valuable question to ask is "what other information/prediction levels can I transform this data into?" 

**Organizing early and organizing consistently can be your best friend**

Prior to working at CLNY, I had only used git in passing (mostly to host things on github), had never touched a makefile, and had rarely if ever thought about the organizational structure of my projects. 

My first day at CLNY, I was provided with a slew of materials to read and watch for how to properly manage a data science project. These proved invaluable, and the techniques I learned here I've since implemented in all of my personal projects. 

A major issue that I have had with projects in the past was that what I was working on would generally accumulate technical complexity, until eventually a point was reached where any time I wanted to pick up working on the project, I'd have to spend time figuring out where I left off and what I had done previously just to keep going. This barrier would sometimes lead to me avoiding working on projects for long periods of time - it took so much energy just to get back to making progress that I never felt up to continuing. 

Organization fixes this problem almost entirely. Separating every major step of your project out into its own files and folders, with well defined relations, makes building on the project later profoundly easier. It should be possible to look at the file structure of your project and know more or less immediately what the steps in your data science pipeline are. You may not know the technical details of every step, but you know *what* is going on without having to open up code files and read. 

I highly recommend 



## Some mistakes I made:

I found that I was very rarely lacking in the "statistics knowledge" department, and my knowledge of how to code things was sufficient for the most part, but domain knowledge for working with data was lacking, as was knowledge of how to manage a project that is expected to be handled by multiple people over the long term. 

** Table operations such as melting and groupbys are absolutely invaluable, and it took me a long time to really understand them. **

This is actaully probably the biggest thing that I've *gained* from this internship in terms of hard technical skills. In my master's coursework and my previous roles, I rarely had to do complex table operations. My previous role had me doing a lot of work in SQL that involved a variety of table operations, but it was not as intensive as during my time at CLNY. 

I've been reworking an old project and cleaning it up for my portfolio. I found that a lot of the work that I did previously on this project was extremely inefficient, simply because I didn't even know about the melting and groupby operations. Using these operations for feature and outcome generation on other projects has significantly sped up the data science pipelines I've created.

** I should have spent more time understanding the code base I was working with early on **

The project that I worked on had been started at a "proof of concept" level by other employees at CLNY before being handed off to me. I was very eager to get to work and to start producing results of my own - so I started building on top of what they had given me immediately. This didn't turn out horribly, but I ran into a few technical issues later on that I could easily have avoided had I spent some time understanding the problems that my predecessors had already run into and how they had overcome them. 

I made this mistake due to a couple issues with my mindset. For one - I was a little overconfident in my coding abilities and had started from an assumption that I could produce something amazing basically right away. It's hard to overstate how many little details are in any major technical project that can make it take a lot longer than initially expected. Being in a rush just makes you miss those details. For two, I assumed that other people who were more senior than me were infallible. This is *not* a dig on them: it was simply foolish of me to take a "proof of concept" code base and assume that everything in it was perfect from the get go. I wouldn't assume this about any code base, going forward.

** I should have spent more time thinking about the data I was using in my project early on **

This is closely related to #2. In particular, the data that I was working with was spread across a number of large datasets. My predecessors had already done a lot of the work required to merge these data tables into a single dataset to generate features on and carry out prediction tasks. Unfortunately, I simply assumed that this part was "done". 

Well past the halfway mark at my internship, I started to realize that a lot of my technical issues on the project came down to the fact that I didn't truly understand the level my dataset was at. Put simply, the dataset was supposed to represent crime incidents. We might have expected that every row of the dataset was unique at the (Offender_ID, Victim_ID, Incident_ID) level. However, upon closer examination, this wasn't true and there wasn't any small subset of columns over which the dataset lacked duplicates - i.e. it didn't have a well defined "level", making it hard to carry out predictions. 

Fixing this problem involved going back and examining the ways the data was originally loaded and merged (see #2), then modifying it and carrying out a lot of deduplication and data cleaning to get the dataset to a single level. 

Doing this work ultimately sped up my productivity a great deal. But a lot of time was wasted prior to this just because I hadn't spend adequate time early on understanding my data and the work done before I came onboard.

