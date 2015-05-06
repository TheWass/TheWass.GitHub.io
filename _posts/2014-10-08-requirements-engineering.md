---
layout: post
title: Requirements Engineering
date: 2014-10-08 14:40:02
modified: 2014-12-17

categories:
- Software Engineering Series
tags:
- Documentation
- Requirements
- Software Engineering

---
There are a couple of assumptions that are best practice when it comes to requirements engineering:  
The customer is always right, but...

*   The customer knows nothing about software.
*   Requirements always change.

You all might have heard about the Healthcare.gov debacle back in October of 2013. 
What you might not know is the reason why it was so terrible.

In July of 2014, the US Government Accountability Office released a 66 page report titled:
[HEALTHCARE.GOV: Ineffective Planning and Oversight Practices Underscore the Need for Improved Contract Management](http://www.gao.gov/assets/670/665179.pdf){:target="_blank"}
An [article on zdnet.com](http://www.zdnet.com/the-billion-dollar-web-site-you-paid-for-7000032564/){:target="_blank"}
summarizes the report. Basically, the issue stemmed from a sudden move to Agile-based
development and a distinct lack of requirements specification. Check this out:

[![Obamafail]({{ site.url }}/images/reqeng/obamafail.jpg)]({{ site.url }}/images/reqeng/obamafail.jpg)

The first timeline shows the initial projected milestones for the Healthcare
marketplace website. The second is the revised one after they finished the requirements and design.  
They initially thought the requirements would only take three months. Instead,
they beat their collective faces over the requirements for a whole year!

So why wasn't the release date changed? Politics. Obamacare became effective on October 1, 2013.
So that was the hard deadline for the website. It _had_ to go live on that date. True to form,
in government, when something goes wrong, throw more money at it! Check this out:

[![Obamafail2]({{ site.url }}/images/reqeng/obamafail2.jpg)]({{ site.url }}/images/reqeng/obamafail2.jpg)

The bubbles indicate the cumulative amount of money thrown at Healthcare.gov and when.
They also indicate when the project was reevaluated to receive such funds. The project
was given just under $91.5M for the requirements, and was evaluated twice in a 1.5 year period.
The next 7 evaluations and $118M were all during development, testing, and verification.
This indicates to me that all throughout development there were numerous times when
the developers were thinking "Oh, !@#$, in order to get this thing done, we need to have
this other thing implemented. Therefore, we need more money!" None of that would have happened
if the requirements were _actually_ good.

Alright, I'm done poking fun at the US Government (for now). That goes to show how important
a proper development plan is.

* * *

# Requirements Engineering

Every great product starts out with a great foundation. Constructing that foundation
is what requirements engineering is all about. A good strong methodology for extracting
and defining software requirements is essential. It helps engineers answer
a fundamentally important question: **Am I building the _correct_ system?** _Correctness_
in this case means that the resulting software behaves according to its requirements.
Requirements Engineering is essentially a process that takes a list of features
and requirements requested by the customer and transforms it into a set of formal specifications
that are verifiable and mathematically provable. This allows the developer to show
that the software conforms to the requirements specification.

## Requirements Engineering Process

### Define Requirements

The first step is to define the requirements. A simple bulleted list will do.
Listing out the functional and non-functional requirements for the software
gives structure to the eventual written specification.

To get the functional requirements, ask the client: **"What do you need the software to do?"**
The responses should give the developer a description of expected behavior of a feature,
the effects given a specific input, and the importance of that feature to the system.  
Think of a security system. An example functional requirement would be:
"The system shall, given a valid key code, unlock the door and activate other devices."
This requirement would also be assigned an importance value: Low, Medium, High,
or some numeric scale, 1-5 etc. In the end, the type of scale really doesn't matter,
so long as it is clear which features the client places the most value in.

To get non-functional requirements, ask the client **"What qualities does the software need to exhibit?"**
The responses should give the developer an idea of how fast it needs to be,
what platforms it needs to run on, any legal issues, any interface requirements,
what language it should be written in, etc.

Both of these lists are equally important, and both of these lists are considered
while designing the software. The software is expected to satisfy all these functional
and non-functional requirements.

### Written Specification

The written specification is a full text description of the system designed for use
by non-technical stakeholders. It is created based on the two lists of requirements
as defined in the last step. The description must not have implied side effects,
statements must not contradict each other, and all desired features must be included
in their entirety. _Precision_, _consistency_, and _completeness_.

Consider this specification fragment for a word-processor:

> Selecting is the process of designating areas of the document that you want to work on.
Most editing and formatting actions require two steps: first you select
what you want to work on, such as text or graphics; then you initiate the appropriate action.

Looks pretty good right? Read it again. Notice that the first sentence is talking
about selection. The next sentence is about editing and formatting. Bad consistency.
It also talks about "Most editing and formatting actions..." What about the other editing
and formatting actions? Bad completeness.  
Here's another fragment:

> The whole text should be kept in lines of equal length. The length
is specified by the user. Unless the user gives an explicit hyphenation command,
a carriage return should occur only at the end of a word.

This fragment talks about "lines of equal length. The length is specified by the user."
This says nothing about an upper bound or a lower bound. What if the user specifies
a 1 character line length? Bad precision. Also, what if the line length is met,
but the word is incomplete? Bad consistency.

My point here is, if a requirements document is not precise, consistent, and complete,
it introduces ambiguity. Ambiguous statements are impossible to verify:  
"The product shall have a good user interface." _You can't measure good._  
"The product shall be error free." _I can design a program that squelches errors no problem._  
"The product shall respond to the user with 1 second for most cases." _What about the other cases?_

In order to write a good description, iterative feedback between the client
and the developer is a _must_! There is no formal method to convert a list of features
into a written requirements document. How good this document is depends on the writer.
So, while writing, avoid imprecise wording, inconsistencies, and incompleteness throughout
the entire document. The less of these, the better the description is.

But is a written description enough? Even a well written one still has faults,
holes are difficult to detect, and contradictions may be hidden within the document.
It might be good enough for the non-technical people, but _it is insufficient for developers_.
It is difficult to convert a written specification directly into code.
Formalisms are required to convert this qualitative description to a quantitative,
verifiable, and provable model. Yes, it can be done. But there are a few intermediary steps
before we can get there. [Stay tuned!](({% post_url 2014-10-17-semi-formal-diagrams %})
