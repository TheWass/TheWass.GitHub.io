---
layout: post
title: Petri Nets
date: 2014-12-15 15:06:13
modified: 2015-01-22

categories:
- Software Engineering Series
tags:
- Diagrams
- Petri nets
- Requirements
- Software Engineering

---
The petri net model is a formal model which models state information a little differently than finite state machines. Petri nets are designed to model asynchronous processes, something that FSMs cannot innately do. Asynchronous processes in this case can refer to many different processes running in tandem, at different times, or even on separate machines. Unlike FSMs, petri nets are non-deterministic, meaning knowing the current state, we cannot determine the next state. These traits should become apparent later on in this post.  

So, let's dive right in! A petri net (Ƥ) is given by this 6-tuple:

> Ƥ = (P, T, F, W, K, M<sub>0</sub>)

where:

*   P is the set of places  
     P cannot contain anything in T.

*   T is the set of transitions  
     T cannot contain anything in P.

*   F is the set of firing relationships: F = P x T ∪ T x P  
     The set F is the cross product of the sets P and T unioned with the cross product of the sets T and P.  
     This will make more sense later.

*   W is the set of weights for each firing relationship: F → ℕ  
     The set W contains a set of integers which are implied by the set F.

*   K is the set of capacities for each place: P → ℕ  
     The set K contains a set of integers which are implied by the set P.

*   M<sub>0</sub> is the set of initial markers for each place: P → ℕ

Petri nets are quite complex, but as a result, they are quite useful. Let's analyze this one step at a time:  
 Places are best thought of as conditions, or critical points in the software's control flow. Some point where the program waits for a trigger coming from another place in the software. Transitions handle these triggers, combining triggers coming from some places and sending them on to other places.

Since pictures are way better at explaining than I am, here's a graphical example:

[![pnh2o]({{ site.url }}/images/reqeng/pnh2o.png)]({{ site.url }}/images/reqeng/pnh2o.png)

Since water is the foundation for beer, we'll use this example first. This petri net represents the transition from two hydrogen and two oxygen atoms to form the H<sub>2</sub>O (water) molecule. Notice that this, like the FSM, is a directed graph. However, there are two different types nodes in the graph, circles representing places, and squares representing transitions. Notice that transitions _only_ point to places and places _only_ point to transitions. Transitions cannot point to other transitions and places cannot point to other places.

Now, let's fill in the variables of the tuple Ƥ:

> P = {H, O, H<sub>2</sub>O} This is all the circles.  
>  T = {t1} This is all the squares.  
>  F = { {H, t1}, {O, t1}, {t1, H<sub>2</sub>O} } This is all the arrows (and their direction).  
>  W = {2, 1, 1} This is the weights of all of F (default is 1).  
>  K = {∞, ∞, ∞} This is the capacity of all places (default is ∞).  
>  M<sub>0</sub> = {0, 0, 0} This is the initial marking for the places.

Notice the notation of F. Like δ in a finite state machine, this is not a complete cross product. Otherwise, we'd have arrows going from everything to everything. We only include elements in F that actually matter, this makes analyzing petri nets a whole lot easier. Also notice that elements in F can be either <place>,<transition> or <transition>,<place> depending on the direction of the arrow. For W, K, and M, the order of the elements depends on the order of parent set, so W[i] corresponds to F[i], K[j] corresponds to P[j] and M[k] corresponds to P[k]. In essence, this means that each place (circle) has a capacity (K) and an initial marking (M); also, each pointer (arrow) has a weight (W).

This particular petri net is only marginally useful, It tells us places, transitions, and relationships, but not much else. We need to add some initial markings to it to make it executable. Lets change M<sub>0</sub> to {2, 2, 0}.

[![pnh2o1]({{ site.url }}/images/reqeng/pnh2o1.png)]({{ site.url }}/images/reqeng/pnh2o1.png)

Ah, Now we can get somewhere. Petri nets execute by firing transitions. A transition that fires will _consume_ as many markings on the input places as the corresponding weight specifies. Once fired, the transition then _produces_ as many markings on its output places as the corresponding weight specifies. An enabled transition means that _all_ the input places contain enough markings match or exceed the weight of the firing relationship. In this petri net, t1 is considered enabled since H contains at least 2 markings and O contains at least 1\. Since this is enabled, let's fire it.

[![pnh2o2]({{ site.url }}/images/reqeng/pnh2o2.png)]({{ site.url }}/images/reqeng/pnh2o2.png)

Notice what happened: Since H needed to have two markings in order to activate t1, both were consumed. O only needed one marking, so only one was consumed. H<sub>2</sub>O gained one marking since the weight of the firing relationship is only one. Now, t1 is deactivated, and the petri net is considered dead, since no transition can fire. This could represent the expected end state of a program, or some critical error occurred which terminated the program. Either way, the process is complete.

