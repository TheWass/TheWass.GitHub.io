---
layout: post
title: Requirements Conclusion
date: 2015-02-04 15:15:11
modified: 2015-02-04

categories:
- Software Engineering Series
tags:
- Requirements
- Software Engineering

---
First off, I don't expect everyone to immediately start using petri nets in their requirements, in fact, I don't expect to see them at all in my career. Even they are extraordinarily useful, creating an accurate petri net is a royal pain in the @$$. However, there are tools out there to help create them, and in particular, running simulations. Webapps are not typically concerned with concurrent users and multitasking where collisions may occur, all that is handled by the web and database servers. For the case of webapps, the Finite State Machine formal model is quite sufficient. The FSM is very effective with web pages since each page can be a state: very easy to construct, very easy to interpret.

My version of well documented requirements would include:

*   Listing of: [Functional requirements and non-functional requirements]({% post_url 2014-10-08-requirements-engineering %})
*   [Written specification]({% post_url 2014-10-08-requirements-engineering %})
*   [Semi-formal diagrams]({% post_url 2014-10-17-semi-formal-diagrams %}) to describe user and data interactions
*   [Finite State Machine]({% post_url 2014-11-05-formalisms %}) diagram (or some other formal model describing state.)

Including all of these items result in the software itself being considered well documented, even before a single line of code is written.

I know, no one wants to work on requirements. They're boring, and nothing is getting created. That's what we really want to do as programmers, code something, create something new, and say "Look! I created this awesome thing!" And as programmers, we can do that! We can create whatever we want and bend the computer or server to our will. It's exhilarating! Unfortunately, we are not being paid to create whatever we want. We are paid to create something that someone else wants. And along with that is a bunch of people who are also being told to make the same thing with you. Everyone has a different vision of what the end product should be. Not strictly defining requirements to where everyone can understand them can spell certain doom for the project.

Regardless, the point I am trying to make with this series is mainly this:  
There is a _lot_ more we can be doing to flesh out requirements before design is even conceived. It may not be easy, or fun, but going through more requirements iterations, and in particular, diagramming use-cases, data flow, and process flow would help in creating something that we know our customers actually want. Remember, we use requirements to determine if software runs correctly. This correctness is interpreted by the customer. It is therefore imperative that both the developers and the customer are on the same page using language that both parties can understand fluently. For customers, it's the high level diagrams and written requirements. For developers, low level diagrams and formal models help us the most in understanding what a software will become and how it should work. In my opinion, both are necessary to fully describe a software application.
