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

![](D:\StackAbuse\Toposort\topoSort\graphics\Untitled-1.png)

On the other hand, in the following graph, the relation `C-A` is directed, which means  `A` has a relation with `C`, but `C` doesn't have a relation with `A`.

![](D:\StackAbuse\Toposort\topoSort\graphics\Untitled-2.png)

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

![](D:\StackAbuse\Toposort\topoSort\graphics\Untitled-3.png)

### Basic Topological Sorting Concept

So how does topological sorting look when used on a graph, and why does the graph have to be acyclic for it to work?

To answer these questions, let's strictly define what it means to topologically sort a graph:

> A graph is topologically sortable if a sequence `a1`,`a2`,`a3`... exists (`ai` being graph nodes), where for every edge `ai`->`aj` , `ai` comes before `aj` in the sequence.

If we say that `actions` are represented by `nodes`. The above definition would basically mean that an `undisputable order` of execution must exist.

To better understand the logic behind topological sorting and why it can't work on a graph that contains a cycle, let's pretend we're a computer that's trying to topologically sort the following graph:

![](D:\StackAbuse\Toposort\topoSort\Untitled-1-[Recovered].png)

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

This is the reason why we can't topologically sort a graph that contains a cycle , because the following two statements are both true:

- X comes before B
- B comes before X

And because of that, we can't determine an absolute order of the given actions.

Now because we're familiar with the algorithm's concepts, let's take a look at the implementation in java. First off, let's construct classes for defining nodes and graphs, and then using said classes, define the following graph:

![](D:\StackAbuse\Toposort\topoSort\graphics\Untitled-1-[Recovered].png)

```java
public class Graph {
      private List<Node> nodes;

      public Graph() {
            this.nodes = new ArrayList<>();
      }

      public Graph(List<Node> nodes) {
            this.nodes = nodes;
      }

      public void addNode(Node e) {
            this.nodes.add(e);
      }

      @Override
      public String toString() {
            return "Graph{" +
                    "nodes=" + nodes +
                    '}';
      }

      public List<Node> getNodes() {
            return nodes;
      }
      public Node getNode(int searchId) {
            for (Node node:this.getNodes()) {
                  if(node.getId()==searchId)
                  {
                        return node;
                  }
            }
            return null;
      }

      public int getSize()
      {
            return this.nodes.size();
      }
}
public class Node {
      private int id;
      private List<Integer> neighbors;

      public Node(int id) {
            this.id = id;
            this.neighbors = new ArrayList<>();
      }
      public void addNeighbor(int e){
            this.neighbors.add(e);
      }

      public int getId() {
            return id;
      }

      public List<Integer> getNeighbors() {
            return neighbors;
      }

      @Override
      public String toString() {
            return "Node{" +
                    "id=" + id +
                    ", neighbors=" + neighbors +
                    '}'+'\n';
      }
}

public class GraphInit {
      public static void main(String[] args) {
            Graph g = new Graph();
            Scanner sc = new Scanner(System.in);
            Node node1 = new Node(1);
            Node node2 = new Node(2);
            Node node3 = new Node(3);
            Node node4 = new Node(3);
            node1.addNeighbor(2);
            node2.addNeighbor(3);
            node4.addNeighbor(3);
            g.addNode(node1);
            g.addNode(node2);
            g.addNode(node3);
            g.addNode(node4);
            System.out.println(g);

      }
}
```

Output:

```java
Graph{nodes=[Node{id=1, neighbors=[2]}, Node{id=2, neighbors=[3]}, Node{id=3, neighbors=[]}, Node{id=3, neighbors=[3]}]}
```



Now let's implement the algorithm itself:

```java
private static void topoSort(Graph g) {

            //fetching the number of nodes in the graph
            int V = g.getSize();
            //list where we'll be storing the topological order
            List<Integer> order = new ArrayList<>();

            // map which indicates if a node is visited (has been processed by the algorithm)
            Map<Integer,Boolean> visited = new HashMap<>();
            for (Node tmp: g.getNodes())
                  visited.put(tmp.getId(),false);

            // we go through the nodes using black magic
            for (Node tmp: g.getNodes())
                  if (!visited.get(tmp.getId()))
                        blackMagic(g, tmp.getId(), visited, order);

            // we reverse the order we constructed to get the
            // proper toposorting
            Collections.reverse(order);
            System.out.println(order);

      }
private static void blackMagic(Graph g, int v, Map<Integer,Boolean> visited, List<Integer> order)
      {
            // mark the current node as visited
            visited.replace(v,true);
            Integer i;

            // we reuse the algorithm on all adjacent nodes to the current node
            for (Integer neighborId : g.getNode(v).getNeighbors()) {
                  if (!visited.get(neighborId))
                        blackMagic(g, neighborId, visited, order);
            }

            // put the current node in the array
            order.add(v);
      }
```

If we call `topoSort(g)` for the graph initialized above, we get the following output:

```
[4, 1, 2, 3]
```

Which is exactly right.



### Problem Modeling Using Topological Sorting

In a real-world scenario, topological sorting can be utilized to write proper assembly instructions for Lego toys, cars and buildings.

There's actually a type of topological sorting which is used daily (or hourly) by most developers, albeit implicitly. If you're thinking `Makefile` or just `Program dependencies`, you'd be absolutely correct.

A typical Makefile looks like this:

```makefile
area_51_invasion.out: me.c, the_boys.c, Chads.c, Karen.c, the_manager.c
	#instructions for assembly when one of the files in the dependency list is modified
```

With this line we define which files depend on other files, or rather, we are defining in which `topological order` *wink wink nudge nudge* the files should be inspected to see if a rebuild is needed. That is, if `area_51_invasion.out` depends on `the_boys.c` and  `the_boys.c` is for some reason modified, we need to rebuild `area_51_invasion.out` and everything that depends on that same file, that is everything that comes before it in the Makefile's topological order.



### Conclusion

Considering Toposort is basically something we do on a regular basis. You may have even implemented it in your software and didn't even know it. And if you haven't, I strongly suggest you give it a whirl.