* * *

That pretty much sums up the basics. Now that this petri net idea has started fermenting, let's make a little beer:

[![pnbeer]({{ site.url }}/images/reqeng/pnbeer.png?w=300)]({{ site.url }}/images/reqeng/pnbeer.png)

This particular petri net is very similar to the first FSM shown on the previous post. Notice, You can have transitions with zero inputs or outputs. These become marker generators and sinks. Transitions without any _input_ arrows are always enabled. Let's fire the Order Beer transition.

[![pnbeer2]({{ site.url }}/images/reqeng/pnbeer2.png?w=300)]({{ site.url }}/images/reqeng/pnbeer2.png)

Alright, Now we have an order, and another transition is now active: Serve Beer.  
 Here's the awesome thing about petri nets: The order in which transitions are fired is entirely non-deterministic. This means that transitions are fired independently and randomly of any other transition in the net. At any point in time, any transition may fire, even multiple transitions at once, so long as they are active. In this case, we can fire the Order Beer, and Serve Beer transitions since they are both active.  
 Let's fire Serve Beer.

[![pnbeer3]({{ site.url }}/images/reqeng/pnbeer3.png)]({{ site.url }}/images/reqeng/pnbeer3.png)

Notice that each output gets one marking. The number of markings on the input does not determine the number of markings on the output. Also, notice that each set of markings roughly corresponds to a particular _state_ in this finite state machine:  
[![fsm]({{ site.url }}/images/reqeng/fsm.png?w=300)]({{ site.url }}/images/reqeng/fsm.png)

We started out in the state Enter Bar, and then moved to the state Beer Ordered, and now we're in the Drink Beer state. With a petri net, states are represented as the _set of markings_ at a particular instance. There is one distinct difference between the FSM and the petri net: Remember that the order beer transition in the petri net has no input arrow. This means it can fire at any time. Because of this, the petri net can simulate multiple orders at once. The FSM cannot.

* * *

Remember the more complicated instance, when the bartender took our keys after 3 drinks? Here's a petri net for that:

[![pnbeerkeys]({{ site.url }}/images/reqeng/pnbeerkeys.png)]({{ site.url }}/images/reqeng/pnbeerkeys.png)

The Beer Limit place has 3 initial markings on it, indicating the three drinks before the bartender refuses service. Once three beers have been served, the Beer Limit place is empty, the Serve Beer transition deactivates, and the Take Keys transition activates. The Keys Limit is there to ensure that our keys cannot be taken more than once. Once the Take Keys transition fires, there is no way to fire it again, since the Keys Limit place cannot be replenished.  
 Alright, ordering three beers puts three markers on the Order place. Then we can serve those three beers. The end result is shown here:

[![pnbeerkeys2]({{ site.url }}/images/reqeng/pnbeerkeys2.png)]({{ site.url }}/images/reqeng/pnbeerkeys2.png)

Once we get to this state, We now can fire Take Keys, Pay Bill, or Order Beer. (Just because we can order beer does not mean the bartender has to serve us beer.) Notice that Take Keys requires 3 marks to be in the Beer place in order to activate. Let's fire Take Keys:

[![pnbeerkeys3]({{ site.url }}/images/reqeng/pnbeerkeys3.png)]({{ site.url }}/images/reqeng/pnbeerkeys3.png)

See that all three marks in Beer have been consumed by Take Keys, and that Keys Taken now has a mark. Take a look at Replenish Limit and the arrows going in and out. Notice that Replenish Limit is now active (one arrow from Keys Taken). When Replenish Limit fires, the token on Keys Taken gets consumed, and two tokens get produced, one to put on Beer Limit, and one to put back on Keys Taken.

[![pnbeerkeys4]({{ site.url }}/images/reqeng/pnbeerkeys4.png)]({{ site.url }}/images/reqeng/pnbeerkeys4.png)

As you can see, the mark on Keys Taken enables Replenish Limit to act as a generator for Beer Limit. Replenish Limit always refreshes the mark on Keys Taken ensuring that Replenish Limit never gets disabled. At this point the number of marks on Beer Limit is unbounded, and you can have all the beer you want!

* * *

That about sums it up for the basics of petri nets and how they work and simulate. Notice that I hardly touched on K (the capacity of a place). This is mainly because it is not an often used feature, and largely unnecessary in most petri nets. Basically, a transition cannot fire if firing would exceed the capacity of an output place. There is a lot of analysis that can go into studying and analyzing a petri net. I'll hit some of these up in the next post. [Stay tuned!]({% post_url 2015-02-02-petri-net-analysis %})
