# 链表

## 单向链表

链表是一种由节点组成的数据结构，每个节点都包含某些信息以及指向链表中另一个节点的指针。如果序列中的节点只包含指向后继节点的指针，该链表就称为单向链表。

节点包含两个数据成员：info和next。info成员用于存储信息（数据），next成员用于将节点链接起来。

链表中的每个节点都是下面类定义的一个实例：

```c++
class IntSLLNode()
{
    public:
  		//默认构造函数，将指针初始化为null
  		IntSLLNode(){
             next=0;
         }
  		//构造函数，用户可以只提供一个数字参数来初始化一个节点
  		IntSLLNode(int el, IntSLLNode* ptr=0){
          	info = el;
          	next = ptr;
  		}
  		int info;
  		IntSLLNode* next;  
};
```

下面让我们创建一个具有三个节点的链表：

```c++
IntSLLNode* p = new IntSLLNode(10);
p->next = new IntSLLNode(8);
p->next->next = new IntSLLNode(50);
```

如果采用这种方法，那么如果需要创建一个有10000个节点的链表，那真是太恐怖了。如何解决呢？方法之一是在链表中保存两个指针：一个指向第一个节点，另一个指向最后一个节点。示例如下：

```c++
class IntSLList
{
    public:
  		//默认构造函数，将head和tail初始化为null
  		IntSLList(){
          	head = tail = 0;
  		}
  		~IntSLList();
  		int isEmpty(){
          	return head == 0;
  		}
  		void addToHead(int);		//头插入的函数声明
  		void addToTail(int);		//尾插入的函数声明
  		int deleteFromHead();		//删除链表开头的节点的函数声明，返回其中存储的值
  		int deleteFromTail();		//删除链表尾部的节点的函数声明，返回其中存储的值
  		void deleteNode(int);		//删除存储特定数据的节点
  		bool isInList(int) const;
  	private:
  		//指针变量的声明，定义head和tail变量
  		IntSLLNode* head;
  		IntSLLNode* tail;
};
```

因为链表类中用到了动态内存分配，所以必须要在析构函数中释放内存，析构函数的实现如下

```c++
IntSLList::~IntSLList()
{
    for(IntSLLNode* p; !isEmpty();){
      	p = head->next;
      	delete head;
      	head = p;
    }
}
```



### 插入

在链表的前面插入一个节点的步骤大致为：创建一个节点，将这个节点的next成员指向head的当前值，最后将head更新为指向新节点的指针。有一种特殊情况，就是向空链表插入一个新节点，空链表中head和tail都是null，此时，它们都指向新链表中唯一的节点，C++实现如下：

```c++
void IntSLList::addToHead(int el)
{
  	head = new IntSLLNode(el,head);
  	if (tail == 0)
    	tail == head;
}
```

向链表的末尾添加一个节点的步骤大致为：创建一个节点，将链表最后一个节点的next成员指向这个节点，将tail更新为指向这个节点的指针。和头插入一样，此时也要考虑链表为空的情况，C++实现如下：

```c++
void IntSLList::addToTail(int el)
{
    if (tail != 0){
      	tail->next = new IntSLLNode(el);
      	tail = tail->next;
    }
  	else
      	head = tail = new IntSLLNode(el);
}
```

### 删除

删除链表开头的节点的步骤大致为：将开头节点的信息临时存储到本地变量el中，然后将head更新为指向原第二个节点的指针，释放原第一个指针的空间，C++实现如下：

```C++
int IntSLList::deleteFromHead()
{
    int el = head->info;
  	IntSLLNode* tmp = head;
  	if (head == tail)
      	head = tail = 0;
  	else
      	head = head->next;
  	delete tmp;
    return el;
}
```

删除链表末尾的节点的步骤大致为：使用一个for循环，用一个临时变量 tmp遍历链表，并恰好在tail前面停止，删除最后一个节点，将tail更新为指向目前tmp指针指向的节点，将目前尾节点的next成员设置为null，C++实现如下：

```c++
int IntSLList::deleteFromTail()
{
    int el = tail->info;
  	if (head == tail){
      	delete head;
      	head = tail = 0;
  	}
  	else{
      	IntSLLNode* tmp;
      	for (tmp = head; tmp->next != tail; tmp = tmp->next);
      	delete tail;
      	tail = tmp;
      	tail->next = 0;
  	}
  	return el;
}
```

与删除头节点相似，删除尾节点也存在两种特殊情况。如果链表为空，就不能删除任何节点，这种情况由用户决定如何处理。第二种情况就是只有一个节点，此时要求将head和tail同时设置为null

删除一个存储了特定数值的节点有两种方法，其一：扫描链表，找到要删除的节点，然后再次扫描链表找到它的前驱。另外一种方法的大致步骤为：使用两个临时指针变量pred和tmp在for循环中迭代，当for循环条件为真时（比如tmp指向需要寻找的节点），退出循环，执行赋值语句pred->next = tmp->next，将要删除的节点排除到链表之外，最后释放所占用的空间，C++实现如下：

