最短路径是指连接图中两个顶点的路径中，所有边构成的权值之和最小的路径。之前提到的[广度优先遍历](https://www.jianshu.com/p/908f0366e1dc)图结构，其实也是一种计算最短路径的方式，只不过广度遍历中，边的长度都为单位长度，所以路径中经过的顶点的个数即为权值的大小。

> 最短路径中不能包含负权回路，因为每次经过负权回路，路径的权值会减少，所以这种情况下不存在最短路径。有些图结构中会存在负权边，用于表达通过某条途径可以降低总消耗，在有向图中，负权边不一定会形成负权回路，所以在一些计算最短路径算法中，负权边也可以计算出最短路径；在无向图中，负权边就意味着负权回路，所以无向图中不能存在负权边。后续的所有讨论都设定图中不存在负权回路的情况。

### 松弛函数

对边集合 $E$ 中任意边，以 $w(u,v)$ 表示顶点 $u$ 出发到顶点 $v$ 的边的权值，以 $d[v]$ 表示**当前**从起点 $s$ 到顶点 $v$ 的路径权值

若存在边 $w(u,v)$，使得： 

$$d[v] \gt d[u]+w(u,v)$$

则更新 $d[v]$ 值：

$$d[v]=d[u]+w(u,v)$$

所以松弛函数的作用，就是判断是否经过某个顶点，或者说经过某条边，可以缩短起点到终点的路径权值。

> 为什么将缩短距离的操作称之为“松弛”，不妨理解为，选择某种方式后，到达目的的总代价降低了。什么名字无关紧要，不必纠结。

##### 松弛函数代码示例

```
def releax(edge, distance, parent):
    if distance[edge.begin - 1] == None:
        pass
    elif distance[edge.end - 1] == None or distance[edge.end - 1] > distance[edge.begin - 1] + edge.weight:
        distance[edge.end - 1] = distance[edge.begin - 1] + edge.weight
        parent[edge.end - 1] = edge.begin - 1
        return True
    return False
```

```distance``` 列表存储从起点到当前顶点的路径权值，```parent``` 列表存储到当前顶点的前驱顶点下标值。初始 ```distance``` 列表和```parent``` 列表元素皆为 ```None```，表示路径权值为无穷大，处于不可达状态。

##### 松弛函数执行次数

以对边集合 $E$ 中每条边执行一次松弛函数作为一次迭代，接下来判断需要执行多少次迭代，可以确保计算出起点到每个顶点的最短距离。

![digraph](https://upload-images.jianshu.io/upload_images/9738807-9b1206e458ed6ecd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以图 ```digraph``` 为例，各顶点之间边的长度如图中所示。以 $d[v]$ 表示起点 $s$ 到顶点 $v$ 的距离，以 $\delta(s,v)$ 表示起点 $s$ 到顶点 $v$ 的最短路径权值，以 $p=\langle v_0,v_1,...,v_k \rangle$ 表示从顶点 $v_0$ 到顶点 $v_k$ 的路径。初始情况 $d[a]=0$，$d[v]=\infty ,v \in V - \{a\}$

> 从 ```digraph``` 图中可以明显发现，若已知 $d[b]$，则对边 $w(b,c)$ 执行松弛函数后，即可更新 $d[c]$ 的值。若已更新 $d[c]$ 的值，则对边 $w(c,d)$ 执行松弛函数后，即可更新 $d[d]$ 的值。

###### 最好情况下分析：

> 若遍历松弛的边顺序为：$w(a,b),w(b,c),w(c,d)$，其他两条边 $w(a,c),w(a,d)$ 顺序无影响

**第一次迭代：**

> 对边 $w(a,b)$ 执行松弛函数，则 $d[b]=d[a]+w(a,b)=1$
对边 $w(b,c)$ 执行松弛函数，则 $d[c]=d[b]+w(b,c)=3$
对边 $w(c,d)$ 执行松弛函数，则 $d[d]=d[c]+w(c,d)=8$

因为图结构比较简单，所以可以直接由观察得知，经过第一次迭代，即得出从起点到各个顶点的最短路径权值。

###### 最坏情况下分析：

> 若遍历松弛的边顺序为：$w(c,d),w(b,c)$ 或 $w(b,c),w(c,d)$，其他三条边 $w(a,b),w(a,c),w(a,d)$ 顺序无影响

**第一次迭代：**

>对边 $w(c,d)$ 执行松弛函数，则 $d[d]=\infty$
对边 $w(b,c)$ 执行松弛函数，则 $d[c]=\infty$
对边 $w(a,b)$ 执行松弛函数，则 $d[b]=d[a]+w(a,b)=1$
对边 $w(a,c)$ 执行松弛函数，则 $d[c]=d[a]+w(a,c)=6$
对边 $w(a,d)$ 执行松弛函数，则 $d[d]=d[a]+w(a,d)=10$

第一次迭代，有三条边起到了松弛的效果，直观的可以看出 $d[b]=\delta(a,b)$，第一次迭代可以获得经过一个顶点的最短路径，路径为 $p=\langle a,b \rangle$

**第二次迭代：**

> 对边 $w(c,d)$ 执行松弛函数，则 $d[d]=10$
对边 $w(b,c)$ 执行松弛函数，则 $d[c]=d[b]+w(b,c)=3$
> 对边 $w(a,b)$ 执行松弛函数，则 $d[b]=1$
对边 $w(a,c)$ 执行松弛函数，则 $d[c]=6$
对边 $w(a,d)$ 执行松弛函数，则 $d[d]=10$

第二次迭代，有一条边起到了松弛的效果，直观的可以看出 $d[c]=\delta(a,c)$，第二次迭代可以获得经过两个顶点的最短路径，路径为 $p=\langle a,b,c \rangle$

**第三次迭代：**

> 对边 $w(c,d)$ 执行松弛函数，则 $d[d]=d[c]+w(c,d)=8$
对边 $w(b,c)$ 执行松弛函数，则 $d[c]=3$
> 对边 $w(a,b)$ 执行松弛函数，则 $d[b]=1$
对边 $w(a,c)$ 执行松弛函数，则 $d[c]=6$
对边 $w(a,d)$ 执行松弛函数，则 $d[d]=10$

第三次迭代，有一条边起到了松弛的效果，直观的可以看出 $d[d]=\delta(a,d)$，第三次迭代可以获得经过三个顶点的最短路径，路径为 $p=\langle a,b,c,d \rangle$，路径如下图所示：

![](https://upload-images.jianshu.io/upload_images/9738807-babec6cc045bcd1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



###### 迭代次数分析：

> 为了方便后续讨论，对于顶点 $v$，若已确定 $d[v]=\delta(s,v)$，即已确定从起点到该顶点的最短路径权值，这里不妨称该顶点 $v$ 为已确认顶点。后续以路径长度表示从起点到顶点 $v$ 的路径上，经过的顶点个数。例如，对于路径 $p=\langle s,v_1,v_2,v \rangle$，路径权值为 $d[v]$，路径长度为 3。

通过前面的示例过程可以推论：

> 若图中存在未确认的顶点，则对边集合的一次迭代松弛后，会增加至少一个已确认顶点

**推论的意思是指，对图中顶点的确认，是以一种波纹扩散的方式进行的，这里增长的扩散半径是指路径中已确认顶点的个数，而不是路径的权值，并且路径中不包含未确认顶点。**

> 注意路径长度和路径权值并无绝对关系，例如若 $\delta(s,v_1)$ 的最短路径为 $p_1=\langle s,v_1 \rangle$，$\delta(s,v_2)$ 的最短路径为 $p_2=\langle s,v_1,v_2\rangle$，虽然 $p_2$ 的路径长度大于 $p_1$，但是 $\delta(s,v_2)$ 的权值并不一定大于 $\delta(s,v_1)$。例如上图中，若边 $w(b,c)$ 的权值为 -2 而不是 2，则 $\delta(a,c)$ 的值为 -1，而不是 3，即路径 $\langle a,b,c \rangle$ 的权值要小于路径 $\langle a,b \rangle$。

###### 推论证明

初始情况下，只有起点 $s$ 属于已确认顶点，根据邻接表记录，若起点存在相邻顶点，则对边集进行一次迭代松弛后，会增加至少一个已确认顶点。

![](https://upload-images.jianshu.io/upload_images/9738807-2c834692f9ff7956.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**反证说明：**

若一次迭代后，起点 $s$ 的所有相邻顶点都仍处于未确认状态，即一次迭代后，对于任意相邻顶点 $v$，存在 $d[v] \gt \delta(s,v)$。

则对于任意由起点 $s$ 出发到相邻顶点 $v_i$ 的路径 $p_i=\langle s,v_i \rangle$，存在由起点 $s$ 出发经过相邻顶点 $v_j$ 到达顶点 $v_i$ 的路径 $p_j=\langle s,v_j,...,v_i \rangle$，使得路径 $p_j$ 的权值小于路径 $p_i$ 的权值，即可以减小 $d[v_i]$ 的值。

* 若 $j = i$，则 $p_j=\langle s,v_i,...,v_i \rangle$，路径 $p_j$ 的权值小于路径 $p_i$ 的权值，说明路径 $\langle v_i,...,v_i \rangle$ 权值为负数，即图中存在负权回路，与讨论背景不符，如下图所示。

![](https://upload-images.jianshu.io/upload_images/9738807-2bba19a799b354de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 若 $j \neq i$，对于路径 $p_j=\langle s,v_j,...,v_i \rangle$ 中的 $\langle s,v_j \rangle$ 部分，因为对于任意相邻顶点 $v$，存在 $d[v]>\delta(s,v)$，所以同样存在由起点 $s$ 出发经过相邻顶点 $v_k$ 到达顶点 $v_j$ 的路径 $p_k=\langle s,v_k,...,v_j \rangle$，使得路径 $p_k$ 的权值小于路径 $\langle s,v_j \rangle$ 的权值，更新路径 $p_j$ 为 $p_j=\langle s,v_k,...,v_j,...,v_i \rangle$。如此重复，若起点 $s$ 的相邻顶点不为无穷多，则必然在某一时刻，更新路径 $p_j$ 为 $p_j=\langle s,v_i...,v_k,...,v_j,...,v_i \rangle$，使得路径 $p_j$ 的权值小于路径 $p_i$ 的权值，即图中存在负权回路，与讨论背景不符，如下图所示。

![](https://upload-images.jianshu.io/upload_images/9738807-5f58ce4aa37c0cb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以对于初始状态的起点 $s$ 而言，执行一次迭代后，至少会增加一个已确认顶点。

**一般性的，当图中已经存在一个或多个已确认顶点时，即图处于任意一种状态，若图中尚存在未确认顶点，则执行一次迭代后，会增加至少一个已确认顶点。**

证明过程与上面类似，使用下图作为辅助说明：

![example](https://upload-images.jianshu.io/upload_images/9738807-5afc3c9a198702fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 图中红色顶点表示已确认顶点，黑色顶点表示未确认顶点，红色路径表示由起点 $s$ 出发到各顶点的最短路径，该图用于辅助理解，所以并未标识边的权值。

对于图中的所有最短路径，以 $ep_i$ 表示每条最短路径上的最后一个顶点，其中 $ 1\le i \le |V|-1$。若一次迭代后，每个顶点 $ep_i$ 的相邻顶点中都未增加已确认顶点。则对于每个顶点 $ep_i$ 的相邻顶点 $v_m$，路径为 $p_i=\langle p,ep_i,v_m \rangle$，总会存在经过顶点 $ep_j$ 及其相邻顶点 $v_n$ 到达 $v_m$ 的路径 $p_j=\langle p,ep_j,v_n,...,v_m \rangle$，使得路径 $p_j$ 的权值小于路径 $p_i$ 的权值。同理，对于路径 $p_j$ 中的 $\langle ep_j,v_n\rangle$ 部分，总会存在更短的经过顶点 $ep_k$ 的路径去减少路径权值。因为 $ 1\le i \le |V|-1$，若顶点 $ep_i$ 的相邻顶点个数不为无穷大，则必然存在负权回路，与讨论背景不符。

所以图处于任意一种状态时，若图中尚存在未确认顶点，则执行一次迭代后，会增加至少一个已确认顶点。

**辅助说明：**

若某条最短路径上的最后一个顶点存在未确认相邻顶点，经过一次迭代松弛后，若经过该顶点的最短路径上未新增已确认顶点，则无论后续经过多少次迭代松弛，经过该顶点的最短路径上都不会新增已确认顶点，即该条路径已经走到头了。

以上图 ```example``` 为例，当前已确认顶点 $a_1$ 存在两个未确认相邻顶点 ${b_1,b_2}$。若执行一次迭代松弛后，并未存在从起点 $s$ 出发经过 $a_1$ 到达 $b_1$ 或 $b_2$ 的最短路径，则永远不会存在从起点 $s$ 出发经过 $a_1$ 到达 $b_1$ 或 $b_2$ 的最短路径。

不妨假设经过某次迭代后，存在最短路径为 $p=\langle s,a_1,b_1 \rangle$，即 $d[b_1]=\delta(s,b_1)=\delta(s,a_1)+w(a_1,b_1)$。因为在上一次的迭代后，$\delta(s,a_1)$ 的值已确定，但最短路径 $p=\langle s,a_1 \rangle$ 并未添加顶点 $b_1$，即 $d[b_1] \gt \delta(s,a_1)+w(a_1,b_1)$，存在悖论。

所以对于任意一条最短路径，若一次迭代后未新增已确认顶点，则该最短路径上不会再新增已确认顶点。

> 若图中存在由起点不可达的顶点，则这些顶点的初始化状态即为最短路径状态，即初始化时就处于已确认状态。

###### 迭代次数结论

根据之前的结论，若图中存在未确认顶点，则每一次迭代后都会新增加至少一个已确认顶点，即图中某条最短路径长度会至少加一。则要找到从起点出发到各顶点的最短路径权值，极端情况下，图中 $|V|$ 个顶点都在一条最短路径上，且松弛边按照最坏情况下进行，即一次只增加一个已确认顶点，则需要执行的迭代次数为 $|V|-1$ 次；另一种极端情况，松弛边按照最好情况下进行，则需要执行的迭代次数为 1 次。

### Bellman-Ford 算法

```Bellman-Ford``` 算法计算最短路径的过程中，使用了上述的松弛函数，通过对路径的不断松弛，来逐渐获取最短路径。

> ```Bellman-Ford``` 算法可以检测带权有向图中是否存在负权回路，根据前面对松弛函数执行次数的分析可知，若图中不存在负权回路，那么即使在最坏情况下，也只需要执行 $|V|-1$ 次迭代松弛，即可获得从起点到各顶点的最短路径。
\
若图中存在负权回路，当回路较小时，例如顶点自身或者两个顶点之间的负权回路，则在 $|V|-1$ 次迭代过程中，可能多次通过了该负权回路；若回路较大，例如从起点出发，串联所有顶点最后回到起点，即通过 $|V|-1$ 条边构成一个圆形，如下图所示。则 $|V|-1$ 次迭代过程中，可能一次也不会通过该负权回路，但是当再执行一次迭代松弛，即可将 $d[s]$ 值更新为负值，所以可以多执行一次迭代，通过判断是否更新从起点到某个顶点的最短路径权值，来判断图中是否存在负权回路。

![](https://upload-images.jianshu.io/upload_images/9738807-7fe774b417018222.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 算法过程

```Bellman-Ford``` 算法的执行过程很简单，就是对边集合进行 $|V|-1$ 次迭代松弛，执行结束后，若返回值为 ```TRUE```，则表示已经计算出起点到图中各顶点的最短路径，若返回值为 ```FALSE```，则表示图中存在负权回路。

##### 算法示例

```
def bellman_ford(graph, start, end):
    distance, parent = [None for i in range(graph.number)], [None for i in range(graph.number)]
    times, distance[start - 1], edges = 1, 0, getEdgesFromAdjacencyList(graph)
    while times < graph.number:
        for i in range(len(edges)):
            releax(edges[i], distance, parent)
        times += 1
    for i in range(len(edges)):
        if releax(edges[i], distance, parent):
            return False
    return True
```

代码中 ```distance``` 和 ```parent``` 两个列表的作用在上面已经提过了，```times``` 变量用于记录迭代的执行次数，```edges``` 列表是存储边的集合，```getEdgesFromAdjacencyList``` 函数用于从邻接矩阵中转换出边的集合。代码中第一个循环内包含一个嵌套循环，用于对边集执行 $|V|-1$ 次迭代松弛，第二个循环用于执行第 $|V|$ 次迭代，判断是否发生更新最短路径权值的情况，若发生更新权值，则表示图中存在负权回路。

> 根据之前的结论可知，若图中仍存在未确认顶点，则执行一次迭代松弛后，必然存在新增加的已确认顶点。代码示例中设定松弛的迭代次数为 $|V|-1$ 次，是为了确保最坏情况下，```Bellman-Ford ``` 算法结束后也能够获得起点到各个顶点的最短路径。但是在最优情况下，对边集进行一次松弛后，即得到了最终的结果。所以可以通过判断一次迭代后是否有新增加的已确认顶点，或者更准确的说，判断一次迭代过程中，是否通过某条边的松弛，减小了从起点到某个顶点的路径权值，以此来判断是否已经到达最终状态，从而减少嵌套循环的执行次数。

**优化算法示例**

```
def bellman_ford(graph, start, end):
    distance, parent = [None for i in range(graph.number)], [None for i in range(graph.number)]
    times, distance[start - 1], edges = 1, 0, getEdgesFromAdjacencyList(graph)
    flag = True
    while flag and times < graph.number:
        flag = False
        for i in range(len(edges)):
            if releax(edges[i], distance, parent) and not flag:
                flag = True
        times += 1
    for i in range(len(edges)):
        if releax(edges[i], distance, parent):
            return False
    return True
```

代码中增加 ```flag``` 变量标志是否已经找到从起点到各个顶点的最短路径，若已找到，则停止迭代松弛，最好情况下只需要一次迭代即可完成，时间复杂度为 $O(|E|)$。
##### 性能分析

```Bellman-Ford``` 算法中共存在 $|V|$ 次对边集合的迭代松弛，边集合的大小为 $|E|$，所以```Bellman-Ford``` 算法的时间复杂度为 $O(|V||E|)$。

### 代码附录

[Bellman-Ford算法代码及测试结果](./bellman-ford.py)


