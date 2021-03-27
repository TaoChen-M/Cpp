# STL常用算法（排序、合并、搜索、分区）

## 只读算法

一些算法只会读取输入范围内的元素，而不改变元素

### find()

用来查找容器中的指定内容，传递给find的前两个参数是表示元素范围的迭代器，第三个参数是要查找的值。find将范围中的每个元素同给定值进行比较，返回指向第一个等于给定值的元素的**迭代器**，可以申明一个auto类型变量进行接收，如果找不到则会返回第二个传进的参数。

```C++
vector<int> vec{1,2,3,4,5};
auto find(vec.begin(),vec.end(),1);

string str("hello world")
str.find('h')//返回索引
find(str.begin(),str.end(),'h')//返回迭代器
```

类似的，由于指针就像内置数组上的迭代器，可以用find在数组中查找值

```c++
int val[]={1,2,3,4,5}
int *res=find(begin(val),end(val),1)
```

使用标准库中的begin和end函数来获得指向val中首元素和尾元素之后的指针，并传递给find。

### accumulate()

定义在头文件numeric中，该函数接收三个参数，前两个指出需要求和的元素的范围，第三个参数是和的初值。

```C++
//对vec中的元素进行求和，和的初值是0
int sum=accumulate(vec.cbegin(),vec.cend(),0)
```

### equal()

用于确定两个序列是否保存相同的值，将第一个序列中的每个元素和第二个序列中的对应元素进行比较，如果所有元素都相等，返回true；否则返回false。

该算法接受三个迭代器：前两个表示第一个序列中的元素范围，第三个表示第二个序列的首元素（该算法默认两个序列的**长度相等**）

```C++
equal(vec.cbegin(),vec.cend(),vec2.cbegin())
```

### count()和count_if()

`count(begin,end,value)`，统计容器中等于value元素的个数，begin是容器的首迭代器，end是容器的尾迭代器，value是需要统计的元素。count函数的复杂度是线性的，最坏情况是O(n)。

`count_if(begin,end,value,cmp)`，cmp为比较的函数，返回值是bool类型。

```C++
vector<int> vec{1,2,3,4,5,6,7,8,9,0}

bool cmp(int x) return x%2;
count_if(vec.begin(),vec.end(),cmp);
```



## 写容器元素的算法

将新值赋予序列中的元素，使用这类算法，必须确保序列原大小至少不少于我们要求算法写入的元素数目，这些算法不会执行容器操作，因此它们自身不会改变容器的大小

### fill()

接受一对迭代器表示一个范围，同时接受一个值作为第三个参数，fill将这个值赋予范围内的每个元素

```C++
//将每个元素重置为0
fill(vec.begin(),vec.end(),0)
//将容器的一个子序列置为10
fill(vec.begin(),vec.begin()+vec.size()/2,10)
```

### back_inserter()

该方法接受一个指向容器的引用，返回一个与该容器绑定的插入迭代器，通过back_inserter()赋值时，赋值运算符会调用push_back将一个具有给定值的元素添加到容器中。

```C++
vector<int> vec;//空向量
auto it=back_inserter(vec);
*it=42;//vec中现在有一个元素，值是42

//常常使用beck_inserter()来创建一个迭代器，作为算法的目的位置来使用
vector<int> vec;
fill(back_inserter(vec),10,0);//添加10个元素到vec中
```

### copy()

该算法是向目的位置迭代器指向的输出序列中的元素写入数据的算法，该算法接受三个迭代器，前两个表示一个输入范围，第三个表示目的序列的起始位置。传递给copy的目的序列至少要包含与输入序列**一样多的元素**。

```C++
int a[]={0,1,2,3,4,5,6,7,8,9};
int a2[sizeof(a)/sizeof(*a)];//此处也可以直接使用a2[size(a)]
auto ret=copy(begin(a),end(a),a2);
```

copy返回的是其目的位置迭代器（递增后)的值，即，ret恰好指向拷贝到a2的尾元素之后的位置

### replace()

读入一个序列，将其中所有等于给定值的元素都改为另一个值，此算法接受四个参数：前两个是迭代器，表示输入序列，后两个一个是要搜索的值，另一个是新值。

```C++
//将所有值为0的元素改为42
replace(vec.begin(),vec.end(),0,42);
```

如果希望保留原序列不变，可以调用replace_copy，此算法接受额外第三个迭代器参数，指出调整后序列的保存位置。

```C++
//使用back_inserter按需增长目标序列,此调用后list并未改变，vec包含lsit的一份拷贝，不过list中值为0的元素在vec中变为42
replace_copy(list.cbegin(),list.cend(),back_inserter(vec),0,42);
```

## 重排元素的算法

### sort()

本质上是一个模板函数该函数专门用来对容器或者普通数组中指定范围的元素进行排序。默认**升序**。基于**快排**实现。

#### 使用限制条件

- 容器支持的迭代器必须是随机访问迭代器，因此sort()只对array、vector、deque这三个容器提供支持
- 元素类型必须支持<小于运算符
- 实现排序时，需要交换容器中元素的存储位置，，这时如果容器中存储的是自定义的类对象，则该类内部必须提供移动构造函数和移动赋值运算符
- 对于指定区域内的值相等的元素，无法保证相对位置不发生改变
- 时间复杂度 $$N*log_2N$$,其中N为指定区域[first,last)之间的距离

#### 示例

```C++
bool fun(int i, int j) { return i > j; }

int main(){
    vector<int> vec{1, 2, 5, 7, 3, 7, 8};
	//默认使用升序 1 2 3 5 7 7 8
    sort(vec.begin(), vec.end());
    //使用自定义顺序 8 7 7 5 3 2 1
    sort(vec.begin(),vec.end(),fun);
    return 0;
}

```

