# Priority_queue

缺省情况下，其利用一个`max-heap`完成，`max-heap`是一个以`vector`表现的`complete binary tree`。

`max-heap`可以满足`priority-queue`所需要的"依照权值高低自动递减排序"。

![priority_q](C:\Users\user\Desktop\研究生\学习\C&C++\STL标准库\img\priority_q.png)

`queue`以底部容器完成所有的工作，具有这种「修改某物接口，形成另一种风貌」 之性质者，称为` adapter` （配接器），

因此  `STL priority_queue `往往不被归类为 `container` （容器），而被归类为`container adapter` 。 

```
template <class T, class Container = vector<T>,
  class Compare = less<typename Container::value_type> > class priority_queue;
```

标准库默认使用 `< `操作符来确定优先级关系，其中第一个为元素类型；第二个为承载优先队列的容器类型，一般是vector；第三个是比较函数。但由于后两个已经带有默认值，所以一般使用时只需要`priority_queue<int>`这样就行了。

#### 常用操作

- 压入: `push()`
- 弹出: `pop()`
- 取队首元素: `top()` //与一般的队列不一样，不用`front()`
- 判空: `empty()`
- 取大小: `size`

#### 样例程序

![](C:\Users\user\Desktop\研究生\学习\C&C++\STL标准库\img\s_code.png)

对于自定义类型需要重载`<`运算符

```c++
struct node{
    int value;

    bool operator < (node x) const //const不能少
    {
        return this->value<x.value;
    }

    node(int _v):value(_v){}
};

int main(){

    priority_queue<node> q;
    q.push(node(3));
    q.push(node(2));
    q.push(node(5));

    cout<<q.size()<<endl;

    while (!q.empty()){
        cout<<q.top().value<<'\t';
        q.pop();
    }

    return 0;
}
```

#### 改变优先级

很多时候，我们需要由小到大排序，这时候就需要这样：

```c++
priority_queue<int, vector<int>, greater<int> >pq;
//三个int要一致！
```

自定义类型也要一致

```c++
struct node
{
	int value;
	bool operator > (node x) const
	{
		return this -> value > x.value;
	}
	node(int _v): value(_v) {}
};
priority_queue<node, vector<node>, greater<node> > q;
//三个node也要一样！
```

### max-heap

`binary heap`就是一种`complete binary tree`，`complete binary tree `整棵树内没有任何节点漏洞，这带来一个极大好处：我们可以利用`array`来储存所有节点。假设动用一个小技巧，将`array`的 ` #0` 元素保 留（或设为无限大值或无限小值），那么当`complete binary tree `中的某个节点位 于`array`的` i`处，其左子节点必位于 `array `的 `2i` 处，其右子节点必位于` array`的 `2i+1 `处，其父节点必位于`「i/2 」`处（此处的「/」权且代表高斯符号，取其整数）。` heap`可分为`max-heap`和 `min-heap `两种，前者每个节点的 键值`（key ）`都大于或等于其子节点键值，后者的每个节点键值`（key ）`都小于或等 于其子节点键值。因此，`max-heap`的最大值在根节点，并总是位于底层 `array `或` vector` 的起头处；`min-heap`的最小值在根节点，亦总是位于底层`array`或` vector `的起头处。`STL ` 供应的是`max-heap`

