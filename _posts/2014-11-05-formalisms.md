---
layout: post
title: Formalisms
date: 2014-11-05 15:03:49
modified: 2014-12-17

categories:
- Software Engineering Series
tags:
- Diagrams
- Finite State Machine
- FSM
- Requirements
- Software Engineering

---
Now, onto the fun stuff! Once the written requirements are adequately diagrammed and documented, the final stage in formalizing the requirements can begin. Remember, the reason for formalizing the written requirements is so the developers can prove that the code they develop is _correct_ according to the requirements. This is the step where the actual process is defined, laid out, and formalized.  

So far, we've looked at three semi-formal diagrams:

*   **Use case diagrams** answer:  
     'Who uses the system and how?'
*   **Data flow diagrams** answer:  
     'How is data manipulated in the system?'
*   **Sequence and collaboration diagrams** answer:  
     'In what order is control passed between objects during a procedure?'

These diagrams are great at informally describing the system. They are relatively easy to interpret, and the models can be built at different levels of complexity to suite a specific audience. But none of these models can answer the questions most pertinent to developers:

*   Given some input, what are the actions that the software needs to perform?
*   Under what conditions is a computation 'successful'?

These questions imply very specific answers that simply cannot be answered looking only at the semi-formal diagrams. We need something more formal, a model that more accurately represents an actual computer.  
 Please note: These formal models have roots in set theory and graph theory. This enables them to model computer systems rather accurately. I'll try to explain the set notations as I go, but if you have any questions, leave a comment.

* * *

# Finite State Machine

A finite state machine (FSM) is a formal model of computation that can describe and _simulate_ the execution of a process. Given a set of inputs, the FSM always produces an expected output - just like a program would.

An FSM can be generalized with this tuple:

> F = (Σ, S, s<sub>0</sub>, T, δ)

where:

*   Σ (Sigma) is a set of all inputs that the FSM can accept. Also called the alphabet. In terms of formalized requirements, this could be a data input or an event.

*   S is a set of internal states: S = {s<sub>1</sub>, s<sub>2</sub>, s<sub>3</sub> ... s<sub>n</sub>}  
     The set 'S' denotes the collection of states, and 's' represents a single state.

*   s<sub>0</sub> represents the starting state: s<sub>0</sub> ∈ S  
     The element 's<sub>0</sub>' is in the set 'S'

*   T is a set of final or accepting states: T ⊆ S  
     The set 'T' is a subset or equivalent to 'S'

*   δ (delta) is a state transition function: δ : S x Σ → S  
     The δ function is defined as the cross product of the set of states and the set of inputs which yields the set of states.

Ok, so some of this may look like Greek to you, but bear with me, I'll try and explain it all. Basically, an FSM boils down to two elements: states and transitions between those states.  
 A state is basically a status, or snapshot of a system that is waiting to execute a transition. A transition is a set of actions to be executed when a condition is fulfilled or when an event is received; the result of which is a state. δ as described above represents the transition function for the FSM. This transition function takes two parameters: the current state and some input, and it returns a state. This can easily be modeled with a table showing the two parameters, and the output.

Example FSM:  
 Σ = {order beer, serve beer, drink, pay bill}  
 S = {Enter Bar, Beer Ordered, Have Beer, Have Bill, Leave Bar}  
 s<sub>0</sub> = Enter Bar  
 T = {Leave Bar}  
 δ =

| Current State (s) |  Input (σ) | New State (s)
|:------------------|:----------:|:-------------
| Enter Bar         | order beer | Beer Ordered
| Beer Ordered      | serve beer | Have Beer
| Have Beer         | drink      | Have Bill
| Have Bill         | order beer | Beer Ordered
| Have Bill         | pay bill   | Leave Bar

So, we're back to the bar example! This is a fairly simplistic example of a finite state machine, but it is one nonetheless. Remember that FSMs can have more than one finishing state, but they always have only one starting state.  
Now, about the δ function: Recall that the definition is the cross product of _all_ of the states and _all_ of the inputs. There should be 20 entries in the table: 4 inputs and 5 states yields 20 transitions. The δ function is typically represented as a partial function, meaning it does not include every possible transition by design. In practice, the input alphabet for a software system's FSM is unfathomably large.   
Think about it: Σ needs to account for _every single input_ that the software system can handle. If we want to model a single input box on a website, we have to account for _every single_ character combination up to 255 characters. Using UTF-8 (which encodes 1,112,064 characters), that's 283,576,320 unique inputs. Got another text box? Square that number. ... You get the idea...

This large amount of inputs is the reason why δ is partial. From a software standpoint, this offers a unique advantage. Go back to the bar example, and look at the δ function. δ is supposed to be a complete cross product, but it is intentionally not. So, in programming terms, what do the _missing_ entries represent? Exceptions. Given a δ function for all valid transitions, we also implicitly have the entire set of all possible exceptions present in the software. By analyzing the list of possible exceptions, we can, for instance, determine which states have the greatest possibility of failure, and therefore, focus exception handling on those states.