### stable_sort()

当指定范围内有多个相等的元素时，stable_sort()可以保证位置不发生变化。有些场景需要保证相等元素的相对位置的，例如对于一个保存某种事务（比如银行账户）的容器，在处理这些事务之前，为了能够有序更新这些账户，需要按照账号对它们进行排序。而这时就很有可能出现相等的账号（即同一账号在某段时间做多次的存取钱操作），它们的相对顺序意味着添加到容器的时间顺序，此顺序不能修改，否则很可能出现账户透支的情况。

stable_sort()的用法和sort()函数完全相同，其时间复杂度是$$N*log_2N^2$$

当可用空间足够的时候，可以达到$$N*log_2N$$。基于归并排序实现。

### partial_sort()

部分排序，从指定区域中提取处部分数据，并对它们进行排序。partial_sort()函数会以交换元素存储位置的方式实现部分排序，具体来说，partial_sort()会将[first,last)范围内最小（最大）的middle-first个元素移动到[first,middle)区域中，并对这部分元素进行升序或者降序排序。

#### 示例

```c++
vector<int> vec{1, 2, 5, 4, 3, 7, 8};
partial_sort(vec.begin(),vec.begin()+4,vec.end());
for(auto it:vec){
    cout << it << endl;
}
```

### partial_sort_copy()

partial_sort_copy()和partial_sort()函数的区别在于，前者不会对原有数据做任何改动，而是先将选定的部分元素拷贝到另外指定的数组或者容器中，然后在对这部分元素进行排序。

```C++
int array[5] = {0};
vector<int> vec{1, 2, 5, 4, 3, 7, 8};
partial_sort_copy(vec.begin(),vec.end(),array,array+5);
for(auto it:array){
    cout << it << endl;
}
```

### unique()

为了消除重复单词，可以使用unique函数来重排容器，使得不重复的元素都出现在容器的开始部分，因为算法不能执行容器的操作，将使用erase函数来完成真正的删除。

unique算法重排输入序列，并返回一个指向不重复值范围末尾的迭代器。

![unique](img\unique.png)

```c++
auto end_unique=unique(words.begin(),words.end());
words.erase(end_unique,words.end())
```

## lambda表达式

一个lambda表达式具有返回类型、一个参数列表和一个函数，但是和普通函数不同，lambda可以定义在函数内部，形式如下：

```C++
[capture list](parameter list)->return type{function body}
```

capture list(捕获列表)是一个lambda所在函数定义的局部变量列表（通常都是空）

lambda必须使用尾置返回来指定返回类型。

## 再探迭代器

### 插入迭代器

插入器是一种迭代器适配器，接收一个容器，生成一个迭代器，实现向容器中增加元素。

三种类型：

- back_inserter：创建一个使用push_back的迭代器
- front_inserter：创建一个使用push_front的迭代器
- inserter：创建一个使用insert的迭代器，该函数接受第二个参数，这个参数必须是指向给定容器的迭代器。元素将会被插到给定迭代器所表示的元素之前。

```C++
list<int> lst1{1,2,3,4};
list<int> lst2,lst3;
copy(lst1.cbegin(),lst2.cend(),front_inserter(lst2));//lst2：4 3 2 1
copy(lst1.cbegin(),lst2.cend(),inserter(lst3,lst2.begin()));//lst3:1 2 3 4
```

### 反向迭代器

反向迭代器是容器中从尾元素到首元素反向移动的迭代器，递增一个反向迭代器（++it）会移动到前一个元素 ，递减（--it）会移动到下一个元素。

除了forward_list之外其他所有的容器都支持反向迭代器

![](img\反向迭代器.png)

假定一个名为line的string，其中保存着逗号分开的单词列表，如果希望打印line的第一个单词，则可以使用find函数来解决。

```C++
auto comma=find(line.cbegin(),line.cend(),',');
cout<<string(line.cbegin(),comma);
```

如果line中有逗号，comma会指向这个逗号，这里我们会打印[line.cbegin(),comma)的字符。

如果希望打印最后一个字符，可以查找最后一个逗号

```C++
auto rcomma=find(line.crbegin(),line.crend(),',');
cout<<string(line.crbegin(),rcomma);
```

但是会输出错误结果

比如line是：hello，world

则会输出dlrow

这是因为这里使用了反向迭代器，会反向处理line，因此上面的语句会从反向开始打印line中的内容。

需要做的是将rcomma变为一个普通的迭代器，能在line中正向移动。

通过调用reverse_iterator中的base函数来完成这一操作。

```C++
cout<<string(rcomma.base(),line.cend());
```

![](\img\反向迭代器和正向迭代器关系.png)

## 算法形参模式

```C++
alg(beg,end,other args);
alg(beg,end,dest,other args);
alg(beg,end,beg2,other args);
alg(beg,end,beg2,end2,other args);
```

其中alg是算法的名字，beg和end表示算法所操作的输入范围，几乎所有算法都接受一个输入范围。

dest通常被绑定到一个插入迭代器上，指明新元素将要插入的位置。

接收单独beg2或者beg2、end2的算法，用这些迭代器表示第二个输入范围。

只接受单独的beg2的算法将beg2作为第二个输入范围中的首元素，这些算法假定从beg2开始的范围和[beg,end)的范围相同。

### _if版本算法

带有_if后缀的算法，通常都接受一个单独的谓词参数

```C++
find_if(beg,end,pred)//查找第一个令pred为真的元素
```

### _copy版本算法

写到额外空间中的算法都在名字后面附加一个_copy。

```C++
reverse(beg,end)//反转输入范围中的元素
reverse_copy(beg,end,dest)//将元素反转后拷贝到dest
```