```c++
void IntSLList::deleteNode(int el)
{
  	//试图从空链表中删除节点，函数立即退出
    if (head != 0)  
      	if (head == tail&&el == head->info){		//从单节链表中删除节点，head和tail都设置为null
          	delete head;
          	head = tail = 0;
      	}
  	else if (el == head->info){
      	IntSLLNode* tmp = head;
      	head = head->next;
      	delete tmp;
  	}
  	else{
      	IntSLLNode* pred;
      	IntSLLNode* tmp;
      	for (pred=head,tmp=head->next;tmp!=0&&!(tmp->info==el);pred=pred->next,tmp=tmp->next);
      	if (tmp != 0){
          	pred->next = tmp->next;
          	if (tmp == tail)
              	tail = pred;
          	delete tmp;
      	}
  	}
}
```

### 查找

查找操作扫描链表，确定是否包含某个数字，步骤大致为：创建一个临时变量tmp扫描整个链表，如果找到了就退出循环，未找到tmp到最后就为null了，所以只需返回比较的结果tmp != 0，C++实现如下：

```C++
bool IntSLList::isInList(int el) const
{
    IntSLLNode* tmp;
  	for (tmp = head; tmp != 0&&!(tmp->info == el); tmp = tmp->next);
  	return tmp != 0;
}
```



## 双向链表

因为单向链表只能访问后继节点，所以效率不是很高，为了避免这个问题，可以重新定义链表，使每个节点有两个指针，一个指向前驱，一个指向后继，这种链表称为双向链表。

双向链表重新定义的节点如下（同单向链表相比仅仅是增添了一个前驱指针）：

```c++
template<typename T>
class DLLNode
{
    public:
  		DLLNode(){
          	next = prev = 0;
  		}
  		DLLNode(const T& el, DLLNode* n = 0, DLLNode* p=0){
          	info = el;
          	next = n;
          	prev = p;
  		}
  		T info;
  		DLLNode *next,*prev;
};
```

双向链表我们只讨论两个成员函数：尾插入和尾删除

向链表的末尾插入一个节点的大致步骤为：创建一个新节点（next成员初始化为null），将prev成员赋值为tail，将泰勒更新为指向新节点的指针，令前驱节点的指针指向新节点，C++实现如下：

```c++
template<typename T>
void DoublyLinkedList<T>::addToDLLTail(const T& el)
{
    if (tail != 0){
      	tail = new DLLNode<T>(el, 0, Tail);
      	tail->prev->next = tail;
    }
  	else
      	head = tail = new DLLNode<T>(el);
}
```

从双向链表上删除最后一个节点很简单，大致步骤为：用el保存尾节点存储的值，令tail指向其前驱节点，删除最后一个节点，再将此时的尾节点的next成员更新为null，C++实现如下：

```c++
template<typename T>
T DoublyLinkedList<T>::deleteFromDLLTail()
{
    T el = tail->info;
  	if (head == tail){
      	delete head;
      	head = tail = 0;
  	}
  	else {
      	tail = tail->prev;
      	delete tail->next;
      	tail->next = 0;
  	}
  	return el;
}
```



## 循环链表

循环链表的节点组成了一个环：链表的长度是有限的，每个节点都能访问后继节点。

在循环单向链表的实现中，可以只使用一个固定指针tail。

在链表尾部插入新节点的大致步骤为：创建一个新节点，新节点的next成员指向tail->next（尾节点的后继节点），尾节点的指针（tail->next）指向新节点，将tail更新为指向新节点的指针，C++实现如下：

```c++
void addToTail(int el)
{
    if (isEmpty()){
      	tail = new IntSLLNode(el);
      	tail->next = tail;
    }
  	else {
      	tail->next = new IntSLLNode(el, tail->next);
      	tail = tail->next;
  	}
}
```

同样，为提高链表的效率，可以重新定义链表，得到双向循环链表。



## 跳跃链表（skip list）

跳跃链表是在有序链表的基础上进行了拓展，解决了有序链表结构查找特定值困难的问题。

跳跃链表具有以下性质：

* 由很多层（level）结构组成
* 每一层都是一个有序的链表
* 最底层(Level 1)的链表包含所有元素
* 如果一个元素出现在 Level i 的链表中，则它在 Level i 之下的链表也都会出现
* 每个节点包含两个指针，一个指向同一链表中的下一个元素，一个指向下面一层的元素

跳跃链表的查找机制是什么样的呢？我们先设定一个这样的场景：有一叠标着号码的文案，现在它们是按标号大小顺序放置的（比如有：22、35、44、68、79、83、111），现在需要你找出特定标号的文档（比如：79），你会怎么办？假如我们未卜先知（或许说有先见之明），用一些标上数字的便利贴（比如是：22、68、111）贴在相应的文案上，并使其凸显出来，我们便可以将要找的数字与便利贴上的数字比较，确定一个小的搜索范围（比如：79比22、68大，比111小，此时就在68-111的下一层寻找），跳跃链表的原理便是如此。（语言表达能力差，希望大家能看懂吧）

下面给出跳跃链表节点的C++实现：

```c++
const int maxLevel = 4;

template <typename T>
class SkipListNode
{
    public:
  		SkipListNode{
  		}
  		T key;
  		SkipListNode **next;
};
```