The table is nice, but it can get quite unwieldy as the δ function gets larger. So, let's graph it!

[![fsm]({{ site.url }}/images/reqeng/fsm.png)]({{ site.url }}/images/reqeng/fsm.png)

Ahh, much better! This is much easier to parse through and follow. Notice that the end state is shown with a double outline, and the start state is indicated by a start arrow.   
As you can see, this is a pretty standard directed graph, which means that we can analyze this using graph theory and techniques.

First thing to note about this graph (and FSMs in general) is that there is a loop in the graph, and under certain conditions (like you never wanting to pay the bill) the FSM will run forever. This is ok. There's no rule that says a Finite State Machine cannot run forever, just that there cannot be an infinite number of states. Using this graph, the major decision points in the system are apparent in the graph. In this case, there's a decision to pay the bill, or make another order when the Have Bill state is active.

The bar example is fairly simplistic, so, let's make it a little more complex. How about, in this bar, the bartender takes your keys after you've had 3 beers (rough right?). In order to accomplish this, we need a counter FSM. We can represent the counter using a state for every count. In this case, there are four states; one for each count, and one for when the count is finished.

[![fsmcount]({{ site.url }}/images/reqeng/fsmcount.png)]({{ site.url }}/images/reqeng/fsmcount.png)

So now, we have two FSMs. One containing the bar actions, and another containing the count. Remember from before: FSMs are grounded in set theory. The states are organized in a set, and the δ function can be represented as a set of transitions. Because of this, we can create a new FSM that is the _cross product_ of the two that we currently have. After removing states and transitions that aren't necessary or logical in the system, you get something like this:

[![fsm2]({{ site.url }}/images/reqeng/fsm2.png)]({{ site.url }}/images/reqeng/fsm2.png)

That's the real power of finite state machines. Combining them is very much a mathematical process. As the count FSM gets bigger, so will the cross product FSM. As you can see, integrating the count FSM creates this 'staged' effect where each stage is repeated until some action occurs. In this case, your keys are taken away. Once they are, then the FSM continues looping for as many beers as you desire. For fun, let's relax the restriction a bit: Now, the bartender will only take your keys if you've had three drinks within a 60 minute period:

[![fsm3]({{ site.url }}/images/reqeng/fsm3.png)]({{ site.url }}/images/reqeng/fsm3.png)

HoliKrapp! That got complicated! Let's walk through this one.  
 Think of the timing system as a queue: Drink a beer, and put the timer in the queue. If the timer on the front of the queue reaches 60, pop it off. If there are three timers queued up at any time, the bartender takes your keys.  
 So you get your first beer, and the timer starts. You drink it in 20 minutes and get your bill. You're at 1 beer / hr. You order your second, and take 45 minutes to drink it. Your first beer timer is at 65, and the second is at 45; you're still at 1 beer/hr. Since the first timer expired, it falls out of the queue and the second timer becomes the first timer in the queue. The process repeats. Once again, after you've surrendered your keys, the timing/counting process stops, and you can imbibe to your heart's content.

I don't know about you, but I'd like my keys back! So, the bartender gives some leeway and says you can get the keys back after 30 minutes for each drink you've had. For instance, you'd have to wait 2 hours if you've had 4 drinks. Each drink adds another 30 minutes to the waiting period.

So, where's the graph? Turns out that this _cannot_ be modeled using a finite state machine due to the infinite number of states required. A state is required each time a drink is taken since we need to count the number of drinks in order to determine the amount of time to wait. Theoretically speaking, there is no limit on how many drinks you can have during a 30 minute period, and therefore, no limit on the number of states.

However, this does not mean that this is un-codable; it certainly is: `int minutes = 30 * drinks.length;` Done. It does mean that the process could run out of memory and cause errors given the right conditions. Each new state requires more memory, and eventually, that memory runs out. Memory is bounded in a finite state machine; an infinite state machine has no such bound.

Finite state machines also lack a mechanism to easily model parallel processing, or asynchronous processing amongst multiple software systems. They _can_ model this, but not easily. If S1 and S2 represent the set of states from the two processes, the resulting FSM would have S1 X S2 states, and the graph would be quite unwieldy.

* * *

## Summary

Finite state machines can display a lot of characteristics about a software system without actually building. Developers can analyze and determine where the problem areas, exception handling, memory leaks, are before a single line of code is written, even before the software is _designed_! Remember back on the [Requrements Engineering]({% post_url 2014-10-08-requirements-engineering %}) page, Requirements engineering is required to ensure that the developer is creating the _correct_ software. The FSM is a key tool in the requirements specification for developers to _prove_ their software is correct: Get to some state, provide some input, and check if the new state matches the FSM.

FSMs aren't the end-all be-all penultimate formalism, they have many flaws: infinite states are not possible, counting is limited, and there is no easy way to model parallel processes - just to name a few. There is another formalism that I will discuss next called Petri nets. Petri nets fill in the gaps that FSMs cannot handle, while showing similar information. [Stay tuned!](({% post_url 2014-12-15-petri-nets %})
