---
layout: post
title: Semi-Formal Diagrams
date: 2014-10-17 14:53:13
modified: 2014-12-17

categories:
- Software Engineering Series
tags:
- Diagrams
- Requirements
- Software Engineering

---
Once the written specification is hammered out, the process of formalizing the requirements begins. The jump from a written description to a formal model is quite large. Semi-formal models initializes this translation by creating diagrams that are more code-like in nature, but are abstract enough to be read and understood by non-technical personnel. These diagrams put the written specification into picture form. There are four different types of diagrams: **Use-Case**, **Data Flow**, **Sequence**, and **Collaboration**.  

* * *

## Use-Cases

Use-cases represent who the users of the system are, and how they interact with it. Each use-case describes a set of related scenarios that could happen in the system given an initial state, and some trigger. Each one of these scenarios features a single actor interacting with a single feature. Essentially, use-cases describe how actors and events are related. Use-cases help developers define the scope of the system by generalizing the ways that users will interact with it. The best way to identify use-cases is to sit down with the user and tell them to _show_ what they do in each scenario that they are involved with. The goal is to understand how they interact with the system. In nearly every case, showing someone else a procedure is more informational than describing that same procedure. In other words, go by what they do, not by what they say they do. This will yield far more accurate information.

## Use-Case Diagram

There are two entities in a use-case diagram (UCD); actors, and events. An actor is an outside entity that is acting on your program. Each event represents some functionality that the actors interact with. The events and actors are related by using different types of relations. Actors can initiate or participate in an event, and events can include or extend other events.

Take a look at this UCD of a bar:  
[![usecase]({{ site.url }}/images/reqeng/usecase.png)]({{ site.url }}/images/reqeng/usecase.png)

Here's some semantics: The **actors** are stick figures, and the **events** are ovals. The events inside the box belong to the system. Events that appear on the outside of the box indicate events that aren't controlled by the system, but the system relies on them. The actor to event relationships come in two flavors: **initiate**, and **participate**. An actor cannot participate in an event until it has been initiated. For instance, the client cannot receive the beer until the bartender serves it. The event to event relationships indicate dependencies. The **include** association indicates that one event requires behavior in another event. The **extend** association indicates that one event can modify or add to the capabilities of another event.

The include and extend are rather similar in concept, but there is one main difference: **extend is optional**, **include is not**. For example, since "Serve Beer" includes "Fill Glass", you _must_ complete the "Fill Glass" event before the "Serve Beer" event can be completed. In another example, "Put on Tab" extends "Pay for Beer", the program _can choose_ whether to leave the "Pay for Beer" event unmodified, or choose to extend it with the "Put on Tab" event, which would change the functionality of "Pay for Beer".

Remember, a use-case diagram shows a collection of scenarios; basically, who does what. This is useful for the developers because it shows who is involved with certain parts of the system. It does have a couple of downfalls: This diagram does not show relationships between main events. Just looking at this diagram, we cannot determine whether to restock or serve beer first, nor can we see what determines which beer to serve. This is where the other diagrams come in.

* * *

## Data Flow Diagram

The data flow diagram (DFD) shows right what it says on the tin, the flow of data throughout the system. It also shows the processes that transform the data, and the data stores to hold the data.

There are four main elements each with their own notation:

*   **External Entity** - A data source or sink
*   **Process** - Models of data transformations
*   **Data Store** - Objects that permanently store data used by the system
*   **Data Flow** - Links the other three elements together and indicates the type of data that flows between the two elements.

The DFD has three different types based on the level of abstraction. The **top level** shows the data flow between the system and external entities. The **mid level** breaks the system apart into distinct processes, and shows the data flow between the processes and the external entities. The **bottom level** breaks each process down into several sub-processes. As the levels get lower, the level of detail increases. Similar to zooming in and out on a map or a picture.

Here's an example of a top level DFD:  
[![dataflow0]({{ site.url }}/images/reqeng/dataflow0.png)]({{ site.url }}/images/reqeng/dataflow0.png)

The top level is designed to show an overview of data (beer) flowing in and out of the system to each of the external entities. In this diagram, External entities are shown as boxes, processes are shown as boxes with rounded edges, and the data flow is shown by arrows. The kind of data is indicated by a label on the arrow.   
You might have noticed that the bartender is missing entirely from this diagram. This is because the bartender is _not_ external to the system. The bartender acts as the data transformation agent that, for instance, takes a beer order and transforms it into beer. Basically, the bartender controls the bar, in the same way a programer controls a computer.

[![dataflow1]({{ site.url }}/images/reqeng/dataflow1.png)]({{ site.url }}/images/reqeng/dataflow1.png)

I like this diagram. Not only is it useful to both developers and non-technical personnel, but also the word "beer" appears 15 different times!  
 The mid level DFD breaks the top level down into sub-components, one for each major process within the system. You'll notice that the external entities are still there, and they are still sinking and sourcing the same data. This time, we can see where in the bar process each piece of data is being handled, and how it is being transformed.

Now we'll take the Tap Beer process down to the bottom level:  
[![dataflow2]({{ site.url }}/images/reqeng/dataflow2.png)]({{ site.url }}/images/reqeng/dataflow2.png)

Notice that the other processes become external data sources and sinks to process 2\. Since this diagram is only concerned with tapping beer, the other processes become data providers. At this level, sub processes can only generate one output, However, multiple different data sinks are able to use that one output. This feature makes them very compatible to programming functions or methods. In fact, these sub processes are likely functions that would call other helping functions in order to complete the task; similar to how a main() function works in procedural programming. Therefore, the bottom level DFD is quite useful for developers and programmers working on the project, but it is too unnecessarily detailed for the non-technical stakeholder. They would not find this particular diagram as useful as the top level.  
 Each mid level process should have one bottom level diagram attached to it. This could mean that there is 5-10 bottom level diagrams for a single software system.

It is certainly possible to combine all of these into a single data flow diagram, showing all of the bottom level processes at once. However, this would end up being a _massive_ diagram, far too much information for anyone to parse through effectively. Each DFD level has a unique intent and a unique audience; they should be treated as separate, but connected diagrams.

* * *

Alright, this post has gone on long enough. I still have two more diagrams to go through. But, I'll save them for the next post. [Stay tuned!]({% post_url 2014-10-22-semi-formal-diagrams-pt2 %})

* * *

All the diagrams shown in this post are generated using [draw.io](https://www.draw.io/). This is a great, free, online resource to use for constructing any sort of diagram. It also saves documents to the browser, which is pretty slick!
