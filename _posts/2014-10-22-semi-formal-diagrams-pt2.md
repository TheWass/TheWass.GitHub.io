---
layout: post
title: Semi-Formal Diagrams pt2
date: 2014-10-22 14:58:39
modified: 2014-12-17

categories:
- Software Engineering Series
tags:
- Diagrams
- Requirements
- Software Engineering

---
Ohkay, where was I?... Ah, yes. Two more diagrams: **Sequence** and **Collaboaration**. The goal of these two diagrams is to fill in the missing information that use case diagrams and data flow diagrams do not have: A specific order to events. Sequence and collaboration diagrams both show the same information, but in two different ways. Sequence diagrams emphasize a temporal element and collaboration diagrams emphasize the relations between events. Remember: Sequence and collaboration diagrams show the _control_ flow. Data flow diagrams show the _data_ flow. Although the data flow could imply the control flow, it does not necessarily have to.  

## Sequence diagram

A sequence diagram is an interaction diagram modeling a single scenario as it executes in the system. There are two elements in this diagram: A **participant** is an object or entity participating in the scenario. A **message** is a communication among participants. The participants are arranged on one axis and time is the other axis.

Here's an example:  
[![sequence]({{ site.url }}/images/reqeng/sequence.png)]({{ site.url }}/images/reqeng/sequence.png)

Quick rundown of the notation: The blocks at the top are the participants, which can be an actor or an object. Time runs vertically downward. The blocks along the timelines represent how long that particular participant is active. The solid arrows represent transfer of control, and the dashed arrows represent a return statement. Using this diagram, it is quite easy to see the chronological ordering of events and how long each process is being kept open, relatively speaking. The downside to this diagram is it can get cluttered pretty quickly as more complexity is added.

## Collaboration diagram

The collaboration diagram fixes this issue by reorganizing the diagram into connected blocks similar to a UML class diagram. This puts focus on the relationships rather than the time. The ordering is still present, but in a different form.

Here's an example:  
[![collaboration]({{ site.url }}/images/reqeng/collaboration.png)]({{ site.url }}/images/reqeng/collaboration.png)

As you can see, the numbering determines the order of the operations. The information presented in both the sequence and collaboration diagrams is the same. Given one, you can easily create the other.

* * *

# Summary

Here's the rundown:

*   Use case diagrams show user-program interactions.
*   Data flow diagrams show the flow of data throughout the system.
*   Sequence and collaboration diagrams show the flow of control between processes.

All three of these diagrams show uniquely different information, and no single diagram can contain all the information of the other two. Ideally, these three diagrams combined contain all of the information that is in the written requirements specification, just in picture form. Looking at and referring back to these diagrams is integral for transforming these semi-formal models into the formal, provable, mathematical models which software engineers need to show that the code they are constructing meets the requirements. [Stay tuned!]({% post_url 2014-11-05-formalisms %})
