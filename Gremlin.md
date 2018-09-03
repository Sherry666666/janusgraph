# Gremlin  
## janusgraph的一部分。TinkerPop0的核心
图的结构是由顶点、边和属性组成的拓扑。顶点有关联边。若两个顶点之间由一条关联边连接，我们认为这两个定点相邻。属性被附加到元素上，每个元素都有一个属性集合。属性是键/值对，键总是字符串。TinkerPop3的图形结构API提供了创建这种结构所需的方法。可以使用下面的Java 8代码创建先前绘制的TinkerPop图形。使用TinkerFactory.createClassic()，这个图就可以作为一个内存中TinkerGraph。
 ```
 Graph graph = TinkerGraph.open(); //1创建TinkerGraph 
Vertex marko = graph.addVertex(T.label, "person", T.id, 1, "name", "marko", "age", 29); //2添加顶点
Vertex vadas = graph.addVertex(T.label, "person", T.id, 2, "name", "vadas", "age", 27);
Vertex lop = graph.addVertex(T.label, "software", T.id, 3, "name", "lop", "lang", "java");
Vertex josh = graph.addVertex(T.label, "person", T.id, 4, "name", "josh", "age", 32);
Vertex ripple = graph.addVertex(T.label, "software", T.id, 5, "name", "ripple", "lang", "java");
Vertex peter = graph.addVertex(T.label, "person", T.id, 6, "name", "peter", "age", 35);
marko.addEdge("knows", vadas, T.id, 7, "weight", 0.5f); //3为顶点添加边
marko.addEdge("knows", josh, T.id, 8, "weight", 1.0f);
marko.addEdge("created", lop, T.id, 9, "weight", 0.4f);
josh.addEdge("created", ripple, T.id, 10, "weight", 1.0f);
josh.addEdge("created", lop, T.id, 11, "weight", 0.4f);
peter.addEdge("created", lop, T.id, 12, "weight", 0.2f);
```
有两个“访问器令牌（accessor tokens）”:T.id和T.label。当向Graph.addVertex(Object…)或Vertex. addedge (String,Vertex,Object…)提供这些值对中的任何一个，以及其他一组键值对时，将创建相应的元素以及附加到其上的键/值对属性。
## The Graph Structure
1、Start at vertex 
```
GraphTraversalSource.V(Object…​ ids)//返回类型GraphTraversal
```
 generates a traversal starting at vertices in the graph (if no ids are provided, all vertices).在从对应顶点开始遍历。若没有定义id则遍历所有顶点  
 2、搜寻顶点对应的边
 ```
 GraphTraversalSource.E(Object…​ ids)//返回类型GraphTraversal
 ```
 generates a traversal starting at edges in the graph (if no ids are provided, all edges).从对应边开始遍历。若没有定义id则遍历所有边。

 GraphTraversal 的每一个方法为一个step,每一个step调用都以五种一般方法之一来调制前一步的结果。[五种一般](http://tinkerpop.apache.org/docs/current/reference/#_the_graph_structure)方法如下。 map, flatMap, filter, sideEffect, branch.几乎GraphTraversal的每一步都是扩展自**MapStep, FlatMapStep, FilterStep, SideEffectStep, 或BranchStep**。  
 对于TinkerPop图形，下面的查询将返回marko-vertex所知道的所有人的名字。
 ```
 $ bin/gremlin.sh

         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----
gremlin> graph = TinkerFactory.createModern() //1
==>tinkergraph[vertices:6 edges:6]
gremlin> g = graph.traversal()        //2
==>graphtraversalsource[tinkergraph[vertices:6 edges:6], standard]
gremlin> g.V().has('name','marko').out('knows').values('name') //3
==>vadas
==>josh
```
等价于
```
gremlin> marko = g.V().has('name','marko').next() //1\
==>v[1]
gremlin> g.V(marko).out('knows') //2\
==>v[2]
==>v[4]
gremlin> g.V(marko).out('knows').values('name') //3\
==>vadas
==>josh
```


 