## Topological Sorting In Java

### Introduction

When getting dressed, as one does, (I hope) you haven't had this kind of problem:

> Oh, it might have been a good idea to put on my underpants before getting into my trousers.

That's because we're used to sorting our actions **topologically**. Or in simpler terms, we're used to logically deducing which actions **have to come before or after other actions**, or rather which actions are prerequisites for other actions.

For example, everybody knows that when you want to play music with your band, you:

1. Decide what you want to play
2. ~~Practice~~
3. ~~Practice~~ during rehearsals
4. ~~Practice~~ during performance
5. Profit

In that **EXACT** order.

In this article we will be talking about the following subjects:

- [Introduction To Graphs](#introductiontographs)
- [Basic Topological Sorting Concept](#basictopologicalsortingconcept)
- [Examples](#examples)
- [Conclusion](#conclusion)

### Introduction To Graphs

We can't talk about Topological Sorting without first talking about **Graphs**. 

A Graph is simply a structure that represents a set of objects that have certain relations between each other. The objects being represented by **nodes** (circles) and the individual relations by **edges** (the lines).

There are many different kinds of graphs, but for the problem at hand we need to learn what is a `Directed Acyclic Graph`. Let's dissect the big bad math term into pieces:

#### Directed

A graph is `directed` if every relation between 2 objects doesn't have to be bidirectional (it has to have a direction), unlike a `unidirectional graph` where **every** relation has to go both ways.

In the graph below, the relation `C-A` is unidirectional, which means `C` has a relation with `A`, and `A` has a relation with `C`.

![Simple graph](D:\StackAbuse\Toposort\graphics\Untitled-1.png)

On the other hand, in the following graph, the relation `C-A` is directed, which means  `A` has a relation with `C`, but `C` doesn't have a relation with `A`.

![Simple graph 2](D:\StackAbuse\Toposort\graphics\Untitled-2.png)

Because of this, we have to strictly define the `neighbors` of a node.

> Unidirected graph:
>
> ​	Two nodes (A and B) are neighboring nodes if a unidirected path exists between them.
>
> Directed graph:
>
> ​	`A` is `B`'s neighbor if a `direct, directed edge` exists which leads from `B` to `A`. The first direct in 	  	this definition refers to the fact that the **length** of the path leading from `B` to `A` has to be strictly **1**.

#### Acyclic 

A given graph is acyclic only if a `cycle` does not exist. A cycle is a path for any node X, which starts at X and leads back to X. The following graph is **not** acyclic because it contains a cycle (`X-B-C-X`).

![](D:\StackAbuse\Toposort\graphics\Untitled-3.png)

### Basic Topological Sorting Concept

So how does topological sorting look when used on a graph, and why does the graph have to be acyclic for it to work?

To answer these questions, let's strictly define what it means to topologically sort a graph:

> A graph is topologically sortable if a sequence `a1`,`a2`,`a3`... exists (`ai` being graph nodes), where for every edge `ai`->`aj` , `ai` comes before `aj` in the sequence.

If we say that `actions` are represented by `nodes`. The above definition would basically mean that an `undisputable order` of execution must exist.

To better understand the logic behind topological sorting and why it can't work on a graph that contains a cycle, let's pretend we're a computer that's trying to topologically sort the following graph:

![](D:\StackAbuse\Toposort\Untitled-1-[Recovered].png)

```pseudocode
# Let's say that we start our search at node X
# Current node: X
step 1: Ok, i'm starting from node X so it must be at the beginnig of the sequence.
    sequence: [X]
    
# The only available edge from X is X->B, so we 'travel' to B
# Current node: B
step 2: Right, B comes after X in the sequence for sure.
	sequence: [X,B]
	
# We travel to C using the edge B->C	
# Current node: C
step 3: Same thing as the last step, we add C.
	sequence: [X,B,C]
	
# Current node: X	
step 4: WHAT IN THE TARNATION, X AGAIN?
	sequence: [X,B,C,X]
```

This is the reason why we can't topologically sort a graph that contains a cycle .