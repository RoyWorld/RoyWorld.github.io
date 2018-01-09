---
layout: post
title:  "Topological Sort"
date:   2018-01-05 11:42:11
category: algorithm
published: true
author: RoyChan
tags:
- java
- algorithm
- graph theory
---

# activity on vertex network

### the definition in logic
摘抄自[某EDU][R2]:
> definition
> A directed graph in which the vertices represent tasks or activities and the edges represent precedence relations between tasks.

需要注意的是: 在AOV中是不能有cycles, 让一个activity的开始要以自身的结束为条件, 这显然是不合理的, 即: Directed Acyclic Graph

### the definition in mathematics
这个definition本质上就是DAG的definition
> definition
> A graph G = (V,E), V and E are two sets, each edge is represented by a ordered pairs <u,v>
> V: finite non-empty set of vertices
> E: set of pairs of vertices, edges

Example: a graph G
* $$V(G) = {0, 1, 2, 3, 4}$$, three vertices
* $$E(G) = {<0,1>,<0,3>,<1,2>,<3,1>,<3,2>}$$

![DAG](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20170105_DAG.png)

### the realization in computer science
```java
public class AOVNetwork {
    private int V;//no and tag of vertices
    private LinkedList<Integer> adjacency[];//adjacency list

    public AOVNetwork(int v) {
        V = v;
        adjacency = new LinkedList[v];
        for (int i = 0; i < v; i++) {
            adjacency[i] = new LinkedList<>();
        }
    }

    /**
     * add an edge from v to w
     * @param v
     * @param w
     */
    public void addEdge(int v, int w){
        adjacency[v].add(w);
    }

    /**
     * add edge from v to w1, w2, w3, ....
     * @param v
     * @param w
     */
    public void addEdges(int v, int... w){
        for (int ww : w) {
            adjacency[v].add(ww);
        }
    }

    @Override
    public String toString() {
        return "AOVNetwork{" +
                "V=" + V +
                ", adjacency=" + Arrays.toString(adjacency) +
                '}';
    }
}
```

# topological sort

### definition
摘抄自[Wikipedia][R4]:
> In the field of computer science, a topological sort or topological ordering of a directed graph is a linear ordering of its vertices such that for every directed edge uv from vertex u to vertex v, u comes before v in the ordering.
> For instance, the vertices of the graph may represent tasks to be performed, and the edges may represent constraints that one task must be performed before another

### about partial order and total order
这是一个mathematic上的概念, partial order指的是: 在部分元素间的先后关系不确定的情况下而产生的排序, 如:
```java
condition: x<=z, y<=z
result: x,y,z or y,x,z
```

而total order则是partial order的一个特例, 即: 在所有元素的先后关系都是完全确定的情况下而产生的排序, 如:
```java
condition: x<=y, y<=z
result: x,y,z
```

如果topological sort是unique, 则说明该order是一个total order, 同时DAG存在hamiltonian path, 否则说明order是一个partial order

# typical algorithms

解决topological sort的typical algorithms有两个:
* Kahn's algorithm
* DFS

这两个algorithms的time complexity都是O(\|V\| + \|E\|), 由于DFS是一个十分强大和通用的algorithm, 为减小篇幅, 这里仅Kahn's algorithm进行介绍

### Kahn's algorithm
#### Kahn's algorithm的伪代码:
```java
L ← Empty list that will contain the sorted elements
S ← Set of all nodes with no incoming edge
while S is non-empty do
    remove a node n from S
    add n to tail of L
    for each node m with an edge e from n to m do
        remove edge e from the graph
        if m has no other incoming edges then
            insert m into S
if graph has edges then
    return error (graph has at least one cycle)
else 
    return L (a topologically sorted order)
```

#### the realization in java
```java
public List<Integer> topologicalSorting(){
    LinkedList<Integer> sortedNodes = new LinkedList<>();//L, nodes that have bean sorted
    Set<Integer> zeroIndegreeNodes = new TreeSet<>();//S, nodes with no incoming edge

    LinkedList<Integer> traveseAdjacency[] = new LinkedList[V];//travese adjacency list

    //travese adjacency init
    IntStream.range(0, traveseAdjacency.length)
            .forEach(n -> {
                traveseAdjacency[n] = new LinkedList<>();
            });
    //tranform adjacency to travese adjacency
    IntStream.range(0, traveseAdjacency.length)
            .forEach(n -> {
                adjacency[n].stream().forEach(m ->{
                    traveseAdjacency[m].add(n);
                });
            });

    //zeroIndegreeNodes init
    IntStream.range(0, traveseAdjacency.length)
            .filter(i -> traveseAdjacency[i].size() == 0)
            .forEach(i -> {
                zeroIndegreeNodes.add(i);
            });
    //judge cycles
    if (zeroIndegreeNodes.isEmpty()){
        System.out.println("AOVNetwork has cycles");
        return null;
    }
    //Kahn's algorithm begin
    Iterator<Integer> iterator = zeroIndegreeNodes.iterator();
    while (iterator.hasNext()){
        Integer n = iterator.next();
        iterator.remove();//remove a node n from S

        sortedNodes.addLast(n);//add n to tail of L

        Iterator<Integer> mIterator = adjacency[n].iterator();
        while (mIterator.hasNext()){
            Integer m = mIterator.next();
            mIterator.remove();//remove edge e from the graph
            traveseAdjacency[m].remove(n);//remove edge e from the graph

            //judge does m has no other incoming edges
            if (traveseAdjacency[m].size() == 0){
                zeroIndegreeNodes.add(m);//insert m into S
            }
        }

        //reassign to iterator
        iterator = zeroIndegreeNodes.iterator();
    }

    //judge does graph has edges
    for (int i = 0; i < adjacency.length; i++) {
        if (adjacency[i].size() == 0){
            System.out.println("AOVNetwork has cycles");
            return null;
        }
    }

    return sortedNodes;
}
```

#### Kahn's algorithm的示意图
![Kahn_algorithm_1](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20170105_Kahn_algorithm_1.png "Kahn's_algorithm_1")
![Kahn_algorithm_2](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20170105_Kahn_algorithm_2.png "Kahn's_algorithm_2")

Conclusion: Kahn's algorithm的实现十分简单明了, 仅需维护一个S集(Set of all nodes with no incoming edge)即可


# references
- [https://www.geeksforgeeks.org/topological-sorting/][R1]
- [https://www.csie.ntu.edu.tw/~ds/ppt/ch6/sld080.htm][R2]
- [http://blog.csdn.net/dm_vincent/article/details/7714519][R3]
- [http://en.wikipedia.org/wiki/Topological_sorting][R4]
- [http://en.wikipedia.org/wiki/Hamiltonian_path][R5]
- [http://blog.csdn.net/jnu_simba/article/details/8872847][R6]



[R1]: https://www.geeksforgeeks.org/topological-sorting/
[R2]: https://www.csie.ntu.edu.tw/~ds/ppt/ch6/sld080.htm
[R3]: http://blog.csdn.net/dm_vincent/article/details/7714519
[R4]: http://en.wikipedia.org/wiki/Topological_sorting
[R5]: http://en.wikipedia.org/wiki/Hamiltonian_path
[R6]: http://blog.csdn.net/jnu_simba/article/details/8872847