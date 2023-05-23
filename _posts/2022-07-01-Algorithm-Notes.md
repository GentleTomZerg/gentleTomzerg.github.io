---
layout: article
title: Notes - Algorithm
author: GentleTomZerg
tag: [notes, algorithm]
mathjax: true
---
# Linked List

## 1.Linked List Construction(Blue)

![image-20220319172332537](/assets/algo-exp-note.assets/image-20220319172332537.png)

![image-20220319173100792](/assets/algo-exp-note.assets/image-20220319173100792.png)

本题的难点主要在于对边界条件的处理，以及在插入删除时对head和tail的及时更新上。

要完整的自己写对有难度。需要反复练习，直到自己可以考虑到所有的特殊情况。

![image-20220319213503552](/assets/algo-exp-note.assets/image-20220319213503552.png)

```java
import java.util.*;

// Feel free to add new properties and methods to the class.
class Program {
  static class DoublyLinkedList {
    public Node head;
    public Node tail;
//O(1) Time O(1) Space
    public void setHead(Node node) {
			if (head == null) {
				head = node;
				tail = node;
				return;
			}
			insertBefore(head, node);
    }
//O(1) Time O(1) Space
    public void setTail(Node node) {
			if (tail == null) {
				tail = node;
				head = node;
				return;
			}
			insertAfter(tail, node);
    }
//O(1) Time O(1) Space
    public void insertBefore(Node node, Node nodeToInsert) {
			if (nodeToInsert == head && nodeToInsert == tail)
				return;

			remove(nodeToInsert);
			//nodeToInsert points to others
			nodeToInsert.prev = node.prev;
			nodeToInsert.next = node;

			//others points to nodeToInsert
			if (node.prev == null) {// node is head
				head = nodeToInsert;
			} else {
				node.prev.next = nodeToInsert;
			}
			node.prev = nodeToInsert;

    }
//O(1) Time O(1) Space
    public void insertAfter(Node node, Node nodeToInsert) {
			if (nodeToInsert == head && nodeToInsert == tail)
				return;

			remove(nodeToInsert);

			//nodeToInsert points to others
			nodeToInsert.prev = node;
			nodeToInsert.next = node.next;

			//others points to node
			if (node.next == null) {// node is tail
				tail = nodeToInsert;
			} else {
				node.next.prev = nodeToInsert;
			}
			node.next = nodeToInsert;
    }
//O(p) Time O(1) Space
    public void insertAtPosition(int position, Node nodeToInsert) {
			if (position == 1) {
				setHead(nodeToInsert);
				return;
			}

			Node p = head;
			int currentPosition = 1;
			while (p != null && currentPosition++ != position)
				p = p.next;

			if (p != null)//
				insertBefore(p, nodeToInsert);
			else
				setTail(nodeToInsert);

    }
//O(n) Time O(1) Space
    public void removeNodesWithValue(int value) {
			Node node = head;
			while(node != null) {
				Node nodeToRemove = node;
				node = node.next;
				if (nodeToRemove.value == value)
					remove(nodeToRemove);
			}
    }

    //在removeNodeBindings的基础上对head和tail进行更新
    public void remove(Node node) {
			if (node == head)
				head = node.next;
			if (node == tail)
				tail = node.prev;
			removeNodeBindings(node);

    }
//O(n) Time O(1) Space
    public boolean containsNodeWithValue(int value) {
			Node node = head;
			while(node != null) {
				if (node.value == value)
					return true;
				node = node.next;
			}
      return false;
    }

 //O(1) Time O(1) Space
	//真正的remove一个Node的逻辑
	public void removeNodeBindings(Node node) {
		if (node.prev != null)
			node.prev.next = node.next;
		if (node.next != null)
			node.next.prev = node.prev;
		node.next = null;
		node.prev = null;
	}
  }

  // Do not edit the class below.
  static class Node {
    public int value;
    public Node prev;
    public Node next;

    public Node(int value) {
      this.value = value;
    }
  }
}

```



## 2.Remove kth Node From End(Blue)

![image-20220319213553701](/assets/algo-exp-note.assets/image-20220319213553701.png)

本题的难点在于对边界条件的考虑，也就是说，要删除的是头节点怎么办？

基本思路是first，second快慢指针，从而能够确定要删除节点的前一个节点的位置。但不能忽视特殊情况，即second在第一步和first拉开k的距离时就已经到了null。此时，要被删除的是头节点，需要特殊处理。

<img src="/assets/algo-exp-note.assets/image-20220319223532091.png" alt="image-20220319223532091" style="zoom:67%;" />

```java
import java.util.*;

//O(n) time O(1) space
class Program {
  public static void removeKthNodeFromEnd(LinkedList head, int k) {
		LinkedList first = head;
		LinkedList second = head;

		int count = 0;
      	//以second == null作为终结条件，特殊情况也可被考虑在内
		while(count++ < k && second != null) {
			second = second.next;
		}

		if (second == null) {
			head.value = head.next.value;
			head.next = head.next.next;
			return;
		}
		//second并非为null，那么要保证first指向要被删除节点的前一个节点
      	//从而，此时的终结条件为s.next != null;
		while (second.next != null) {
			first = first.next;
			second = second.next;
		}

		first.next = first.next.next;
  }

  static class LinkedList {
    int value;
    LinkedList next = null;

    public LinkedList(int value) {
      this.value = value;
    }
  }
}
```



## 3.Find Loop(Red)

本题要在一个单链表中找到循环部分的起始点，具体例子如下所示，4就是整个链表循环部分的起始点。

![image-20220319224729990](/assets/algo-exp-note.assets/image-20220319224729990.png)

我们可以使用Hash表将链表进行遍历，当发现4这个重复的值时就可以将他返回

这个方法不错，但是占用了更多的空间

```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static LinkedList findLoop(LinkedList head) {
        LinkedList first = head.next;
        LinkedList second = head.next.next;
        //理论上first和second应该同时出发，但是这样无法进入第一个循环，
        //所以先将两个指针指向走完第一步的样子后再进入循环
        while(first!=second){
            first = first.next;
            second = second.next.next;
        }
        first = head;
        while(first!=second){
            first = first.next;
            second = second.next;
        }
        return first;
    }

    static class LinkedList {
        int value;
        LinkedList next = null;

        public LinkedList(int value) {
            this.value = value;
        }
    }
}


```

上述代码的图解如下所示：

<img src="/assets/algo-exp-note.assets/image-20220319230756443.png" alt="image-20220319230756443" style="zoom:50%;" />

如上图推理可以得出head到环起始点的位置等于FS相遇位置到起始点的距离，所以，再令F为头节点，循环直到两点相遇的地方就是环的起始点。

## 4.Reverse Linked List(Red)

![image-20220319231339769](/assets/algo-exp-note.assets/image-20220319231339769.png)

反转链表的思路需要用到三个指针 p1，p2 , p3。

p2 p3

0->1->2->3->4->5->6->7->8->9->null      起始状态  p1指向null

​                                                             p1   p2

**null<-0<-1<-2<-3<-4<-5<-6<-7<-8<-9**->null      终止状态  p3指向null

中间过程：

​          p1  p2

​				 p3

**0<-1<-2**->3->4<-5->6<-7<-8<-9<-null     p3 = p2.next;



​          p1  p2  p3

**0<-1<-2<-3**->4<-5->6<-7<-8<-9<-null     p2.next = p1;



​                p1  p2

​					   p3

**0<-1<-2<-3**->4<-5->6<-7<-8<-9<-null     p1 = p2; p2 = p3;


中间过程是每一次将p2使next指向前一节点（被p1所指向的地方），然后**三个指针集体向后移动一位**. 移动后，p1所指向的节点是已经完成反转的部分链表的头节点。

<img src="/assets/algo-exp-note.assets/image-20220320004603146.png" alt="image-20220320004603146" style="zoom:67%;" />

```java
import java.util.*;
//O(N) time O(1) space
class Program {
  public static LinkedList reverseLinkedList(LinkedList head) {
    LinkedList previousNode = null;
	LinkedList currentNode = head;

	while(currentNode != null) {
		LinkedList nextNode = currentNode.next;
		currentNode.next = previousNode;
		//one reverse has been done, right shift
		previousNode = currentNode;
		currentNode = nextNode;
	}
    return previousNode;
  }

  static class LinkedList {
    int value;
    LinkedList next = null;

    public LinkedList(int value) {
      this.value = value;
    }
  }
}
```



```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static LinkedList reverseLinkedList(LinkedList head) {
        LinkedList p1 = null;
        LinkedList p2 = head;
        LinkedList p3 = null;
        //这里我们没有给p3赋值为p2.next，
        //若提前初始化，我们需要对边界做更多的考虑
        //！！！！！！！！！！！！！！！！！！！！！！
        while (p2 != null) {
            p3 = p2.next;
            p2.next = p1;
            p1 = p2;
            p2 = p3;
        }
        return p1;
    }


    static class LinkedList {
        int value;
        LinkedList next = null;

        public LinkedList(int value) {
            this.value = value;
        }
    }
}


```

## 5.Merge Linked List(Red*)

本题很难用静态的图片展示，所以将详述基本的思路：

![image-20220320141012861](/assets/algo-exp-note.assets/image-20220320141012861.png)

<img src="/assets/algo-exp-note.assets/image-20220320152141430.png" alt="image-20220320152141430" style="zoom:50%;" />

我们将用到3个指针

**在整个过程中我们是在将链表2嵌入链表一中**, `p1`和`p2`一直跟踪链表一二中未被merge的节点，`prev`用来记录完成merge的节点组成的链表的tail。本质上，这道题的思路就是，如果`p1.value > p2.value`, 那么`prev`就等于`p2`，也就是说`p2`是merge list的tail; 否则， `prev`就等于`p1`，也就是说`p1`是merge list的tail。

于此同时，我们也要更新`p1,p2`，保证他们指向未被merge的节点。

**关键点：`prev.next`最后永远得等于`p1`, 也就是说，merge好的list永远和链表1连在一起。**这样，如果链表1先被merge完，那么直接合并p2剩下的部分即可。如果链表2先被merge完，因为`prev.next`始终连接链表1，所以merge已经结束。

- **解法一（Iterative）**

在开始拼接前，我们需要知道p1的值是否小于p2的值：

if（p1的值<p2的值）

那么，我们不需要将p2插入到p1之前

所以，我们用prev保存p1原来的位置然后让p1指向下一个节点

else(p1的值>=p2的值)

那么， 我们需要p2的next指向p1

在这一步之前我们知道prev是p1前一位的指针，那么prev应该指向要插入的p2。但是在这之前我们需要做一个判断，因为prev有可能是null，在这种初始情况下，prev不可能有prev.next，所以最后的代码为

if(prev != null)
         prev.next = p2;

然后我们再用与prev连接了的p2指向p1即可，具体如下所示。

prev = p2; 		首先，我们用prev保存p2的位置

p2 = p2.next;		然后将p2平移到下一个点（这样我们就不会丢失对p2所在链表的控制）

prev.next = p1;		最后，我们用之前的p2节点（现在是prev），将他指向p1，从而将p2插入到了p1之前



**说完了while内部的工作，这里将说明while停止的判断，我们以链表1作为基础将链表2中的内加入到链表1中，若链表一先结束（p1=null）那么p2所指的链表2之后的的节点（包括p2）都大于p1的prev所指节点的最大值，所以直接拼接即可：**

**if(p1 == null)**
            **prev.next = p2;**

**若p2先结束，那么说明p2所在的链表2已经完全被嵌入了链表1，说明merge的操作已经全部完成**

```java
import java.util.*;
//O(N+M) time O(1) space
class Program {
    // This is an input class. Do not edit.
    public static class LinkedList {
        int value;
        LinkedList next;

        LinkedList(int value) {
            this.value = value;
            this.next = null;
        }
    }

    public static LinkedList mergeLinkedLists(LinkedList headOne, LinkedList headTwo) {
        LinkedList p1 = headOne;
        LinkedList p2 = headTwo;
        LinkedList prev = null;

        while (p1 != null && p2!= null){
            if(p1.value < p2.value){
                prev = p1;
                p1 = p1.next;
            }
            else{
                //因为prev.next总之指向p1，
                //所以这里要多一步，prev.next = p2;
                //if 判断用于筛出prev还未被赋值的情况
                if(prev != null){
                    prev.next = p2;
                }
                    prev = p2;
                    p2 = p2.next;
                //将新的prev重新指向p1
                    prev.next = p1;
            }
        }
        if(p1 == null)
            prev.next = p2;
        //这一块的比较得和p1.value < p2.value对应，否则会返回错误的头节点
            return headOne.value<headTwo.value? headOne:headTwo;
    }
}
```

- **解法2(Recursion)**

本例参照while循环的代码进行了recursion的改写，需要注意的地方是recursion函数传入的参数

```java
import java.util.*;

class Program1 {
    // This is an input class. Do not edit.
    public static class LinkedList {
        int value;
        LinkedList next;

        LinkedList(int value) {
            this.value = value;
            this.next = null;
        }
    }

    public static LinkedList mergeLinkedLists(LinkedList headOne, LinkedList headTwo) {
        recursiveMerge(headOne, headTwo, null);
        return headOne.value < headTwo.value ? headOne : headTwo;
    }

    public static void recursiveMerge(LinkedList p1, LinkedList p2, LinkedList p1prev) {
        if (p1 == null) {
            p1prev.next = p2;
            return;
        }
        if (p2 == null)
            return;
        if (p1.value < p2.value)
            recursiveMerge(p1.next, p2, p1);
        else {
            if (p1prev != null)
                p1prev.next = p2;
            LinkedList newP2 = p2.next;
            p2.next = p1;
            recursiveMerge(p1, newP2, p2);
        }
    }
}
```

## 6.Shifted Linked List(Red)

思路简单，但要快速写对，需要清晰考虑各种边界情况，需要多练。

![image-20220320155229014](/assets/algo-exp-note.assets/image-20220320155229014.png)

<img src="/assets/algo-exp-note.assets/image-20220320180500023.png" alt="image-20220320180500023" style="zoom:50%;" />

这道题我们需要4个指针，分别指向：head, newhead, tail, newtail

具体思路：

我们会得到单链表的头，所以可以用head得到tail，同时可以得到链表中node的总个数

之后我们就要对k的值进行处理，先用k的绝对值除以总的长度，**我们没有必要做多次的移动**

例如：k = 30， 而length只有6，最后移动的结果就是原来的链表

处理过后，我们就可以用已知的数据得出新的tail所处的位置，具体有以下2种情况：

H                                   T

0 -> 1 -> 2 -> 3 -> 4 -> 5 ->          k  = 2  > 0

H                   NT   NH   T

0 -> 1 -> 2 -> 3 -> 4 -> 5 ->          k  = 2  > 0      newTailPosition = 6 - 2 = 4

H                   NT   NH   T

0 -> 1 -> 2 -> 3    4 -> 5 ->0



H                                    T

0 -> 1 -> 2 -> 3 -> 4 -> 5 ->          k  = -2  < 0

H    NT  NH                   T

0 -> 1 -> 2 -> 3 -> 4 -> 5 ->          k  = -2  < 0      newTailPosition = 2

H    NT  NH                   T

0 -> 1    2 -> 3 -> 4 -> 5 ->0          k  = -2  < 0      newTailPosition = 2

**可以看到不管k的值是否大于0，我们最后要做的仅仅是： `newTail.next = null; tail.next = head;`**

让NT指向null，让T指向head即可

```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static LinkedList shiftLinkedList(LinkedList head, int k) {
        LinkedList tail = head;
        int length = 1;

        while (tail.next != null) {
            tail = tail.next;
            length++;
        }

        int offset = Math.abs(k) % length;
        if (offset == 0)
            return head;
        int newTailPosition = k > 0 ? length - offset : offset;

        LinkedList newTail = head;
        for (int i = 1; i < newTailPosition; i++)
            newTail = newTail.next;
        LinkedList newHead = newTail.next;
        newTail.next = null;
        tail.next = head;

        return newHead;
    }

    static class LinkedList {
        public int value;
        public LinkedList next;

        public LinkedList(int value) {
            this.value = value;
            next = null;
        }
    }
}

```

## 7.Remove Duplicates From Linked List（Green）

![image-20220320223323708](/assets/algo-exp-note.assets/image-20220320223323708.png)

我的解法

```java
import java.util.*;
//O(N) time O(1) space
class Program {
  // This is an input class. Do not edit.
  public static class LinkedList {
    public int value;
    public LinkedList next;

    public LinkedList(int value) {
      this.value = value;
      this.next = null;
    }
  }

  public LinkedList removeDuplicatesFromLinkedList(LinkedList linkedList) {
    LinkedList currentNode = linkedList;
	LinkedList nextNode = currentNode.next;

	while(nextNode != null) {
        //nextNode的值和当前node的值相等，删除nextNode，并指向下一个node
		if (nextNode.value == currentNode.value){
			currentNode.next = nextNode.next;
			nextNode = nextNode.next;
			continue;
		}
        //相邻两个node的值不相等，向右同时移动
		currentNode = currentNode.next;
		nextNode = nextNode.next;
	}
    return linkedList;
  }
}
```

官方解法

```java
import java.util.*;

class Program {
    // This is an input class. Do not edit.
    public static class LinkedList {
        public int value;
        public LinkedList next;

        public LinkedList(int value) {
            this.value = value;
            this.next = null;
        }
    }

    public LinkedList removeDuplicatesFromLinkedList(LinkedList linkedList) {
        LinkedList currentNode = linkedList;

        while (currentNode != null) {
            LinkedList nextDistinctNode = currentNode.next;
            //找到和currentNode不同的node
            //这里顺序也很重要，要先判断next是否为null
            while (nextDistinctNode != null && nextDistinctNode.value == currentNode.value) {
                nextDistinctNode = nextDistinctNode.next;
            }

            currentNode.next = nextDistinctNode;
            currentNode = nextDistinctNode;
        }

        return linkedList;
    }
}
```

## 8.Sum of Linked Lists(Blue)

![image-20220320230633181](/assets/algo-exp-note.assets/image-20220320230633181.png)

```java
class Program {
    // This is an input class. Do not edit.
    public static class LinkedList {
        public int value;
        public LinkedList next;

        public LinkedList(int value) {
            this.value = value;
            this.next = null;
        }
    }

    public LinkedList sumOfLinkedLists(LinkedList linkedListOne, LinkedList linkedListTwo) {
        //构建返回值链表
        LinkedList sumLinkedListHead = new LinkedList(0);
        LinkedList currentNode = sumLinkedListHead;
        //建立两个指针追踪链表1，2
        LinkedList p1 = linkedListOne;
        LinkedList p2 = linkedListTwo;
        //记录进位情况
        int carry = 0;
        while (p1 != null || p2 != null || carry == 1) {
            int value1 = p1 == null? 0 : p1.value;
            int value2 = p2 == null? 0 : p2.value;

            int sum = value1 + value2 + carry;
            int newValue = sum % 10;
            carry = sum / 10;

            LinkedList newNode = new LinkedList(newValue);
            currentNode.next = newNode;
            currentNode = newNode;

            p1 = p1 == null ? null : p1.next;
            p2 = p2 == null ? null : p2.next;
        }

        return sumLinkedListHead.next;
    }
}
```



# Binary Search Tree

## 1.Find Closest value in BST(Green)

![image-20220320184215771](/assets/algo-exp-note.assets/image-20220320184215771.png)

基本思路：

先令closest的值为**无穷大**或者为**头节点的值**(无穷大不是很好的选择，如果target = -1， `MAX - (-1) =0xffffffffffffffff+0x1 = 0x0 `, 从而无穷大和target最接近)。



先计算出closest到target的值和第一个当前节点到target的值

若|closest - target| > |tree.value - target|

那么更新closest为`tree.value`



之后比较target的值和tree.value的大小（**充分利用BST的特点**）

若target > value

那么value左侧的值都不会比value更加接近，所以继续看右节点

若target < value

那么value右侧的值都不会比value更加接近，所以继续看左节点



- **解法一（iterative）**

```java
import java.util.*;
//Average O(log(N)) time  O(1) space
//Worst O(N) time  O(1) space
class Program {
    public static int findClosestValueInBst(BST tree, int target) {
        int closest = tree.value;
        BST currentNode = tree;

        while(currentNode != null){
            if(Math.abs(closest-target) > Math.abs(currentNode.value-target))
                closest = currentNode.value;
            if(target < currentNode.value)
                currentNode = currentNode.left;
            else if(target >currentNode.value)
                currentNode = currentNode.right;
            else
                break;
        }
        return closest;
    }

    static class BST {
        public int value;
        public BST left;
        public BST right;

        public BST(int value) {
            this.value = value;
        }
    }
}
```

- **解法二（recursive）**

```java
import java.util.*;
//Average O(log(N)) time  O(log(N)) space
//Worst O(N) time  O(N) space
class Program1 {
    public static int findClosestValueInBst(BST tree, int target) {
        return recursiveFindClosestValueInBst(tree, target, tree.value);
    }

    public static int recursiveFindClosestValueInBst(BST tree, int target, int closest){
        if(tree == null)
            return closest;
        if(Math.abs(closest - target) > Math.abs(tree.value - target))
            closest = tree.value;
        if(target > tree.value)
            return recursiveFindClosestValueInBst(tree.right, target, closest);
        else if(target < tree.value)
            return recursiveFindClosestValueInBst(tree.left, target, closest);
        else
            return closest;
    }
    static class BST {
        public int value;
        public BST left;
        public BST right;

        public BST(int value) {
            this.value = value;
        }
    }
}
```

## 2.BST construction(Blue*)

![image-20220322132314878](/assets/algo-exp-note.assets/image-20220322132314878.png)

![image-20220322132328188](/assets/algo-exp-note.assets/image-20220322132328188.png)

BST的构建主要应用到3个函数，插入、查找和删除

插入：插入是在原有的BST的叶节点插入，不需要改变其他的节点

查找：查找是在原有的BST中查找，找到的话就返回true

前两个方法与第一问的想法基本相同

删除（难点）：我们需要在这种情形下考虑更多的可能性.第一个解法用循环的方式完成了remove，这种方式需要随时跟踪要删除的节点和他的父节点，难度较大，需要考虑的情形较为复杂。第二个解法用recursion完成了节点的删除，因为recursion的帮助，我们不需要随时记录要删除的节点的父节点，recursion可以帮助我们记录节点间的父子关系。这一部分建议调试代码理解。

**删除的代码部分逻辑性很强，赋值语句顺序的错误可能导致很大的问题**

```java
import java.util.*;
//Average O(log(N)) time O(1) space
//Worst O(N) time O(1) space
class Program {
    static class BST {
        public int value;
        public BST left;
        public BST right;

        public BST(int value) {
            this.value = value;
        }

        public BST insert(int value) {
            //insert是在什么情况下都会加进去的，不存在加不进去的情况
            //所以我们将这个功能放在一个死循环中，当insert完成后即可break跳出循环
            BST currentNode = this;
            while (true) {
                if (value < currentNode.value) {
                    if (currentNode.left == null) {
                        BST newNode = new BST(value);
                        currentNode.left = newNode;
                        break;
                    } else {
                        currentNode = currentNode.left;
                    }
                } else {
                    if (currentNode.right == null) {
                        BST newNode = new BST(value);
                        currentNode.right = newNode;
                        break;
                    } else {
                        currentNode = currentNode.right;
                    }
                }
            }
            // Do not edit the return statement of this method.
            return this;
        }

        public boolean contains(int value) {
            BST currentNode = this;
            //contains函数可能会找不到，所以while函数需要设定一个循环截止的标志
            //这里我们使用null作为终结，即搜索完了所有可能的节点
            while (currentNode != null) {
                if (value < currentNode.value) {
                    currentNode = currentNode.left;
                } else if (value > currentNode.value) {
                    currentNode = currentNode.right;
                } else//找到了
                    return true;
            }
			//循环结束没有找到
            return false;
        }

        public BST remove(int value) {
            //输入要删除的value
            remove(value, null);
            return this;
        }

        //难点
        public void remove(int value, BST parentNode) {
            BST currentNode = this;
            //我们需要遍历所有的可能性，所以我们循环的终止为node为空
            while (currentNode != null) {
                if (value < currentNode.value) {//寻找
                    parentNode = currentNode;
                    currentNode = currentNode.left;
                } else if (value > currentNode.value) {//寻找
                    parentNode = currentNode;
                    currentNode = currentNode.right;
                } else {//找到要删除的点，做进一步的处理
                    //下面if判断的顺序十分重要，不可以打乱！！！！！！！！！！！！！！！！

                    //如果当前节点同时拥有左右子节点
                    if (currentNode.right != null && currentNode.left != null) {
                        currentNode.value = currentNode.right.getMinValue();
                        currentNode.right.remove(currentNode.value, currentNode);
					//当前节点为最多有一个子节点的根节点
                    } else if (parentNode == null) {//该点为根节点
                        if (currentNode.left != null) {
                            //这里的顺序一定要注意！！！！！！！！！！！！1
                      		//否则指针可能已经被改变，但我们想当然的以为他还是指向原来的地方
                            currentNode.value = currentNode.left.value;
							currentNode.right = currentNode.left.right;
                            currentNode.left = currentNode.left.left;

                        } else if (currentNode.right != null) {
                            currentNode.value = currentNode.right.value;
                            currentNode.left = currentNode.right.left;
                            currentNode.right = currentNode.right.right;
                        } else {
                            //只有一个根节点
                        }
                     //当前节点不是根节点，且最多有一个子节点
                    } else if (parentNode.left == currentNode)
                        parentNode.left = currentNode.left != null ? currentNode.left : currentNode.right;
                    else if (parentNode.right == currentNode)
                        parentNode.right = currentNode.left != null ? currentNode.left : currentNode.right;

                    break;
                }
				//什么都没找到，啥也不做

            }

        }

        public int getMinValue() {
            BST currentNode = this;
            while (currentNode.left != null)
                currentNode = currentNode.left;
            return currentNode.value;
        }

    }
}

```

- 我的解法（优于官方的思路，更好理解，但费空间）

  二叉搜索树的中序遍历的序列是递增排序的序列。中序遍历的遍历次序：`Left -> Node -> Right`。

  <img src="/assets/algo-exp-note.assets/image-20220321215225524.png" alt="image-20220321215225524" style="zoom:67%;" />

  - `Successor`代表的是中序遍历序列的下一个节点。即**比当前节点大的最小节点**，简称后继节点。 先取当前节点的右节点，然后一直取该节点的左节点，直到左节点为空，则最后指向的节点为后继节点。(eg: 33的successor就是34)；

  - `Predecessor` 代表的是中序遍历序列的前一个节点。即**比当前节点小的最大节点**，简称前驱节点。先取当前节点的左节点，然后取该节点的右节点，直到右节点为空，则最后指向的节点为前驱节点。（eg：25的predecessor就是15）；



  **删除节点的本质，其实就是将`currentNode`的值变为它successor的值或者predecessor的值。然后，删除successor或者predecessor的值即可。**

  这里有四种可能的情况：

  1. 要删除的节点为叶子节点，可以直接删除。

     <img src="/assets/algo-exp-note.assets/b86c5d5866fb8b1f6a2f15f47262adf3ae68e56498c9e261a031bbb8ebc55588-file_1576477912302.jpeg" alt="在这里插入图片描述" style="zoom:33%;" />

  2. 要删除的节点具有左节点和右节点，**我们既可以选择predecessor（25）也可以选择successor（34）**，在这里，我们默认选择successor的值替换`currentNode`的值。之后，然后再递归的向下删除前驱节点。

     <img src="/assets/algo-exp-note.assets/12353e5c71267aafd355319a8b881f0b9efae0680358b7ce738228151a42d3cc-file_1576477912312.jpeg" alt="在这里插入图片描述" style="zoom: 33%;" />

  3. 要删除的节点只有左节点。这就意味着我们只能用predecessor。我们可以使用它的前驱节点进行替代，然后再递归的向下删除前驱节点。

     <img src="/assets/algo-exp-note.assets/2a9aa44aab7948e78e06182791e2eaaf00fb72eff054a1f4612030a047dde59a-file_1576477912315.jpeg" alt="在这里插入图片描述" style="zoom:33%;" />

  4. 要删除的节点只有右节点。这就意味着我们只能用successor。这和第二种情况的处理方式相同，在具体实现时会与第二种情况相结合。

  ```java
  import java.util.*;
  //Average O(log(N)) time O(log(N)) space
  //Worst O(N) time O(N) space
  class Program {
      static class BST {
          public int value;
          public BST left;
          public BST right;

          public BST(int value) {
              this.value = value;
          }

          public BST insert(int value) {
              // Write your code here.
              // Do not edit the return statement of this method.
              BST currentNode = this;
              while (true) {
                  if (value < currentNode.value) {
                      if (currentNode.left == null) {
                          BST newNode = new BST(value);
                          currentNode.left = newNode;
                          break;
                      } else {
                          currentNode = currentNode.left;
                      }
                  } else {
                      if (currentNode.right == null) {
                          BST newNode = new BST(value);
                          currentNode.right = newNode;
                          break;
                      } else {
                          currentNode = currentNode.right;
                      }
                  }
              }
              return this;
          }

          public boolean contains(int value) {
              BST currentNode = this;
              while (currentNode != null) {
                  if (value == currentNode.value) return true;
                  else if (value < currentNode.value) {
                      currentNode = currentNode.left;
                  } else {
                      currentNode = currentNode.right;
                  }
              }
              return false;
          }

          public BST remove(int value) {
              return remove(this, value);
          }

          public BST remove(BST node, int value) {
              // delete from the right subtree
              if (value > node.value) node.right = remove(node.right, value);
                  // delete from the left subtree
              else if (value < node.value) node.left = remove (node.left, value);
                  // delete the current node
              else {
                  // the node is a leaf
                  if (node.left == null && node.right == null) node = null;
                  // the node is not a leaf and has a right child
                  else if (node.right != null) {
                      node.value = getSuccessor(node).value;
                      node.right = remove(node.right, node.value);
                  }
                  // the node is not a leaf, has no right child, and has a left child
                  else {
                      node.value = getPredecessor(node).value;
                      node.left = remove(node.left, node.value);
                  }
              }
              return node;
          }


          private BST getPredecessor(BST currentNode) {
              BST predecessor = currentNode.left;
              while(predecessor.right != null)
                  predecessor = predecessor.right;
              return predecessor;
          }

          private BST getSuccessor(BST currentNode) {
              BST successor = currentNode.right;
              while(successor.left != null)
                  successor = successor.left;
              return successor;
          }
      }
  }
  ```


## 3.Validate BST(Blue)

![image-20220322132013836](/assets/algo-exp-note.assets/image-20220322132013836.png)

本题希望遍历整个BST然后得出BST是否为一个BST

本题的主要工作就是确定**每一个Node都比它应该小的值小比它应该大的值大。**

<u>注意：我们不能仅仅只是比较每个节点和它的左右两个子节点，这样是无法确定一个BST是否为真的。</u>

BST的特点是：左子树放小于等于自己的节点；右子树放大于自己的节点

所以对于一个正常的节点`currentNode`， min <= `currentNode.value` < max

```java
import java.util.*;
//O(N) time O(d) space
class Program {
    public static boolean validateBst(BST tree) {
        return validateBstHelper(tree, Integer.MAX_VALUE, Integer.MIN_VALUE);
    }

    public static boolean validateBstHelper(BST tree, int max, int min){
        if(tree == null)
            return true;
        if(tree.value >= max || tree.value < min)
            return false;
        boolean leftIsvalid = validateBstHelper(tree.left, tree.value, min);
        boolean rightIsvalid = validateBstHelper(tree.right, max, tree.value);
        return leftIsvalid && rightIsvalid;
    }



    static class BST {
        public int value;
        public BST left;
        public BST right;

        public BST(int value) {
            this.value = value;
        }
    }
}
```

## 4.BST Traversal(Blue)

先序，中序，后序是指`currentNode`出现的位置

```java
import java.util.*;
//O(N)time O(N)space
class Program {
    public static List<Integer> inOrderTraverse(BST tree, List<Integer> array) {
        if(tree.left != null)
            inOrderTraverse(tree.left, array);
        array.add(tree.value);
        if(tree.right != null)
            inOrderTraverse(tree.right,array);

        return array;
    }

    public static List<Integer> preOrderTraverse(BST tree, List<Integer> array) {
        array.add(tree.value);
        if(tree.left != null)
            preOrderTraverse(tree.left, array);
        if(tree.right != null)
            preOrderTraverse(tree.right,array);

        return array;

    }

    public static List<Integer> postOrderTraverse(BST tree, List<Integer> array) {

        if(tree.left != null)
            postOrderTraverse(tree.left, array);
        if(tree.right != null)
            postOrderTraverse(tree.right,array);
        array.add(tree.value);
        return array;
    }

    static class BST {
        public int value;
        public BST left;
        public BST right;

        public BST(int value) {
            this.value = value;
        }
    }
}
```

## 5.Min Height BST(Blue)

![image-20220322135011658](/assets/algo-exp-note.assets/image-20220322135011658.png)

![image-20220322135036060](/assets/algo-exp-note.assets/image-20220322135036060.png)

**要想让BST最小，首先选取大小居于中间的值为父节点，然后父节点左右居于中间的值再分别做父节点的左右节点**: 这样。BST中的每个节点的左右子树的深度差距最大为1。

- 方法一：这个方法是通过二分法确定一个需要插入的值，然后调用`insert(int value)`函数将该值插入。从而，我们一共要插入N个值，每一次插入需要使用的insert的时间复杂度为O(log(N))。从而，这个解法的时间复杂度很大。**不推荐！！！**

```java
import java.util.*;
//O(Nlog(N)) time O(N) space
class Program {
    public static BST minHeightBst(List<Integer> array) {
        return constructMinHeightBst(array, null, 0,array.size()-1);

    }

    //注意：这里的BST bst仅会被赋值一次，代表着根节点。
    public static BST constructMinHeightBst(List<Integer> array, BST bst, int start, int end) {
        if(end < start)
            return null;
        int mid = (start + end) / 2;
        int valueToAdd = array.get(mid);
        if (bst == null) {
            bst = new BST(valueToAdd);
        } else {
            bst.insert(valueToAdd);
        }
        constructMinHeightBst(array, bst, start, mid-1);
        constructMinHeightBst(array, bst, mid+1, end);
        return bst;
    }

    static class BST {
        public int value;
        public BST left;
        public BST right;

        public BST(int value) {
            this.value = value;
            left = null;
            right = null;
        }

        public void insert(int value) {
            if (value < this.value) {
                if (left == null) {
                    left = new BST(value);
                } else {
                    left.insert(value);
                }
            } else {
                if (right == null) {
                    right = new BST(value);
                } else {
                    right.insert(value);
                }
            }
        }
    }
}
```

- **方法二**：本方法不调用`insert(int value)`函数，而是在递归的过程中进行节点的插入。更加简洁，易于理解，且时间复杂度更低。

```java
import java.util.*;
//O(N) time O(N) space
class Program1 {
    public static BST minHeightBst(List<Integer> array) {
        return constructMinHeightBst(array, 0, array.size() - 1);
    }

    public static BST constructMinHeightBst(List<Integer> array, int startIdx, int endIdx) {
        if (startIdx > endIdx)
            return null;

        int midIdx = (startIdx + endIdx) / 2;
        BST newNode = new BST(array.get(midIdx));

        newNode.left = constructMinHeightBst(array, startIdx, midIdx - 1);
        newNode.right = constructMinHeightBst(array, midIdx + 1, endIdx);

        return newNode;
    }

    static class BST {
        public int value;
        public BST left;
        public BST right;

        public BST(int value) {
            this.value = value;
            left = null;
            right = null;
        }

    }
}
```

## 6.SameBSTs(Red)

![image-20220322163228135](/assets/algo-exp-note.assets/image-20220322163228135.png)

- 方法一

1.如果两个数组的第一个元素不一样，那么它们所生成的树一定不一样

2.如果两个数组的长度不一样，那么他们所生成的树一定不一样

如果第一个元素一样，根据第一个元素的大小将剩下的元素分成2个部分：大于等于父节点的和小于父节点的

将新得到的2个数组再进行比较（recursive）

看他们是否依然符合1，2两点。

Note：如果父节点一样，那么父节点下的左右子节点也要一样！！！！！！

<img src="/assets/algo-exp-note.assets/image-20220323175059797.png" alt="image-20220323175059797" style="zoom: 50%;" />

通过上图不难发现，**每个节点的左子节点只能选择离自己最近的且小于自身的节点；每个节点的右子节点只能选择离自己最近的且大于等于自身的节点。**

```java
import java.util.*;
//O(N^2) time O(N^2) space
class Program {
    public static boolean sameBsts(List<Integer> arrayOne, List<Integer> arrayTwo) {
        return sameBstsHelper(arrayOne, arrayTwo);
    }

    public static boolean sameBstsHelper(List<Integer> arrayOne, List<Integer> arrayTwo) {
        if (arrayOne.size() == 0 || arrayTwo.size() == 0)
            return true;
        if (arrayOne.get(0).intValue() != arrayTwo.get(0).intValue())
            return false;
        if (arrayOne.size() != arrayTwo.size())
            return false;
        List<Integer> smallerOne = getSmaller(arrayOne);
        List<Integer> smallerTwo = getSmaller(arrayTwo);
        List<Integer> biggerOne = getBiggerOrEqual(arrayOne);
        List<Integer> biggerTwo = getBiggerOrEqual(arrayTwo);
        return sameBstsHelper(smallerOne,smallerTwo)&& sameBstsHelper(biggerOne,biggerTwo);
    }

    public static List<Integer> getSmaller(List<Integer> array) {
        List<Integer> smaller = new ArrayList<>();
        for (int i = 1; i < array.size(); i++) {
            if(array.get(i).intValue() < array.get(0).intValue())
                smaller.add(array.get(i));
        }
        return smaller;
    }

    public static List<Integer> getBiggerOrEqual(List<Integer> array) {
        List<Integer> biggerOrEqual = new ArrayList<>();
        for (int i = 1; i < array.size(); i++) {
            if(array.get(i).intValue() >= array.get(0).intValue())
                biggerOrEqual.add(array.get(i));
        }
        return biggerOrEqual;
    }
}

```

- 方法二(*)

方法一的思路很好理解，但是空间复杂度很大，由于每个节点的左子节点只能选择离自己最近的且小于自身的节点；每个节点的右子节点只能选择离自己最近的且大于等于自身的节点。那么，我们可以不创造新的数组，通过返回每一个节点的下一个左节点或者右节点的index的方式来解决本问题。这样，空间复杂度可以缩减到`O(d)`.

<img src="/assets/algo-exp-note.assets/image-20220323190917362.png" alt="image-20220323190917362" style="zoom: 50%;" />

```java
import java.util.*;
//O(N^2) time O(d) space
class Program1 {
    public static boolean sameBsts(List<Integer> arrayOne, List<Integer> arrayTwo) {
        return areSameBsts(arrayOne, arrayTwo, 0, 0, Integer.MIN_VALUE, Integer.MAX_VALUE);
    }

    //rootOneIdx, rootTwoIdx记录的是从arrayOne，arrayTwo中新选出来的节点的index
    public static boolean areSameBsts(
            List<Integer> arrayOne,
            List<Integer> arrayTwo,
            int rootIdxOne,
            int rootIdxTwo,
            int min,
            int max) {
        //找不到符合要求的节点
        if (rootIdxOne == -1 || rootIdxTwo == -1) return rootIdxOne == rootIdxTwo;
        //找到了符合要求的节点，对比值是否一样
        if (arrayOne.get(rootIdxOne).intValue() != arrayTwo.get(rootIdxTwo).intValue())
            return false;

        //arrayOne, arrayTwo的当前节点(rootIdx)的左子节点
        int leftRootIdxOne = getIdxOfFirstSmaller(arrayOne, rootIdxOne, min);
        int leftRootIdxTwo = getIdxOfFirstSmaller(arrayTwo, rootIdxTwo, min);

        //arrayOne, arrayTwo的当前节点(rootIdx)的右子节点
        int rightRootIdxOne = getIdxOfFirstBiggerOrEqual(arrayOne, rootIdxOne, max);
        int rightRootIdxTwo = getIdxOfFirstBiggerOrEqual(arrayTwo, rootIdxTwo, max);

        int currentValue = arrayOne.get(rootIdxOne);
        boolean leftAreSame = areSameBsts(arrayOne, arrayTwo, leftRootIdxOne, leftRootIdxTwo, min, currentValue);
        boolean rightAreSame = areSameBsts(arrayOne, arrayTwo, rightRootIdxOne, rightRootIdxTwo, currentValue, max);

        return leftAreSame && rightAreSame;
    }

    private static int getIdxOfFirstSmaller(List<Integer> array, int rootIdx, int min) {
        for (int i = rootIdx + 1; i < array.size(); i++) {
            if (array.get(i) >= min && array.get(i) < array.get(rootIdx))
                return i;
        }
        return -1;
    }

    private static int getIdxOfFirstBiggerOrEqual(List<Integer> array, int rootIdx, int max) {
        for (int i = rootIdx + 1; i < array.size(); i++) {
            if (array.get(i) >= array.get(rootIdx) && array.get(i) < max)
                return i;
        }
        return -1;
    }
}
```

## 7.Find Kth largest Value In BST(Blue)

![image-20220323192112776](/assets/algo-exp-note.assets/image-20220323192112776.png)

- 方法一：

中序遍历BST，获得一个升序数组，返回第k大的元素即可。

```java
import java.util.*;
//O(N) time O(N) space
class Program {
    // This is an input class. Do not edit.
    static class BST {
        public int value;
        public BST left = null;
        public BST right = null;

        public BST(int value) {
            this.value = value;
        }
    }

    public int findKthLargestValueInBst(BST tree, int k) {
        List<Integer> sortedNodeValues = new ArrayList<>();
        inOrderTraverse(tree, sortedNodeValues);

        return sortedNodeValues.get(sortedNodeValues.size() - k);
    }

    public void inOrderTraverse(BST tree, List<Integer> sortedNodeValues) {
        if (tree == null)
            return;
        inOrderTraverse(tree.left, sortedNodeValues);
        sortedNodeValues.add(tree.value);
        inOrderTraverse(tree.right, sortedNodeValues);
    }
}
```

- 方法二：

由于中序遍历可以获得一个升序数组，那么将中序遍历的顺序反过来就可以得到一个降序数组，也就是说，我们reverse in order traverse遍历到的第一个节点是BST中值最大的节点，第二个是第二大的节点，........，第k个就是第k大的节点了。

```java
import java.util.*;
//O(h+k) time O(h) space
class Program1 {
    // This is an input class. Do not edit.
    static class BST {
        public int value;
        public BST left = null;
        public BST right = null;

        public BST(int value) {
            this.value = value;
        }
    }

    static class TreeInfo {
        public int numberOfNodesVisted;
        public int latestVisitedNodeValue;

        public TreeInfo(int numberOfNodesVisted, int latestVisitedNodeValue) {
            this.numberOfNodesVisted = numberOfNodesVisted;
            this.latestVisitedNodeValue = latestVisitedNodeValue;
        }
    }

    public int findKthLargestValueInBst(BST tree, int k) {
        TreeInfo treeInfo = new TreeInfo(0, -1);
        reverseInOrderTraverse(tree, treeInfo, k);
        return treeInfo.latestVisitedNodeValue;
    }

    public void reverseInOrderTraverse(BST tree, TreeInfo treeInfo, int k) {
        if (tree == null || treeInfo.numberOfNodesVisted >= k)
            return;

        reverseInOrderTraverse(tree.right, treeInfo, k);
        if (treeInfo.numberOfNodesVisted < k) {
            treeInfo.latestVisitedNodeValue = tree.value;
            treeInfo.numberOfNodesVisted += 1;
            reverseInOrderTraverse(tree.left, treeInfo, k);
        }
    }
}
```

## 8.Reconstruct BST(Blue)

![image-20220323213908527](/assets/algo-exp-note.assets/image-20220323213908527.png)

![image-20220323213950901](/assets/algo-exp-note.assets/image-20220323213950901.png)

因为已知二叉树是**前序遍历**且二叉树是**二叉搜索树**，所以我们所构建的BST唯一。

- 方法一：

<img src="/assets/algo-exp-note.assets/image-20220324151533074.png" alt="image-20220324151533074"  />

```java
import java.util.*;
//O(N^2) time O(N) space
class Program {
    // This is an input class. Do not edit.
    static class BST {
        public int value;
        public BST left = null;
        public BST right = null;

        public BST(int value) {
            this.value = value;
        }
    }

    public BST reconstructBst(ArrayList<Integer> preOrderTraversalValues) {
        return reconstructBstHelper(preOrderTraversalValues);
    }

    public BST reconstructBstHelper(List<Integer> subTreePreOrderTraversalValues) {
        if (subTreePreOrderTraversalValues.size() == 0)
            return null;

        int currentValue = subTreePreOrderTraversalValues.get(0);
        int rightSubTreeRootIdx = subTreePreOrderTraversalValues.size();

        for (int i = 1; i < subTreePreOrderTraversalValues.size(); i++) {
            if (subTreePreOrderTraversalValues.get(i) >= currentValue) {
                rightSubTreeRootIdx = i;
                break;
            }
        }

        BST newNode = new BST(currentValue);
        newNode.left =
                reconstructBstHelper(
                        subTreePreOrderTraversalValues.subList(1, rightSubTreeRootIdx));
        newNode.right =
                reconstructBstHelper(
                        subTreePreOrderTraversalValues.subList(rightSubTreeRootIdx, subTreePreOrderTraversalValues.size()));
        return newNode;
    }
}
```



- 方法二：

  方法一的缺点在于，在每一个recursion call中需要遍历数组找到当前节点右子节点的坐标值，从而能够确定左子树和右子树的区间。这样时间复杂度很高。方法二只遍历一遍数组，具体的思路就是按照前序遍历的思路去重构二叉树。

  <img src="/assets/algo-exp-note.assets/image-20220324162026602.png" alt="image-20220324162026602" style="zoom: 50%;" />

  ```java
  import java.util.*;
  //O(N) time O(N) space
  class Program1 {
      // This is an input class. Do not edit.
      static class BST {
          public int value;
          public BST left = null;
          public BST right = null;

          public BST(int value) {
              this.value = value;
          }
      }

      static class TreeInfo {
          public int rootIdx;

          public TreeInfo(int rootIdx) {
              this.rootIdx = rootIdx;
          }
      }

      public BST reconstructBst(ArrayList<Integer> preOrderTraversalValues) {
          TreeInfo treeInfo = new TreeInfo(0);
          return reconstructBstHelper(preOrderTraversalValues, Integer.MIN_VALUE, Integer.MAX_VALUE, treeInfo);
      }

      public BST reconstructBstHelper(List<Integer> preOrderTraversalValues, int min, int max, TreeInfo treeInfo) {
          //all the elements in the array have been added into the tree
          if (treeInfo.rootIdx == preOrderTraversalValues.size())
              return null;

          int currentValue = preOrderTraversalValues.get(treeInfo.rootIdx);
          //currentValue is not in the range of [min, max)
          //which means that we cannot create a new node with currentValue
          if (currentValue < min || currentValue >= max)
              return null;

          BST newNode = new BST(currentValue);
          treeInfo.rootIdx += 1;
          newNode.left = reconstructBstHelper(preOrderTraversalValues, min, currentValue, treeInfo);
          newNode.right = reconstructBstHelper(preOrderTraversalValues, currentValue, max, treeInfo);

          return newNode;
      }
  }
  ```

# Binary Trees

## 1.Branch sums(Green)

![image-20201206202050388](/assets/algo-exp-note.assets/image-20201206202050388.png)

![image-20201206202211457](/assets/algo-exp-note.assets/image-20201206202211457.png)

```java
import java.util.*;
//O(N) time O(N) space
class Program {
    // This is the class of the input root. Do not edit it.
    public static class BinaryTree {
        int value;
        BinaryTree left;
        BinaryTree right;

        BinaryTree(int value) {
            this.value = value;
            this.left = null;
            this.right = null;
        }
    }

    public static List<Integer> branchSums(BinaryTree root) {
        List<Integer> sums = new ArrayList<Integer>();
        branchSumsHelper(root, sums, 0);
        return sums;
    }

    public static void branchSumsHelper(BinaryTree node, List<Integer> sums, int runningSum) {
        if (node == null)
            return;
        runningSum += node.value;
        if (node.left == null && node.right == null)
            sums.add(runningSum);
        branchSumsHelper(node.left, sums, runningSum);
        branchSumsHelper(node.right, sums, runningSum);
    }
}
```

## 2.Node Depths(Green)

![image-20220324200534978](/assets/algo-exp-note.assets/image-20220324200534978.png)

- 方法一：

```java
import java.util.*;
//O(N) time O(h) space
class Program {

    public static int nodeDepths(BinaryTree root) {
        return nodeDepthsHelper(root, 0);
    }

    public static int nodeDepthsHelper(BinaryTree node, int depth) {
        if (node == null)
            return 0;

        return depth +
                nodeDepthsHelper(node.left, depth + 1) +
                nodeDepthsHelper(node.right, depth + 1);

    }

    static class BinaryTree {
        int value;
        BinaryTree left;
        BinaryTree right;

        public BinaryTree(int value) {
            this.value = value;
            left = null;
            right = null;
        }
    }
}
```

- 方法二：自己建立一个栈的写法，不借用操作系统的栈帧完成递归。

![image-20201210172420441](/assets/algo-exp-note.assets/image-20201210172420441.png)

![image-20201210172439432](/assets/algo-exp-note.assets/image-20201210172439432.png)

## 3.Invert Binary Tree(Blue)

![image-20210111152226345](/assets/algo-exp-note.assets/image-20210111152226345.png)

![image-20210111152332329](/assets/algo-exp-note.assets/image-20210111152332329.png)

我们只需要先将根节点的左右子节点的指针互换，然后使用recursive方法，将其余的节点的左右节点指针互换就可以了。

```java
import java.util.*;
//O(N) time O(d) space
class Program {
    public static void invertBinaryTree(BinaryTree tree) {
        if (tree == null)
            return;
        swapLeftAndRightSubTree(tree);
        invertBinaryTree(tree.left);
        invertBinaryTree(tree.right);
    }

    public static void swapLeftAndRightSubTree(BinaryTree node) {
        BinaryTree temp = node.left;
        node.left = node.right;
        node.right = temp;
    }

    static class BinaryTree {
        public int value;
        public BinaryTree left;
        public BinaryTree right;

        public BinaryTree(int value) {
            this.value = value;
        }
    }
}
```

第二种方法：

![image-20210111152857021](/assets/algo-exp-note.assets/image-20210111152857021.png)



## 4.Binary Tree Diameter(Blue)

![image-20210111153208910](/assets/algo-exp-note.assets/image-20210111153208910.png)

![image-20210111153417557](/assets/algo-exp-note.assets/image-20210111153417557.png)

<img src="/assets/algo-exp-note.assets/image-20220325145131165.png" alt="image-20220325145131165" style="zoom:67%;" />

上图展示了本题的基本思路，我们为每一个节点保存2个相关的值

`diameter`：到当前node为止，**以当前node为根节点的子树中拥有的最长的diameter**；

`height`：从叶节点开始到当前的node已经走过了的最多的节点数（取左和右子树的height的最大值+1）

总而言之，diameter存储着以当前节点为根节点的子树的最大diameter。height表示的是当前节点作为单链中的一个节点时，他能提供的最长的长度。

```java
import java.util.*;
//O(N) time O(h) sapce
class Program {
    // This is an input class. Do not edit.
    static class BinaryTree {
        public int value;
        public BinaryTree left = null;
        public BinaryTree right = null;

        public BinaryTree(int value) {
            this.value = value;
        }
    }

    static class TreeInfo {
        public int diameter;
        public int height;

        public TreeInfo(int diameter, int height) {
            this.diameter = diameter;
            this.height = height;
        }
    }

    public int binaryTreeDiameter(BinaryTree tree) {
        return getTreeInfo(tree).diameter;
    }

    public TreeInfo getTreeInfo(BinaryTree node) {
        if (node == null)
            return new TreeInfo(0, 0);

        TreeInfo leftNodeInfo = getTreeInfo(node.left);
        TreeInfo rightNodeInfo = getTreeInfo(node.right);

        //当前node的diameter
        int longestPathThroughCurrentNode = leftNodeInfo.height + rightNodeInfo.height;
        //当前node子树的最大diameter
        int maxDiameterSoFar = Math.max(leftNodeInfo.diameter, rightNodeInfo.diameter);
        //diameter始终保存当前以当前node为root的树的最大diameter
        int currentDiameter = Math.max(maxDiameterSoFar, longestPathThroughCurrentNode);
        //更新height值
        int currentHeight = 1 + Math.max(leftNodeInfo.height, rightNodeInfo.height);

        return new TreeInfo(currentDiameter, currentHeight);
    }
}
```

## 5.Find Successor(Blue)

![image-20210114140807652](/assets/algo-exp-note.assets/image-20210114140807652.png)

![image-20210114140943124](/assets/algo-exp-note.assets/image-20210114140943124.png)

- 方法一：进行中序遍历，将结果存到arraylist里，遍历arraylist，找到目标后返回他的后一个node即可

  ​				细节见代码，在此不做记录。

- 方法二：因为本题给了一个可以指向父节点的指针，所以我们可以把每个目标节点（在本例中为5）分成如下几种情况：

1. `successor`为当前节点的子节点（当前节点必须有右子节点，从而能继续向下递归）

   1）该右子节点无左子节点：`successor`为该右子节点

   2） 该右子节点有左子节点：`successor`为该右子节点的最左子节点

2. `successor`为当前节点的祖先节点（当前节点没有右子节点，只能回溯）

   1）目标节点是其父节点的左子节点：`successor`为其父节点

   2）目标节点是其父节点的右子节点：`successor`为right most parent(见代码)

   ![image-20220325164948918](/assets/algo-exp-note.assets/image-20220325164948918.png)

   ```java
   import java.util.*;

   class Program1 {
       // This is an input class. Do not edit.
       static class BinaryTree {
           public int value;
           public BinaryTree left = null;
           public BinaryTree right = null;
           public BinaryTree parent = null;

           public BinaryTree(int value) {
               this.value = value;
           }
       }

       public BinaryTree findSuccessor(BinaryTree tree, BinaryTree node) {
           // Write your code here.
           if (node.right != null)
               return getLeftMost(node.right);
           return getRightMostParent(node);
       }

       public BinaryTree getLeftMost(BinaryTree node) {
           BinaryTree currentNode = node;
           while (currentNode.left != null)
               currentNode = currentNode.left;
           return currentNode;
       }

       public BinaryTree getRightMostParent(BinaryTree node) {
           BinaryTree currentNode = node;
           while (currentNode.parent != null && currentNode.parent.right == currentNode)
               currentNode = currentNode.parent;
           return currentNode.parent;
       }
   }
   ```

## 6.Max Path Sum In Binary Tree(Red)

![image-20210224152014791](/assets/algo-exp-note.assets/image-20210224152014791.png)

![image-20210224152109532](/assets/algo-exp-note.assets/image-20210224152109532.png)

在每一个节点，我们会为它保存2种信息，`maxSumAsBranch` 和 `maxPathSum`.

`maxSumAsBranch` 记录的是如果当前node被作为branch的一部分，那么这些branch中哪一条的sum值最大。

`maxPathSum` 记录的并不是当前node加上左右节点的`maxSumAsBranch`后得到的path sum，而是递归到当前node时，已经遍历过的节点中所产生的max path。从而，这个值可以一直被更新，直到回溯到根节点并作为结果输出。

所以，对于每个node我们主要干两件事：

1. 更新当前node的最大branch sum
2. 计算出当前node的path sum并将它与他子节点的max path sum去比较，若产生了更大的path sum，就对max path sum更新。

![image-20220326120531907](/assets/algo-exp-note.assets/image-20220326120531907.png)

```java
import java.util.*;
//O(N) time O(log(N)) space
class Program {
    public static int maxPathSum(BinaryTree tree) {
        // Write your code here.
        List<Integer> maxPathSumArray = findMaxSum(tree);
        return maxPathSumArray.get(1);
    }

    public static List<Integer> findMaxSum(BinaryTree node) {
        if (node == null)
            return new ArrayList<Integer>(Arrays.asList(0, Integer.MIN_VALUE));
        //处理边界情况，若整个树节点中的值都为负数，那么返回的RMPS应该为一个负无穷的数，若RMPS为0，那么最后的结果只能为0
        List<Integer> leftMaxSumArray = findMaxSum(node.left);
        Integer leftMaxSumAsBranch = leftMaxSumArray.get(0);
        Integer leftMaxSum = leftMaxSumArray.get(1);

        List<Integer> rightMaxSumArray = findMaxSum(node.right);
        Integer rightMaxSumAsBranch = rightMaxSumArray.get(0);
        Integer rightMaxSum = rightMaxSumArray.get(1);

        Integer maxChildSumAsBranch = Math.max(leftMaxSumAsBranch, rightMaxSumAsBranch);
        //如果maxChildSumAsBranch为负数，就可以跳过这些节点，从当前节点开始算起
        Integer maxSumAsBranch = Math.max(maxChildSumAsBranch + node.value, node.value);
        //maxSumAsTriangle
        Integer maxSumAsRootNode = Math.max(maxSumAsBranch, leftMaxSumAsBranch + node.value + rightMaxSumAsBranch);
        Integer maxPathSum = Math.max(maxSumAsRootNode, Math.max(leftMaxSum, rightMaxSum));

        return new ArrayList<Integer>(Arrays.asList(maxSumAsBranch, maxPathSum));
    }

    static class BinaryTree {
        public int value;
        public BinaryTree left;
        public BinaryTree right;

        public BinaryTree(int value) {
            this.value = value;
        }
    }
}

```



```java
import java.util.*;
//O(N) time O(log(N)) space
class Program1 {
    public static int maxPathSum(BinaryTree tree) {
        return findMaxSum(tree).maxPathSum;
    }

    public static TreeInfo findMaxSum(BinaryTree node) {
        if (node == null)
            return new TreeInfo(0, Integer.MIN_VALUE);

        TreeInfo leftTreeInfo = findMaxSum(node.left);
        TreeInfo rightTreeInfo = findMaxSum(node.right);

        int maxChildBranchSum = Math.max(leftTreeInfo.maxSumAsBranch, rightTreeInfo.maxSumAsBranch);
        //if maxChildBranchSum is negative, we will abandon those nodes
        int maxSumAsBranch = Math.max(maxChildBranchSum + node.value, node.value);

        //regard current node as the root of a tree
        //figure out the maxPathSum(might be a tree or a branch of the tree)
        int maxSumPathAsTree = Math.max(maxSumAsBranch,
                leftTreeInfo.maxSumAsBranch + node.value + rightTreeInfo.maxSumAsBranch);
        int runningMaxPathSum = Math.max(maxSumPathAsTree, Math.max(leftTreeInfo.maxPathSum, rightTreeInfo.maxPathSum));

        return new TreeInfo(maxSumAsBranch, runningMaxPathSum);
    }

    static class TreeInfo {
        public int maxSumAsBranch;
        public int maxPathSum;

        public TreeInfo(int maxSumAsBranch, int maxPathSum) {
            this.maxSumAsBranch = maxSumAsBranch;
            this.maxPathSum = maxPathSum;
        }
    }

    static class BinaryTree {
        public int value;
        public BinaryTree left;
        public BinaryTree right;

        public BinaryTree(int value) {
            this.value = value;
        }
    }
}
```

## 7.Height Balanced Binary Tree(Blue)

![image-20220325172045783](/assets/algo-exp-note.assets/image-20220325172045783.png)

```java
import java.util.*;
//O(N) time O(h) space
class Program {
    // This is an input class. Do not edit.
    static class BinaryTree {
        public int value;
        public BinaryTree left = null;
        public BinaryTree right = null;

        public BinaryTree(int value) {
            this.value = value;
        }
    }

    static class TreeInfo {
        public int depth;
        public boolean isBalanced;

        public TreeInfo(int depth, boolean isBalanced) {
            this.depth = depth;
            this.isBalanced = isBalanced;
        }

    }

    public boolean heightBalancedBinaryTree(BinaryTree tree) {
        return getTreeInfo(tree).isBalanced;
    }

    public TreeInfo getTreeInfo(BinaryTree node) {
        if (node == null)
            return new TreeInfo(-1, true);

        TreeInfo leftNodeTreeInfo = getTreeInfo(node.left);
        TreeInfo rightNodeTreeInfo = getTreeInfo(node.right);

        boolean isBalanced = Math.abs(leftNodeTreeInfo.depth - rightNodeTreeInfo.depth) <= 1
                && leftNodeTreeInfo.isBalanced
                && rightNodeTreeInfo.isBalanced;
        int depth = Math.max(leftNodeTreeInfo.depth, rightNodeTreeInfo.depth) + 1;

        return new TreeInfo(depth, isBalanced);
    }

}
```

## 8.Find Nodes Distance K..................

![image-20220326142405382](/assets/algo-exp-note.assets/image-20220326142405382.png)

# Arrays

## 1.Two Number Sum(Green)

![image-20210224202344991](/assets/algo-exp-note.assets/image-20210224202344991.png)

![image-20210224202411072](/assets/algo-exp-note.assets/image-20210224202411072.png)



方法一：穷举法

```java
import java.util.*;
//O(N^2) time  O(1) space
class Program {
    public static int[] twoNumberSum(int[] array, int targetSum) {
        // Write your code here.
        for (int i = 0; i < array.length; i++) {
            for (int j = i + 1; j < array.length; j++){
                if(array[i] + array[j] == targetSum)
                    return new int[]{array[i], array[j]};
            }
        }
        return new int[0];
    }
}
```

方法二：

建立一个`hashset`，遍历一遍数组，对于每一个`current_element`, 算出`potential_element = target_num - current_number`。在`hashset`中寻找是否有该元素，如果有返回；如果没有就将current_element写入set中。

```java
import java.util.*;
//time:O(n) space:O(n)
class Program2 {
    public static int[] twoNumberSum(int[] array, int targetSum) {
        // Write your code here.
        Set<Integer> nums = new HashSet<>();
        for (int num : array) {
            int potentialMatch = targetSum - num;
            if (nums.contains(potentialMatch))
                return new int[]{potentialMatch, num};
            else
                nums.add(num);
        }
        return new int[0];
    }
}
```

方法三：

对数组进行排序，算出最大值和最小值的和，根据实际结果和目标结果间的区别调整左右两端的走向。

<img src="/assets/algo-exp-note.assets/image-20210224205145951.png" alt="image-20210224205145951" style="zoom: 67%;" />

## 2.Validate Subsequence(Green)

![image-20210224205628682](/assets/algo-exp-note.assets/image-20210224205628682.png)

Subsequence是对顺序有要求的，所以我们给出两个指针指向两个array的初始位置。

之后遍历array和sequence，若array中的元素和sequence中的符合那么sequence指针后移。否则就继续遍历array。最终，如果sequence的指针指向了最后一个，那么证明全部按顺序匹配成功。

```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static boolean isValidSubsequence(List<Integer> array, List<Integer> sequence) {
        // Write your code here.
        int arrIdx = 0;
        int seqIdx = 0;
        while (arrIdx<array.size()&& seqIdx < sequence.size()){
            if(array.get(arrIdx).equals(sequence.get(seqIdx)))
                seqIdx++;
            arrIdx++;
        }
        return seqIdx == sequence.size();
    }
}
```



## 3.Three Number Sum(Blue)

![image-20210225111435091](/assets/algo-exp-note.assets/image-20210225111435091.png)

因为本题对顺序有要求，所以我们要先对array进行排序。

之后对array进行遍历，在对array进行遍历的过程中，对于当前元素array[i]，在它之后设立left和right，之后调整left和right去寻找和为target的三元组。

```java
import java.util.*;
//O(N^2) time O(N) space
class Program {
    public static List<Integer[]> threeNumberSum(int[] array, int targetSum) {
        // Write your code here.
        Arrays.sort(array);
        List<Integer[]> triplets = new ArrayList<Integer[]>();
        for (int i = 0; i < array.length - 2; i++) {
            int left = i + 1;
            int right = array.length - 1;
            while (left < right) {
                if (array[i] + array[left] + array[right] == targetSum) {
                    Integer[] newTriplets = {array[i], array[left], array[right]};
                    triplets.add(newTriplets);
                    left++;
                    right--;
                } else if (array[i] + array[left] + array[right] < targetSum)
                    left++;
                else if (array[i] + array[left] + array[right] > targetSum)
                    right--;
            }
        }
        return triplets;
    }
}
```

## 4.Smallest Difference(Blue*)

![image-20210225121145964](/assets/algo-exp-note.assets/image-20210225121145964.png)

本算法的时间复杂度主要由排序算法决定，之后的比较和遍历并不影响整体的复杂度。

本题的思路：

先对两个数组进行排序。

之后用两个指针指向两个数组的第一个元素，并设置最小值和当前值为max

对于分别从两个数组中取出的两个数，他们有三种可能的关系：

1. element1<element2：这种情况下，我们记录下当前的差值current ，然后右移idx1（新的element1将更大），期待能获得更小的差值

2. element1>element2：这种情况下，我们记录下当前的差值current，然后右移idx2（新的element2将更大），期待能获得更小的差值
3. element1=element2：在这种情况下，已经获得最小差值0，直接返回

最后，我们看看current是否比smallest小，如果是这样，那么更新smallest为current。并更新当前的一对数为我们希望返回的smallestPair。

重复上述过程直到一方数组被完全遍历。

![image-20220326153338639](/assets/algo-exp-note.assets/image-20220326153338639.png)

```java
import java.util.*;
//O(Nlog(N) + O(Mlog(M))) time O(1) space

import java.util.*;

class Program {
    public static int[] smallestDifference(int[] arrayOne, int[] arrayTwo) {
        Arrays.sort(arrayOne);
        Arrays.sort(arrayTwo);

        int i = 0;
        int j = 0;

        int smallestDifference = Integer.MAX_VALUE;
        int[] pair = new int[] {};


        while( i < arrayOne.length && j < arrayTwo.length) {
            int firstNum = arrayOne[i];
            int secondNum = arrayTwo[j];
            int currentDifference = Integer.MAX_VALUE;

            if (firstNum == secondNum)
                return new int[] {firstNum, secondNum};

            else if (firstNum < secondNum) {
                currentDifference = secondNum - firstNum;
                i++;
            }

            else if (firstNum > secondNum) {
                currentDifference = firstNum - secondNum;
                j++;
            }

            if (currentDifference < smallestDifference) {
                smallestDifference = currentDifference;
                pair = new int[] {firstNum, secondNum};
            }
        }

        return pair;
    }
}
```

## 5.Move Element To End(Blue)

![image-20210225130308577](/assets/algo-exp-note.assets/image-20210225130308577.png)

由于本题不需要在意顺序的问题，所以我们直接用两个指针left，right指向第一个和最后一个元素。当left遇到目标数字时，就将它与right所指的元素互换即可。

如下面的代码所示：

然而这个代码忽略了边界的问题，例如：

2 1 2 2 2 3 4 2     要移动的数字为2

那么第一个2就会和最后一个2互换，这样就并没有把所有的2都移到最后边

```java
public static List<Integer> moveElementToEnd(List<Integer> array, int toMove) {//错误
    // Write your code here.
    int left = 0;
    int right = array.size() - 1;

    while (left < right) {
        if (array.get(left) == toMove) {
            swap(left, right, array);
            right--;
        }
        left++;
    }
    return array;
}
```

为了解决上述所说的问题， 我们应该先将right向前移动到一个其所指的位置不是目标数字的地方，然后再做互换。这样就解决了上述可能发生的情况。

```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static List<Integer> moveElementToEnd(List<Integer> array, int toMove) {
        // Write your code here.
        int left = 0;
        int right = array.size() - 1;

        while (left < right) {
            while(left < right && array.get(right) == toMove)
                right--;
            if (array.get(left) == toMove) {
                swap(left, right, array);
                right--;
            }
            left++;
        }
        return array;
    }

    public static void swap(int left, int right, List<Integer> array) {
        int temp = array.get(right);
        array.set(right, array.get(left));
        array.set(left, temp);
    }
}
```

## 6.Monotonic Array(Blue)

![image-20210225204050174](/assets/algo-exp-note.assets/image-20210225204050174.png)

方法一（最清晰）：

monotonic的意思是这个数组要么完全是增长的（可以出现相等的数），要么完全是下降的（可以出现相等的数）所以，如果我们在遍历数组的时候发现它运动的方向和之前出现了变化，那么，这个数组就不是monotonic的。

我们可以先假设这个数组不仅是增长的，而且是下降的。之后在遍历的过程中，如果后一个数小于前一个数，那么增长（isNonDecreasing）就是不满足的了；如果后一个数大于前一个数，那么下降（isNonIncreasing）就是不满足的了；

这样，我们最后就可以知道这个数组是否是monotonic的了。

```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static boolean isMonotonic(int[] array) {
        // Write your code here.
        boolean isNonDecreasing = true;
        boolean isNonIncreasing = true;

        for (int i = 1; i < array.length; i++) {
            if (array[i] < array[i - 1])
                isNonDecreasing = false;
            if (array[i] > array[i - 1])
                isNonIncreasing = false;
        }

        return isNonDecreasing || isNonIncreasing;
    }
}
```

方法二：



<img src="/assets/algo-exp-note.assets/image-20210226111029061.png" alt="image-20210226111029061" style="zoom:67%;" />

## 7.Spiral Traverse（Blue*）

![image-20210226111240868](/assets/algo-exp-note.assets/image-20210226111240868.png)

设定start row，end row，start column，end column

![image-20220327183840922](/assets/algo-exp-note.assets/image-20220327183840922.png)

使用设定的四个值遍历整个矩阵的外围（1-4，5-7，8-10，11-12），之后更新四个值，让它们进入到矩阵的内部，重复之前的遍历动作（13-14，15，16）。

方法一：iterative

需要注意边界条件！！！

```java
import java.util.*;
//O(N) time O(N) space
class Program {
    public static List<Integer> spiralTraverse(int[][] array) {
        // Write your code here.
        if (array.length == 0)
            return new ArrayList<Integer>();
        List<Integer> result = new ArrayList<Integer>();
        int startRow = 0;
        int startColumn = 0;
        int endRow = array.length - 1;
        int endColumn = array[0].length - 1;

        while (startColumn <= endColumn && startRow <= endRow) {
            for (int col = startColumn; col <= endColumn; col++)
                result.add(array[startRow][col]);

            for (int row = startRow + 1; row <= endRow; row++)
                result.add(array[row][endColumn]);

            for (int col = endColumn - 1; col >= startColumn; col--) {
                //边界条件
                if (startRow == endRow)
                    break;
                result.add(array[endRow][col]);
            }

            for (int row = endRow - 1; row > startRow; row--) {
                //边界条件
                if (startColumn == endColumn)
                    break;
                result.add(array[row][startColumn]);
            }
            startRow++;
            startColumn++;
            endRow--;
            endColumn--;
        }

        return result;
    }
}
```

在最后的两个for循环中，我们加入了2个边界条件的判断，防止重复计数。例子如下所示：

<img src="/assets/algo-exp-note.assets/image-20210226124235320.png" alt="image-20210226124235320" style="zoom:50%;" />

在第一个例子中，遍历完边界之后，剩下的部分为[11,12];在第二个例子中，遍历完边界之后，剩下的部分为T([13,14,15])。如果不加入上述的2个条件判断，我们将会重复加入数字。

方法二（recursive）：和上述思路基本一致，只是用recursive代替了while循环。

```java
import java.util.*;
//O(N) time O(N) space
class Program {
    public static List<Integer> spiralTraverse(int[][] array) {
        List<Integer> result = new ArrayList<>();
        spiralFill(array, result, 0, 0, array[0].length - 1, array.length - 1);
        return result;
    }

    public static void spiralFill(
            int[][] array,
            List<Integer> result,
            int startCol,
            int startRow,
            int endCol,
            int endRow) {
        if (startRow > endRow || startCol > endCol)
            return;
        for (int col = startCol; col <= endCol; col++)
            result.add(array[startRow][col]);

        for (int row = startRow + 1; row <= endRow; row++)
            result.add(array[row][endCol]);

        for (int col = endCol - 1; col >= startCol; col--) {
            if (startRow == endRow)
                break;
            result.add(array[endRow][col]);
        }


        for (int row = endRow - 1; row > startRow; row--) {
            if (startCol == endCol)
                break;
            result.add(array[row][startCol]);
        }


        spiralFill(array, result, startCol + 1, startRow + 1, endCol - 1, endRow - 1);
    }
}
```

## 8.Longest Peak(Blue)

![image-20210228094258706](/assets/algo-exp-note.assets/image-20210228094258706.png)

要解决本题，要分两步：

1. 找出数组中存在的山峰（tip）；
2. 从山峰开始向左右遍历，找出整个peak；

应用到代码中，我们可以把两步结合到一起。

因为peak最少需要三个数，那么当我们寻找山峰时，我们就从数组的第二个元素找到倒数第二个元素即可。对应到数组中的index就是从1-->array.length-2.

当我们找到了一个山峰（tip）之后之后，我们就要分别向左右方向寻找以tip为山峰的peak有多长。tip临近的左右元素肯定是符合要求的。那么就从left = peak - 2和right = peak + 2开始找起。

最后可以计算出当前tip的peak的长度。

再寻找下一个tip时，我们只需从之前得出的rightIdx开始找就可以了，因为上一个tip到rightIdx-1经历的元素都是严格下降的。

```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static int longestPeak(int[] array) {
        // Write your code here.
        int longestPeak = 0;
        int i = 1;
        //find the tips of the peak(找到peak的峰值)
        while (i < array.length - 1) {
            boolean isPeak = array[i - 1] < array[i] && array[i] > array[i + 1];
            if (!isPeak) {
                i++;
                continue;
            }

            //找到tip可以延申到的最远的left和right
            int leftIdx = i - 2;
            while (leftIdx >= 0 && array[leftIdx] < array[leftIdx + 1])
                leftIdx--;

            int rightIdx = i + 2;
            while (rightIdx < array.length && array[rightIdx] < array[rightIdx - 1])
                rightIdx++;
            //更新最长的peak；
            int currentPeak = rightIdx - leftIdx - 1;
            longestPeak = Math.max(longestPeak, currentPeak);
            i = rightIdx;
        }
        return longestPeak;
    }
}
```

```java
import java.util.*;

class Program {
    public static int longestPeak(int[] array) {
        int longestPeakLength = 0;
        int i = 1;
        while (i <= array.length - 2) {
            if (array[i] > array[i - 1] && array[i] > array[i + 1]) {
                //a peak is found

                int left = i - 1;
                //find left border
                while (left > 0 && array[left] > array[left - 1])
                    left--;

                int right = i + 1;
                //find right border
                while (right < array.length - 1 && array[right] > array[right + 1])
                    right++;

                int currentPeakLength = right - left + 1;

                if (currentPeakLength > longestPeakLength)
                    longestPeakLength = currentPeakLength;
            }

            i++;
        }
        return longestPeakLength;
    }
}
```

## 9.Four Number Sum(Red)

![image-20210228110231518](/assets/algo-exp-note.assets/image-20210228110231518.png)

本题的基本思路是将数组中的数看成是一对一对的，并得出每一对的和，当发现有2个和相加等于targetSum时，那么每一对里的2个数合起来就是我们要找的答案之一。

我们可以遍历数组得出每一对的和然后把他们存到一个hash table（Map）中，然后通过差值寻找匹配的另一对。这样做会产生一个**问题：就是重复的计入符合条件的四元组**。所以，应该采用如下的算法：

遍历数组中的每一个元素，将他与其之后的所有元素相加（也就是使当前元素和其后的所有元素组成一对）。对于每一个[当前元素，其后的某个元素]，我们先计算出他们与`targetSum`的差值，然后在`allPairSums`中寻找是否有匹配的另一对。

`allPairSums`里存储的是当前元素之前的元素，所能组合出来的所有Pair。

这样，我们就解决了重复计入四元组的问题。因为，我们以每一个元素为基准，我们只会将该元素左侧符合条件的pair和该元素右侧的符合条件的pair组合，这样就避免了重复的计入。

<img src="/assets/algo-exp-note.assets/image-20220329150827851.png" alt="image-20220329150827851" style="zoom:50%;" />

`Worst O(N^3) time` 的情况源于array能组成的pair中，有很多组pair的和是相同的.

这样,第三层循环`for (Integer[] pair : allPairSums.get(difference))` 产生`O(N)` 的时间复杂度。

```java
import java.util.*;
// Average O(N^2) time O(N^2) space
// Worst O(N^3) time O(N^2) space
class Program {
    public static List<Integer[]> fourNumberSum(int[] array, int targetSum) {
        // Write your code here.
        Map<Integer, List<Integer[]>> allPairSums = new HashMap<>();
        List<Integer[]> quadruplets = new ArrayList<>();
        //i可以不从0开始，因为第一次的遍历什么都不会做（Map是空的、第一个元素前面也没有其他的元素）
        //i可以不到最后一个元素，因为最后一个元素后边没有了元素，无法组成pair
        for (int i = 1; i < array.length - 1; i++) {
            //寻找四元组
            for (int j = i + 1; j < array.length; j++) {
                int currentSum = array[i] + array[j];
                int difference = targetSum - currentSum;
                if (allPairSums.containsKey(difference)) {
                    for (Integer[] pair : allPairSums.get(difference)) {
                        Integer[] newQuadruplets = {pair[0], pair[1], array[i], array[j]};
                        quadruplets.add(newQuadruplets);
                    }
                }
            }
            //为Map添加内容
            for (int k = 0; k < i; k++) {
                int currentSum = array[i] + array[k];
                Integer[] pair = {array[k], array[i]};
                if (!allPairSums.containsKey(currentSum)) {
                    List<Integer[]> pairGroup = new ArrayList<>();
                    pairGroup.add(pair);
                    allPairSums.put(currentSum, pairGroup);
                } else {
                    allPairSums.get(currentSum).add(pair);
                }
            }
        }

        return quadruplets;
    }
}
```

## 10.Subarray Sort(Red*)

![image-20210228133814621](/assets/algo-exp-note.assets/image-20210228133814621.png)

本题的目标在于要找到一个subarray，**只要我们对这个subarray进行了排序，那么整个array就都是有序的了**。

本题的基本思路如下：

1. 遍历整个数组确定哪些数排序正确哪些排序不正确

   1   2  4  7  10  11  7  12  6  7  16  18  19

   √   √  √  √    √   √   ×     ×   ×  ×   √    √    √

2. 在排序不正确的数中找到最大值和最小值

3. 为最大值和最小值寻找他们排序正确后应该在的位置

   1   2  4   （6） **|7  10  11  7  12  6  7   |**（12） 16  18  19

4. 从而，黑体部分就是我们找到的需要的sub array

**subarray的范围取决于无顺序数中的最大值和最小值。**

<img src="/assets/algo-exp-note.assets/image-20220329170010513.png" alt="image-20220329170010513" style="zoom:50%;" />

```java
import java.util.*;
//O(N) time O(1) space
class Program {
    public static int[] subarraySort(int[] array) {
        // Write your code here.
        int minOutOfOrder = Integer.MAX_VALUE;
        int maxOutOfOrder = Integer.MIN_VALUE;

        for (int i = 0; i < array.length; i++) {
            int num = array[i];
            if (isOutOfOrder(i, num, array)) {
                minOutOfOrder = Math.min(minOutOfOrder, num);
                maxOutOfOrder = Math.max(maxOutOfOrder, num);
            }
        }

        //如果max和min没有被找到，那么就意味着array已经是sort好了的
        if (minOutOfOrder == Integer.MAX_VALUE)
            return new int[]{-1, -1};

        int subarrayLeftIdx = 0;
        while (minOutOfOrder >= array[subarrayLeftIdx])
            subarrayLeftIdx++;

        int subarrayRightIdx = array.length - 1;
        while (maxOutOfOrder <= array[subarrayRightIdx])
            subarrayRightIdx--;

        return new int[]{subarrayLeftIdx, subarrayRightIdx};
    }

    public static boolean isOutOfOrder(int i, int num, int[] array) {
        if (i == 0)
            return num > array[i + 1];
        if (i == array.length - 1)
            return num < array[i - 1];
        return array[i - 1] > num || array[i + 1] < num;
    }
}
```

## 11.Largest Range(Red)

![image-20210228192927039](/assets/algo-exp-note.assets/image-20210228192927039.png)

本题的目标是在给定数组中找到最长的连续元素的长度。

在上述例子中，我们可以看到array有两组range：1）0 1 2 3 4 5 6 7；2）10 11 12 。显然[0, 7]是最长的。

所以，最简单的办法就是先给array排序，然后找到最长的range即可。当然，这种情况的时间复杂度为O(nlogn)。

下面展示的是时间复杂度为O(n)的算法：

1. 遍历数组，对于每一个数都将他们存入HashTable（Map）中，形式为<num, true>
2. 第二次遍历数组，如果num对应的布尔值为false那么跳过这个num；否则，我们将根据num向他的左右方向拓展，寻找Map中是否有....num -2 , num - 1, num + 1, num + 2....。对于找到的数字都将他们在Map中的值设为false（避免重复寻找range）。
3. 更新最长range，最后返回最大值即可。

<img src="/assets/algo-exp-note.assets/image-20220329181936564.png" alt="image-20220329181936564" style="zoom:50%;" />

```java
import java.util.*;
//O(N) time O(N) space
class Program {
    public static int[] largestRange(int[] array) {
        // Write your code here.
        int[] bestRange = new int[2];
        int longestRange = 0;
        Map<Integer, Boolean> nums = new HashMap<>();
        //将所有的数放入Map中
        for (int num : array) {
            nums.put(num, true);
        }
        //寻找最长range
        for (int num : array) {
            //如果该数已经被包含在之前找到的range之中，跳过
            if (!nums.get(num)) {
                continue;
            }
            //对于true的数字向左右开始寻找临近的num
            nums.put(num, false);
            int currentLength = 1;
            int left = num - 1;
            int right = num + 1;
            while (nums.containsKey(left)) {
                nums.put(left, false);
                left--;
                currentLength++;
            }

            while (nums.containsKey(right)) {
                nums.put(right, false);
                right++;
                currentLength++;
            }
            //更新最大值，和最长的range
            if (currentLength > longestRange) {
                longestRange = currentLength;
                bestRange = new int[]{left + 1, right - 1};
            }
        }
        return bestRange;
    }
}
```

## 12.Min Rewards（Red*）

![image-20210301111726339](/assets/algo-exp-note.assets/image-20210301111726339.png)

![image-20210301111757023](/assets/algo-exp-note.assets/image-20210301111757023.png)

本题有三种解法：

<img src="/assets/algo-exp-note.assets/image-20220329193213023.png" alt="image-20220329193213023" style="zoom:50%;" />

- 遍历数组中的每一个数（从第二个开始），如果当前的score比前一个大，那么就让当前score的reward比前一个多一；否则，更新之前的所有reward值。

  `O(N^2) time O(N) space`

![image-20210301124830791](/assets/algo-exp-note.assets/image-20210301124830791.png)

- 方法二：`O(N) time  O(N) space`

[8,4,2,1,3,6,7,9,5]

![image-20220331165925235](/assets/algo-exp-note.assets/image-20220331165925235.png)

1. 遍历数组，找出整个数组中的极小值。在本例中，极小值为1，5
2. 极小值所处的地方所获得的奖励一定是1，对于每一个极小值让他向左右分别扩张，更新其他点的reward值。
3. 完成后计算reward的和即可得出答案。

![image-20210301130814908](/assets/algo-exp-note.assets/image-20210301130814908.png)

- 方法三：

有方法二可以看出，本题本质上来说就是从极小值的地方向左右一次增加reward的值，所以我们只需对array进行从左到右和从右到左的两次遍历即可。

1. 先将所有的score对应的reward设为1
2. 先从左到右遍历（相当于让每个极小值完成向右更新reward），如果array[i-1] > array[i]（如例子中的8，4，2，1）,那么就什么都不做；如果array[i-1] < array[i]（如：1，3），这时开始为array[i]的reward++。
3. 然后从右到左遍历（相当于让每个极小值完成向左更新reward）

<img src="/assets/algo-exp-note.assets/image-20220331170741066.png" alt="image-20220331170741066" style="zoom:50%;" />

```java
import java.util.*;
import java.util.stream.IntStream;
//O(N) time O(N) space
class Program {
    public static int minRewards(int[] scores) {
        // Write your code here.
        int[] rewards = new int[scores.length];
        Arrays.fill(rewards, 1);

        //从左向右遍历
        for (int i = 1; i < scores.length; i++) {
            if (scores[i] > scores[i - 1])
                rewards[i] = rewards[i - 1] + 1;
        }

        //从右向左遍历
        for (int i = scores.length - 2; i >= 0; i--) {
            if (scores[i] > scores[i + 1]) {
                rewards[i] = Math.max(rewards[i], rewards[i + 1] + 1);
            }
        }

        //计算reward的和
        return IntStream.of(rewards).sum();
    }
}
```

## 13.ZigZag Traverse(Red*)

![image-20220331193002642](/assets/algo-exp-note.assets/image-20220331193002642.png)

由上图可知，整个路线拥有两种状态：上升和下降；整个路线拥有两种形式：对角线和横竖线。对角线路线的实现十分简单，难点在于控制横竖线的走向。

一开始，路线的走向肯定是下降的。观察处前一步在下降状态下的横竖线（1-2，6-7，13-14）我们可以发现，他们都处于左边界和下边界。经过他们之后，路线的状态就会由下降转为上升。所以，当遇到1，6，13这三个在左边界和下边界的点时，我们需要调整路线的方向。然后根据它们所在的边界向下或向右走横竖线。

同样，观察前一步处在上升状态下的横竖线（3-4，10-11，15-16），他们都处于右边界和上边界。经过它们之后，路线会由上升转为下降。所以，当遇3，10，15这三个在右边界和上边界的点时，我们需要调整路线的方向。然后根据它们所在的边界向下或向右走横竖线。

```java
import java.util.ArrayList;
import java.util.List;
//O(N) time O(N) space
class Program {
    public static List<Integer> zigzagTraverse(List<List<Integer>> array) {
        // Write your code here.
        int height = array.size() - 1;
        int width = array.get(0).size() - 1;
        List<Integer> result = new ArrayList<>();
        int row = 0;
        int col = 0;
        boolean goingDown = true;

        while (!isOutOfBound(row, col, height, width)) {
            //将当前数字加入result中
            result.add(array.get(row).get(col));
            //在前一步状态为下降的情况下
            if (goingDown) {
                //如果当前点在左下边界
                //走横竖线，并改变路线方向
                //否则按照原来路线行进
                if (col == 0 || row == height) {
                    goingDown = false;
                    if (row == height)
                        col++;
                    else
                        row++;
                } else {
                    row++;
                    col--;
                }
            } else {//在前一步状态为上升的情况下
                //如果当前点在右上边界
                //走横竖线，并改变路线方向
                //否则按照原来路线行进
                if (row == 0 || col == width) {
                    goingDown = true;
                    if (col == width)
                        row++;
                    else
                        col++;
                } else {
                    row--;
                    col++;
                }
            }
        }
        return result;
    }

    public static boolean isOutOfBound(int row, int col, int height, int width) {
        return row < 0 || row > height || col < 0 || col > width;
    }
}
```

## 14.Array Of Products(Blue)

![image-20210301205535246](/assets/algo-exp-note.assets/image-20210301205535246.png)

- 蛮力法（不作赘述）
- 方法二：

如下图所示，对于每一个位置上的product，我们可以把它拆分成 ：

该位置左边的所有数字的product * 该位置右边的所有数字的product

这样，我们只需正向、反向遍历一遍数组就可以知道对应于每一个位置的左边的所有数字的product 和该位置右边的所有数字的product。

最后将它们相乘就可以得到最终的结果。

<img src="/assets/algo-exp-note.assets/image-20210301211022284.png" alt="image-20210301211022284" style="zoom: 33%;" />

```java
import java.util.*;
//O(N) time O(N) space
class Program {
    public int[] arrayOfProducts(int[] array) {
        // Write your code here.
        int[] products = new int[array.length];
        int[] leftProducts = new int[array.length];
        int[] rightProducts = new int[array.length];

        int leftRunningProduct = 1;
        for (int i = 0; i < array.length; i++) {
            leftProducts[i] = leftRunningProduct;
            leftRunningProduct *= array[i];
        }

        int rightRunningProdut = 1;
        for (int i = array.length - 1; i >= 0; i--) {
            rightProducts[i] = rightRunningProdut;
            rightRunningProdut *= array[i];
        }

        for (int i = 0; i < array.length; i++) {
            products[i] = leftProducts[i] * rightProducts[i];
        }

        return products;
    }
}
```

- 方法三建立在方法二之上，减少了空间的使用。

```java
import java.util.*;
// O(N) time O(N) space
class Program {
    public int[] arrayOfProducts(int[] array) {
        int[] products = new int[array.length];

        int leftRunningProduct = 1;
        for (int i = 0; i < array.length; i++) {
            products[i] = leftRunningProduct;
            leftRunningProduct *= array[i];
        }

        int rightRunningProduct = 1;
        for (int i = array.length -1; i >= 0; i--) {
            products[i] *= rightRunningProduct;
            rightRunningProduct *= array[i];
        }
        return products;
    }
}
```

## 15.First Duplicate Value(Blue)

![image-20210302103250176](/assets/algo-exp-note.assets/image-20210302103250176.png)

![image-20210302103723282](/assets/algo-exp-note.assets/image-20210302103723282.png)

**`1<=array[i]<=N(array的长度)`**

- 蛮力法：两两对比，大小一样记录当前的位置，最后返回最小的位置。
- 方法二：用一个`hashset`保存没有见过的数字，当遇到一个数字它已经在set中时，返回这个数即可。

<img src="/assets/algo-exp-note.assets/image-20210302110452355.png" alt="image-20210302110452355" style="zoom: 33%;" />

```java
import java.util.*;
//O(N) time O(N) space
class Program {

    public int firstDuplicateValue(int[] array) {
        // Write your code here.
        Set<Integer> seen = new HashSet<>();
        for (int num : array) {
            if (seen.contains(num))
                return num;
            seen.add(num);
        }
        return -1;
    }
}
```

- 方法三：这个解法利用了条件：1<=array[i]<=N(array的长度)

  本题的原理十分简单：当遇到一个新的数num时，就把abs（num）-1位置上的数变为负数给这个num作为一种标记（证明已经经历过了num）；当遇到下一个num时，num经过同样的计算也会到达之前的标记位置，当他发现标记位的值已经为负数时，证明它是第二个出现的数。

  这种标记也只有在本题的条件下才能实现。

  eg;

  我们在遍历每一个数的时候，用当前的|array[i]| - 1可以得到一个index，然后我们把array[index]的值变为他的相反数。在下面的例子中，当遍历到array[4]的时候，index = 4而且array[4]本身是负数（证明之前已经见到过这个数），所以最终找到了这个结果。

<img src="/assets/algo-exp-note.assets/image-20210302112057114.png" alt="image-20210302112057114" style="zoom:33%;" />

```java
import java.util.*;
//O(n) time O(1) space
class Program {

    public int firstDuplicateValue(int[] array) {
        // Write your code here.
        for (int i = 0; i < array.length; i++) {
            int index = Math.abs(array[i]) - 1;
            if (array[index] < 0)
                return Math.abs(array[i]);
            array[index] *= -1;
        }
        return -1;
    }
}
```

## 16.Sorted Squared Array(Green)

![](/assets/algo-exp-note.assets/image-20220401163357471.png)

- 方法一：对array中的每一个值计算平方，然后重新排序即可。`O(Nlog(N) time) O(N) space`

- 方法二：我们用两个指针指向数组的左右两端，比较左右所指向的数字的绝对值大小，将大的一方的平方存入另一数组，并更新指针的位置。

  <img src="/assets/algo-exp-note.assets/image-20220401181840024.png" alt="image-20220401181840024" style="zoom:50%;" />

  ```java
  import java.util.*;
  //O(N) time O(N) space
  class Program {

      public int[] sortedSquaredArray(int[] array) {
          int left = 0;
          int right = array.length - 1;
          int[] sortedSquares = new int[array.length];
          int idx = array.length - 1;

          while (left <= right) {
              int leftValue = array[left];
              int rightValue = array[right];

              if (Math.abs(leftValue) < Math.abs(rightValue)) {
                  sortedSquares[idx] = rightValue * rightValue;
                  right--;
                  idx--;
              } else {
                  sortedSquares[idx] = leftValue * leftValue;
                  left++;
                  idx--;
              }
          }

          return sortedSquares;
      }
  }
  ```

  ![image-20220401184409884](/assets/algo-exp-note.assets/image-20220401184409884.png)

## 17.Merge Overlapping Intervals(Blue)

![image-20220401184531471](/assets/algo-exp-note.assets/image-20220401184531471.png)

1. 先给interval排序（按照interval[0]）
2. 遍历每一个interval，检查他们是否与已经在result list中的interval重叠，若overlap，那么更新result list中的interval；否则，将当前interval加入result list之中。

<img src="/assets/algo-exp-note.assets/image-20220401191028663.png" alt="image-20220401191028663" style="zoom:50%;" />

```java
import java.util.*;
//O(Nlog(N)) time O(N) space
class Program {
    public int[][] mergeOverlappingIntervals(int[][] intervals) {
        int[][] sortedIntervals = intervals.clone();
        Arrays.sort(sortedIntervals, Comparator.comparingInt(a -> a[0]));
        // add the first interval into result list
        List<int[]> mergedIntervals = new ArrayList<>();
        int[] currentInterval = sortedIntervals[0];
        mergedIntervals.add(currentInterval);

        for (int i = 1; i < sortedIntervals.length; i++) {
            int[] nextInterval = sortedIntervals[i];
            if (currentInterval[1] >= nextInterval[0]) {
                //Overlapped-->Update the higher border
                currentInterval[1] = Math.max(currentInterval[1], nextInterval[1]);
            } else {
                //not Overlapped
                mergedIntervals.add(nextInterval);
                currentInterval = nextInterval;
            }
        }
        return mergedIntervals.toArray(new int[mergedIntervals.size()][]);
    }
}
```

## 18.Tournament Winner(Green)

![image-20220401203317961](/assets/algo-exp-note.assets/image-20220401203317961.png)

![image-20220401203348069](/assets/algo-exp-note.assets/image-20220401203348069.png)

```java
import java.util.*;

class Program {
//O(N) time O(K) space
//N is the number of competitions
//K is the number of teams
    public String tournamentWinner(
            ArrayList<ArrayList<String>> competitions, ArrayList<Integer> results) {
        int HOME_TEAM_WON = 1;
        String winner = "";
        Map<String, Integer> scores = new HashMap<>();
        scores.put(winner, 0);

        for (int i = 0; i < competitions.size(); i++) {
            List<String> competition = competitions.get(i);
            String hostTeam = competition.get(0);
            String awayTeam = competition.get(1);
            int result = results.get(i);

            String winningTeam = result == HOME_TEAM_WON ? hostTeam : awayTeam;

            if(scores.containsKey(winningTeam))
                scores.put(winningTeam, scores.get(winningTeam) + 3);
            else
                scores.put(winningTeam, 3);

            if (scores.get(winner) < scores.get(winningTeam))
                winner = winningTeam;
        }

        return winner;
    }
}
```

## 19.Non-Constructible Change(Green)

![image-20220401210529007](/assets/algo-exp-note.assets/image-20220401210529007.png)

1. 对原数组排序

   | Coins                 | 1    | 1    | 2       | 3     | 5      | 7      | 22   |
   | --------------------- | ---- | ---- | ------- | ----- | ------ | ------ | ---- |
   | Change can be created | 1    | 1,2  | 1,2,3,4 | [1,7] | [1,12] | [1,19] |      |

2. 我们所要找的是第一个通过已经有的硬币无法组合出的零钱。从上表中可以看出，一开始我们只能组合出1，当我们又拿到一个硬币1的时候，我们可以组合出1，2；接着我们可以组合出1，2，3，4。此时我们拿到了硬币3，因为，我们所能创造创造出的change的值是连续的，**所以，只要`新的硬币的值<=当前能组合出的最大的change`, 那么，我们就可以继续连续的创造下去**。

3. 硬币22就违反了这个规则，22 > 19 + 1, 也就是说，22只能和1到19分别组合，组成新的零钱[23, 41]。但是，19和23之间的20，21，22没有办法被组合出来了。从而，20就是我们需要的答案。

   ```java
   import java.util.*;
   //O(N) time O(1) space
   class Program {

       public int nonConstructibleChange(int[] coins) {
           Arrays.sort(coins);

           int currentChangeCreated = 0;
           for (int coin : coins) {
               if (coin <= currentChangeCreated + 1)
                   currentChangeCreated += coin;
               else
                   return currentChangeCreated + 1;
           }

           return currentChangeCreated + 1;
       }
   }
   ```

# Dynamic Programming

动态规划五步

1. 确定`dp`数组以及下标的含义
2.  确定递推公式
3. `dp`数组如何初始化
4. 确定遍历顺序
5. 举例推导`dp`数组

## 1.Max Subset Sum No Adjacent(Blue)

![image-20210302122336672](/assets/algo-exp-note.assets/image-20210302122336672.png)

1. `dp[i]`用来保存在i位置上的数字可以和之前的数字相加得到的最大值
2. 见下图
3. **本题的难点在于初始化第0，1个`dp`中的内容**
4. 按顺序遍历即可

- 方法一：在array的基础上建立另一个数组`maxSums`用来存储每个数与其之前的数字按照规定所能得到的最大和。eg：7、10只能取到自身大小，12可以和7相加取得19......

![image-20210302123718318](/assets/algo-exp-note.assets/image-20210302123718318.png)

```java
import java.util.*;
// O(N) time O(N) space
class Program {
    public static int maxSubsetSumNoAdjacent(int[] array) {
        if (array.length == 0)
            return 0;
        else if (array.length == 1)
            return array[0];

        int[] maxSums = array.clone();
        // 初始化dp数组
        maxSums[1] = Math.max(array[0], array[1]);
        for (int i = 2; i < array.length; i++) {
            maxSums[i] = Math.max(maxSums[i - 1], maxSums[i - 2] + array[i]);
        }
        return maxSums[array.length - 1];
    }
}
```

不初始化`dp`无法通过的用例, 因为`dp[]`的定义是当前所能有的最大值，所以初始化时，我们需要保证`dp[1] >= dp[0]`.

> **Expected Output**
>
> ```json
> 207
> ```
>
> **Your Code's Output**
>
> ```json
> 206
> ```
>
> **Input(s)**
>
> ```json
> {
>   "array": [4, 3, 5, 200, 5, 3]
> ```

- 方法二：在方法一的基础上降低了空间复杂度。用first实时记录`maxSum[i-1]`,用second实时记录`maxSum[i-2]`;完成对当前节点最大值的更新后，当前节点current变为了first，之前的first变成了second，这样就可以为下一个元素能取得的最大值进行计算。

![image-20210302124349877](/assets/algo-exp-note.assets/image-20210302124349877.png)

<img src="/assets/algo-exp-note.assets/image-20210302130114198.png" alt="image-20210302130114198"  />

## 2.Number Of Ways To Make Change(Blue)

![image-20210302130318469](/assets/algo-exp-note.assets/image-20210302130318469.png)



解题思路如下图所示：

我们可以先建立一个数组名叫ways，在本例中，这个数组的index从0到10。每一个ways[index]表示达到index所有的方法总数。例如，在开始状态，ways如下所示：

1 0 0 0 0 0 0 0 0 0 0

0 1 2 3 4 5 6 7 8 9 10

也就是说用零钱（denomination）0得到0有一种方法，得到1-10只有0种方法。

之后，我们将题目所给的[1,5,10,25]依次带入更新ways[index].具体的更新算法展示在下图中：

如果 零钱 <= 要达到的钱：

​			那么，ways[要达到的钱（加入了新的零钱）] =

ways[要达到的钱（未加入新的零钱）] + ways[要达到的钱 - 新加入的零钱值]

用一个例子来理解一下：

当只有零钱1的时候，5只有1×5一种方法表示，10只有1×10一种方法表示；

引入零钱5之后，5有了两种方式表示：1×5，5×1；那么对于10来说，10 = 5×1 + 1×5或5×1；

所以，10多出的方法数是由5的方法数来确定的（确定一个5元，看剩下缺的5元如何用现有的零钱拼凑）。

![image-20210302152005968](/assets/algo-exp-note.assets/image-20210302152005968.png)

```java
import java.util.*;

class Program {
    public static int numberOfWaysToMakeChange(int n, int[] denoms) {
        // Write your code here.
        int[] ways = new int[n + 1];
        ways[0] = 1;

        for (int denom : denoms) {
            for (int i = 1; i < n + 1; i++) {
                if (denom <= i)
                    ways[i] += ways[i - denom];
            }
        }
        return ways[n];
    }
}
```

```java
import java.util.*;

class Program {
    public static int numberOfWaysToMakeChange(int n, int[] denoms) {
        // dp[j]表示当钱数为j时，能用[0, i]组成j的方法数
        int[] dp = new int[n + 1];
        dp[0] = 1;
        // 完全背包问题，组合（denoms的顺序不影响）
        // 顺序遍历即可
        for (int i = 0; i < denoms.length; i++) {
            for (int j = denoms[i]; j <= n; j++) {
                dp[j] += dp[j - denoms[i]];
            }
        }
        return dp[n];
    }
}
```

## 3.Min Number Of Coins For Change(Blue)

![image-20210302204037093](/assets/algo-exp-note.assets/image-20210302204037093.png)

本题也需要建立一个数组，如下图所示。

橙色的部分代表硬币要达到的amount，蓝色的部分表示要达到对应amount所需要的最小硬币的数量。

与上题一样，我们将1，2，4依次带入新建立的数字nums中，求出不同amount在最少需要的零钱数量。下图展示了这种更新的过程。

用最后一步举例，当拥有三种零钱1，2，4后，在只有1，2时nums[6] = 3(3个2元)；有了4之后，用6减去4得到2，那么我们只需要1张4和1张2就可以得到6。2比3小，所以更新nums[6] = 2即可。

![image-20210302205834472](/assets/algo-exp-note.assets/image-20210302205834472.png)

本题在代码的实践中，需要将numOfCoins的内容设为max(除numOfCoins[0]以外)，这样在之后才能把更小的值赋给它；这会带来一些边界条件需要处理。如果numOfCoins[amount - denom] == Integer.MAX_VALUE，也就是说，对应的另一半零钱还没有找到合适的最小零钱数量。那么，我们应该先让它们保持原状。（eg：n = 7; denoms=[3,7]）

```java
import java.util.*;

class Program {
    public static int minNumberOfCoinsForChange(int n, int[] denoms) {
        // Write your code here.
        int[] numOfCoins = new int[n + 1];
        Arrays.fill(numOfCoins, Integer.MAX_VALUE);
        numOfCoins[0] = 0;
        int toCompare = 0;

        for (int denom : denoms) {
            for (int amount = 0; amount < numOfCoins.length; amount++) {
                if (denom <= amount) {
                    if (numOfCoins[amount - denom] == Integer.MAX_VALUE)
                        toCompare = numOfCoins[amount - denom];
                    else
                        toCompare = numOfCoins[amount - denom] + 1;
                    numOfCoins[amount] = Math.min(numOfCoins[amount], toCompare);
                }
            }
        }
        return numOfCoins[n] != Integer.MAX_VALUE ? numOfCoins[n] : -1;
    }
}
```

```java
import java.util.*;

class Program {
    public static int minNumberOfCoinsForChange(int n, int[] denoms) {
        // 完全背包问题， 组合
        // dp[j]是用denoms[0,i]组成j的所需最少硬币数
        // dp[i][j] = min(dp[i - 1][j], dp[j - denoms[i]] + 1)

        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;

        for (int i = 0; i < denoms.length; i++)
            for (int j = denoms[i]; j <= n; j++) {
                int currentNum =
                        dp[j - denoms[i]] == Integer.MAX_VALUE ?
                                Integer.MAX_VALUE :
                                dp[j - denoms[i]] + 1;
                    dp[j] = Math.min(dp[j], currentNum);
            }


        return dp[n] == Integer.MAX_VALUE ? -1 : dp[n];
    }
}
```

## 4.Levenshtein Distance

![image-20210302213733352](/assets/algo-exp-note.assets/image-20210302213733352.png)

本题需要建立一个二维数组E，如下图所示；例如：以b开头的这一行所有的数字（2，2，2，1，2），它们的含义是“ab”变成“ ”，“ y”，“ya ”，“yab ”,“yabd ”所需要的最小操作数。

这些数值的计算由下图的公式所示：

对于str1的字母与str2的字母相等的情况：参照以a开头的那一行的第三个1，它的值来自他的左上角。

否则：E[r] [c]的值就等于它左上、左、上的最小值 + 1。

这种公式的原理自行思考即可。

![image-20210303122009950](/assets/algo-exp-note.assets/image-20210303122009950.png)

```java
import java.util.*;

class Program {
    public static int levenshteinDistance(String str1, String str2) {
        // Write your code here.
        int[][] edits = new int[str2.length() + 1][str1.length() + 1];
        //初始化边界值
        /*
        ''a b c
     '' 0 1 2 3
     y  1
     a  2
     b  3
     d  4
         */
        for (int i = 0; i < str2.length() + 1; i++)
            edits[i][0] = i;
        for (int j = 0; j < str1.length() + 1; j++)
            edits[0][j] = j;
        //填补空白的部分
        for (int i = 1; i < str2.length() + 1; i++) {
            for (int j = 1; j < str1.length() + 1; j++) {
                if (str2.charAt(i - 1) == str1.charAt(j - 1))
                    edits[i][j] = edits[i - 1][j - 1];
                else
                    edits[i][j] = 1 + Math.min(edits[i][j - 1], Math.min(edits[i - 1][j], edits[i - 1][j - 1]));
            }
        }
        return edits[str2.length()][str1.length()];
    }

    public static void main(String[] args) {
        int edits = levenshteinDistance("abc", "yabd");
        System.out.println(edits);
    }
}
```

实际上，我们只需要使用2行存储空间就可以完成本题的计算，由下图可以看出。

![image-20210303124007940](/assets/algo-exp-note.assets/image-20210303124007940.png)

![image-20210303134219216](/assets/algo-exp-note.assets/image-20210303134219216.png)

## 5.Max Sum Increasing Subsequence(Red).........

![image-20210303134521314](/assets/algo-exp-note.assets/image-20210303134521314.png)

如下图所示，建立数组sums和sequences。

sums用来存储每个数与按要求能达到的最大和；seqences用来存储可以和当前数字相加的前一个数字的坐标。例如：对于15来说，他的index=4；seqences[4] =1，array[1]=12就是可以和15相加的前一个数字；对于12，seqences[1] = 0, array[0] = 8就是可以和12相加的前一个数字。

sums的更新公式写在了下边，也就是说，对于每一个数，向前找比他小的数。如果当前的数+sums(比他小的数.index)更大，那就更新。例如，对于array中的15，15之前的数字都要比他小，他们的sums分别为8,20,2,5。15和这些sums相加的和分别为23，35，17，20，从中我们可以找到最大的值35。

![image-20210303142819148](/assets/algo-exp-note.assets/image-20210303142819148.png)

```java
import java.util.*;

class Program {
    public static List<List<Integer>> maxSumIncreasingSubsequence(int[] array) {
        // Write your code here.
        int[] sums = array.clone();
        int[] sequences = new int[array.length];
        Arrays.fill(sequences, Integer.MIN_VALUE);
        int maxSumIdx = 0;
		//更新sums[i]
        for (int i = 0; i < array.length; i++) {
            int currentNum = array[i];
            for (int j = 0; j < i; j++) {
                int otherNum = array[j];
                if (otherNum < currentNum && sums[i] <= currentNum + sums[j]) {
                    sums[i] = currentNum + sums[j];
                    sequences[i] = j;
                }
            }

            if (sums[i] >= sums[maxSumIdx])
                maxSumIdx = i;
        }

        return buildSequence(array, sequences, sums[maxSumIdx], maxSumIdx);
    }

    public static List<List<Integer>> buildSequence(int[] array, int[] sequences, int sums, int maxSumIdx) {
        List<List<Integer>> sequence = new ArrayList<>();
        sequence.add(new ArrayList<>());//sum
        sequence.add(new ArrayList<>());//sequence
        sequence.get(0).add(sums);
        while (maxSumIdx != Integer.MIN_VALUE) {
            //把每一个数放到第一个
            sequence.get(1).add(0, array[maxSumIdx]);
            maxSumIdx = sequences[maxSumIdx];
        }
        return sequence;
    }
}
```

## 6.Longest Common Subsequence（难）

![image-20210304114342989](/assets/algo-exp-note.assets/image-20210304114342989.png)

建立一个二维数组，蓝色的部分存储对应阶段的lcs（longest common subsequence）。

当遇到左侧的字符等于右侧的字符时，将该字符append到其左上方的lcs之后；若两字符不等，那么就选取其左、上侧的最大值即可。这种算法的意思很好理解，如果发现了一样的字符，那么就要把它加到没遇到两个相同字符之前的lcs后；如果还没有遇到，那么就继续保存已经有了的最大lcs；

![image-20210304130824762](/assets/algo-exp-note.assets/image-20210304130824762.png)

![image-20210304132507254](/assets/algo-exp-note.assets/image-20210304132507254.png)

上述的方法显然需要耗费大量的时间和空间。

方法二：如下图所示，建立一个三维数组lcs，不同于之前在lcs[i] [j]中存字符串，这次我们在lcs[i] [j]中存4个元素[字符，当前最长字串的长度，前一位置的i坐标，前一位置的j坐标].

例如，当找到了两个相同的字符时，将这个字符存入，最长长度加1，左上角 i，左上角j。

![image-20210304134855898](/assets/algo-exp-note.assets/image-20210304134855898.png)

在最后还原最长子串时，只需从最后一个找起，按照i，j的坐标向前寻找，将所有的字符找出来即可。

![image-20210304134933422](/assets/algo-exp-note.assets/image-20210304134933422.png)

## 7.Min Number Of Jumps

![image-20210304140503159](/assets/algo-exp-note.assets/image-20210304140503159.png)

方法一：

我们建立一个数组jumps，记录到一个节点所需要的最小跳数。如，下图所示jumps被初始化为无限大（起始处被设定为0）。令i等于要跳去的地方，令j等于起跳的地方；若array[j]（起跳点所能跳的最大距离）+ 起跳点位置 >= 要跳去的位置，那么jumps[i] = min{jumps[i], jumps[j]+1};

![image-20210304185159327](/assets/algo-exp-note.assets/image-20210304185159327.png)

这里可以自行推算jumps，在推算过程中就可以理解这个过程。eg：3可以跳到2，1；4也可以跳到2，1；但是4跳到2，1根据公式计算需要2步，大于3能做到的1步，所以我们保留了1步。

![image-20210304191411762](/assets/algo-exp-note.assets/image-20210304191411762.png)

```java
import java.util.*;

class Program {
    public static int minNumberOfJumps(int[] array) {
        // Write your code here.
        int[] jumps = new int[array.length];
        Arrays.fill(jumps, Integer.MAX_VALUE);
        jumps[0] = 0;

        for (int i = 1; i < array.length; i++) {
            for (int j = 0; j < i; j++) {
                if (array[j] + j >= i)
                    jumps[i] = Math.min(jumps[i], jumps[j] + 1);
            }
        }

        return jumps[array.length - 1];
    }
}
```

方法二：

从方法一得到的jump表中，可以看出在坐标3，5，8处，jumps开始发生变化。

index    0,1,2,**3**,4,**5**,6,7,**8**,9,10

arrays=[3,4,2,**1**,2,**3**,7,1,**1**,1,3]

jumps=[0,1,1,**1**,2,**2**,3,3,**3**,4,4]

由此可以简化整个算法：maxReach只当前能达到的最远的坐标；steps指从当前index到maxReach需要的步数。

对于本题来说，初始的maxReach=3，也就是说在index1-3之间，只需要跳动一步；当steps==0时，证明再向前进只能多跳一步；在遍历数组的过程中，我们也在更新maxReach的值，当我们到达index=3的时候，新的maxReach变为了5，那么从index4-5就需要2步，因为要到达4，5必须经过1，2，3中的一个。依次类推，我们就可以得到最后的结果。

![image-20210307132957283](/assets/algo-exp-note.assets/image-20210307132957283.png)

```java
class Program1 {
    public static int minNumberOfJumps(int[] array) {
        // Write your code here.
        if (array.length == 1)
            return 0;

        int maxReach = array[0];
        int steps = array[0];
        int jumps = 0;

        //这里是 1->length-2;注意边界的情况！！！
        //eg：[1,1]
        //如果最后一跳刚好跳到最后一个数，那么jumps就会+1
        //但return的时候又会+1，这样多算了一遍
        for (int i = 1; i < array.length - 1; i++) {
            maxReach = Math.max(maxReach, array[i] + i);
            steps--;
            if (steps == 0) {//到达了不得不再跳一步的时候
                jumps++;
                steps = maxReach - i;
            }
        }
		//如果还没有到naxReach就结尾了，那么进不到jumps++语句中去
        //所以在最后补加一
        return jumps + 1;/
    }
}
```

## 8.Water Area（*）

![image-20210307141820035](/assets/algo-exp-note.assets/image-20210307141820035.png)

非0的数字代表pillar（柱子的高度），结果是要计算这样的一个水域可以容纳多少水量。

<img src="/assets/algo-exp-note.assets/image-20210307153515723.png" alt="image-20210307153515723" style="zoom: 33%;" />

方法一：

左侧pillar的最大高度（从当前点开始算起）：蓝色数组

右侧pillar的最大高度（从当前点开始算起）：绿色数组

当前节点可以拥有的水量：白色数组w

显然一个点所能具有的水位高度由其左右最高的两个柱子中高度较小的一个决定；从而我们可以确定每一个点的最高水位线minHeight = min（leftMax，rightMax）。如果当前的柱子高度<minHeight,证明当前节点可以储存水，那么储水量就等于minHeight - height。最后可以得到每个节点储水量的数组w，得到结果。

![image-20210307193440131](/assets/algo-exp-note.assets/image-20210307193440131.png)

```java
import java.util.*;

class Program {
    public static int waterArea(int[] heights) {
        // Write your code here.
        int[] maxes = new int[heights.length];

        int leftMax = 0;
        for (int i = 0; i < heights.length; i++) {
            int height = heights[i];
            maxes[i] = leftMax;
            leftMax = Math.max(leftMax, height);
        }
        //这里把三个数组的逻辑结合到了一起，减小space的使用
        int rightMax = 0;
        for (int i = heights.length - 1; i >= 0; i--) {
            int height = heights[i];
            int minHeight = Math.min(maxes[i], rightMax);
            if (minHeight > height)
                maxes[i] = minHeight - height;
            else
                maxes[i] = 0;
            rightMax = Math.max(rightMax, height);
        }

        int total = 0;
        for (int max : maxes) total += max;

        return total;
    }
}
```

方法二：

我们可以把space complexity降到O(1)。我们需要5个参数：leftIdx，rightIdx，leftMax，rightMax，surfaceArea。从本质上讲，如果左右两个柱子的高度有差异，那么在他们之间就可以存储水，而且水的高度取决于高度较低的一方。我们让leftIdx和rightIdx不断向内深入，填补内部的水，并及时更新leftMax和rightMax控制之后的水位变化即可解决这一问题。

```java
import java.util.*;

class Program1 {
    public static int waterArea(int[] heights) {
        // Write your code here.
        if (heights.length == 0)
            return 0;

        int leftIdx = 0;
        int rightIdx = heights.length - 1;
        int leftMax = heights[leftIdx];
        int rightMax = heights[rightIdx];
        int waterArea = 0;

        while (leftIdx < rightIdx) {
            if (heights[leftIdx] < heights[rightIdx]) {
                //可以向右填补，进入left所指的右方区域
                leftIdx++;
                //更新左侧最大值
                leftMax = Math.max(leftMax, heights[leftIdx]);
                waterArea += leftMax - heights[leftIdx];
            } else {//可以向左进行填补
                rightIdx--;
                rightMax = Math.max(rightMax, heights[rightIdx]);
                waterArea += rightMax - heights[rightIdx];
            }
        }
        return waterArea;
    }
}
```

## 9.Knapsack Problem

![image-20210307203639710](/assets/algo-exp-note.assets/image-20210307203639710.png)

![image-20210307203702731](/assets/algo-exp-note.assets/image-20210307203702731.png)

建立一个二维数组values，绿色代表value，白色代表weight。

通过填写二维数组内容的过程中，我们可以发现如下的规律：

当w <= j时；

values[i] [j] = max(values[i - 1] [j], values[i-1] [j - w] + v)

拿图中二维数组中的5举个例子；在放入[4,3]之前，maxValue是1，若要加入[4.3], 那么，目前总的capacity减去新放入的item后还剩5-3 = 2的空余空间。在只有[1,2]时，capacity = 2的对应maxValue = 1；所以，加入新的item后，maxValue就变成了1 + 4 =5；

当w > j时：

values[i] [j] = values[i - 1] [j]

![image-20210307205927712](/assets/algo-exp-note.assets/image-20210307205927712.png)

![image-20210307220019668](/assets/algo-exp-note.assets/image-20210307220019668.png)

```java
import java.util.*;

class Program {
    public static List<List<Integer>> knapsackProblem(int[][] items, int capacity) {
        // Write your code here.
        // 建立动态规划二维表
        int[][] knapsackValues = new int[items.length + 1][capacity + 1];
        for (int i = 1; i < items.length + 1; i++) {
            int currentValue = items[i - 1][0];
            int currentWeight = items[i - 1][1];
            for (int c = 0; c < capacity + 1; c++) {
                if (currentWeight > c)
                    knapsackValues[i][c] = knapsackValues[i - 1][c];
                else
                    knapsackValues[i][c] =
                            Math.max(
                                    knapsackValues[i - 1][c],
                                    knapsackValues[i - 1][c - currentWeight] + currentValue);
            }
        }
        return getKnapsackItems(knapsackValues, items, knapsackValues[items.length][capacity]);
    }
//找到装入背包中的物品
    public static List<List<Integer>> getKnapsackItems(
            int[][] knapsackValues, int[][] items, int weight) {
        List<List<Integer>> sequence = new ArrayList<>();
        List<Integer> totalWeight = new ArrayList<>();
        totalWeight.add(weight);
        sequence.add(totalWeight);
        sequence.add(new ArrayList<>());
//确定背包的最大价值所处的位置
        int i = knapsackValues.length - 1;
        int c = knapsackValues[0].length - 1;

        while (i > 0) {
            //如果和上方的数一样，证明没有装入当前的item
            if (knapsackValues[i][c] == knapsackValues[i - 1][c])
                i--;
            else {//当前物品装入了背包
                sequence.get(1).add(0, i - 1);
                c -= items[i - 1][1];//更新取出该物品后剩余的空间
                i--;
            }

            if (c == 0)
                break;
        }

        return sequence;
    }
}
```

## 10.Disk Stacking

![image-20210307220215666](/assets/algo-exp-note.assets/image-20210307220215666.png)

![image-20210307220242009](/assets/algo-exp-note.assets/image-20210307220242009.png)

本题的思路如下图所示：首先我们根据disk的height对array进行升序的排序，这样会方便我们之后的计算。建立一个新的数组heights，这个数组将存储不同状态下disk能堆出的最大高度。在初始阶段，我们假定每个状态的最大height等于只放入当前对应的disk所能获得的高度（见下图）。

之后，我们开始对heights数组进行更新，对于一个currentDisk，他需要向前找能堆在它上方的otherDisk；

如果otherDisk的三维都小于currentDisk的三维，heights[otherDiskIdx] + heights[currentDiskIdx] > heights[currentDiskIdx] ,那么，heights[currentDiskIdx] = heights[otherDiskIdx] + heights[currentDiskIdx] （eg：橙色的3会变成5）

![image-20210308161429717](/assets/algo-exp-note.assets/image-20210308161429717.png)

那么，我们如何找到我们所用到的所有disk呢？方法就是我们再建立一个sequence数组，sequence中存的是在当前disk上堆放的disk的index，这样我们就可以从尾部到头部找到所有的disk。

Java语法介绍：

```java
disks.sort((disk1, disk2) -> disk1[2].compareTo(disk2[2]));
//other form
disks.sort(Comparator.comparing(disk -> disk[2]));
```

对于数组disks，其中的每一个disk的形式为[w,d,h],我们要按照h的大小对所有的disk进行排序。所以我们要定义自己的Comparable方法：如上图所示，对于disk1，disk2，我们用他们的height为标准进行排序。

```java
import java.util.*;

class Program {
    public static List<Integer[]> diskStacking(List<Integer[]> disks) {
        // 以height为标准为disk排序
        disks.sort(Comparator.comparing(disk -> disk[2]));
        //初始化heights
        int[] heights = new int[disks.size()];
        for (int i = 0; i < heights.length; i++) {
            heights[i] = disks.get(i)[2];
        }
        //初始化sequence（用于找到结果需要的disk）
        int[] sequences = new int[disks.size()];
        Arrays.fill(sequences, Integer.MIN_VALUE);

        int maxHeightIdx = 0;
        for (int i = 1; i < disks.size(); i++) {
            Integer[] currentDisk = disks.get(i);
            for (int j = 0; j < i; j++) {
                Integer[] otherDisk = disks.get(j);
                if (areValidDimensions(otherDisk,currentDisk)){
                    if(heights[i] < currentDisk[2] + heights[j]){
                        heights[i] = currentDisk[2] + heights[j];
                        sequences[i] = j;
                    }
                }
            }
            if(heights[i] >= heights[maxHeightIdx])
                maxHeightIdx = i;
        }

        return buildSequence(sequences, disks, maxHeightIdx);
    }

    public static boolean areValidDimensions(Integer[] otherDisk, Integer[] currentDisk) {
        return otherDisk[0] < currentDisk[0]
                && otherDisk[1] < currentDisk[1]
                && otherDisk[2] < currentDisk[2];
    }

    public static List<Integer[]> buildSequence(
            int[] sequences, List<Integer[]> disks, int maxHeightIdx){
        List<Integer[]> result = new ArrayList<>();
        while(maxHeightIdx != Integer.MIN_VALUE){
            result.add(0,disks.get(maxHeightIdx));
            maxHeightIdx = sequences[maxHeightIdx];
        }
        return result;
    }
}
```

## 11.Numbers in Pi（难）

![image-20210308171913826](/assets/algo-exp-note.assets/image-20210308171913826.png)

![image-20210308172005604](/assets/algo-exp-note.assets/image-20210308172005604.png)

本题简单的来说，就是用numbers里的数怎么用最小的“|”的分割pi。

如下图的例子所示：

<img src="/assets/algo-exp-note.assets/image-20210409155306269.png" alt="image-20210409155306269" style="zoom:33%;" />

本题的算法将遍历整个pi；若发现当前所指的index之前的数在numbers中，那么就将index所指的地方和之前的数视为prefix（图中的31），将它之后的部分视作suffix（图中的41592）；然后对suffix中的String进行找最小分割的过程。

<img src="/assets/algo-exp-note.assets/image-20210409160235166.png" alt="image-20210409160235166" style="zoom:33%;" />

在该过程中，我们将保存每一个suffix得到的最小分割值。例如：2的最小分隔值为0；41592为1；3141592为2；这样可以减少后续多余的运算。

```java
import java.util.*;

class Program {
    public static int numbersInPi(String pi, String[] numbers) {
        Set<String> numberTable = new HashSet<>();
        for (String number : numbers) {
            numberTable.add(number);
        }
        Map<Integer, Integer> cache = new HashMap<>();
        int minSpaces = getMinSpaces(pi, numberTable, cache, 0);
        return minSpaces == Integer.MAX_VALUE ? -1 : minSpaces;
    }

    public static int getMinSpaces(String pi, Set<String> numberTable, Map<Integer, Integer> cache, int idx) {
        if (idx == pi.length())
            return -1;

        if (cache.containsKey(idx))
            return cache.get(idx);

        int minSpaces = Integer.MAX_VALUE;

        for (int i = idx; i < pi.length(); i++) {
            String prefix = pi.substring(idx, i + 1);
            if (numberTable.contains(prefix)) {
                int minSpacesInSuffix = getMinSpaces(pi, numberTable, cache, i + 1);
                if (minSpacesInSuffix == Integer.MAX_VALUE)
                    minSpaces = Math.min(minSpaces, minSpacesInSuffix);
                else
                    minSpaces = Math.min(minSpaces, minSpacesInSuffix + 1);
            }
        }
        cache.put(idx, minSpaces);
        return cache.get(idx);
    }
}
```

## 12.Max Profit With K Transactions

> You're given an array of positive integers representing the prices of a single stock on various days (each index in the array represents a different day). You're also given an integer k, which represents the number of transactions you're allowed to make. One transaction consists of buying the stock on a given day and selling it on another, later day.
>
> Write a function that returns the maximum profit that you can make by buying and selling the stock, given k transactions.
>
> Note that you can only hold one share of the stock at a time; in other words, you can't buy more than one share of the stock on any given day, and you can't buy a share of the stock if you're still holding another share. Also, you don't need to use all k transactions that you're allowed.
>
> ### Sample Input
>
> ```
> prices = [5, 11, 3, 50, 60, 90]
> k = 2
> ```
>
> ### Sample Output
>
> ```
> 93 // Buy: 5, Sell: 11; Buy: 3, Sell: 90
> ```



# Graph

## 1.Depth First Search(Green)

![image-20210410115709005](/assets/algo-exp-note.assets/image-20210410115709005.png)

![image-20210410115807998](/assets/algo-exp-note.assets/image-20210410115807998.png)

```java
import java.util.*;
// O(V + E) time O(V) space
class Program {
    // Do not edit the class below except
    // for the depthFirstSearch method.
    // Feel free to add new properties
    // and methods to the class.
    static class Node {
        String name;
        List<Node> children = new ArrayList<Node>();

        public Node(String name) {
            this.name = name;
        }

        public List<String> depthFirstSearch(List<String> array) {
            //写入当前节点名
            array.add(this.name);
            //对于该节点的每一个子节点做相同的处理
            for (int i = 0; i < children.size(); i++) {
                children.get(i).depthFirstSearch(array);
            }
            return array;
        }

        public Node addChild(String name) {
            Node child = new Node(name);
            children.add(child);
            return this;
        }
    }
}
```

## 2.Single Circle Check(Blue)

![image-20210410122133900](/assets/algo-exp-note.assets/image-20210410122133900.png)

![image-20220415094637507](/assets/algo-exp-note.assets/image-20220415094637507.png)

<img src="/assets/algo-exp-note.assets/image-20210410123146022.png" alt="image-20210410123146022" style="zoom:33%;" />

本题的一种思路是用另一个数组记录原数组里每一个值被访问的次数；这种算法实现起来并不是很简单，代码也并不美观。

按照题意，如果有`single circle`，那么，数组中的每个元素都会被遍历一次且最后会回到起始地址。

本题最好的思路就是：我们需要关注三个最重要的内容。（以上图为例）

1. 数组的长度6：也就是说，从任意一个元素开始，走6步最后会回到该起始元素。
2. 当我们走了n步（0< n <6）我们就回到了起始点2，那么没有正确结果
3. 当我们走了n = 6步时，并没有回到起始点2，那么没有正确的结果

本题对图数据结构的考察并不明显，偏向于需要巧妙解法的题目。

```java
import java.util.*;
// O(N) time O(1) space
class Program {
    public static boolean hasSingleCycle(int[] array) {
        int numElementVisited = 0;//记录已经访问了数组多少次
        int currentIdx = 0;

        while (numElementVisited < array.length) {
            //若0 < n < length，而且已经回到了起始Idx，那么false
            if (numElementVisited > 0 && currentIdx == 0)
                return false;
            numElementVisited++;
            currentIdx = getNextIdx(currentIdx, array);
        }
        return currentIdx == 0;//走了length步以后是否回到了原点
    }

    //计算下一个index是多少：需要注意区分正负的不同情况
    public static int getNextIdx(int currentIdx, int[] array) {
        int jump = array[currentIdx];
        int nextIdx = (currentIdx + jump) % array.length;
        return nextIdx >= 0 ? nextIdx : nextIdx + array.length;
    }
}
```

## 3.Breadth First Search(Blue)

![image-20210410155052477](/assets/algo-exp-note.assets/image-20210410155052477.png)

![image-20210410155626100](/assets/algo-exp-note.assets/image-20210410155626100.png)

本题需要借助队列（Queue）完成，具体思路见下图：

<img src="/assets/algo-exp-note.assets/image-20210410173624965.png" alt="image-20210410173624965" style="zoom:33%;" />

从A开始，将A的子节点B，C，D写入Queue中，之后，将B弹出，将B的子节点加入队列中，以此类推。

```java
import java.util.*;
// O(V + E) time O(V) space
class Program {
    // Do not edit the class below except
    // for the breadthFirstSearch method.
    // Feel free to add new properties
    // and methods to the class.
    static class Node {
        String name;
        List<Node> children = new ArrayList<Node>();

        public Node(String name) {
            this.name = name;
        }

        public List<String> breadthFirstSearch(List<String> array) {
            Queue<Node> queue = new LinkedList<>();
            queue.add(this);
            while (!queue.isEmpty()) {
                Node current = queue.poll();
                array.add(current.name);
                queue.addAll(current.children);
            }
            return array;
        }

        public Node addChild(String name) {
            Node child = new Node(name);
            children.add(child);
            return this;
        }
    }
}
```

## 4.River Sizes(Blue*)

![image-20210410182110439](/assets/algo-exp-note.assets/image-20210410182110439.png)

![image-20210410182145262](/assets/algo-exp-note.assets/image-20210410182145262.png)

本题的思路如下图所示：

从矩阵的左上角开始，对于该元素周围的所有4个方向的元素进行检索。当检查到一个方向的值为1时，就对该方向的元素四周的元素进行检索（与之前相同）。如此重复，直到最后一个1的四周没有1的出现，然后将记录的长度存储起来。

除此之外，我们要对已经访问过的元素位置进行记录，避免重复的计算。

<img src="/assets/algo-exp-note.assets/image-20210410205435122.png" alt="image-20210410205435122" style="zoom:33%;" />

```java
import java.util.*;
// O(wh) time O(wh) space
class Program {
    public static List<Integer> riverSizes(int[][] matrix) {
        List<Integer> sizes = new ArrayList<>();
        boolean[][] visited = new boolean[matrix.length][matrix[0].length];
        //遍历数组中的每一个数
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                if (visited[i][j])
                    continue;
                traverseNode(i, j, matrix, visited, sizes);
            }
        }
        return sizes;
    }

    public static void traverseNode(int i, int j, int[][] matrix, boolean[][] visited, List<Integer> sizes) {
        //进入一个节点去寻找从该节点开始的river
        int currentRiverSize = 0;
        //把符合的节点放入Stack中
        Stack<Integer[]> nodeToExplore = new Stack<>();
        nodeToExplore.push(new Integer[]{i, j});
        while (!nodeToExplore.empty()) {
            Integer[] currentNode = nodeToExplore.pop();
            i = currentNode[0];
            j = currentNode[1];
            if (visited[i][j])
                continue;
            visited[i][j] = true;

            if (matrix[i][j] == 0)
                continue;
			//找到了一个1
            currentRiverSize++;
			//从刚才找到的1开始对上下左右四个方向开始遍历，并把合适的节点存入栈中
            List<Integer[]> unvisitedNeighbors = getUnvisitedNeighbors(i, j, matrix, visited);
            nodeToExplore.addAll(unvisitedNeighbors);
        }
        //找到了从一个节点开始一整个river的路线
        if (currentRiverSize > 0)
            sizes.add(currentRiverSize);
    }


    public static List<Integer[]> getUnvisitedNeighbors(int i, int j, int[][] matrix, boolean[][] visited) {
        List<Integer[]> unvisitedNeighbors = new ArrayList<>();
        //上侧
        if (i > 0 && !visited[i - 1][j])
            unvisitedNeighbors.add(new Integer[]{i - 1, j});
        //下侧
        if (i < matrix.length - 1 && !visited[i + 1][j])
            unvisitedNeighbors.add(new Integer[]{i + 1, j});
        //左侧
        if (j > 0 && !visited[i][j - 1])
            unvisitedNeighbors.add(new Integer[]{i, j - 1});
        //右侧
        if (j < matrix[0].length - 1 && !visited[i][j + 1])
            unvisitedNeighbors.add(new Integer[]{i, j + 1});
        return unvisitedNeighbors;
    }
}
```

需要考虑的特殊情况, 为什么要在`traverseNode()`中也要判断一次这个点是否visited了呢？可以按照算法思路用下边的例子试一试。

```java
{
  "matrix": [
    [1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1]
  ]
}
```

## 5.Youngest Common Ancestor(Blue)

![image-20210411182946030](/assets/algo-exp-note.assets/image-20210411182946030.png)

![image-20210411183210158](/assets/algo-exp-note.assets/image-20210411183210158.png)

本题的思路具体如下所示：

对于给出的两个descendants，我们要先让他们达到同一个深度，然后让他们一起向上搜索，直到找到共同的ancestor。如下图的绿线，T的深度为4，I的深度为2，所以先让T向上检索4-2 = 2次到达H，之后让H、I共同向上检索即可得到结果。

<img src="/assets/algo-exp-note.assets/image-20210411185734981.png" alt="image-20210411185734981" style="zoom:33%;" />

```java
import java.util.*;
// O(d) time O(1) space
class Program {
    public static AncestralTree getYoungestCommonAncestor(
            AncestralTree topAncestor, AncestralTree descendantOne, AncestralTree descendantTwo) {
        int depthOne = getDescendantDepth(topAncestor, descendantOne);
        int depthTwo = getDescendantDepth(topAncestor, descendantTwo);

        if (depthOne > depthTwo)//lower：1；higher：2
            return backtrackAncestralTree(descendantTwo, descendantOne, depthOne - depthTwo);
        else//lower：2；higher：1 or equal
            return backtrackAncestralTree(descendantOne, descendantTwo, depthTwo - depthOne);
    }

    public static int getDescendantDepth(AncestralTree topAncestor, AncestralTree descendant) {
        int depth = 0;
        while (descendant != topAncestor) {
            descendant = descendant.ancestor;
            depth++;
        }
        return depth;
    }

    public static AncestralTree backtrackAncestralTree(
            AncestralTree higherDescendant, AncestralTree lowerDescendant, int diff) {
        //使两个descendants到达同一depth
        while (diff > 0) {
            lowerDescendant = lowerDescendant.ancestor;
            diff--;
        }

        //寻找最小祖先节点
        while (lowerDescendant != higherDescendant) {
            lowerDescendant = lowerDescendant.ancestor;
            higherDescendant = higherDescendant.ancestor;
        }
        return lowerDescendant;
    }

    static class AncestralTree {
        public char name;
        public AncestralTree ancestor;

        AncestralTree(char name) {
            this.name = name;
            this.ancestor = null;
        }

        // This method is for testing only.
        void addAsAncestor(AncestralTree[] descendants) {
            for (AncestralTree descendant : descendants) {
                descendant.ancestor = this;
            }
        }
    }
}
```

## 6.Remove Islands(Blue)

![image-20210411192410955](/assets/algo-exp-note.assets/image-20210411192410955.png)

![image-20210411192625882](/assets/algo-exp-note.assets/image-20210411192625882.png)

本题的第一种思路如下所示：从边界开始向内去找所有和边界相连的岛屿，给他们标记为True，然后将非True的部分全部变为0即可。

<img src="/assets/algo-exp-note.assets/image-20210412140730220.png" alt="image-20210412140730220" style="zoom:33%;" />

```java
import java.util.*;
// O(wh) time O(wh) space
class Program {

    public int[][] removeIslands(int[][] matrix) {
        boolean[][] onesConnectedToBorder = new boolean[matrix.length][matrix[0].length];
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                onesConnectedToBorder[i][j] = false;
            }
        }
        //找出所有的和陆地连接的部分
        for (int row = 0; row < matrix.length; row++) {
            for (int col = 0; col < matrix[row].length; col++) {
                boolean rowIsBorder = row == 0 || row == matrix.length - 1;
                boolean colIsBorder = col == 0 || col == matrix[row].length - 1;
                boolean isBorder = rowIsBorder || colIsBorder;
                //不在边界上，则继续搜寻
                if (!isBorder)
                    continue;
                //在边界上但不是陆地，则继续搜寻
                if (matrix[row][col] != 1)
                    continue;

                //找到了在边界上的陆地，寻找和他相连的陆地,
                findOnesConnectedToBorder(matrix, row, col, onesConnectedToBorder);
            }
        }
        //清除岛屿
        for (int row = 0; row < matrix.length; row++) {
            for (int col = 0; col < matrix[row].length; col++) {
                //和陆地相连的部分都已经被标为了1
                if (onesConnectedToBorder[row][col])
                    continue;
                matrix[row][col] = 0;
            }
        }
        return matrix;
    }

    public static void findOnesConnectedToBorder(
            int[][] matrix, int startRow, int startCol, boolean[][] onesConnectedToBorder) {
        Stack<Integer[]> stack = new Stack<>();
        stack.push(new Integer[]{startRow, startCol});

        while (!stack.isEmpty()) {
            Integer[] currentPosition = stack.pop();
            int currentRow = currentPosition[0];
            int currentCol = currentPosition[1];
            //已经访问过
            //这个判定条件很重要，如果没有这个判定且matrix全部都是1，那么我们的栈就会不停的放入已经被计入陆地的位置
            //这样stack就永远无法清空
            if (onesConnectedToBorder[currentRow][currentCol])
                continue;
            //未访问过，更新状态
            onesConnectedToBorder[currentRow][currentCol] = true;
            //获取周围的所有节点
            List<Integer[]> neighbors = getNeighbors(matrix, currentRow, currentCol);

            for (Integer[] neighbor : neighbors) {
                int row = neighbor[0];
                int col = neighbor[1];
                //如果周边的节点不是陆地跳过
                if (matrix[row][col] != 1)
                    continue;
                //如果是陆地则加入栈中
                stack.push(neighbor);
            }
        }
    }

    public static List<Integer[]> getNeighbors(int[][] matrix, int startRow, int startCol) {
        List<Integer[]> Neighbors = new ArrayList<>();
        //上侧
        if (startRow > 0)
            Neighbors.add(new Integer[]{startRow - 1, startCol});
        //下侧
        if (startRow < matrix.length - 1)
            Neighbors.add(new Integer[]{startRow + 1, startCol});
        //左侧
        if (startCol > 0)
            Neighbors.add(new Integer[]{startRow, startCol - 1});
        //右侧
        if (startCol < matrix[0].length - 1)
            Neighbors.add(new Integer[]{startRow, startCol + 1});
        return Neighbors;
    }
}
```

## 7.Boggle Board(Red*)

![image-20210412162035310](/assets/algo-exp-note.assets/image-20210412162035310.png)

![image-20210412162100553](/assets/algo-exp-note.assets/image-20210412162100553.png)

本题要解决String和char之间的一一比较，需要引入数据结构Trie

<img src="/assets/algo-exp-note.assets/image-20210413143749564.png" alt="image-20210413143749564" style="zoom:67%;" />

本题的基本解题思路就是遍历board里得每一个char，向他的周围去寻找有机会能够组成String的char。

```java
// O(nm*8^s + ws) time | O(nm + ws) space
import java.util.*;

class Program {
    public static List<String> boggleBoard(char[][] board, String[] words) {
        Trie trie = new Trie();
        //建立Trie
        for (String word : words)
            trie.add(word);
        Set<String> finalWords = new HashSet<>();
        boolean[][] visited = new boolean[board.length][board[0].length];
        //找到所有结果
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                explore(i, j, board, trie.root, visited, finalWords);
            }
        }
        List<String> finalWordsArray = new ArrayList<>(finalWords);
        return finalWordsArray;
    }

    public static void explore(
            int i, int j,
            char[][] board,
            TrieNode trieNode,
            boolean[][] visited,
            Set<String> finalWords) {
        //组成过词，该char不能再次被使用
        if (visited[i][j])
            return;
        //还未组成过词
        char letter = board[i][j];
        //如果该letter不在Trie的相应路径上，不再进行后续工作
        if (!trieNode.children.containsKey(letter))
            return;
        //该letter有机会组成目标String
        visited[i][j] = true;
        //得到letter后的children
        trieNode = trieNode.children.get(letter);
        //如果到了结束符，那么找到了一个目标String
        if (trieNode.children.containsKey('*'))
            finalWords.add(trieNode.word);
        //如果还没有到结束符，就向周围进行寻找
        List<Integer[]> neighbors = getNeighbors(i, j, board);
        //这时，以当前的letter为出发点，向他的四周寻找
        for (Integer[] neighbor : neighbors)
            explore(neighbor[0], neighbor[1], board, trieNode, visited, finalWords);

        visited[i][j] = false;
    }


    public static List<Integer[]> getNeighbors(int i, int j, char[][] board) {
        List<Integer[]> neighbors = new ArrayList<>();
        //左上
        if (i > 0 && j > 0)
            neighbors.add(new Integer[]{i - 1, j - 1});
        //右上
        if (i > 0 && j < board[0].length - 1)
            neighbors.add(new Integer[]{i - 1, j + 1});
        //左下
        if (i < board.length - 1 && j > 0)
            neighbors.add(new Integer[]{i + 1, j - 1});
        //右下
        if (i < board.length - 1 && j < board[0].length - 1)
            neighbors.add(new Integer[]{i + 1, j + 1});
        //上
        if (i > 0)
            neighbors.add(new Integer[]{i - 1, j});
        //下
        if (i < board.length - 1)
            neighbors.add(new Integer[]{i + 1, j});
        //左
        if (j > 0)
            neighbors.add(new Integer[]{i, j - 1});
        //右
        if (j < board[0].length - 1)
            neighbors.add(new Integer[]{i, j + 1});

        return neighbors;
    }

    //需要采用数据结构Trie（字典树）
    static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<>();
        String word = "";
    }

    static class Trie {
        TrieNode root;
        char endSymbol;

        public Trie() {
            this.root = new TrieNode();
            this.endSymbol = '*';
        }

        public void add(String str) {
            TrieNode node = this.root;
            for (int i = 0; i < str.length(); i++) {
                char letter = str.charAt(i);
                if (!node.children.containsKey(letter)) {
                    TrieNode newNode = new TrieNode();
                    node.children.put(letter, newNode);
                }
                node = node.children.get(letter);
            }
            node.children.put(this.endSymbol, null);
            node.word = str;
        }
    }
}
```

## 8.Cycle In Graph(Blue)

![image-20220416155307890](/assets/algo-exp-note.assets/image-20220416155307890.png)

![image-20220416155322891](/assets/algo-exp-note.assets/image-20220416155322891.png)

- 方法一：`visited`用来保存已经遍历了哪些Node，`instack`用来记录深度优先遍历过程中当前Node和其之前的Node。如果我们发现当前Node的neighbors已经在栈中，那么我们就找到了一个环。我们采用了递归的方式，用栈来记录深度优先遍历的状态，这样每一个被遍历过的Node都是经过了彻底的是否存在着环的检验的。

<img src="/assets/algo-exp-note.assets/image-20220418161349873.png" alt="image-20220418161349873" style="zoom:50%;" />

```java
import java.util.*;
// O(v + e) time O(v) space
class Program {

    public boolean cycleInGraph(int[][] edges) {
        boolean[] visited = new boolean[edges.length];
        boolean[] currentlyInStack = new boolean[edges.length];
		//遍历每一个节点
        for (int i = 0; i < edges.length; i++) {
            if (visited[i])
                continue;
			//进行递归，得出当前节点所能产生的所有路径是否存在环
            boolean containsCycle = isNodeInCycle(i, edges, visited, currentlyInStack);
            if (containsCycle)
                return true;
        }
        return false;
    }

    private boolean isNodeInCycle(int i, int[][] edges, boolean[] visited, boolean[] currentlyInStack) {
        //当前节点已经被访问
        //当前节点被记录在当前路径中
        visited[i] = true;
        currentlyInStack[i] = true;
        boolean containsCycle = false;
		//检查当前节点所有的neighbor是否是环的一部分
        int[] neighbors = edges[i];
        for (int neighbor : neighbors) {
            //若当前neighbor没有被访问过，对它能产生的路径进行检测
            if (!visited[neighbor])
                containsCycle = isNodeInCycle(neighbor, edges, visited, currentlyInStack);
            //若存在环，则返回true
            if (containsCycle)
                return true;
            //若neighbor已经被记录在当前路径中，证明环存在
            if (currentlyInStack[neighbor])
                return true;
        }
        //对当前节点能产生的所有路径进行了检测，没有环出现
        //将当前节点弹出栈
        currentlyInStack[i] = false;
        return false;
    }
}
```

- 方法二（意义不大）：方法二在方法一的基础上对存储各节点状态的方式做了优化。

  ```java
  import java.util.*;

  class Program1 {
      //用于表示没有访问
      public int WHITE = 0;
      //用于表示currently in stack
      public int GREY = 1;
      //用于表示不必再访问的节点，已经检查过了，没有环
      public int BLACK = 3;

      // O(v + e) time | O(v) space - where v is the number of

      // vertices and e is the number of edges in the graph

      public boolean cycleInGraph(int[][] edges) {
          int numberOfNodes = edges.length;
          int[] colors = new int[numberOfNodes];
          Arrays.fill(colors, WHITE);

          for (int node = 0; node < numberOfNodes; node++) {
              if (colors[node] != WHITE) continue;
              boolean containsCycle = traverseAndColorNodes(node, edges, colors);
              if (containsCycle) return true;

          }
          return false;

      }


      public boolean traverseAndColorNodes(int node, int[][] edges, int[] colors) {

          colors[node] = GREY;
          int[] neighbors = edges[node];
          for (int neighbor : neighbors) {
              int neighborColor = colors[neighbor];
              if (neighborColor == GREY) {
                  return true;
              }
              if (neighborColor == BLACK) {
                  continue;
              }
              boolean containsCycle = traverseAndColorNodes(neighbor, edges, colors);

              if (containsCycle) {
                  return true;
              }
          }
          colors[node] = BLACK;
          return false;
      }

  }
  ```

## 9.Minimum Passes Of Matrix(Blue)

![image-20220417103506453](/assets/algo-exp-note.assets/image-20220417103506453.png)

![image-20220417103658354](/assets/algo-exp-note.assets/image-20220417103658354.png)

![image-20220417103721771](/assets/algo-exp-note.assets/image-20220417103721771.png)

- 方法一：

<img src="/assets/algo-exp-note.assets/image-20220418161014171.png" alt="image-20220418161014171" style="zoom:50%;" />

```java
import java.util.*;
// O(w*h) time O(w*h) space
class Program {

    public int minimumPassesOfMatrix(int[][] matrix) {
        int passes = convertNegatives(matrix);
        return (!containsNegative(matrix)) ? passes - 1 : -1;
    }

    //检查处理后的matrix是否依然含有negative
    private boolean containsNegative(int[][] matrix) {
        for (int[] row : matrix)
            for (int val : row)
                if (val < 0) return true;
        return false;
    }

    //使用两个queue，currentQueue用于记录当前matrix含有的positives
    //nextQueue用于记录经过处理后，新产生的positives
    public int convertNegatives(int[][] matrix) {
        Queue<Integer[]> nextPassQueue = new LinkedList<>();
        nextPassQueue.addAll(getAllPositivePostions(matrix));

        int passes = 0;
        while (!nextPassQueue.isEmpty()) {
            Queue<Integer[]> currentPassQueue = nextPassQueue;
            nextPassQueue = new LinkedList<>();
            //将currentQueue中的positive位置一个一个取出
            //寻找其neighbors，并把其中的negative变为正数并存入nextQueue中
            //作为下一个pass需要处理的内容
            while (!currentPassQueue.isEmpty()) {
                Integer[] currentPos = currentPassQueue.poll();
                int row = currentPos[0];
                int col = currentPos[1];
                List<Integer[]> neighbors = getNeighbors(row, col, matrix);
                for (Integer[] neighbor : neighbors) {
                    row = neighbor[0];
                    col = neighbor[1];
                    if (matrix[row][col] < 0) {
                        nextPassQueue.add(new Integer[]{row, col});
                        matrix[row][col] *= -1;
                    }
                }
            }
            //当前队列中的内容全部取出，说明完成了一次负数转正数
            passes++;
        }
        return passes;
    }

    private List<Integer[]> getNeighbors(int row, int col, int[][] matrix) {
        List<Integer[]> neighbors = new ArrayList<>();
        //up
        if (row > 0)
            neighbors.add(new Integer[]{row - 1, col});
        //down
        if (row < matrix.length - 1)
            neighbors.add(new Integer[]{row + 1, col});
        //left
        if (col > 0)
            neighbors.add(new Integer[]{row, col - 1});
        //right
        if (col < matrix[0].length - 1)
            neighbors.add(new Integer[]{row, col + 1});

        return neighbors;
    }

    public List<Integer[]> getAllPositivePostions(int[][] matrix) {
        List<Integer[]> positivePositions = new LinkedList<>();
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                if (matrix[i][j] > 0)
                    positivePositions.add(new Integer[]{i, j});
            }
        }
        return positivePositions;
    }
}
```

- 方法二：方法二是在方法一的基础上将用到的两个queue合并成一个queue，但对时间空间复杂度没有任何影响。

# Greedy Algorithm

## 1.Minimum Waiting Time(Green)

![image-20210413172548269](/assets/algo-exp-note.assets/image-20210413172548269.png)

![image-20210413172706104](/assets/algo-exp-note.assets/image-20210413172706104.png)

本题使用贪心算法，基本的思路就是，让执行时间最短的queries先执行，这样，对于每一步来说，它所等待的时间都是最短的。

<img src="/assets/algo-exp-note.assets/image-20210413173828086.png" alt="image-20210413173828086" style="zoom:33%;" />

算法的计算方式是（用上述例子来说）：

遍历一遍数组，对于其中的每一个query，它都需要让他之后的query等待他所执行的时间。每一个query让它之后的query所等待的时间加起来，就可以得到最后的答案。

```java
import java.util.*;
//O(Nlog(N)) time O(1) space
class Program {

    public int minimumWaitingTime(int[] queries) {
        Arrays.sort(queries);

        int totalWaitingTime = 0;
        for (int idx = 0; idx < queries.length; idx++) {
            int duration = queries[idx];
            //右侧剩余的query
            int queriesLeft = queries.length - (idx + 1);
            totalWaitingTime += queriesLeft * duration;
        }
        return totalWaitingTime;
    }
}
```

## 2.Class Photos(Green)

![image-20210413175157400](/assets/algo-exp-note.assets/image-20210413175157400.png)

![image-20210413193248551](/assets/algo-exp-note.assets/image-20210413193248551.png)

本题首先要确当红蓝两排谁要站到后边一排。采用贪心算法，我们首先要找到两队中身高最高的学生，因为身高最高的学生是最难以找到合适的前排的。按照此种思路，我们首先确定蓝色的一排站在后排（因为蓝色的最高身高> 红色队伍的最高身高），之后就可以让红色的最高个站在蓝色的最高同学之前；随后，拿出各个组的第二高，让他们一前一后，以此类推。

<img src="/assets/algo-exp-note.assets/image-20210413194407798.png" alt="image-20210413194407798" style="zoom: 33%;" />

所以本题的算法其实就是为两组人排序，如果站在第二排的组不能每一个学生都比前一排的人高的话，那么就不存在这样的排序。

```java
import java.util.*;
//O(Nlog(N)) time O(1) space
class Program {

    public boolean classPhotos(
            ArrayList<Integer> redShirtHeights, ArrayList<Integer> blueShirtHeights) {
        //从大到小排序
        redShirtHeights.sort(Collections.reverseOrder());
        blueShirtHeights.sort(Collections.reverseOrder());

        String shirtColorInFirstRow =
                redShirtHeights.get(0) < blueShirtHeights.get(0) ? "RED" : "BLUE";

        for (int i = 0; i < redShirtHeights.size(); i++) {
            int redShirtHeight = redShirtHeights.get(i);
            int blueShirtHeight = blueShirtHeights.get(i);

            if (shirtColorInFirstRow.equals("RED")) {
                if (redShirtHeight >= blueShirtHeight)
                    return false;
            } else {//BLUE
                if (blueShirtHeight >= redShirtHeight)
                    return false;
            }
        }
        return true;
    }
}
```

## 3.Tandem Bicycle(Green)

![image-20210413200406287](/assets/algo-exp-note.assets/image-20210413200406287.png)

![image-20210413200438217](/assets/algo-exp-note.assets/image-20210413200438217.png)

如果要找到速度最大的匹配方式：那么我们只需让红蓝两组的高速和低速互相匹配即可。如下图所示，红组9是最大的，所以和蓝组最小的1匹配；之后蓝组有最大值7，就和红组的最小值2相匹配。以此类推，利用贪心算法就可以让所有的高速车手主导。

<img src="/assets/algo-exp-note.assets/image-20210414133636720.png" alt="image-20210414133636720" style="zoom:33%;" />

如果要找到速度最小的匹配方式：只需让红蓝两队的最大值互相匹配、次最大值互相匹配.......。

<img src="/assets/algo-exp-note.assets/image-20210414134327138.png" alt="image-20210414134327138" style="zoom:33%;" />

```java
import java.util.*;
//O(Nlog(N)) time O(1) space
class Program {

    public int tandemBicycle(int[] redShirtSpeeds, int[] blueShirtSpeeds, boolean fastest) {
        Arrays.sort(redShirtSpeeds);
        Arrays.sort(blueShirtSpeeds);

        //找最小值
        if (!fastest)
            reverseArray(redShirtSpeeds);
        //找最大值
        int totalSpeed = 0;
        for (int i = 0; i < redShirtSpeeds.length; i++) {
            int rider1 = redShirtSpeeds[i];
            int rider2 = blueShirtSpeeds[redShirtSpeeds.length - 1 - i];
            totalSpeed += Math.max(rider1, rider2);
        }
        return totalSpeed;
    }

    public void reverseArray(int[] array) {
        int start = 0;
        int end = array.length - 1;
        while (start < end) {
            int temp = array[start];
            array[start] = array[end];
            array[end] = temp;
            start++;
            end--;
        }
    }
}
```

## 4.Task Assignment(Blue)

![image-20210414221651930](/assets/algo-exp-note.assets/image-20210414221651930.png)

![image-20210414221709476](/assets/algo-exp-note.assets/image-20210414221709476.png)

本题的思路如下所示，我们先给原始数组排序，排序后让最小最大的task一组；次小次大的task一组；以此类推。

<img src="/assets/algo-exp-note.assets/image-20210414222804218.png" alt="image-20210414222804218" style="zoom:33%;" />

```java
import java.util.*;
// O(Nlog(N)) time O(N) space
class Program {

    public ArrayList<ArrayList<Integer>> taskAssignment(int k, ArrayList<Integer> tasks) {
        ArrayList<ArrayList<Integer>> pairedTasks = new ArrayList<>();
        Map<Integer, ArrayList<Integer>> taskDurationsToIndices = getTaskDurationsToIndices(tasks);
        //对原数组进行排序
        Collections.sort(tasks);

        //得到所有的pairedTasks
        for (int i = 0; i < k; i++) {
            //获得排序后在第i组的第一个task的Index
            int task1Duration = tasks.get(i);
            ArrayList<Integer> indicesWithTask1Duration = taskDurationsToIndices.get(task1Duration);
            int task1Index = indicesWithTask1Duration.remove(indicesWithTask1Duration.size() - 1);
            //获得排序后在第i组的第二个task的Index
            int task2Duration = tasks.get(tasks.size() - 1 - i);
            ArrayList<Integer> indicesWithTask2Duration = taskDurationsToIndices.get(task2Duration);
            int task2Index = indicesWithTask2Duration.remove(indicesWithTask2Duration.size() - 1);

            ArrayList<Integer> pairedTask = new ArrayList<>();
            pairedTask.add(task1Index);
            pairedTask.add(task2Index);
            pairedTasks.add(pairedTask);
        }

        return pairedTasks;
    }

    //因为题目要求返回的是原数组的index，所以我们把原始数组的和他的坐标存成键值对
    //duration: index1,index2
    public Map<Integer, ArrayList<Integer>> getTaskDurationsToIndices(
            ArrayList<Integer> tasks) {
        Map<Integer, ArrayList<Integer>> taskDurationToIndices = new HashMap<>();

        for (int i = 0; i < tasks.size(); i++) {
            int taskDuration = tasks.get(i);
            if (taskDurationToIndices.containsKey(taskDuration))
                taskDurationToIndices.get(taskDuration).add(i);
            else {
                ArrayList<Integer> temp = new ArrayList<>();
                temp.add(i);
                taskDurationToIndices.put(taskDuration, temp);
            }
        }
        return taskDurationToIndices;
    }
}
```

## 5.Valid Starting City(Red*)

![image-20210415131646311](/assets/algo-exp-note.assets/image-20210415131646311.png)

![image-20210415131706091](/assets/algo-exp-note.assets/image-20210415131706091.png)

![image-20210415175801817](/assets/algo-exp-note.assets/image-20210415175801817.png)

方法一：Brute Force

我们只需遍历每一个点，如果从该点开始按照题目的逻辑可以走回该点，那么该点就是一个valid starting city。

![image-20210415184811439](/assets/algo-exp-note.assets/image-20210415184811439.png)

方法二：贪心算法（理解上有难度）

如下图所示，蓝色的数字代表到达一个城市的时候，车辆中的油还能行进的里程数。从0开始算起，我们可以得到下图所示的结果。事实上，从1，2，3算起，到达城市4的时候，都会出现最大的亏空。所以，我们必须要以亏空最大的城市作为出发点，否则，从其他任何城市出发，最后都会出现亏空的情况。

<img src="/assets/algo-exp-note.assets/image-20210415184855725.png" alt="image-20210415184855725" style="zoom: 67%;" />

```java
import java.util.*;

class Program {

    public int validStartingCity(int[] distances, int[] fuel, int mpg) {
        int numberOfCities = distances.length;
        int milesRemaining = 0;//存储每一个城市车辆中的油还能行进的里程数

        int indexOfStartingCityCandidate = 0;//要返回的最终值
        int milesRemainingAtStartingCityCandidate = 0;

        for (int cityIdx = 1; cityIdx < numberOfCities; cityIdx++) {
            int distanceFromPreviousCity = distances[cityIdx - 1];
            int fuelFromPreviousCity = fuel[cityIdx - 1];
            milesRemaining += fuelFromPreviousCity * mpg - distanceFromPreviousCity;
			//出现了亏空更大的城市，更新
            if (milesRemaining < milesRemainingAtStartingCityCandidate) {
                indexOfStartingCityCandidate = cityIdx;
                milesRemainingAtStartingCityCandidate = milesRemaining;
            }
        }
        return indexOfStartingCityCandidate;
    }
}
```

# Heaps

## 1.Min Heap Construction(Blue*)

堆在结构上很像二叉树，然而，堆必须按照如下规则：

- 堆中某个节点的值总是不大于(<=)或不小于(>=)其父节点的值。
- 堆总是一棵完全二叉树。

完全二叉树：一棵深度为k的有n个结点的二叉树，对树中的结点按从上至下、从左到右的顺序进行编号，如果编号为`i（1≤i≤n）`的结点与满二叉树中编号为`i`的结点在二叉树中的位置相同，则这棵二叉树称为完全二叉树。

> 对于二叉堆，有如下几种操作。
>
> 1. 插入节点。
>
> 2. 删除节点。
>
> 3. 构建二叉堆。
>
>    这几种操作都基于堆的自我调整。所谓堆的自我调整，就是把一个 不符合堆性质的完全二叉树，调整成一个堆。下面让我们以最小堆为 例，看一看二叉堆是如何进行自我调整的。

### 插入节点

当二叉堆插入节点时，插入位置是完全二叉树的最后一个位置。例如插入一个新节点，值是 0。

![image-20220418161917717](/assets/algo-exp-note.assets/image-20220418161917717.png)

这时，新节点的父节点5比0大，显然不符合最小堆的性质。于是让 新节点“上浮”，和父节点交换位置。

![image-20220418162132927](/assets/algo-exp-note.assets/image-20220418162132927.png)



继续用节点0和父节点3做比较，因为0小于3，则让新节点继续“上 浮”。

![image-20220418162435984](/assets/algo-exp-note.assets/image-20220418162435984.png)

继续比较，最终新节点0“上浮”到了堆顶位置。

![image-20220418162501464](/assets/algo-exp-note.assets/image-20220418162501464.png)

###  删除节点

二叉堆删除节点的过程和插入节点的过程正好相反，**所删除的是处于堆顶的节点**。例如删除最小堆的堆顶节点1。

![image-20220418162622210](/assets/algo-exp-note.assets/image-20220418162622210.png)

这时，为了继续维持完全二叉树的结构，我们把堆的最后一个节点 10临时补到原本堆顶的位置。

![image-20220418162652036](/assets/algo-exp-note.assets/image-20220418162652036.png)

接下来，让暂处堆顶位置的节点10和它的左、右孩子进行比较，如 果左、右孩子节点中最小的一个（显然是节点2）比节点10小，那么让 节点10“下沉”。



![image-20220418162731640](/assets/algo-exp-note.assets/image-20220418162731640.png)

继续让节点10和它的左、右孩子做比较，左、右孩子中最小的是节 点7，由于10大于7，让节点10继续“下沉”。

![image-20220418162821222](/assets/algo-exp-note.assets/image-20220418162821222.png)

### 构建二叉堆

构建二叉堆，也就是把一个无序的完全二叉树调整为二叉堆，本质 就是让所有非叶子节点依次“下沉”。

![image-20220418164539997](/assets/algo-exp-note.assets/image-20220418164539997.png)

首先，从最后一个非叶子节点开始，也就是从节点10开始。如果节 点10大于它左、右孩子节点中最小的一个，则节点10“下沉”。

![image-20220418164716984](/assets/algo-exp-note.assets/image-20220418164716984.png)

接下来轮到节点3，如果节点3大于它左、右孩子节点中最小的一 个，则节点3“下沉”。

![image-20220418164753311](/assets/algo-exp-note.assets/image-20220418164753311.png)

然后轮到节点1，如果节点1大于它左、右孩子节点中最小的一个， 则节点1“下沉”。事实上节点1小于它的左、右孩子，所以不用改变。

接下来轮到节点7，如果节点7大于它左、右孩子节点中最小的一 个，则节点7“下沉”。

![image-20220418164854591](/assets/algo-exp-note.assets/image-20220418164854591.png)

节点7继续比较，继续“下沉”。

![image-20220418164934577](/assets/algo-exp-note.assets/image-20220418164934577.png)



经过上述几轮比较和“下沉”操作，最终每一节点都小于它的左、右 孩子节点，一个无序的完全二叉树就被构建成了一个最小堆。

### 堆的存储方式

一般我们用数组表示一个堆（节省空间）。

<img src="/assets/algo-exp-note.assets/image-20220212222703557.png" alt="image-20220212222703557" style="zoom: 50%;" />

![image-20210629213041417](/assets/algo-exp-note.assets/image-20210629213041417.png)

![image-20210629213129045](/assets/algo-exp-note.assets/image-20210629213129045.png)

MIn Heap的特点是对于该堆中的每一个节点，parentNode的值都会比childrenNode的值要小。从下图来看，本题主要需要实现如下几个方法：

- Build Heap：用于将传入的数组调整为Min Heap。Build Heap的方法有很多种，本题采用的方法是从第一个parentNode开始，对每一个parentNode进行Sift Down。

  [30,102,23,17,18,9,44,12,31]

  下面的例子中有9个数，其中的第一个父节点的idx = **firstParentIdx = (array.size() - 2) / 2**

  （9-2）/ 2 = 3，所以17就是第一个父节点。

  第一个父节点的意义在于，我们可以确定所有父节点范围在（0，firstParentIdx）之间，从而减少多余的运算。

<img src="/assets/algo-exp-note.assets/image-20210630134856543.png" alt="image-20210630134856543" style="zoom:33%;" />

- **Sift Down**：当发现currentNode大于childrenNode时（说明该节点不符合Min heap的要求），currentNode应该向下改变自己在堆中的位置，直到满足Min heap的要求为止
- **Sift Up**：当发现currentNode小于parentNode时（说明该节点不符合Min heap的要求），currentNode应该向上改变自己在堆中的位置，直到满足Min Heap的要求为止
- Insert：加入一个新的数后（加在heap的末尾），若加入的数是的Min Heap的规则被打破，那么就对加入的数进行Sift Up

<img src="/assets/algo-exp-note.assets/image-20210630132750070.png" alt="image-20210630132750070" style="zoom:33%;" />

<img src="/assets/algo-exp-note.assets/image-20210630132934619.png" alt="image-20210630132934619" style="zoom:33%;" />

- Remove：删除root后，Min Heap的规则被打破。首先需要将root（8）和堆中的最后一个数（31）互换位置。互换后，root8被丢弃，然后对root31进行sift down。

<img src="/assets/algo-exp-note.assets/image-20210630133148281.png" alt="image-20210630133148281" style="zoom:33%;" />

<img src="/assets/algo-exp-note.assets/image-20210630133406929.png" alt="image-20210630133406929" style="zoom:33%;" />

![image-20210630124521777](/assets/algo-exp-note.assets/image-20210630124521777.png)

```java
import java.util.*;

// Do not edit the class below except for the buildHeap,
// siftDown, siftUp, peek, remove, and insert methods.
// Feel free to add new properties and methods to the class.
class Program {
    static class MinHeap {
        List<Integer> heap = new ArrayList<Integer>();

        public MinHeap(List<Integer> array) {
            heap = buildHeap(array);
        }
//O(n)time
        public List<Integer> buildHeap(List<Integer> array) {
            int firstParentIdx = (array.size() - 2) / 2;
            for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
                siftDown(currentIdx, array.size() - 1, array);
            return array;
        }
//O(log(n))time
        public void siftDown(int currentIdx, int endIdx, List<Integer> heap) {
            int childOneIdx = currentIdx * 2 + 1;
            while (childOneIdx <= endIdx) {
                int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
                //确定两个子节点哪一个更小
                //更小的有可能需要与当前节点互换
                int idxToSwap = 0;
                if (childTwoIdx != -1 && heap.get(childTwoIdx) < heap.get(childOneIdx))
                    idxToSwap = childTwoIdx;
                else
                    idxToSwap = childOneIdx;

                //确定了更小的子节点后，需要验证是否需要向下交换
                //若需要，则在swap后更新相应位置信息
                if (heap.get(idxToSwap) < heap.get(currentIdx)) {
                    swap(idxToSwap, currentIdx, heap);
                    currentIdx = idxToSwap;
                    childOneIdx = currentIdx * 2 + 1;
                } else {//否则，当前节点已经满足minHeap的要求，返回即可
                    return;
                }
            }
        }
//O(log(n))time
        public void siftUp(int currentIdx, List<Integer> heap) {
            while (currentIdx > 0) {
                int parentIdx = (currentIdx - 1) / 2;
                if (heap.get(currentIdx) < heap.get(parentIdx))
                    swap(currentIdx, parentIdx, heap);
                currentIdx = parentIdx;
            }
        }

        public int peek() {
            return heap.get(0);
        }

        public int remove() {
            swap(0, heap.size() - 1, heap);
            int valueToRemove = heap.get(heap.size() - 1);
            heap.remove(heap.size() - 1);
            siftDown(0, heap.size() - 1, heap);
            return valueToRemove;
        }

        public void insert(int value) {
            heap.add(value);
            siftUp(heap.size() - 1, heap);
        }


        public void swap(int i, int j, List<Integer> heap) {
            Integer temp = heap.get(i);
            heap.set(i, heap.get(j));
            heap.set(j, temp);
        }
    }
}


```

## 2.Continuous Median(Red)

![image-20210705134649835](/assets/algo-exp-note.assets/image-20210705134649835.png)![image-20210705134724565](algo-exp-note.assets/image-20210705134724565.png)

本题的思路如下图所示：

如果我们想要不通过遍历，以O(1)的时间复杂度取得一组数的中位数，那么我们需要借助堆（heap）的帮助。如下图所示，我们将建立两个堆一个叫做Lower Half，一个叫做Greater Half。Lower Half是一个max Heap，Greater Half是一个min Heap，当图中白色的数组依次被insert时，我们只需要把它们放到合适的Heap中即可。

若2个堆的个数不一样时（相差1），也就意味着现在有奇数个数，那么中位数就是Max Heap的root或者是Min Heap的root。

若2个堆的个数一样时，也就意味着有偶数个数，那么中位数就是(Max Heap.root+Min Heap.root)/2。

![image-20210705143251515](/assets/algo-exp-note.assets/image-20210705143251515.png)

**从而本题的重点就要着眼于对2个堆的维护。**当出现下图所示的情况时，我们可以发现两个堆之间节点个数的差值为2，此时我们需要对两个堆进行调整。**我们只能使得两个堆节点个数的差值为一或者使得两个堆节点个数相同**。因为我们要保证我们始终把数组分成了大小最多相差为1的两半，这样我们才能获得中位数。

<img src="/assets/algo-exp-note.assets/image-20210705144414084.png" alt="image-20210705144414084" style="zoom:33%;" />

下图为调整之后的样子。

<img src="/assets/algo-exp-note.assets/image-20210705145013124.png" alt="image-20210705145013124" style="zoom:33%;" />

总而言之，`minHeap`中存的是数组中值较大的那一半，它可以提供较大值中的最小值。`maxHeap`中存的是数组中值较小的那一半，它可以提供较小值中的最大值。这样我们就可以以`O(1)`的时间复杂度取得中位数。

本题的实现使用了Lambda表达式等java8新特性（需要仔细学习该编程方法）。

```java
package heaps.continuousmedian;

import java.util.ArrayList;
import java.util.List;
import java.util.function.BiFunction;

// Do not edit the class below except for
// the insert method. Feel free to add new
// properties and methods to the class.
class Program {
    static class ContinuousMedianHandler {
        Heap lowers = new Heap(Heap::MAX_HEAP_FUNC, new ArrayList<Integer>());
        Heap greaters = new Heap(Heap::MIN_HEAP_FUNC, new ArrayList<Integer>());
        double median = 0;
        // O(log(N)) time O(N) space
        public void insert(int number) {
            // lowers是大顶堆，比lowers.peak()小的数，一定在中位数左测
            if (lowers.length == 0 || number < lowers.peek())
                lowers.insert(number);
            // 比lowers.peak()大的数字都存到greaters，他们一定都在中位数的右侧
            else
                greaters.insert(number);
            // 发生不平衡，就需要将lowers的最大值给greaters
            // 或者将greaters的最小值给lowers
            rebalanceHeaps();
            updateMedian();
        }

        public void rebalanceHeaps() {
            if (lowers.length - greaters.length == 2)
                greaters.insert(lowers.remove());
            else if (greaters.length - lowers.length == 2)
                lowers.insert(greaters.remove());
        }

        public void updateMedian() {
            if (lowers.length == greaters.length)
                median = ((double) lowers.peek() + (double) greaters.peek()) / 2;
            else if (lowers.length > greaters.length)
                median = lowers.peek();
            else
                median = greaters.peek();
        }

        public double getMedian() {
            return median;
        }
    }

    //在Min Heap的基础上建立一个更加通用的Heap类
    static class Heap {
        List<Integer> heap = new ArrayList<Integer>();
        //使用BiFunction用以设置heap为Min或者Max
        BiFunction<Integer, Integer, Boolean> comparisionFunc;
        int length;

        public Heap(BiFunction<Integer, Integer, Boolean> func, List<Integer> array) {
            comparisionFunc = func;
            heap = buildHeap(array);
            length = heap.size();
        }

        public List<Integer> buildHeap(List<Integer> array) {
            int firstParentIdx = (array.size() - 2) / 2;
            for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
                siftDown(currentIdx, array.size() - 1, array);
            return array;
        }

        public void siftDown(int currentIdx, int endIdx, List<Integer> heap) {
            int childOneIdx = currentIdx * 2 + 1;
            while (childOneIdx <= endIdx) {
                int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
                //确定两个子节点哪一个更小(大)
                //更小（大）的有可能需要与当前节点互换
                int idxToSwap = 0;
                if (childTwoIdx != -1 && comparisionFunc.apply(heap.get(childTwoIdx), heap.get(childOneIdx)))
                    idxToSwap = childTwoIdx;
                else
                    idxToSwap = childOneIdx;

                //确定了更小（大）的子节点后，需要验证是否需要向下交换
                //若需要，则在swap后更新相应位置信息
                if (comparisionFunc.apply(heap.get(idxToSwap), heap.get(currentIdx))) {
                    swap(idxToSwap, currentIdx, heap);
                    currentIdx = idxToSwap;
                    childOneIdx = currentIdx * 2 + 1;
                } else {//否则，停止向下交换
                    return;
                }
            }
        }

        public void siftUp(int currentIdx, List<Integer> heap) {
            while (currentIdx > 0) {
                int parentIdx = (currentIdx - 1) / 2;
                if (comparisionFunc.apply(heap.get(currentIdx), heap.get(parentIdx)))
                    swap(currentIdx, parentIdx, heap);
                currentIdx = parentIdx;
            }
        }

        public int peek() {
            return heap.get(0);
        }

        public int remove() {
            swap(0, heap.size() - 1, heap);
            int valueToRemove = heap.get(heap.size() - 1);
            heap.remove(heap.size() - 1);
            length--;
            siftDown(0, heap.size() - 1, heap);
            return valueToRemove;
        }

        public void insert(int value) {
            heap.add(value);
            length++;
            siftUp(heap.size() - 1, heap);
        }


        public void swap(int i, int j, List<Integer> heap) {
            Integer temp = heap.get(i);
            heap.set(i, heap.get(j));
            heap.set(j, temp);
        }

        public static Boolean MAX_HEAP_FUNC(Integer a, Integer b) {
            return a > b;
        }

        public static Boolean MIN_HEAP_FUNC(Integer a, Integer b) {
            return a < b;
        }
    }
}


```

## 3.Sort K-Sorted Array

![image-20210705155755878](/assets/algo-exp-note.assets/image-20210705155755878.png)

![image-20210705155822244](/assets/algo-exp-note.assets/image-20210705155822244.png)

本题不同于经典的排序算法。本题设置了一个k值，该k值指的是数组中的某个位置与它排序之后的位置的距离<=k。从而，本题需要利用这一点解决排序问题。

我们可以建立一个空的数组output（和题目所给的array一样大）作为排序完成后的数组。简单的来说，对于output[0]来说，它的值就是array[0]->array[3]中的最小值；也就是说，我们只需要为output[i]找到array[i]周围k范围内的最小值就可以了。要完成这件事，我们可以用Min Heap。

如下图所示，我们要为index0找到排序后的数。我们将0，1，2，3处的数全部都加入到Min Heap中。

<img src="/assets/algo-exp-note.assets/image-20210705214228351.png" alt="image-20210705214228351" style="zoom:33%;" />

然后，我们将MIn Heap的peek取出（也就是最小值1）并填入index0处。之后，我们要为index1找到排序后得数，此时，我们需要将index4处的数加到Min Heap中。最后，index1处的值就是新的Min Heap中的peek（2）。之后的过程以此类推即可。

<img src="/assets/algo-exp-note.assets/image-20210705214143925.png" alt="image-20210705214143925" style="zoom:33%;" />

```java
import java.util.*;

class Program {

    public int[] sortKSortedArray(int[] array, int k) {
        //初始化一个Min Heap（只存入k范围内的数）
        List<Integer> heapValues = new ArrayList<Integer>();
        //这里Math.min(k + 1, array.length)防止k大于数组长度越界
        for (int i = 0; i < Math.min(k + 1, array.length); i++)
            heapValues.add(array[i]);
        MinHeap minHeapWithElements = new MinHeap(heapValues);

        //开始排序
        int nextIdxToInsertElement = 0;
        for (int idx = k + 1; idx < array.length; idx++) {
            int minElement = minHeapWithElements.remove();
            array[nextIdxToInsertElement] = minElement;
            nextIdxToInsertElement += 1;
            //动态更新Min Heap，为找到下一个Min value做准备
            int currentElement = array[idx];
            minHeapWithElements.insert(currentElement);
        }
        //当上述idx = array.length后，Min Heap已经没有新的内容可以加入
        //我们只需要将Min Heap中剩余的东西清空即可
        while (!minHeapWithElements.isEmpty()) {
            int minElement = minHeapWithElements.remove();
            array[nextIdxToInsertElement] = minElement;
            nextIdxToInsertElement += 1;
        }
        return array;
    }

    static class MinHeap {
        List<Integer> heap = new ArrayList<Integer>();

        public MinHeap(List<Integer> array) {
            heap = buildHeap(array);
        }

        public List<Integer> buildHeap(List<Integer> array) {
            int firstParentIdx = (array.size() - 2) / 2;
            for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
                siftDown(currentIdx, array.size() - 1, array);
            return array;
        }

        public void siftDown(int currentIdx, int endIdx, List<Integer> heap) {
            int childOneIdx = currentIdx * 2 + 1;
            while (childOneIdx <= endIdx) {
                int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
                //确定两个子节点哪一个更小
                //更小的有可能需要与当前节点互换
                int idxToSwap = 0;
                if (childTwoIdx != -1 && heap.get(childTwoIdx) < heap.get(childOneIdx))
                    idxToSwap = childTwoIdx;
                else
                    idxToSwap = childOneIdx;

                //确定了更小的子节点后，需要验证是否需要向下交换
                //若需要，则在swap后更新相应位置信息
                if (heap.get(idxToSwap) < heap.get(currentIdx)) {
                    swap(idxToSwap, currentIdx, heap);
                    currentIdx = idxToSwap;
                    childOneIdx = currentIdx * 2 + 1;
                } else {//否则，停止向下交换
                    return;
                }
            }
        }

        public void siftUp(int currentIdx, List<Integer> heap) {
            while (currentIdx > 0) {
                int parentIdx = (currentIdx - 1) / 2;
                if (heap.get(currentIdx) < heap.get(parentIdx))
                    swap(currentIdx, parentIdx, heap);
                currentIdx = parentIdx;
            }
        }

        public int peek() {
            return heap.get(0);
        }

        public int remove() {
            swap(0, heap.size() - 1, heap);
            int valueToRemove = heap.get(heap.size() - 1);
            heap.remove(heap.size() - 1);
            siftDown(0, heap.size() - 1, heap);
            return valueToRemove;
        }

        public void insert(int value) {
            heap.add(value);
            siftUp(heap.size() - 1, heap);
        }


        public void swap(int i, int j, List<Integer> heap) {
            Integer temp = heap.get(i);
            heap.set(i, heap.get(j));
            heap.set(j, temp);
        }

        public boolean isEmpty() {
            return heap.size() == 0;
        }
    }
}
```

## 4.Laptop Rentals

![image-20210713134000073](/assets/algo-exp-note.assets/image-20210713134000073.png)

![image-20210713134017243](/assets/algo-exp-note.assets/image-20210713134017243.png)

本题的思路如下，我们建立一个时间线，将不同的时间段整合到时间线上。通过时间段之间的重叠，我们可以确定在[i,i+1]时间段上所需要的最小的laptop数量。

<img src="/assets/algo-exp-note.assets/image-20210713142231243.png" alt="image-20210713142231243" style="zoom:33%;" />

- 方法一：可以先将所有的时间段（以start 为基准）。之后建立一个Min Heap，对于所有的时间段，如果B时间段的start<已经在堆中的A时间段的end，那么就将B加入Min Heap中；否则，B时间段将代替A时间段的位置（A被移出堆，B加入堆）。如下图所示，前三个时间段都需要一台电脑，当[3,10]这个时间段加入的时候，它可以接替[0,2]，从而[0,2]被移出，[3,10]被加入。

![image-20210713145407988](/assets/algo-exp-note.assets/image-20210713145407988.png)

<img src="/assets/algo-exp-note.assets/image-20210713145502022.png" alt="image-20210713145502022" style="zoom:25%;" />

本题为了适应题目，对之前的Min Heap做了相应的修改。

```java
import java.util.*;

class Program1 {

    public int laptopRentals(ArrayList<ArrayList<Integer>> times) {
        // Write your code here.
        if (times.size() == 0)
            return 0;
        //以times中的start为基准对times进行排序
        times.sort((a, b) -> Integer.compare(a.get(0), b.get(0)));

        ArrayList<ArrayList<Integer>> timesWhenLaptopIsUsed = new ArrayList<>();
        timesWhenLaptopIsUsed.add(times.get(0));
        //将第一个时间段放入堆中
        MinHeap heap = new MinHeap(timesWhenLaptopIsUsed);

        for (int i = 1; i < times.size(); i++) {
            ArrayList<Integer> currentInterval = times.get(i);
            //如果最小的结束时间<=当前时间段的开始时间（正在使用的电脑数-1）
            if (heap.peek().get(1) <= currentInterval.get(0))
                heap.remove();
            //无论如何新的任务都要加入到Min heap中
            heap.insert(currentInterval);
        }

        return timesWhenLaptopIsUsed.size();
    }

    static class MinHeap {
        ArrayList<ArrayList<Integer>> heap = new ArrayList<ArrayList<Integer>>();

        public MinHeap(ArrayList<ArrayList<Integer>> array) {
            heap = buildHeap(array);
        }

        public ArrayList<ArrayList<Integer>> buildHeap(ArrayList<ArrayList<Integer>> array) {
            int firstParentIdx = (array.size() - 2) / 2;
            for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--)
                siftDown(currentIdx, array.size() - 1, array);
            return array;
        }

        public void siftDown(int currentIdx, int endIdx, ArrayList<ArrayList<Integer>> heap) {
            int childOneIdx = currentIdx * 2 + 1;
            while (childOneIdx <= endIdx) {
                int childTwoIdx = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
                //确定两个子节点哪一个更小
                //更小的有可能需要与当前节点互换
                int idxToSwap = 0;
                if (childTwoIdx != -1 && heap.get(childTwoIdx).get(1) < heap.get(childOneIdx).get(1))
                    idxToSwap = childTwoIdx;
                else
                    idxToSwap = childOneIdx;

                //确定了更小的子节点后，需要验证是否需要向下交换
                //若需要，则在swap后更新相应位置信息
                if (heap.get(idxToSwap).get(1) < heap.get(currentIdx).get(1)) {
                    swap(idxToSwap, currentIdx, heap);
                    currentIdx = idxToSwap;
                    childOneIdx = currentIdx * 2 + 1;
                } else {//否则，停止向下交换
                    return;
                }
            }
        }

        public void siftUp(int currentIdx, ArrayList<ArrayList<Integer>> heap) {
            while (currentIdx > 0) {
                int parentIdx = (currentIdx - 1) / 2;
                if (heap.get(currentIdx).get(1) < heap.get(parentIdx).get(1))
                    swap(currentIdx, parentIdx, heap);
                currentIdx = parentIdx;
            }
        }

        public ArrayList<Integer> peek() {
            return heap.get(0);
        }

        public ArrayList<Integer> remove() {
            swap(0, heap.size() - 1, heap);
            ArrayList<Integer> valueToRemove = heap.get(heap.size() - 1);
            heap.remove(heap.size() - 1);
            siftDown(0, heap.size() - 1, heap);
            return valueToRemove;
        }

        public void insert(ArrayList<Integer> value) {
            heap.add(value);
            siftUp(heap.size() - 1, heap);
        }


        public void swap(int i, int j, ArrayList<ArrayList<Integer>> heap) {
            ArrayList<Integer> temp = heap.get(i);
            heap.set(i, heap.get(j));
            heap.set(j, temp);
        }

    }
}
```

- 方法二：本方法将start和end分别排序（如下图所示），然后用i，j两个指针分别对start和end数组进行遍历。如果start[i] < end[j]，那么，就需要一个笔记本来完成start[i] ->end[j]之间的任务，同时i需要指向下一个新的开始时间；如果start[i] >= end[j]，那么，证明已经有电脑空余出来供使用，同时j需要指向下一个新的结束时间；

<img src="/assets/algo-exp-note.assets/image-20210713151228271.png" alt="image-20210713151228271" style="zoom:33%;" />

```java
import java.util.*;

class Program {

    public int laptopRentals(ArrayList<ArrayList<Integer>> times) {
        // Write your code here.
        if (times.size() == 0)
            return 0;

        int usedLaptops = 0;
        ArrayList<Integer> startTimes = new ArrayList<>();
        ArrayList<Integer> endTimes = new ArrayList<>();

        for (ArrayList<Integer> interval : times) {
            startTimes.add(interval.get(0));
            endTimes.add(interval.get(1));
        }

        Collections.sort(startTimes);
        Collections.sort(endTimes);

        int startIterator = 0;
        int endIterator = 0;

        //此处的逻辑和用Min Heap的逻辑类似
        while (startIterator < times.size()) {
            if (startTimes.get(startIterator) >= endTimes.get(endIterator)) {
                usedLaptops -= 1;
                endIterator += 1;
            }

            usedLaptops += 1;
            startIterator += 1;
        }
        return usedLaptops;
    }
}
```

# Recursion

## 1.Nth Fibonacci(Green)

![image-20210713182823111](/assets/algo-exp-note.assets/image-20210713182823111.png)

<img src="/assets/algo-exp-note.assets/image-20210713182849695.png" alt="image-20210713182849695" style="zoom:75%;" />

- 方法一：recursion方法

该方法比较简单，看代码即可理解。本方法的缺点在于时间复杂度达到了2的n次方。

<img src="/assets/algo-exp-note.assets/image-20210717182847428.png" alt="image-20210717182847428" style="zoom: 33%;" />

```java
import java.util.*;
// O(2^N) time O(N) space
class Program {
    public static int getNthFib(int n) {
        // Write your code here.
        if (n == 2)
            return 1;
        else if (n == 1)
            return 0;
        else
            return getNthFib(n - 1) + getNthFib(n - 2);
    }
}
```

- 方法二：

从recursion的方法中我们可以看出，我们进行了大量的重复计算。所以，我们可以建立一个容器去存储已经被计算过的Fibonacci(n)，从而减少相应的时间复杂度。本方法将时间复杂度降低到了O(n)，但是空间复杂度依然较高。

```java
package recursion.nthfibonacci;

import java.util.*;
// O(N) time O(N) space
class Program1 {
    public static int getNthFib(int n) {
        Map<Integer, Integer> memorize = new HashMap<>();
        memorize.put(1, 0);
        memorize.put(2, 1);
        return getNthFib(n, memorize);
    }

    public static int getNthFib(int n, Map<Integer, Integer> memorize) {
        //如果已经计算过Fib(n)，直接返回他的值
        if (memorize.containsKey(n))
            return memorize.get(n);
        //如果没有计算过，计算他的值并写入memorize中
        else {
            memorize.put(n, getNthFib(n - 1, memorize) + getNthFib(n - 2, memorize));
            return memorize.get(n);
        }
    }
}
```

- 方法三：Iterative

本方法聚焦于Fib(n) = Fib(n-1) + Fib(n-2)，其实，对于Fib(n)来说，最重要的就是它前边的两个数。所以，我们只需要从[0,1]开始，不停的维护一个只有2个元素的数组。例如，我们要得到Fib(5)，首先我们从[0,1]开始,按照下述表格，计算出和，然后调整lastTwo，让他只存储Fib(n-1)、 Fib(n-2)的值。

| lastTwo | 和   | 新的lastTwo |
| ------- | ---- | :---------: |
| [0,1]   | 1    |    [1,1]    |
| [1,1]   | 2    |    [1,2]    |
| [1,2]   | 3    |    [2,3]    |
| [2,3]   | 5    |    [3,5]    |

```java
import java.util.*;
// O(N) time O(1) space
class Program2 {
    public static int getNthFib(int n) {
        int[] lastTwo = {0, 1};
        //已经有了Fib(1)、Fib(2)，所以计数从3开始
        int counter = 3;
        while (counter <= n) {
            int nextFib = lastTwo[0] + lastTwo[1];
            lastTwo[0] = lastTwo[1];
            lastTwo[1] = nextFib;
            counter++;
        }
        return n > 1 ? lastTwo[1] : lastTwo[0];
    }
}
```

## 2.Product Sum(Green)

![image-20210718143100847](/assets/algo-exp-note.assets/image-20210718143100847.png)

![image-20210718143137582](/assets/algo-exp-note.assets/image-20210718143137582.png)

本题采用recursion的方法思路十分简单。我们需要对array中的每个元素element进行审查，如果element只是一个数，那就直接把它加入到sum里边去；如果element是一个list，那么我们就对这个list调用本方法，直到list的所有内容都按照要求加入到sum中为止。

```java
import java.util.*;
// O(N) time O(d) space
class Program {
    // Tip: You can use `element instanceof ArrayList` to check whether an item
    // is an array or an integer.
    public static int productSum(List<Object> array) {
        // Write your code here.
        return productSumHelper(array, 1);
    }

    //multiplier用于更新[],[[]],[[[]]]对应的乘数1，2，3
    public static int productSumHelper(List<Object> array, int multiplier) {
        int sum = 0;
        for (Object element : array) {
            if (element instanceof ArrayList) {
                //如果array中出现了[[]，[]],那么需要对array中的list计算sum
                @SuppressWarnings("unchecked")
                ArrayList<Object> list = (ArrayList<Object>) element;
                sum += productSumHelper(list, multiplier + 1);
            } else {//element是一个int
                sum += (int) element;
            }
        }
        return multiplier * sum;
    }
}
```

## 3.Permutations 排列方式(Blue*)

![image-20210718165850252](/assets/algo-exp-note.assets/image-20210718165850252.png)

- 方法一：如下图的伪代码所示，当我们收到一个array时（eg:[1,2,3]）,我们需要提取出其中的一个数（本例中为1）占用第一个位置，然后我们就会得到一个newArray（[2,3]）；与此同时，我们也获得了一个新的组合的一部分newPerm[1, , ]。之后，我们继续调用本函数，对[2,3]进行相同的操作，提取其中的一个数追加到newPerm中占据第二个位置，当array中的数都被拿完时，我们就会获得[1,2,3]所具有的一种新的排列方式了。

<img src="/assets/algo-exp-note.assets/image-20210718171844753.png" alt="image-20210718171844753" style="zoom:33%;" />

```java
import java.util.*;
// Upper Bound: O(n^2*n!) time | O(n*n!) space
// Roughly: O(n*n!) time | O(n*n!) space
class Program {
    public static List<List<Integer>> getPermutations(List<Integer> array) {
        List<List<Integer>> permutations = new ArrayList<>();
        getPermutations(array, new ArrayList<Integer>(), permutations);
        return permutations;
    }

    public static void getPermutations(
            List<Integer> array, List<Integer> currentPermutation, List<List<Integer>> permutations) {
        //currentPermutation.size() > 0 否则：[[]]
        if (array.size() == 0 && currentPermutation.size() > 0)
            permutations.add(currentPermutation);
        else {
            for (Integer num : array) {
                //使用newArray，防止破坏array内容
                List<Integer> newArray = new ArrayList<>(array);
                newArray.remove(num);
                List<Integer> newPermutation = new ArrayList<>(currentPermutation);
                newPermutation.add(num);
                getPermutations(newArray, newPermutation, permutations);
            }
        }
    }
}
```

为什么时间复杂度的`upperbound`是`O(n^2*n!)`呢？因为在for循环中用我们用了`ArrayList`的remove函数，如果删除的不是尾部元素，那么每条分支就又多了调整数组的操作，这又要耗费O(N)。从下图也可以看出，这种题的本质其实是深度优先遍历。

<img src="/assets/algo-exp-note.assets/image-20220421183732727.png" alt="image-20220421183732727" style="zoom:50%;" />

- 方法二：（需要带入例子理解）

方法一的for循环使用了remove()函数，这个函数使得算法的时间复杂度不理想。方法二使用了swap()的设计，减少了对array的操作。



![image-20220421190051808](/assets/algo-exp-note.assets/image-20220421190051808.png)

<img src="/assets/algo-exp-note.assets/image-20220421191719341.png" alt="image-20220421191719341" style="zoom:50%;" />

```java
import java.util.*;
O(n*n!) time | O(n*n!) space
class Program {
    public static List<List<Integer>> getPermutations(List<Integer> array) {
        // Write your code here.
        List<List<Integer>> perms = new ArrayList<>();
        getPermutationsHelper(0, array, perms);
        return perms;
    }

    private static void getPermutationsHelper(int first, List<Integer> array, List<List<Integer>> perms) {
        //permutations.add(array);注意：这里要新建一个ArrayList，否则最终的结果都会是初始的array！！！！
        if (first == array.size() - 1)
            perms.add(new ArrayList<>(array));

        for (int i = first; i < array.size(); i++) {
            swap(first, i, array);
            getPermutationsHelper(first + 1, array, perms);
            swap(first, i, array);
        }
    }

    public static void swap(int i, int j, List<Integer> array) {
        int temp = array.get(i);
        array.set(i, array.get(j));
        array.set(j, temp);
    }
}
```

## 4.Powerset 幂集(Blue)

![image-20210719141643508](/assets/algo-exp-note.assets/image-20210719141643508.png)

本题的思路如下所示，首先我们会初始化一个有空集的subsets=[[]]；然后，我们读入第一个元素1，让他和subsets内的所有子集结合，从而subset=[[],[1]]；接下来，2被读入，重复之前的操作，我们就又获得了[2]和[1,2];以此类推，我们就可以得到所有的子集。

<img src="/assets/algo-exp-note.assets/image-20210719142429978.png" alt="image-20210719142429978" style="zoom: 33%;" />

- 方法一：recursive

本方法是方法二的逆向工程，也就是说要知道[1,2,3]的所有子集就需要先知道[1,2]的所有子集，最后把3依次和[1,2]的所有子集合并加入即可。

<img src="/assets/algo-exp-note.assets/image-20220422160422932.png" alt="image-20220422160422932" style="zoom:50%;" />

```java
import java.util.*;
// O(n*2^n) time | O(n*2^n) space
class Program1 {
    public static List<List<Integer>> powerset(List<Integer> array) {
        return powerset(array, array.size() - 1);
    }

    public static List<List<Integer>> powerset(List<Integer> array, int idx) {
        //[[]]
        if (idx < 0) {
            List<List<Integer>> emptySet = new ArrayList<>();
            emptySet.add(new ArrayList<>());
            return emptySet;
        }
        //逆向工程，从最后一个element开始
        int element = array.get(idx);
        List<List<Integer>> subsets = powerset(array, idx - 1);
        int length = subsets.size();
        for (int i = 0; i < length; i++) {
            List<Integer> currentSubset = new ArrayList<>(subsets.get(i));
            currentSubset.add(element);
            subsets.add(currentSubset);
        }
        return subsets;
    }
}
```

- 方法二（推荐）：iterative

```java
import java.util.*;
// O(n*2^n) time | O(n*2^n) space
class Program {
    public static List<List<Integer>> powerset(List<Integer> array) {
        List<List<Integer>> subsets = new ArrayList<>();
        subsets.add(new ArrayList<>());
        for (Integer element : array) {
            //需要先把length取出来，因为在for循环中subsets.size()会扩大从而进入死循环。
            int length = subsets.size();
            for (int i = 0; i < length; i++) {
                List<Integer> currentSubset = new ArrayList<>(subsets.get(i));
                currentSubset.add(element);
                subsets.add(currentSubset);
            }
        }
        return subsets;
    }
}
```

- Note

  这里使用了两个`foreach`, 操纵集合，但会出现报错:`java.util.ConcurrentModificationException`。原因是`foreach`底层调用的是iterator对数组进行处理，在本例中，iterator对powerset进行遍历，然而在遍历的过程中，powerset本身发生了改变，其中加入了新的内容。这时，iterator本身却没有被更新，它对应的还是未经改变的powerset的镜像。从而出现该报错。解决的办法就是像方法二一样，用最朴素的方式遍历数组。

  ```java
  import java.util.*;

  class Program {
      public static List<List<Integer>> powerset(List<Integer> array) {
          // Write your code here.
          List<List<Integer>> powerset = new ArrayList<>();
          powerset.add(new ArrayList<>());

          for (int currentValue : array) {
              for (List<Integer> subset : powerset) {
                  List<Integer> newSubset = new ArrayList<>(subset);
                  newSubset.add(currentValue);
                  powerset.add(newSubset);
              }
          }
          return powerset;
      }
  }
  ```



## 5.Phone Number Mnemonics(Blue)

<img src="/assets/algo-exp-note.assets/image-20210719150955803.png" alt="image-20210719150955803"  />

![image-20210719151023956](/assets/algo-exp-note.assets/image-20210719151023956.png)

![image-20210719151322644](/assets/algo-exp-note.assets/image-20210719151322644.png)

本题的目的是要找出所有的mnemonics（助记符号）。本题的思路就是对于每一个数字都去遍历它所能代表的字母即可, 本质上本题就是深度优先遍历。

<img src="/assets/algo-exp-note.assets/image-20220422162254835.png" alt="image-20220422162254835" style="zoom:50%;" />

```java
import java.util.*;

class Program {

    public static Map<Character, String[]> DIGIT_LETTERS = new HashMap<>();

    static {
        DIGIT_LETTERS.put('0', new String[]{"0"});
        DIGIT_LETTERS.put('1', new String[]{"1"});
        DIGIT_LETTERS.put('2', new String[]{"a", "b", "c"});
        DIGIT_LETTERS.put('3', new String[]{"d", "e", "f"});
        DIGIT_LETTERS.put('4', new String[]{"g", "h", "i"});
        DIGIT_LETTERS.put('5', new String[]{"j", "k", "l"});
        DIGIT_LETTERS.put('6', new String[]{"m", "n", "o"});
        DIGIT_LETTERS.put('7', new String[]{"p", "q", "r", "s"});
        DIGIT_LETTERS.put('8', new String[]{"t", "u", "v"});
        DIGIT_LETTERS.put('9', new String[]{"w", "x", "y", "z"});
    }

    public ArrayList<String> phoneNumberMnemonics(String phoneNumber) {
        String[] currentMnemonic = new String[phoneNumber.length()];
        Arrays.fill(currentMnemonic, "0");

        ArrayList<String> mnemonicsFound = new ArrayList<>();
        phoneNumberMnemonicsHelper(0, phoneNumber, currentMnemonic, mnemonicsFound);
        return mnemonicsFound;
    }

    public void phoneNumberMnemonicsHelper(
            int idx, String phoneNumber, String[] currentMnemonic, ArrayList<String> mnemonicsFound
    ) {
        //此时，一个mnemonic已经生成
        if (idx == phoneNumber.length()) {
            String mnemonic = String.join("", currentMnemonic);
            mnemonicsFound.add(mnemonic);
        } else {//要为当前的数字替换为字母
            char digit = phoneNumber.charAt(idx);
            String[] letters = DIGIT_LETTERS.get(digit);
            for (String letter : letters) {
                currentMnemonic[idx] = letter;
                //确定当前digit的字母后，再对下一个数字进行同样的操作
                phoneNumberMnemonicsHelper(idx + 1, phoneNumber, currentMnemonic, mnemonicsFound);
            }
        }
    }
}
```

## 6.Staircase Traversal(Blue*)

![image-20210719162110161](/assets/algo-exp-note.assets/image-20210719162110161.png)

从下图的例子可以看出，以2为maxSteps，跳到各个台阶所有的方法为1，1，2，3，5；不难发现，ways(n) = ways(n-1) + ways(n - 2)

因为2、3号台阶可以一步直接跳到4，所以把到达2和3的所有方法加起来就可以得到到达4的所有方法。

<img src="/assets/algo-exp-note.assets/image-20210728201120961.png" alt="image-20210728201120961" style="zoom:33%;" />

ways(n) = ways(n-1) + ways(n - 2) + ways(n - 3)

<img src="/assets/algo-exp-note.assets/image-20210728201521301.png" alt="image-20210728201521301" style="zoom:33%;" />

- 方法一：

```java
import java.util.*;
// O(k^n) time | O(n) space - where n is the height of the staircase and k is the number of
// allowed steps
class Program {

    public int staircaseTraversal(int height, int maxSteps) {
        return numberOfWaysToTop(height, maxSteps);
    }

    public int numberOfWaysToTop(int height, int maxSteps) {
        //初始化0，1台阶
        if (height <= 1)
            return 1;

        int numberOfWays = 0;
        //对于>1的台阶向前遍历，加入maxSteps可以涵盖到的所有位置
        for (int step = 1; step < Math.min(maxSteps, height) + 1; step++) {
            numberOfWays += numberOfWaysToTop(height - step, maxSteps);
        }
        return numberOfWays;
    }
}
```

本题的复杂度分析如下所示。

<img src="/assets/algo-exp-note.assets/image-20210728203332613.png" alt="image-20210728203332613" style="zoom:33%;" />

- 方法二：

可以看到，方法一有许多的多于计算了的recursive，可以用容器Map减少多余的计算。

![image-20210728203926871](/assets/algo-exp-note.assets/image-20210728203926871.png)

- 方法三：iterative

我们可以借助如下所示的数组，ways[n] = ways[n-1] + ways[n - 2]

<img src="/assets/algo-exp-note.assets/image-20210728204614565.png" alt="image-20210728204614565" style="zoom:33%;" />

![image-20210728204805302](/assets/algo-exp-note.assets/image-20210728204805302.png)

- 方法四(*)：

方法四采用了滑动窗口的思路。如下图所示，cur记录着到达当前Height的所有方法数，[ .... ]中记录到达每一个Height所需要的方法总数。本例中maxSteps只有2，所以我们只用维护一个大小为2的滑动窗口即可。

<img src="/assets/algo-exp-note.assets/image-20210728210319332.png" alt="image-20210728210319332" style="zoom:33%;" />

![image-20210728210709208](/assets/algo-exp-note.assets/image-20210728210709208.png)

## 7.Lowest Common Manager(Red)

![image-20210728224810548](/assets/algo-exp-note.assets/image-20210728224810548.png)

![image-20210728224719801](/assets/algo-exp-note.assets/image-20210728224719801.png)

本题的思路类似于Tree类的题目: 寻找最小的公共`parentNode`, 然而，那一道题，每个节点保存了一个指向父节点的指针，从而可以分别确定两个节点的深度，然后统一深度并向着父节点回溯。本题的节点并不具备这个能力，从而采用了一种不同的方法。

本题的方法展示如下，I和P是输入的reportNode。算法会对整个Tree进行遍历，采用深度优先遍历，并对每个节点进行标号，每个节点的值等于其子节点之和，若当前节点为reportNode那么就再+1，当找到了一个值为2的节点时，该节点就是我们要找的lowest common manager。

<img src="/assets/algo-exp-note.assets/image-20210728230255674.png" alt="image-20210728230255674" style="zoom:33%;" />

```java
import java.util.*;
// O(N) time O(d) space
class Program {
    public static OrgChart getLowestCommonManager(
            OrgChart topManager, OrgChart reportOne, OrgChart reportTwo) {
        return getOrgInfo(topManager, reportOne, reportTwo).lowestCommonManager;
    }

    public static OrgInfo getOrgInfo
            (OrgChart manager, OrgChart reportOne, OrgChart reportTwo) {
        int numImportantReports = 0;
        //从topManager开始向下遍历，记录每一个节点的orgInfo
        for (OrgChart directReport : manager.directReports) {
            OrgInfo orgInfo = getOrgInfo(directReport, reportOne, reportTwo);
            //已经找到了lowestCommonManager，向上一个manager传递即可
            if (orgInfo.lowestCommonManager != null)
                return orgInfo;
            //若还未找到，将子节点已经得到的orgInfo加入当前节点的记录中
            numImportantReports += orgInfo.numImportantReports;
        }
        //完成子节点的统计后，如果当前节点本身是重要report，增加重要report个数
        if (manager == reportOne || manager == reportTwo)
            numImportantReports++;
        //为当前节点生成OrgInfo
        OrgChart lowestCommonManager = numImportantReports == 2 ? manager : null;
        return new OrgInfo(lowestCommonManager, numImportantReports);
    }

    //新建一个类OrgInfo用于记录各个节点的情况
    static class OrgInfo {
        public OrgChart lowestCommonManager;
        int numImportantReports;

        public OrgInfo(OrgChart lowestCommonManager, int numImportantReport) {
            this.lowestCommonManager = lowestCommonManager;
            this.numImportantReports = numImportantReport;
        }
    }

    static class OrgChart {
        public char name;
        public List<OrgChart> directReports;

        OrgChart(char name) {
            this.name = name;
            this.directReports = new ArrayList<OrgChart>();
        }

        // This method is for testing only.
        public void addDirectReports(OrgChart[] directReports) {
            for (OrgChart directReport : directReports) {
                this.directReports.add(directReport);
            }
        }
    }
}
```

## 8.Interweaving Strings(Red*)

![image-20210729100832990](/assets/algo-exp-note.assets/image-20210729100832990.png)

本题通过遍历three字符串中的字符，用栈保存每一种可能的情况的状态。因为three中的每一个字符不是在one中就是在two中，当我们发现存在不满足这个条件的字符时，就可以返回false。本题的难点在于细节的考虑。

- 方法一：Time: O(2^(n+m))

如果遇到下图所示情况，one=[a,a,a,a],two=[a,a,a,a],两个String之间就会有下图所示的各种可能有的联系。这里的Time是极端情况下的数值。

<img src="/assets/algo-exp-note.assets/image-20210729120845765.png" alt="image-20210729120845765" style="zoom:33%;" />

本题可以通过带入例子理解。

```java
import java.util.*;
// O(2^(n + m)) time | O(n + m) space - where n is the length
// of the first string and m is the length of the second string
class Program {
    public static boolean interweavingStrings(String one, String two, String three) {
        // 一定要判断长度是否一致，否则也会得出错误结果,例如：
        /**
        {
          "one": "algoexpert",
          "three": "your-algodream-expertjo",
          "two": "your-dream-job"
		}
		**/
        if (one.length() + two.length() != three.length())
            return false;
        return areInterwoven(one, two, three, 0, 0);
    }

    //i是指向one的指针，j是指向two的指针
    public static boolean areInterwoven
    (String one, String two, String three, int i, int j) {
        //k代表着当前已经完成验证的three字符串部分的长度
        int k = i + j;
        if (k == three.length())
            return true;
        //若one[i]里的字符和three[k]符合
        //那么就更新i = i + 1，并按照这种匹配的路线继续探索下去
        if (i < one.length() && one.charAt(i) == three.charAt(k)) {
            if (areInterwoven(one, two, three, i + 1, j))
                return true;
        }
        //同理
        if (j < two.length() && two.charAt(j) == three.charAt(k)) {
            return areInterwoven(one, two, three, i, j + 1);
        }
        //如果都不符合，那么返回错误结果
        return false;
    }
}
```

注意：可能会忽视的细节！！！

```java
		//若one[i]里的字符和three[k]符合
        //那么就更新i = i + 1，并按照这种匹配的路线继续探索下去
        if (i < one.length() && one.charAt(i) == three.charAt(k)) {
            if (areInterwoven(one, two, three, i + 1, j))
                return true;
        }

		//错误写法：
		if (i < one.length() && one.charAt(i) == three.charAt(k)) {
            return areInterwoven(one, two, three, i + 1, j)
        }
		// 如果i的这一条路走不通，我们就直接返回false，那么他之后j的另一条路就无法被执行验证了
		// 从而，我们会得出错误的答案
		// 所以，为了让所有的情况都被考虑到，当i是false是我们什么都不返回，继续去探寻j的路线。
```

- 方法二：与之前的recursion问题类似，本题在recursion的过程中，也做了许多重复的计算，改进的方法就是使用cache，对于已经在cache中的结果，不再进行重复的计算。

![image-20210729121836558](/assets/algo-exp-note.assets/image-20210729121836558.png)

## 9.Solve Sudoku(Red)

![image-20210729122237820](/assets/algo-exp-note.assets/image-20210729122237820.png)

![image-20210729122253501](/assets/algo-exp-note.assets/image-20210729122253501.png)

对于九宫格中空白的部分，我们一开始只需要对所有的空白部分从1-9赋值，直到找到第一个满足条件的值即可。当我们发现一个空格的值已经达到了9但是还是不满足情况时（我们先将该空白设置为0），我们就要向前回溯因为肯定是之前填的数字出现了问题。当不再有问题时，回溯结束，我们将继续向后更新空白的部分。`solvePartialSuduko(rol, col) -> tryDigitsAtPostion(row, col) -> solvePartialSuduko(rol, col + 1) -> tryDigitsAtPostion(row, col + 1) ->..........   `。因为，我们的数独一定是可解的，所以在回溯的过程中，一定不会回溯到最初开始的地方也找不到合适的答案。

```java
import java.util.*;

class Program {
    public ArrayList<ArrayList<Integer>> solveSudoku(ArrayList<ArrayList<Integer>> board) {
        solvePartialSudoku(0, 0, board);
        return board;
    }

    public boolean solvePartialSudoku(int row, int col, ArrayList<ArrayList<Integer>> board) {
        int currentRow = row;
        int currentCol = col;
        //若col的值已经越界，说明上一行已经全部完成了数独
        //重置坐标至下一行
        if (currentCol == board.get(currentRow).size()) {
            currentRow++;
            currentCol = 0;
            //已经到的最底，数独已经完成
            if (currentRow == board.size())
                return true;
        }
        //若还没有给当前位置填入数字，进行数字的填入
        if (board.get(currentRow).get(currentCol) == 0) {
            return tryDigitsAtPosition(currentRow, currentCol, board);
        }
        //对下一个position进行处理
        return solvePartialSudoku(currentRow, currentCol + 1, board);
    }

    public boolean tryDigitsAtPosition(int row, int col, ArrayList<ArrayList<Integer>> board) {
        for (int digit = 1; digit < 10; digit++) {
            //当前位置没有问题，插入digit
            if (isValidPosition(digit, row, col, board)) {
                board.get(row).set(col, digit);
                //继续对下一个position进行处理
                if (solvePartialSudoku(row, col + 1, board))
                    return true;
            }
        }
        //若上一步中对下一个position的处理发生错误（没有return true）
        // 重置当前位置的信息
        board.get(row).set(col, 0);
        return false;
    }

    //判断新加入的value是否满足数独的要求
    public boolean isValidPosition(
            int value, int row, int col, ArrayList<ArrayList<Integer>> board) {
        //确定在当前位置的横向和纵向上所有的数字是否=value
        boolean rowIsValid = !board.get(row).contains(value);
        boolean colIsValid = true;
        for (int rowIdx = 0; rowIdx < board.size(); rowIdx++) {
            if (board.get(rowIdx).get(col) == value) {
                colIsValid = false;
            }
        }
        if (!rowIsValid || !colIsValid)
            return false;

        //确定value与当前位置所在的小格子是否存在重复
        //确定当前位置所在的小格子的的起始行和起始列
        int subgridRowStart = (row / 3) * 3;
        int subgridColStart = (col / 3) * 3;

        for (int rowIdx = 0; rowIdx < 3; rowIdx++)
            for (int colIdx = 0; colIdx < 3; colIdx++) {
                int rowToCheck = subgridRowStart + rowIdx;
                int colToCheck = subgridColStart + colIdx;
                int existingValue = board.get(rowToCheck).get(colToCheck);

                if (existingValue == value)
                    return false;
            }
        //经过所有检测皆无等于value的地方
        return true;
    }
}
```

## 10.Generate `Div` Tags(Red)

![image-20210729163404220](/assets/algo-exp-note.assets/image-20210729163404220.png)

下图展示了一个Tags=2时的算法执行过程，我们使用的递归函数（recursive function）的结构为：rec（prefix，opening，closing）。

整个recursion的调用过程如下图所示，简单来说，当prefix中加入了opening时，下一次递归可以继续加入opening或者加入一个closing；我们只需要在递归函数中控制添加的逻辑即可。

本题的Time需要数学公式推导，Catalan number。

<img src="/assets/algo-exp-note.assets/image-20210730110035682.png" alt="image-20210730110035682" style="zoom: 33%;" />

```java
import java.util.*;
// O((2n)!/((n!((n + 1)!)))) time | O((2n)!/((n!((n + 1)!)))) space -
// where n is the input number
class Program {

    public ArrayList<String> generateDivTags(int numberOfTags) {
        ArrayList<String> result = new ArrayList<>();
        generateDivTagsFromPrefix(numberOfTags, numberOfTags, "", result);
        return result;
    }

    public void generateDivTagsFromPrefix(
            int openingTagsNeeded, int closingTagsNeeded, String prefix, ArrayList<String> result
    ) {
        //若还有剩余的<div>可以添加，那么就添加
        if (openingTagsNeeded > 0) {
            String newPrefix = prefix + "<div>";
            generateDivTagsFromPrefix(openingTagsNeeded - 1, closingTagsNeeded, newPrefix, result);
        }
        //若剩余的<div>数量小于</div>，那么证明我们可以添加</div>
        if (openingTagsNeeded < closingTagsNeeded) {
            String newPrefix = prefix + "</div>";
            generateDivTagsFromPrefix(openingTagsNeeded, closingTagsNeeded - 1, newPrefix, result);
        }

        if (closingTagsNeeded == 0)
            result.add(prefix);
    }
}
```

## 11.Ambiguous Measurements(Red)

> This problem deals with measuring cups that are missing important measuring labels. Specifically, a measuring cup only has two measuring lines, a Low (L) line and a High (H) line. This means that these cups can't precisely measure and can only guarantee that the substance poured into them will be between the L and H line. For example, you might have a measuring cup that has a Low line at `400ml` and a high line at `435ml`. This means that when you use this measuring cup, you can only be sure that what you're measuring is between `400ml` and `435ml`.
>
> You're given a list of measuring cups containing their low and high lines as well as one low integer and one high integer representing a range for a target measurement. Write a function that returns a `boolean` representing whether you can use the cups to accurately measure a volume in the specified `[low, high] ` range (the range is inclusive).
>
> Note that:
>
> - Each measuring cup will be represented by a pair of positive integers `[L, H]`, where `0 <= L <= H`.
> - You'll always be given at least one measuring cup, and the `low` and `high` input parameters will always satisfy the following constraint: `0 <= low <= high`.
> - Once you've measured some liquid, it will immediately be transferred to a larger bowl that will eventually (possibly) contain the target measurement.
> - You can't pour the contents of one measuring cup into another cup.
>
> ### Sample Input
>
> ```json
> measuringCups = [
>   [200, 210],
>   [450, 465],
>   [800, 850],
> ]
> low = 2100
> high = 2300
> ```
>
> ### Sample Output
>
> ```
> true
> // We use cup [450, 465] to measure four volumes:
> // First measurement: Low = 450, High = 465
> // Second measurement: Low = 450 + 450 = 900, High = 465 + 465 = 930
> // Third measurement: Low = 900 + 450 = 1350, High = 930 + 465 = 1395
> // Fourth measurement: Low = 1350 + 450 = 1800, High = 1395 + 465 = 1860
>
> // Then we use cup [200, 210] to measure two volumes:
> // Fifth measurement: Low = 1800 + 200 = 2000, High = 1860 + 210 = 2070
> // Sixth measurement: Low = 2000 + 200 = 2200, High = 2070 + 210 = 2280
>
> // We've measured a volume in the range [2200, 2280].
> // This is within our target range, so we return `true`.
>
> // Note: there are other ways to measure a volume in the target range.
> ```

![image-20220514165311789](/assets/algo-exp-note.assets/image-20220514165311789.png)

```java
import java.util.*;
// O(low * high * n) time | O(low * high) space - where n is the number of measuring cups
class Program {

    public boolean ambiguousMeasurements(int[][] measuringCups, int low, int high) {
        Map<String, Boolean> memory = new HashMap<>();
        return canMeasureInRange(measuringCups, memory, low, high);
    }

    private boolean canMeasureInRange(int[][] measuringCups, Map<String, Boolean> memory, int low, int high) {
        String key = createHashtableKey(low, high);
        if (memory.containsKey(key)) {
            return memory.get(key);
        }

        if (low <= 0 && high <= 0) {
            return false;
        }

        boolean canMeasure = false;
        for (int[] measuringCup : measuringCups) {
            int cupLow = measuringCup[0];
            int cupHigh = measuringCup[1];

            // [low, cupLow, cupHigh, high]:说明经过多次recursion后，当前的这个cup可以保证量出在
            // [low , high]区间的水量
            if (low <= cupLow && cupHigh <= high) {
                canMeasure = true;
                break;
            }

            int newLow = Math.max(0, low - cupLow);
            int newHigh = Math.max(0, high - cupHigh);
            canMeasure = canMeasureInRange(measuringCups, memory, newLow, newHigh);
            // 当发现可行的路径后，不再进行对所有cup的尝试，一直回溯到根节点
            if(canMeasure) break;
        }

        memory.put(key, canMeasure);
        return canMeasure;
    }

    private String createHashtableKey(int low, int high) {
        return String.valueOf(low) + ":" + String.valueOf(high);
    }

}
```

# Searching

## 1.Binary Search(Green)

![image-20210730112957348](/assets/algo-exp-note.assets/image-20210730112957348.png)

设定left，right指针，并通过left和right获得mid指针，将target与array[mid]进行比较，从而可以确定目标值在mid指针的左侧还是右侧。具体实现见代码。

<img src="/assets/algo-exp-note.assets/image-20210730172249539.png" alt="image-20210730172249539" style="zoom:33%;" />

<img src="/assets/algo-exp-note.assets/image-20210730172652617.png" alt="image-20210730172652617" style="zoom:33%;" />

- 方法一：recursion

```java
import java.util.*;
// O(log(N)) time O(log(N)) space
class Program {
    public static int binarySearch(int[] array, int target) {
        return binarySearch(array, target, 0, array.length - 1);
    }

    public static int binarySearch(int[] array, int target, int left, int right) {
        int mid = (left + right) / 2;
        if (left > right)
            return -1;
        if (array[mid] == target)
            return mid;
        else if (array[mid] > target)
            return binarySearch(array, target, left, mid - 1);
        else //array[mid] < target
            return binarySearch(array, target, mid + 1, right);
    }
}
```

- 方法二：iteration

```java
import java.util.*;
// O(log(N)) time O(1) space
class Program1 {
    public static int binarySearch(int[] array, int target) {
        return binarySearch(array, target, 0, array.length - 1);
    }

    public static int binarySearch(int[] array, int target, int left, int right) {
        while (left <= right) {
            int mid = (left + right) / 2;
            if (array[mid] == target)
                return mid;
            if (array[mid] > target)
                right = mid - 1;
            else
                left = mid + 1;
        }
        return -1;
    }
}
```

本题时间复杂度的证明：

二分查找最差的情况就是分到无法再分，也就是分割到只有一个元素。假设我们需要x次才能分割到只有到一个元素，那么`N/(2^x) = 1`, 所以`x = log2(N)`.

## 2.Find Three Largest Number(Green)

![image-20210730180002553](/assets/algo-exp-note.assets/image-20210730180002553.png)

如下图所示，本题会维护一个大小为三的数组threeLargest，通过遍历整个array，不停地修改threeLargest完成题目要求。具体的逻辑见代码。

<img src="/assets/algo-exp-note.assets/image-20210730195414929.png" alt="image-20210730195414929" style="zoom:33%;" />

```java
import java.util.*;
// O(N) time O(1) space
class Program {
    public static int[] findThreeLargestNumbers(int[] array) {
        int[] threeLargest = {Integer.MIN_VALUE, Integer.MIN_VALUE, Integer.MIN_VALUE};
        for (int num : array)
            updateLargest(threeLargest, num);
        return threeLargest;
    }

    //依次将num与threeLargest中的最大值、次大值、最小值进行比较
    //并将他们插入合适的位置（由shiftAndUpdate完成）
    public static void updateLargest(int[] threeLargest, int num) {
        if (num > threeLargest[2])
            shiftAndUpdate(threeLargest, num, 2);
        else if (num > threeLargest[1])
            shiftAndUpdate(threeLargest, num, 1);
        else if (num > threeLargest[0])
            shiftAndUpdate(threeLargest, num, 0);
    }

    //对threeLargest数组进行移动和更新
    public static void shiftAndUpdate(int[] array, int num, int idx) {
        for (int i = 0; i <= idx; i++) {
            //到达了需要替换的坐标，替换坐标处的值
            if (i == idx)
                array[i] = num;
                //对于idx之前的值，需要将它们整体前移
            else
                array[i] = array[i + 1];
        }
    }
}
```

## 3.Search In Sorted Matrix(Blue)

![image-20210730201259315](/assets/algo-exp-note.assets/image-20210730201259315.png)

本题的思路是从右上角开始，向内部一层一层地排除其他可能性，缩小范围，直到找到target或完成所有遍历返回-1。绿色的圈是我们走过的所有节点，当target<当前节点时，当前节点以下的部分将被划去；当target > 当前节点时，当前节点左侧的部分将被划去。

<img src="/assets/algo-exp-note.assets/image-20210730202426842.png" alt="image-20210730202426842" style="zoom:33%;" />

当target=99时，算法的执行过程，由此可以看出Time=N+M

<img src="/assets/algo-exp-note.assets/image-20210730203130776.png" alt="image-20210730203130776" style="zoom:33%;" />

```java
import java.util.*;
// O(N + M) time O(1) space
class Program {
    public static int[] searchInSortedMatrix(int[][] matrix, int target) {
        int row = 0;
        int col = matrix[0].length - 1;
        //在不超出边界的情况下
        while (row < matrix.length && col >= 0) {
            if (target < matrix[row][col])
                col--;
            else if (target > matrix[row][col])
                row++;
            else
                return new int[]{row, col};
        }
        return new int[]{-1, -1};
    }
}
```

## 4.Shift Binary Search(Red*)

![image-20210730222550491](/assets/algo-exp-note.assets/image-20210730222550491.png)

本题的基本思路和binary Search相似，也需要left和right两个指针，当然两个指针的移动需要更多的逻辑判断。本题通过left和right指针可以将原数组分为递增的和部分递增的两部分，从而可以解决问题。

若array[left] <= array[middle]:（说明L-M之间是递增的）

​			 若array[left]<=target<array[middle]（target在L-M之间）

​			 那么，向middle的左侧继续寻找

​			若target在M-R之间

​			那么向middle的右侧继续寻找

若array[left] > array[middle]:(说明M-R之间是递增的)

​			若array[middle]<target<=array[right]（target在M-R之间）

​			那么，向middle的右侧继续寻找

​			若target在L-M之间

​			那么，向middle的左侧继续寻找

<img src="/assets/algo-exp-note.assets/image-20210731144006322.png" alt="image-20210731144006322" style="zoom:33%;" />

- 方法一：recursion

```java
import java.util.*;
// O(log(N)) time O(1) space
class Program {
    public static int shiftedBinarySearch(int[] array, int target) {
        return shiftedBinarySearch(array, target, 0, array.length - 1);
    }

    public static int shiftedBinarySearch(int[] array, int target, int left, int right) {
        if (left > right)
            return -1;
        int mid = (left + right) / 2;
        int leftNum = array[left];
        int rightNum = array[right];
        int midNum = array[mid];

        if (target == midNum)
            return mid;
        else if (leftNum <= midNum) {//1.说明L-M之间是递增的
            if (leftNum <= target && target < midNum)//1.1target在L-M之间
                return shiftedBinarySearch(array, target, left, mid - 1);
            else//1.2target在M-R之间
                return shiftedBinarySearch(array, target, mid + 1, right);
        } else {//2.M-R之间是递增的
                if (midNum < target && target <= rightNum)//2.1target在M-R之间
                    return shiftedBinarySearch(array, target, mid + 1, right);
                else//2.2target在L-M之间
                    return shiftedBinarySearch(array, target, left, mid - 1);
        }
    }
}
```

- 方法二：iterative

![image-20210731152553480](/assets/algo-exp-note.assets/image-20210731152553480.png)

本题对输入的数组有一定的要求：

![image-20210731150508002](/assets/algo-exp-note.assets/image-20210731150508002.png)

## 5.Search For Range(Red*)

![image-20210731153630955](/assets/algo-exp-note.assets/image-20210731153630955.png)

本题仍然需要left和right两个指针，通过mid指针定位答案。要解决本题，我们首先要寻找target在array中第一次出现的位置，由于本题的特殊性，在确定了第一个target后，我们需要分别向左侧和右侧展开搜寻。**本题是通过二分查找的方式来确定左右边界的，从而可以让时间复杂度保持在`O(log(N))`.**

本算法的细节需要根据代码带入例子理解。简单的来说，当array[mid] == target时，若array[mid-1] != target那么我们就找到了range的左边界；若array[mid+1] != target那么我们就找到了range的右边界；若array[mid]的左右两侧还有值=target，那么我们就要向左/右定位新的边界。

<img src="/assets/algo-exp-note.assets/image-20210731203342483.png" alt="image-20210731203342483" style="zoom:33%;" />

- 方法一：iterative

```java
import java.util.*;
// O(log(N)) time O(1) space
class Program {
    public static int[] searchForRange(int[] array, int target) {
        int[] finalRange = {-1, -1};
        //找到左边界值
        alteredBinarySearch(array, target, 0, array.length - 1, finalRange, true);
        //找到右边界值
        alteredBinarySearch(array, target, 0, array.length - 1, finalRange, false);
        return finalRange;
    }

    public static void alteredBinarySearch(
            int[] array, int target, int left, int right, int[] finalRange, boolean goLeft
    ) {
        while (left <= right) {
            int mid = (left + right) / 2;
            //寻找array[mid] = target
            if (array[mid] < target)
                left = mid + 1;
            else if (array[mid] > target)
                right = mid - 1;
            else {//找到了array[mid] == target
                if (goLeft) {//向左找最小值
                    if (mid == 0 || array[mid - 1] != target) {
                        finalRange[0] = mid;
                        return;
                    } else {//左侧还能找到更小值,继续用二分查找定位这个值
                        right = mid - 1;
                    }
                } else {//向右找最大值
                    if (mid == array.length - 1 || array[mid + 1] != target) {
                        finalRange[1] = mid;
                        return;
                    } else {//右侧还能找到更大值，继续用二分查找定位这个值
                        left = mid + 1;
                    }
                }
            }
        }
    }
}
```

- 方法二：recursion

![image-20210731210833199](/assets/algo-exp-note.assets/image-20210731210833199.png)

## 6.Quickselect(Red)

![image-20210801103553181](/assets/algo-exp-note.assets/image-20210801103553181.png)

本题类似于quick sort的排序过程，我们确立一个pivot，通过left和right指针将其他数组元素中小于等于pivot的放到pivot的左边，把大于等于pivot的放到pivot的右边。**最后完成排列后，pivot之前的元素都比他小，之后的元素都比他大，从而我们可以确定pivot在整个数组中是第X小的元素**。这样，如果X < K，那么第K小的元素必然在X的右侧；如果X > K,  那么第K小的元素必然在X的左侧。

![image-20220211223708978](/assets/algo-exp-note.assets/image-20220211223708978.png)

<img src="/assets/algo-exp-note.assets/image-20220212001534310.png" alt="image-20220212001534310" style="zoom:50%;" />

```java
import java.util.*;
// Best: O(n) time | O(1) space
// Average: O(n) time | O(1) space
// Worst: O(n^2) time | O(1) space
class Program {
    public static int quickselect(int[] array, int k) {
        int position = k - 1;
        return quickselectHelper(array, 0, array.length - 1, position);
    }

    public static int quickselectHelper(int[] array, int startIdx, int endIdx, int position)  {

        while (true) {
            if (startIdx > endIdx) {
                throw new RuntimeException("This Algorithm should never arrive here");
            }
            //确定pivot，pivot之后的数组片段的头部为left，尾部为right
            int pivot = startIdx;
            int left = startIdx + 1;
            int right = endIdx;
            //遍历[left, right]数组片段
            while (left <= right) {
                //left所能经过的数组元素必须小于等于[pivot]，right所能经过的数组元素必须大于等于array[pivot]
                //若发现left和right所指向的元素同时不符合要求，将他们换位置
                if (array[left] > array[pivot] && array[right] < array[pivot])
                    swap(left, right, array);
                //若符合要求则继续收缩遍历范围
                if (array[left] <= array[pivot])
                    left++;
                if (array[right] >= array[pivot])
                    right--;
            }
            //循环结束，此时right在left的左侧，[pivot，小于pivot的元素+right，left+大于pivot的元素]
            //交换right和pivot，这样[小于pivot的元素，pivot,大于pivot的元素]
            //之后判断该位置是否是我们要找的position
            swap(pivot, right, array);
            if (right == position)
                return array[right];
            else if (right < position)
                startIdx = right + 1;
            else if (right > position)
                endIdx = right - 1;
        }
    }

    public static void swap(int a, int b, int[] array) {
        int temp = array[a];
        array[a] = array[b];
        array[b] = temp;
    }
}
```

## 7.Index Equals Value(Red)

> Write a function that takes in a sorted array of distinct integers and returns the first index in the array that is equal to the value at that index. In other words, your function should return the minimum index where index == array[index].
>
> If there is no such index, your function should return -1.
>
> ### Sample Input
>
> ```
> array = [-5, -3, 0, 3, 4, 5, 9]
> ```
>
> ### Sample Output
>
> ```
> 3 // 3 == array[3]
> ```

- 方法一：遍历整个数组，找到index == array[index]的内容。时间复杂度为`O(N)`.
- 方法二：本思路的难点在于理清分支条件出现的顺序以及判断条件。

<img src="/assets/algo-exp-note.assets/image-20220515212103023.png" alt="image-20220515212103023" style="zoom:50%;" />

```java
import java.util.*;
// O(log(N)) time O(1) space
class Program {
    public int indexEqualsValue(int[] array) {
        return indexEqualsValueHelper(array, 0, array.length - 1);
    }

    private int indexEqualsValueHelper(int[] array, int left, int right) {
        while (left <= right) {
            int mid = (left + right) / 2;
            int midNum = array[mid];

            if (midNum < mid) // mid左侧不可能有结果
                left = mid + 1;
            //找到了一个mid但要判断其之前是否还有合格的mid
            else if (midNum == mid && mid == 0) // 排除边界条件
                return mid;
            else if (midNum == mid && array[mid - 1] < mid - 1) // 检查mid之前的元素
                return mid;
            // mid右侧不可能有结果 or mid之前还有合格的数字，需要更新right继续查询
            else
                right = mid - 1;
        }

        return -1;
    }
}
```

# Sorting

## 1.Bubble Sort(Green)

冒泡排序，就是从数组中第一个元素开始，将每一个元素和其后一个元素比较大小，若current > next, 那么我们就将他们交换位置。这样，每经过一次对数组的遍历，我们可以将最大的元素交换到最后的位置，在下一次的遍历中，我们只需要对最大元素之前的数组元素进行冒泡排序即可。

```java
import java.util.*;
// Best: O(n) time | O(1) space
// Average: O(n^2) time | O(1) space
// Worst: O(n^2) time | O(1) space
class Program {
    public static int[] bubbleSort(int[] array) {
        if (array.length == 0)
            return new int[]{};
        boolean isSorted = false;
        int counter = 0;
        while (!isSorted) {
            isSorted = true;
            // 每for循环遍历数组一次，确定当前[0, array.length - 1 - counter]
            // 之间中的最大值
            for (int i = 0; i < array.length - 1 - counter; i++) {
                if (array[i] > array[i + 1]) {
                    swap(i, i + 1, array);
                    isSorted = false;
                }
            }
            counter++;
        }
        return array;
    }

    public static void swap(int i, int j, int[] array) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

## 2. Insertion Sort(Green)

插入排序的思路就是遍历数组中的每一个元素，将这个元素插入到它之前的数组片段的合适位置中。**插入的基本思路为：将当前元素与其之前的数组片段中的元素进行比较，若当前元素<数组片段中的元素就将他们交换位置，直到找到合适的位置**。

```java
import java.util.*;
// Best: O(n) time | O(1) space
// Average: O(n^2) time | O(1) space
// Worst: O(n^2) time | O(1) space
class Program {
    public static int[] insertionSort(int[] array) {
        if (array.length == 0)
            return new int[]{};
        for (int i = 1; i < array.length; i++) {
            int j = i;

            while (j > 0 && array[j] < array[j - 1]) {
                swap(j, j - 1, array);
                j--;
            }
        }
        return array;
    }

    public static void swap(int i, int j, int[] array) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

## 3. Selection Sort(Green)

选择排序的思路就是给每一个特定的位置选出排序后应该出现在该位置的值。例如，对于0号位置，我们应该从整个数组中选出最小的元素并放到该位置上。对于1号位置，我们应该在剩下的（0号位置已经确定了一个元素）元素中找出第二小的放到1号位置，以此类推。具体的选择过程见代码。

```java
import java.util.*;
// Best: O(n^2) time | O(1) space
// Average: O(n^2) time | O(1) space
// Worst: O(n^2) time | O(1) space
class Program {
    public static int[] selectionSort(int[] array) {
        if (array.length == 0)
            return new int[]{};
        //i指的是我们当前要选择数组元素的位置
        for (int i = 0; i < array.length - 1; i++) {
            int smallestIdx = i;
            //从i位置开始向后遍历所有的数组元素，找到最小值index
            for (int j = i + 1; j < array.length; j++) {
                if (array[j] < array[smallestIdx])
                    smallestIdx = j;
            }
            //将最小值放到放到位置i上
            swap(i, smallestIdx,array);
        }
        return array;
    }

    public static void swap(int i, int j, int[] array) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

## 4. Quick Sort(Red*)

参考`quickselect`，本质上是通过pivot将小于大于pivot的数组元素放到pivot的左右两边（这样pivot就找到了它在数组排序后应该处于的位置）。通过对pivot左右两侧的数组片段进行同样的处理，直到左右两侧的数组片段只剩一个数组元素（此时所有的pivot都找到了自己排序后的位置）。

**Space complexity**

The space used by quicksort depends on the version used.

The in-place version of quicksort has a space complexity of *O*(log *n*), even in the worst case, when it is carefully implemented using the following strategies.

- In-place partitioning is used. This unstable partition requires *O*(1) space.
- After partitioning, **the partition with the fewest elements is (recursively) sorted first, requiring at most *O*(log *n*) space. Then the other partition is sorted using [tail recursion](https://en.wikipedia.org/wiki/Tail_recursion) or iteration, which doesn't add to the call stack.** This idea, as discussed above, was described by [R. Sedgewick](https://en.wikipedia.org/wiki/Robert_Sedgewick_(computer_scientist)), and keeps the stack depth bounded by *O*(log *n*).

为了让quick sort达到*O*(log *n*)的空间复杂度，我们需要对遵守黑体部分的规则。其中，较大的子数组会用到**尾递归**这个概念。尾递归将不会增加新的栈帧。

> 函数在return的时候调用自身，这种情况称为尾递归，除了调用自身不能有其他额外的。尾递归是递归的一种特殊形式，也是是尾调用的一种特殊形式（尾调用最后调用函数自身，就是一个尾递归）。尾调用不一定是尾递归。
> 尾递归特点：
>
> 在回归过程中不用做任何操作，编译器会利用这种特点自动生成优化的代码。（编译器发现在回归调用时通过覆盖当前的栈，而不是创建新的栈，使用来减少栈空间的使用和减少栈的分配释放开销）
> 尾递归是把变化的参数传递给递归函数的变量了。递归发生时，外层函数把计算结果当成参数传给内存函数。
> 尾递归的优化从函数调用的汇编实现很容易理解，只要不涉及多余的参数（也就是不用额外的栈容量存，栈顶不用往下扩展再调用回函数），直接就可以改变`%edi,%esi`的值，重新调用函数，也就是说不用任何额外的变量。

然而，java的JVM并不支持这种优化，java需要通过iterative的方式来达到**尾递归**的目的。下图展示了假设java具有尾递归的情况下，代码的写法。

![image-20220212181400316](/assets/algo-exp-note.assets/image-20220212181400316.png)

```java
import java.util.*;
// Best: O(nlog(n)) time | O(log(n)) space
// Average: O(nlog(n)) time | O(log(n)) space
// Worst: O(n^2) time | O(log(n)) space
class Program {
    public static int[] quickSort(int[] array) {
        quickSortHelper(array, 0, array.length - 1);
        return array;
    }

    public static void quickSortHelper(int[] array, int startIdx, int endIdx) {
        if (startIdx >= endIdx)
            return;
        int pivot = startIdx;
        int left = startIdx + 1;
        int right = endIdx;

        while (left <= right) {
            if (array[left] > array[pivot] && array[right] < array[pivot])
                swap(left, right, array);
            if(array[left] <= array[pivot])
                left++;
            if(array[right] >= array[pivot])
                right--;
        }
        swap(pivot,right,array);
        quickSortHelper(array,startIdx,right-1);
        quickSortHelper(array,right+1,endIdx);
    }

    public static void swap(int i, int j, int[] array) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

## 5. Heap Sort(Red)

堆排序依靠的是大顶堆（max Heap）。在大顶堆中，根节点的值大于其他一切节点的值。**堆排序就是通过大顶堆不断地remove根节点从而依次获得最大值，第二大值，第三大值**，以此类推。每次remove根节点后，对剩余的节点进行调整再次组成新的大顶堆，从而不断地选出新的堆中的最大值，从而完成排序。

为什么不用Min Heap呢？如果用Min Heap，那么array[0]就是最小值了，要取得array[1]这个次小值，就需要对array[1, length - 1]构建新的最小堆，构建一次，时间复杂度为`O(N)`.  Max Heap并没有真的remove根节点，而是把它与数组的末尾互换，这样我们就只需要将新的根节点sift down就可以了。这个时间复杂度只有`O(log(N))`.

本题的重点在于对堆数据结构的理解。

```java
import java.util.*;
// Best: O(nlog(n)) time | O(1) space
// Average: O(nlog(n)) time | O(1) space
// Worst: O(nlog(n)) time | O(1) space
class Program {
    public static int[] heapSort(int[] array) {
        buildMaxHeap(array);
        for (int endIdx = array.length - 1; endIdx > 0; endIdx--) {
            //将大顶堆的root节点值赋给endIdx
            swap(endIdx, 0, array);
            //对新的堆进行siftdown（这个堆只有root节点不符合规矩），只需用siftdown即可，无需重构整个堆
            siftDown(0, endIdx - 1, array);
        }
        return array;
    }

    public static void buildMaxHeap(int[] array) {
        int firstParent = (array.length - 2) / 2;
        for (int currentIdx = firstParent; currentIdx >= 0; currentIdx--) {
            siftDown(currentIdx, array.length - 1, array);
        }
    }

    public static void siftDown(int currentIdx, int endIdx, int[] array) {
        int childOne = currentIdx * 2 + 1;
        while (childOne <= endIdx) {
            int childTwo = currentIdx * 2 + 2 <= endIdx ? currentIdx * 2 + 2 : -1;
            int idxToSwap;
            if (childTwo != -1 && array[childTwo] > array[childOne])
                idxToSwap = childTwo;
            else
                idxToSwap = childOne;
            if (array[idxToSwap] > array[currentIdx]) {
                swap(idxToSwap, currentIdx, array);
                currentIdx = idxToSwap;
                childOne = currentIdx * 2 + 1;
            }
            else
                return;
        }
    }

    public static void swap(int i, int j, int[] array) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

这个堆写的更简洁一些😎😎😎😎😎😎😎😎😎

```java
import java.util.*;

class Program {
    public static int[] heapSort(int[] array) {
        buildHeap(array);
        for (int endIdx = array.length - 1; endIdx > 0; endIdx--) {
            swap(0, endIdx, array);
            siftDown(0, endIdx - 1, array);
        }
        return array;
    }

    public static void buildHeap(int[] array) {
        int firstParentIdx = (array.length - 2) / 2;
        for (int currentIdx = firstParentIdx; currentIdx >= 0; currentIdx--) {
            siftDown(currentIdx, array.length - 1, array);
        }
    }

    private static void siftDown(int currentIdx, int endIdx, int[] array) {
        int childOneIdx = 2 * currentIdx + 1;
        while (childOneIdx <= endIdx) {
            // childTwoIdx完全取决于childOneIdx
            int childTwoIdx = childOneIdx == endIdx ? -1 : childOneIdx + 1;
            // 可以先将idxToSwap指定为childOneIdx
            int idxToSwap = childOneIdx;

            // 若childTwoIdx存在，那么更新idxToSwap的值，否则为之前指定的childOneIdx
            if (childTwoIdx != -1) {
                idxToSwap = array[childOneIdx] < array[childTwoIdx] ? childTwoIdx : childOneIdx;
            }

            if (array[idxToSwap] > array[currentIdx]) {
                swap(currentIdx, idxToSwap, array);
                currentIdx = idxToSwap;
                childOneIdx = 2 * currentIdx + 1;
            } else {
                break;
            }
        }
    }

    public static void swap(int i, int j, int[] array) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

## 6. Merge Sort(Black)

由下图可以看出，当整个数组被divide到只有一个元素时，开始进行merge. 本题容易出错的地方在于`mergeSort()`部分，需要保证`leftArray`和`rightArray`是被平均分开的（他们的length之差最多为1）。**建议在写这一部分的代码时，以[A, B]这种array带入`mergeSort`,  保证代码可以将他们分成[A],[B]两部分，而不是[], [A, B]两部分。**

<img src="/assets/algo-exp-note.assets/image-20220310120431973.png" alt="image-20220310120431973" style="zoom:67%;" />

```java
import java.util.*;
// Best: O(nlog(n)) time | O(nlog(n)) space
// Average: O(nlog(n)) time | O(nlog(n)) space
// Worst: O(nlog(n)) time | O(nlog(n)) space
class Program {
    public static int[] mergeSort(int[] array) {
        if (array.length <= 1)
            return array;
        int mid = array.length / 2;
        //[from, to) 新截取的数组不包含to所指向的元素
        int[] leftArray = Arrays.copyOfRange(array, 0, mid);
        int[] rightArray = Arrays.copyOfRange(array, mid, array.length);
        return mergeSortedArrays(mergeSort(leftArray), mergeSort(rightArray));
    }

    public static int[] mergeSortedArrays(int[] leftArray, int[] rightArray) {
        int[] sortedArray = new int[leftArray.length + rightArray.length];
        int i = 0;
        int j = 0;
        int k = 0;

        while (i < leftArray.length && j < rightArray.length) {
            if (leftArray[i] <= rightArray[j]) {
                sortedArray[k] = leftArray[i];
                i++;
            } else {
                sortedArray[k] = rightArray[j];
                j++;
            }
            k++;
        }

        while (i < leftArray.length) {
            sortedArray[k] = leftArray[i];
            i++;
            k++;
        }

        while (j < rightArray.length) {
            sortedArray[k] = rightArray[j];
            j++;
            k++;
        }
        return sortedArray;
    }
}
```

方法二：用两个数组Main和Auxiliary，Main的角色是根据Auxiliary数组sort其自身。可以看到每一次Helper后M，A的角色其实会互换。建议与代码结合理解。

方法二更加节省空间。Space = `O(N)`.

![image-20220310132457048](/assets/algo-exp-note.assets/image-20220310132457048.png)

```java
import java.util.Arrays;

public class Program {
    public static int[] mergeSort(int[] array) {
        if (array.length <= 1)
            return array;

        int[] auxilaryArray = array.clone();
        mergeSortHelper(array, 0, array.length - 1, auxilaryArray);
        return array;
    }

    public static void mergeSortHelper(int[] mainArray, int startIdx, int endIdx, int[] auxilaryArray) {
        //Only one element in array
        if (startIdx == endIdx)
            return;

        int midIdx = (startIdx + endIdx) / 2;
        mergeSortHelper(auxilaryArray, startIdx, midIdx, mainArray);
        mergeSortHelper(auxilaryArray, midIdx + 1, endIdx, mainArray);
        doMerge(mainArray, startIdx, midIdx, endIdx, auxilaryArray);
    }

    private static void doMerge(int[] mainArray, int startIdx, int midIdx, int endIdx, int[] auxilaryArray) {
        int k = startIdx;
        int i = startIdx;
        int j = midIdx + 1;

        while (i <= midIdx && j <= endIdx) {
            if (auxilaryArray[i] <= auxilaryArray[j]) {
                mainArray[k] = auxilaryArray[i];
                i++;
            } else {
                mainArray[k] = auxilaryArray[j];
                j++;
            }
            k++;
        }

        while (i <= midIdx)
            mainArray[k++] = auxilaryArray[i++];

        while (j <= endIdx)
            mainArray[k++] = auxilaryArray[j++];
    }
}
```

# Stacks

## 1.Min Max Stack Construction(Blue)

本题的难点在于第三点要求，就是在stack的任意状态下都可以以`O(1) time O(1) space`的复杂度取得当前stack中的最大最小值。所以本题的思路就是在建立一个普通stack的同时，再建立一个`minMaxStack`用于保存不同状态下stack中的最大最小值。

![image-20220310153846581](/assets/algo-exp-note.assets/image-20220310153846581.png)

![image-20220310153901492](/assets/algo-exp-note.assets/image-20220310153901492.png)

```java
import java.util.*;

class Program {
    // Feel free to add new properties and methods to the class.
    static class MinMaxStack {
        //minMaxStack用于记录stack中的最大值和最小值，会随着stack的变化而变化
        List<Map<String, Integer>> minMaxStack = new ArrayList<>();
        List<Integer> stack = new ArrayList<>();
		// O(1) time O(1) space
        public int peek() {
            return stack.get(stack.size() - 1);
        }
		// O(1) time O(1) space
        public int pop() {
            minMaxStack.remove(minMaxStack.size() - 1);
            return stack.remove(stack.size() - 1);
        }
		// O(1) time O(1) space
        public void push(Integer number) {
            Map<String, Integer> newMinMax = new HashMap<>();
            newMinMax.put("min", number);
            newMinMax.put("max", number);

            if(minMaxStack.size() > 0) {
                Map<String, Integer> lastMinMax = minMaxStack.get(minMaxStack.size() - 1);
                newMinMax.replace("min", Math.min(newMinMax.get("min"), lastMinMax.get("min")));
                newMinMax.replace("max", Math.max(newMinMax.get("max"), lastMinMax.get("max")));
            }

            minMaxStack.add(newMinMax);
            stack.add(number);
        }
		// O(1) time O(1) space
        public int getMin() {
            return minMaxStack.get(minMaxStack.size() - 1).get("min");
        }
		// O(1) time O(1) space
        public int getMax() {
            return minMaxStack.get(minMaxStack.size() -1).get("max");
        }
    }
}
```

## 2.Balanced Brackets(Blue)

![image-20220310161509166](/assets/algo-exp-note.assets/image-20220310161509166.png)

```java
import java.util.*;
// O(N) time O(N) space
class Program {
    public static boolean balancedBrackets(String str) {
        String openingBrackets = "([{";
        String closingBrackets = ")]}";
        List<Character> stack = new ArrayList<>();

        Map<Character,Character> matchingBrackets = new HashMap<>();
        matchingBrackets.put(')','(');
        matchingBrackets.put(']','[');
        matchingBrackets.put('}','{');

        for(int i = 0; i < str.length(); i++) {
            char letter = str.charAt(i);
            //如果是opening，加到栈中
            if (openingBrackets.indexOf(letter) != -1) {
                stack.add(letter);
            } else if (closingBrackets.indexOf(letter) != -1) {//如果是closing，和栈顶元素对比
                if (stack.size() == 0)
                    return false;
                else {
                    if (matchingBrackets.get(letter).equals(stack.get(stack.size() - 1)))
                        stack.remove(stack.size() - 1);
                    else
                        return false;
                }
            }
            //输入为其他字符
            //do nothing
        }
        //排除“(((((((”的情况
        return stack.size() == 0;
    }
}
```

简化了条件分支，更简洁的写法，更清晰的思路

```java
import java.util.*;

class Program {
    public static boolean balancedBrackets(String str) {
        String openingBrackets = "([{";
        String closingBrackets = ")]}";
        List<Character> stack = new ArrayList<>();

        Map<Character, Character> matching = new HashMap<>();
        matching.put(')', '(');
        matching.put(']', '[');
        matching.put('}', '{');

        for (int i = 0; i < str.length(); i++) {
            char currentchar = str.charAt(i);

            if (closingBrackets.indexOf(currentchar) != -1) {
                char expectedchar = matching.get(currentchar);
                if (stack.size() == 0 || expectedchar != stack.get(stack.size() - 1))
                    return false;
                else
                    stack.remove(stack.size() - 1);
            } else if (openingBrackets.indexOf(currentchar) != -1) {
                stack.add(currentchar);
            }
        }

        return stack.size() == 0;
    }
}
```

## 3.Shorten Path(Red)

![image-20220310173758292](/assets/algo-exp-note.assets/image-20220310173758292.png)

![image-20220310173841098](/assets/algo-exp-note.assets/image-20220310173841098.png)

对Path进行预处理，其实就是将无意义的字符剔除。整个path中只有`".." , "filename" `是有意义的。

若我们遇到了`".."`, 那么有四种情况：

1. `..`之前还是`..`，push
2. `..`之前是`filename`， pop
3. `..`之前是根目录，pass
4. `..`之前什么都没有， push

若我么遇到了`filename`，push即可

```java
import java.util.*;
import java.util.stream.Collectors;

class Program {
    public static String shortenPath(String path) {
        boolean hasRoot = path.charAt(0) == '/';
        String[] tokenArr = path.split("/");
        List<String> tokenList = Arrays.asList(tokenArr);
        // 只剩".." 和 "filename"
        List<String> filterdTokens = tokenList.stream().filter(token -> isImmportantToken(token)).collect(Collectors.toList());
        List<String> stack = new ArrayList<>();

        if (hasRoot)
            stack.add("");
        for (String token : filterdTokens) {
            if (token.equals("..")) {
                //: /../../..是无意义的
                //: ../../../是有意义的，这表示相对路径
                // 只有../../../可以在缩减后的path中出现
                if(stack.size() == 0 || stack.get(stack.size() -1).equals(".."))
                    stack.add(token);
                //出现在filename之后的.., 要被优化
                else if (!stack.get(stack.size() -1).equals("")) {
                    stack.remove(stack.size() - 1);
                }
            } else {
                stack.add(token);
            }
        }

        if (stack.size() == 1 && stack.get(0).equals(""))
            return "/";
        return String.join("/", stack);
    }

    // 过滤内容："" 和 "."
    public static boolean isImmportantToken(String token) {
        return token.length() > 0 && !token.equals(".");
    }
}
```

## 4.Sunset Views(Blue)

> Given an array of buildings and a direction that all of the buildings face, return an array of the indices of the buildings that can see the sunset.
>
> A building can see the sunset if it's strictly taller than all of the buildings that come after it in the direction that it faces.
>
> The input array named `buildings` contains positive, non-zero integers representing the heights of the buildings. A building at index `i` thus has a height denoted by `buildings[i]`. All of the buildings face the same direction, and this direction is either east or west, denoted by the input string named`direction`, which will always be equal to either `"EAST"` or `"WEST"`. In relation to the input array, you can interpret these directions as right for east and left for west.
>
> Important note: the indices in the ouput array should be sorted in ascending order.
>
> ### Sample Input #1
>
> ```
> buildings = [3, 5, 4, 4, 3, 1, 3, 2]
> direction = "EAST"
> ```
>
> ### Sample Output #1
>
> ```
> [1, 3, 6, 7]
>
> // Below is a visual representation of the sample input.
> //    _
> //   | |_ _
> //  _| | | |_   _
> // | | | | | | | |_
> // | | | | | |_| | |
> // |_|_|_|_|_|_|_|_|
> ```
>
> ### Sample Input #2
>
> ```
> buildings = [3, 5, 4, 4, 3, 1, 3, 2]
> direction = "WEST"
> ```
>
> ### Sample Output #2
>
> ```
> [0, 1]
>
> // The buildings are the same as in the first sample
> // input, but their direction is reversed.
> ```

- 方法一(推荐)：如下图所示，如果面向east，那么就从右向左遍历数组；如果面向west，就从左向右遍历数组。以下图为例，我们从右到左遍历数组并维护一个`maxHeight`变量。当发现有楼层高于`maxHeight`时，更新`maxHeight`。以这种思路，我们可以看出，每个楼层都在和他之前楼层的最高者比较，若他比他之前楼层的最高者高，那么它可以看到太阳。

  <img src="/assets/algo-exp-note.assets/image-20220516191102374.png" alt="image-20220516191102374" style="zoom:33%;" />

  ```java
  import java.util.*;
  // O(N) time O(N) space
  class Program {

      public ArrayList<Integer> sunsetViews(int[] buildings, String direction) {
          ArrayList<Integer> buildingsWithSunsetViews = new ArrayList<>();

          // 默认楼层朝向右边（EAST）
          int startIdx = buildings.length - 1;
          int step = -1;
          // 若楼层朝向左边
          if (direction.equals("WEST")) {
              startIdx = 0;
              step = 1;
          }

          int idx = startIdx;
          int maxHeight = 0;
          while (idx >= 0 && idx < buildings.length) {
              int currentHeight = buildings[idx];
              // 当前房屋之前没有比他高的
              if (currentHeight > maxHeight) {
                  buildingsWithSunsetViews.add(idx);
                  maxHeight = currentHeight;
              }
              idx += step;
          }

          if (direction.equals("EAST"))
              Collections.reverse(buildingsWithSunsetViews);

          return buildingsWithSunsetViews;
      }
  }
  ```

- 方法二（使用了stack）：方法二在方法一的基础上引入了栈，栈用于记录假如当前楼层是最后一个面向太阳的楼层时，有哪些楼层可以见到阳光。当有新的楼层被引入时，我们需要判断这个楼层的高度是否高于栈中的楼层。若高的话，就需要将上一个状态能照到太阳的楼层出栈，并将新引入的楼层入栈，从而完成栈状态的更新。

  **需要注意，采用stack，如果面向east，就从左向右遍历数组。这和方法一是相反的。**

  ```java
  import java.util.*;

  class Program {

      public ArrayList<Integer> sunsetViews(int[] buildings, String direction) {
          ArrayList<Integer> currentBuildingsWithSunsetViews = new ArrayList<>();

          // 与第一种方法不同，采用stack思路
          // 遍历的方向是相反的
          int startIdx = buildings.length - 1;
          int step = -1;
          if (direction.equals("EAST")) {
              startIdx = 0;
              step = 1;
          }

          int idx = startIdx;
          while (idx >= 0 && idx < buildings.length) {
              int currentHeight = buildings[idx];
              while (currentBuildingsWithSunsetViews.size() > 0
                      &&
                      currentHeight >= buildings[currentBuildingsWithSunsetViews.get(currentBuildingsWithSunsetViews.size() - 1)]) {
                  currentBuildingsWithSunsetViews.remove(currentBuildingsWithSunsetViews.size() - 1);
              }

              currentBuildingsWithSunsetViews.add(idx);
              idx += step;
          }

          if (direction.equals("WEST"))
              Collections.reverse(currentBuildingsWithSunsetViews);

          return currentBuildingsWithSunsetViews;
      }
  }
  ```

## 5.Sort Stack(Blue*)

> Write a function that takes in an array of integers representing a stack, recursively sorts the stack in place (i.e., doesn't create a brand new array), and returns it.
>
> The array must be treated as a stack, with the end of the array as the top of the stack. Therefore, you're only allowed to
>
> - Pop elements from the top of the stack by removing elements from the end of the array using the built-in `.pop()` method in your programming language of choice.
> - Push elements to the top of the stack by appending elements to the end of the array using the built-in `.append()` method in your programming language of choice.
> - Peek at the element on top of the stack by accessing the last element in the array.
>
> You're not allowed to perform any other operations on the input array, including accessing elements (except for the last element), moving elements, etc.. You're also not allowed to use any other data structures, and your solution must be recursive.
>
> ### Sample Input
>
> ```
> stack = [-5, 2, -2, 4, 3, 1]
> ```
>
> ### Sample Output
>
> ```markdown
> [-5, -2, 1, 2, 3, 4]
> ```

<img src="/assets/algo-exp-note.assets/image-20220516222959185.png" alt="image-20220516222959185" style="zoom:67%;" />

上图展示了本题的计算过程，建议结合代码理解。简单来说，该算法通过递归将栈中的元素一一弹出并，之后再回溯的过程中，调用insert函数为栈帧中保存的top找到合适的位置插入。

```java
import java.util.*;
// O(N^2) time O(1) space
class Program {

    public ArrayList<Integer> sortStack(ArrayList<Integer> stack) {
        if (stack.size() == 0)
            return stack;

        // 通过os提供的栈帧保存stack中的每一个元素
        int top = stack.remove(stack.size() - 1);
        sortStack(stack);

        // 将每个栈帧中存放的top值插入到stack中的合适位置
        insert(top, stack);
        return stack;
    }

    private void insert(int value, ArrayList<Integer> stack) {
        // 若stack为空，或者value的值比stack中栈顶的元素大
        // 说明此时顺序无误
        if (stack.size() == 0 || value > stack.get(stack.size() - 1)) {
            stack.add(value);
            return;
        }

        //顺序有误, 先pop当前stack的top保存
        int top = stack.remove(stack.size() - 1);
        // 向新的stack中继续尝试插入value
        insert(value, stack);
        // value插入到stack中的合适位置后，将top重新加到顶部
        stack.add(top);
    }
}
```

## 6.Next Greater Element(Blue*)

> Write a function that takes in an array of integers and returns a new array containing, at each index, the next element in the input array that's greater than the element at that index in the input array.
>
> In other words, your function should return a new array where `outputArray[i]` is the next element in the input array that's greater than `inputArray[i]`. If there's no such next greater element for a particular index, the value at that index in the output array should be -1. For example, given array = [1, 2], your function should return [2, -1].
>
> Additionally, your function should treat the input array as a **circular** array. A circular array wraps around itself as if it were connected end-to-end. So the next index after the last index in a circular array is the first index. This means that, for our problem, given array = [0, 0, 5, 0, 0, 3, 0 0], the next greater element after 3 is 5, since the array is circular.
>
> ### Sample Input
>
> ```
> array = [2, 5, -3, -4, 6, 7, 2]
> ```
>
> ### Sample Output
>
> ```
> [5, 6, 6, 6, 7, -1, 5]
> ```

- 方法一：遍历数组两遍，用栈记录还没有找到`nextGreater`的index。对于遍历的每一个`num`，将他与栈中的index代表的数字比较，若`num`大于`array[index]`则证明，`num`就是该index的`nextGreater`, 弹出符合要求的index即可。由于题目将数组视为一个环形，那么对于index=6位置上的`nextGreater`, 就只能通过第二遍遍历来找到。

  <img src="/assets/algo-exp-note.assets/image-20220517163236585.png" alt="image-20220517163236585" style="zoom:67%;" />

```java
import java.util.*;
// O(N) time O(N) space
class Program {

    public int[] nextGreaterElement(int[] array) {
        int[] result = new int[array.length];
        Arrays.fill(result, -1);

        Stack<Integer> stack = new Stack<>();

        // 循环两遍
        for (int i = 0; i < 2 * array.length; i++) {
            int currentIdx = i % array.length;
            // 这里要思考while的条件，容易写成死循环
            while(!stack.isEmpty() && array[stack.peek()] < array[currentIdx]) {
                    result[stack.pop()] = array[currentIdx];
            }
            stack.push(currentIdx);
        }

        return result;
    }
}
```

- 方法二：从右向左遍历。用栈保存可能成为`nextGreater`的数字。下图展示了从左到右第一次遍历的结果，对于遍历中的每一个`num`，查看stack中栈顶的元素`top`是否大于`num`。若大于，那么说明`top`就是`num`的next Greater，将`num`入栈。否则，证明`top`不可能是next Greater，将`top`出栈，将`num`入栈。具体的实现见代码，建立按照例子自己重复整个过程。

  ![image-20220517173137758](/assets/algo-exp-note.assets/image-20220517173137758.png)

```java
import java.util.*;

class Program {

    public int[] nextGreaterElement(int[] array) {
        int[] result = new int[array.length];
        Arrays.fill(result, -1);
        Stack<Integer> stack = new Stack<>();

        for (int i = 2 * array.length - 1; i >= 0; i--) {
            int currentIdx = i % array.length;

            while (!stack.isEmpty()) {
                if (stack.peek() <= array[currentIdx])
                    stack.pop();
                else {
                    result[currentIdx] = stack.peek();
                    break;
                }
            }
            stack.push(array[currentIdx]);
        }

        return result;
    }
}
```

## 7.Largest Rectangle Under Skyline(Red*)

> Write a function that takes in an array of positive integers representing the heights of adjacent buildings and returns the area of the largest rectangle that can be created by any number of adjacent buildings, including just one building. Note that all buildings have the same width of `1` unit.
>
> For example, given `buildings = [2, 1, 2]`, the area of the largest rectangle that can be created is `3`, using all three buildings. Since the minimum height of the three buildings is `1`, you can create a rectangle that has a height of `1` and a width of `3` (the number of buildings). You could also create rectangles of area `2` by using only the first building or the last building, but these clearly wouldn't be the largest rectangles. Similarly, you could create rectangles of area `2` by using the first and second building or the second and third building.
>
> To clarify, the width of a created rectangle is the number of buildings used to create the rectangle, and its height is the height of the smallest building used to create it.
>
> Note that if no rectangles can be created, your function should return 0.
>
> ### Sample Input
>
> ```
> buildings = [1, 3, 3, 2, 4, 1, 5, 3, 2]
> ```
>
> ### Sample Output
>
> ```
> 9
>
> // Below is a visual representation of the sample input.
> //              _
> //          _  | |
> //    _ _  | | | |_
> //   | | |_| | | | |_
> //  _| | | | |_| | | |
> // |_|_|_|_|_|_|_|_|_|
> ```

- 方法一：遍历每一个楼，从该楼开始向左右遍历找到该楼的左边界和右边界，从而知道该楼能产生的最大长方形面积。(`O(N^2) time`)

- 方法二：

  stack中多存储的都是还没有确定右边界的building的坐标。

  红色标记的pop动作是因为我们找到了栈中building的右边界（`stack.peek() >= currentBuilding`），从而可以对其所能组成的最大面积进行计算。

  接下来需要解释一下在计算最大面积时，宽度是怎么计算的。首先，如果`stack.peek() < currentBuilding`，我们会将`currentBUilding`入栈，因为他和栈中的其他building可以继续向右延申。从而，栈顶元素的下一个元素总是该栈顶元素的左边界。

  以坐标5栈中的坐标为例，在坐标5之前，[0，3，4]这三个坐标对应的building还没有找到对应的右边界（右边界的高度<=栈中的building高度）。这是我们得到坐标5的值为1，将它与stack中的内容一一比较。首先，1是building4的右边界，我们pop 4然后用4 * （5 - 3 - 1）= 4得到最大面积。然后，1是building3的右边界，pop 3， 2 * （5 - 0 - 1）= 8。最后1是building0的右边界，pop 1， 1 *（5）= 5。

  <img src="/assets/algo-exp-note.assets/image-20220519182505940.png" alt="image-20220519182505940" style="zoom:50%;" />

```java
import java.util.*;
// O(N) time O(N) space
class Program {

    public int largestRectangleUnderSkyline(ArrayList<Integer> buildings) {
        Stack<Integer> buildingIndices = new Stack<>();
        int maxArea = 0;

        // the algo use index i to calculate the maxArea between [0, i - 1]
        // so we need to add 0 as the right border
        ArrayList<Integer> extendedBuildings = new ArrayList<>(buildings);
        extendedBuildings.add(0);

        for (int i = 0; i < extendedBuildings.size(); i++) {
            int currentHeight = extendedBuildings.get(i);
            while (!buildingIndices.isEmpty()
                    && currentHeight <= extendedBuildings.get(buildingIndices.peek())) {
                int idxToCalculate = buildingIndices.pop();
                int width = buildingIndices.isEmpty() ? i : i - buildingIndices.peek() - 1;
                maxArea = Math.max(maxArea, width * extendedBuildings.get(idxToCalculate));
            }

            buildingIndices.push(i);
        }

        return maxArea;
    }
}
```

# String

## 1.Palindrome Check(Green)

![image-20220310195631837](/assets/algo-exp-note.assets/image-20220310195631837.png)

- 方法一：把原String翻转过来比较，如果内容一样，则是Palindrome

  ![image-20220310201046888](/assets/algo-exp-note.assets/image-20220310201046888.png)

- 方法二：同方法一

  > Why does the first solution run in *O(n^2)* time when it only uses a single *for* loop?
  >
  > At each iteration in the *for* loop, the first solution adds a character to the `reversedString`. In most languages where strings are immutable, adding a character to a string involves re-creating the entire string, which in turn involves iterating through every character in the string (an *O(n)*-time operation).
  >
  > This, the first solution has us perform an *O(n)*-time operation at each iteration in the *for* loop, leading to an *O(n^2)*-time algorithm overall.

  ![image-20220310201120369](/assets/algo-exp-note.assets/image-20220310201120369.png)

- 方法三：

  ```java
  import java.util.*;
  // O(N) time O(1) space
  class Program {
      public static boolean isPalindrome(String str) {
          int leftIdx = 0;
          int rightIdx = str.length() - 1;

          while (leftIdx < rightIdx) {
              if (str.charAt(leftIdx) != str.charAt(rightIdx))
                  return false;
              leftIdx++;
              rightIdx--;
          }
          return true;
      }
  }
  ```

## 2.Caesar Cipher Encryptor(Green)

![image-20220310202449985](/assets/algo-exp-note.assets/image-20220310202449985.png)

```java
import java.util.*;

class Program {
    public static String caesarCypherEncryptor(String str, int key) {
        char[] newLetters = new char[str.length()];
        int offset = key % 26;
        String alphabet = "abcdefghijklmnopqrstuvwxyz";

        for (int i = 0; i < str.length(); i++) {
            newLetters[i] = getNewLetter(str.charAt(i), offset, alphabet);
        }
        return new String(newLetters);
    }

    private static char getNewLetter(char oldLetter, int offset, String alphabet) {
        int newLetterCode = alphabet.indexOf(oldLetter) + offset;
        return alphabet.charAt(newLetterCode % 26);
    }
}
```

## 3.Longest Palindromic Substring(Blue*)

![image-20220310204318759](/assets/algo-exp-note.assets/image-20220310204318759.png)

- 方法一：ab | ba（even） , ab<u>x</u>ba（odd）

  回文字符串有上述两种可能，所以，思路如下：

  遍历String中的每一个char，向char的两边延申，若无法找到回文String，那么尝试当前char是否和左边的char具有相同的值，若相同就继续向两边探索。

  若失败，则继续遍历下一个。

  ![image-20220310225450543](/assets/algo-exp-note.assets/image-20220310225450543.png)

  ```java
  import java.util.*;
  // O(N^2) time O(N) space
  class Program {
      public static String longestPalindromicSubstring(String str) {
          //[0,1):一开始，字符串0号位的char是最长回文字符串
          int[] currentLongest = {0, 1};
          for (int i = 1; i < str.length(); i++) {
              //abxba
              int[] odd = getLongestPalindromeFrom(str, i - 1, i + 1);
              //abba
              int[] even = getLongestPalindromeFrom(str, i - 1, i);

              int[] longest = odd[1] - odd[0] > even[1] - even[0] ? odd : even;
              currentLongest = currentLongest[1] - currentLongest[0] > longest[1] - longest[0]
                      ? currentLongest
                      : longest;
          }
          return str.substring(currentLongest[0], currentLongest[1]);
      }

      private static int[] getLongestPalindromeFrom(String str, int leftIdx, int rightIdx) {
          while (leftIdx >= 0 && rightIdx < str.length()) {
              if (str.charAt(leftIdx) != str.charAt(rightIdx))
                  break;
              leftIdx--;
              rightIdx++;
          }
          //这里也可以写成{leftIdx, rightIdx - 1}
          //这里字串如下表示[leftIdx + 1, rightIdx),便于之后的substring
          return new int[]{leftIdx + 1, rightIdx};
      }

  }
  ```



- 方法二：遍历所有子集，不采用！！！

  ![image-20220310231400547](/assets/algo-exp-note.assets/image-20220310231400547.png)

## 4.Group Anagrams(Blue)

![image-20220310234835802](/assets/algo-exp-note.assets/image-20220310234835802.png)

- 方法一：

<img src="/assets/algo-exp-note.assets/image-20220311185827335.png" alt="image-20220311185827335" style="zoom: 50%;" />

![image-20220311190305260](/assets/algo-exp-note.assets/image-20220311190305260.png)

- 方法二：使用Map，遍历每一个String。例如，

  {oy : [yo, oy]}, {act : [act, tac, cat]}.....

  ```java
  import java.util.*;
  // O(w * n * log(n)) time | O(wn) space - where w is the number of words and n is the length of
  // the longest word
  class Program {
      public static List<List<String>> groupAnagrams(List<String> words) {
          Map<String, List<String>> anagrams = new HashMap<>();

          for (String word : words) {
              char[] chars = word.toCharArray();
              Arrays.sort(chars);
              String sortedWord = new String(chars);

              if (anagrams.containsKey(sortedWord)) {
                  anagrams.get(sortedWord).add(word);
              } else {
                  anagrams.put(sortedWord, new ArrayList<>(Arrays.asList(word)));
              }
          }

          return new ArrayList<>(anagrams.values());
      }
  }
  ```


## 5.Longest Substring Without Duplication(Red*)

![image-20220311202655575](/assets/algo-exp-note.assets/image-20220311202655575.png)

本题的思路就是遍历String，并记录每一个char上一次出现的位置。当发现有char之前出现过时，更新`startIdx`和char上一次的出现位置，从`startIdx`向后继续遍历。在遍历的过程中，不断更新符合要求的最长数组。建议调试代码理解。**本题的难点在于确定新的`startIdx`, 可以下图3号为例（一种边界情况例如：`abba`）**。

`startIdx`算是一种滑动窗口的方式，也就是说， `[abcdefg]aaaa`窗口中一直维护着一条当前最长的无重复子串，然而下一步他就会再次遇到a，这时我们先要记录当前这个最长的子串，然后滑动窗口`a[bcdefga]aaa`, `startIdx`就是窗口的左边界，当前for循环到的`i`就是这个右边界.



<img src="/assets/algo-exp-note.assets/image-20220311205731892.png" alt="image-20220311205731892" style="zoom:50%;" />

```java
import java.util.*;
// O(n) time | O(min(n, a)) space - where n is the length of the input string and a is the length of the character alphabet represented in the input string
class Program {
    public static String longestSubstringWithoutDuplication(String str) {
        int[] longest = {0, 1}; //[0,1)
        int startIdx = 0;
        Map<Character, Integer> lastSeen = new HashMap<>();

        for (int i = 0; i < str.length(); i++) {
            char c = str.charAt(i);
            if (lastSeen.containsKey(c))
                startIdx = Math.max(startIdx, lastSeen.get(c) + 1);
            if (longest[1] - longest[0] < i + 1 - startIdx)
                longest = new int[]{startIdx, i + 1};
            lastSeen.put(c, i);
        }
        return str.substring(longest[0], longest[1]);
    }
}
```

```java
import java.util.*;
// 我的版本，当遇到重复时，对找到的当前无重复子串进行记录
class Program {
    public static String longestSubstringWithoutDuplication(String str) {
        HashMap<Character, Integer> lastSeen = new HashMap<>();
        int[] longest = {0, 1};
        int startIdx = 0;

        for (int i = 0; i < str.length(); i++) {
            char letter = str.charAt(i);
            if (lastSeen.containsKey(letter)) {
                longest = longest[1] - longest[0] > i - startIdx ? longest : new int[] {startIdx, i};
                startIdx = Math.max(lastSeen.get(letter) + 1, startIdx);
            }

            lastSeen.put(letter, i);
        }
        // 遍历结束后，最后找到的子串可能没有触发条件，所以需要在最后补充一次
        longest = longest[1] - longest[0] > str.length() - startIdx ? longest : new int[] {startIdx, str.length()};
        return str.substring(longest[0], longest[1]);
    }
}
```

## 6. Underscorify Substring(Red*)

![image-20220311214012126](/assets/algo-exp-note.assets/image-20220311214012126.png)

本题方法主要为遍历每一个char，对其之后的string调用find函数，找到所有出现substring的index范围，然后同时处理index之间相互交叉的问题。这里需要注意的是find()函数的时间复杂度为`O(N+M)`, N是string长度，M是匹配字符串长度，字符串匹配算法见Famous Algorithm。

本题有两个难点，第一个是处理collapse：我们所获得的区间只有两种可能，相交或者互斥。对于相交的区间我们更新右边界最大值，对于互斥的区间我们直接把它加入。第二个难点就是根据获得的区间给substring周围画下划线，加入下划线需要一些巧思，可以看看代码的示例，另外，还需要考虑到边界情况，总而言之，细节很复杂。

![image-20220317223423193](/assets/algo-exp-note.assets/image-20220317223423193.png)



```java
import java.util.*;
// Average case: O(n + m) | O(n) space - where n is the length
// of the main string and m is the length of the substring
class Program {
    public static String underscorifySubstring(String str, String substring) {
        List<Integer[]> locations = collapse(getLocations(str,substring));
        return underscorify(str, locations);
    }

    public static List<Integer[]> collapse(List<Integer[]> locations) {
        if (locations.size() == 0)
            return locations;
        List<Integer[]> newLocations = new ArrayList<>();
        newLocations.add(locations.get(0));
        Integer[] previous = locations.get(0);

        for (int i = 1; i < locations.size(); i++) {
            if (previous[1] >= locations.get(i)[0]) {
                previous[1] = locations.get(i)[1];
            } else {
                newLocations.add(locations.get(i));
                previous = locations.get(i);
            }
        }

        return newLocations;
    }

    public static List<Integer[]> getLocations(String str, String substring) {
        List<Integer[]> locations = new ArrayList<>();
        int startIdx = 0;

        while (startIdx < str.length()) {
            int nextIdx = str.indexOf(substring, startIdx);
            if (nextIdx != -1) {
                locations.add(new Integer[]{nextIdx, nextIdx + substring.length()});
                startIdx = nextIdx + 1;
            } else {
                break;
            }
        }
        return locations;
    }

    public static String underscorify(String str, List<Integer[]> locations) {
        int locationIdx = 0;
        int stringIdx = 0;
        int i = 0;
        boolean inBetweenUnderscores = false;
        List<String> finalChars = new ArrayList<>();
        while (stringIdx < str.length() && locationIdx < locations.size()) {
            //当当前char和loaction记录的边界idx一致时，需要加入“_”
            //对于每个location，“_”要被加入两次
            //所以要使用inBetweenUnderscores判断是否可以进入下一个location
            if (stringIdx == locations.get(locationIdx)[i]) {
                finalChars.add("_");// ["_"] ["_","t","e","s","t","_"]
                inBetweenUnderscores = !inBetweenUnderscores;
                if (!inBetweenUnderscores)
                    locationIdx++;
                i = i == 0 ? 1 : 0;
            }
            //遍历过程中的每一个char都要加入到finalChars中
            //["_","t","e","s","t"]
            finalChars.add(String.valueOf(str.charAt(stringIdx)));
            stringIdx++;
        }
        //处理特殊情况
        if (locationIdx < locations.size()) {
            finalChars.add("_");
        } else if (stringIdx < str.length()) {
            finalChars.add(str.substring(stringIdx));
        }

        return String.join("", finalChars);
    }

}
```

## 7. Pattern Matcher(Red)

![image-20220318001030333](/assets/algo-exp-note.assets/image-20220318001030333.png)

<img src="/assets/algo-exp-note.assets/image-20220318181953369.png" alt="image-20220318181953369" style="zoom: 50%;" />

本题的主要难度就是多个步骤，细节的处理。思路难度不大，但一次准确写出有难度。

```java
import java.util.*;
// O(N^2 + M) time O(N + M) space
class Program {
    public static String[] patternMatcher(String pattern, String str) {
        if (pattern.length() > str.length())
            return new String[]{};

        char[] newPattern = getNewPattern(pattern);
        boolean didSwitch = newPattern[0] != pattern.charAt(0);
        Map<Character, Integer> counts = new HashMap<>();
        counts.put('x', 0);
        counts.put('y', 0);
        int firstYPos = getCountsAndFirstYPos(newPattern, counts);

        //pattern has x and y
        if (counts.get('y') != 0) {
            for (int lenOfX = 1; lenOfX < str.length(); lenOfX++) {
                double lenOfY = ((double) str.length() - (double) lenOfX * (double) counts.get('x'))
                        / (double) counts.get('y');
                if (lenOfY <= 0 || lenOfY % 1 != 0)
                    continue;

                int yIdx = firstYPos * lenOfX;
                String x = str.substring(0, lenOfX);
                String y = str.substring(yIdx, yIdx + (int) lenOfY);
                String potentialMatch = buildPotentialMatch(newPattern, x, y);
                if (str.equals(potentialMatch))
                    return didSwitch ? new String[]{y, x} : new String[]{x, y};
            }
        } else { //pattern only has x
            double lenOfX = (double) str.length() / counts.get('x');
            if (lenOfX % 1 == 0) {
                String x = str.substring(0, (int) lenOfX);
                String potentialMatch = buildPotentialMatch(newPattern, x, "");
                if (str.equals(potentialMatch))
                    return didSwitch ? new String[]{"", x} : new String[]{x, ""};
            }
        }

        return new String[]{};
    }

    public static char[] getNewPattern(String pattern) {
        char[] patternLetters = pattern.toCharArray();
        //pattern like xxyxxy
        if (pattern.charAt(0) == 'x')
            return patternLetters;
        //pattern like yyxyyx
        //change to xxyxxy
        for (int i = 0; i < patternLetters.length; i++) {
            if (patternLetters[i] == 'x')
                patternLetters[i] = 'y';
            else
                patternLetters[i] = 'x';
        }
        return patternLetters;
    }

    public static int getCountsAndFirstYPos(char[] pattern, Map<Character, Integer> counts) {
        int firstYPos = -1;
        for (int i = 0; i < pattern.length; i++) {
            char c = pattern[i];
            counts.put(c, counts.get(c) + 1);
            if (c == 'y' && firstYPos == -1)
                firstYPos = i;
        }
        return firstYPos;
    }

    public static String buildPotentialMatch(char[] pattern, String x, String y) {
        StringBuilder potentialMatch = new StringBuilder();

        for (char c : pattern) {
            if (c == 'x')
                potentialMatch.append(x);
            else
                potentialMatch.append(y);
        }
        return potentialMatch.toString();
    }
}
```

- Note

```tex
public static char[] getNewPattern(String pattern) {
        char[] patternLetters = pattern.toCharArray();
        //pattern like xxyxxy
        if (pattern.charAt(0) == 'x')
            return patternLetters;
        //pattern like yyxyyx
        //change to xxyxxy
        for (int i = 0; i < patternLetters.length; i++) {
            if (patternLetters[i] == 'x')
                patternLetters[i] = 'y';
            else
                patternLetters[i] = 'x';
        }
        return patternLetters;
}
我一开始将这一段的for循环写成了foreach，然而foreach的每一个元素并不是对应着patternLetters里的的每一个数组元素，改变foreach元素的值并不能对patternLetters的内容进行更改。
```

## 8.Run-Length Encoding(Green)

> Write a function that takes in a non-empty string and returns its run-length encoding.
>
> From Wikipedia, "run-length encoding is a form of lossless data compression in which runs of data are stored as a single data value and count, rather than as the original run." For this problem, a run of data is any sequence of consecutive, identical characters. So the run "AAA" would be run-length-encoded as "3A".
>
> To make things more complicated, however, the input string can contain all sorts of special characters, including numbers. And since encoded data must be decodable, this means that we can't naively run-length-encode long runs. For example, the run "AAAAAAAAAAAA" (12 As), can't naively be encoded as "12A", since this string can be decoded as either "AAAAAAAAAAAA" or "1AA". Thus, long runs (runs of 10 or more characters) should be encoded in a split fashion; the aforementioned run should be encoded as "9A3A".
>
> ### Sample Input
>
> ```
> string = "AAAAAAAAAAAAABBCCCCDD"
> ```
>
> ### Sample Output
>
> ```
> "9A4A2B4C2D"
> ```

本题的思路如下，简单来说，当发现9个一样的letter或者letter出现不连续时，就将letter进行编码。难点在于如何提炼逻辑使得代码简洁。

```java
import java.util.*;
// O(N) time O(N) space
class Program {
    public String runLengthEncoding(String string) {
        StringBuilder encodedString = new StringBuilder();
        int currentLength = 1;

        for (int i = 1; i < string.length(); i++) {
            char previousLetter = string.charAt(i - 1);
            char currentLetter = string.charAt(i);

            if (previousLetter != currentLetter || currentLength == 9) {
                encodedString.append(String.valueOf(currentLength));
                encodedString.append(previousLetter);
                currentLength = 0;
            }

            currentLength++;
        }

        encodedString.append(String.valueOf(currentLength));
        encodedString.append(string.charAt(string.length() - 1));

        return encodedString.toString();
    }
}
```

## 9.Generate Document(Green)

> You're given a string of available characters and a string representing a document that you need to generate. Write a function that determines if you can generate the document using the available characters. If you can generate the document, your function should return true; otherwise, it should return false.
>
> You're only able to generate the document if the frequency of unique characters in the characters string is greater than or equal to the frequency of unique characters in the document string. For example, if you're given characters = "abcabc" and document = "aabbccc" you **cannot** generate the document because you're missing one c.
>
> The document that you need to create may contain any characters, including special characters, capital letters, numbers, and spaces.
>
> Note: you can always generate the empty string ("").
>
> ### Sample Input
>
> ```
> characters = "Bste!hetsi ogEAxpelrt x " length = N
> document = "AlgoExpert is the Best!" lenght = M
> ```
>
> ### Sample Output
>
> ```
> true
> ```

- 方法一：统计document中每个字符出现的次数，并与characters中出现的次数相比较。`O(M*(N + M)) time`. 例如，对于document中的A我们需要遍历一遍document计算A的个数然后再遍历characters统计A的个数。

- 方法二：方法一显然会重复count一些相同的字符，所以我们可以建立一个Set用来保存已经count过的字符，从而时间复杂度变为`O(C*(N + M)) time`。C为document中存在的不同的字符的个数。

- 方法三：在方法二的基础上引入哈希表，遍历一遍characters建立一张<字符，出现次数>表。之后遍历document，遇到在表中的字符就对出现次数-1，若出现次数小于0那么说明不满足条件。这样时间复杂度就变得更低。

  ```java
  import java.util.*;

  class Program {
      // O(N + M) time O(C) space
      public boolean generateDocument(String characters, String document) {
          Map<Character, Integer> characterCount = new HashMap<>();

          for (int i = 0; i < characters.length(); i++) {
              char character = characters.charAt(i);
              characterCount.put(character, characterCount.getOrDefault(character, 0) + 1);
          }

          for (int i = 0; i < document.length(); i++) {
              char character = document.charAt(i);
              // 如果没有这个字符或者这个字符超过了characters中本来有的数量
              if (!characterCount.containsKey(character) || characterCount.get(character) == 0) {
                  return false;
              }

              characterCount.put(character, characterCount.get(character) - 1);
          }

          return true;
      }
  }
  ```

## 10.First Non-Repeating Character(Green)

> Write a function that takes in a string of lowercase English-alphabet letters and returns the index of the string's first non-repeating character.
>
> The first non-repeating character is the first character in a string that occurs only once.
>
> If the input string doesn't have any non-repeating characters, your function should return -1.
>
> ### Sample Input
>
> ```
> string = "abcdcaf"
> ```
>
> ### Sample Output
>
> ```
> 1 // The first non-repeating character is "b" and is found at index 1.
> ```

- 方法一：遍历String中的所有字符。对于每个字符，继续遍历原来的String查看是否有重复的字符`O(N^2) time`。
- 方法二：用哈希表。与第9题类似。

```java
import java.util.*;
// O(n) time | O(1) space - where n is the length of the input string
// The constant space is because the input string only has lowercase
// English-alphabet letters; thus, our hash table will never have more
// than 26 character frequencies.
class Program {

    public int firstNonRepeatingCharacter(String string) {
        HashMap<Character, Integer> characterCount = new HashMap<>();

        for (int i = 0; i < string.length(); i++) {
            char letter = string.charAt(i);
            characterCount.put(letter, characterCount.getOrDefault(letter, 0) + 1);
        }

        for (int i = 0; i < string.length(); i++) {
            char letter = string.charAt(i);
            if (characterCount.get(letter) == 1) {
                return i;
            }
        }

        return -1;
    }
}
```

## 11.Valid IP Addresses(Blue)

> You're given a string of length 12 or smaller, containing only digits. Write a function that returns all the possible IP addresses that can be created by inserting three .s in the string.
>
> An IP address is a sequence of four positive integers that are separated by .s, where each individual integer is within the range 0 - 255, inclusive.
>
> An IP address isn't valid if any of the individual integers contains leading 0s. For example, "192.168.0.1" is a valid IP address, but "192.168.00.1" and "192.168.0.01" aren't, because they contain "00" and 01, respectively. Another example of a valid IP address is "99.1.1.10"; conversely, "991.1.1.0" isn't valid, because "991" is greater than 255.
>
> Your function should return the IP addresses in string format and in no particular order. If no valid IP addresses can be created from the string, your function should return an empty list.
>
> Note: check out our Systems Design Fundamentals on SystemsExpert to learn more about IP addresses!
>
> ### Sample Input
>
> ```
> string = "1921680"
> ```
>
> ### Sample Output
>
> ```
> [
>   "1.9.216.80",
>   "1.92.16.80",
>   "1.92.168.0",
>   "19.2.16.80",
>   "19.2.168.0",
>   "19.21.6.80",
>   "19.21.68.0",
>   "19.216.8.0",
>   "192.1.6.80",
>   "192.1.68.0",
>   "192.16.8.0"
> ]
> // The IP addresses could be ordered differently.
> ```

<img src="/assets/algo-exp-note.assets/image-20220520221942027.png" alt="image-20220520221942027" style="zoom: 50%;" />

本题其实可以有一种recursion的想发，用栈帧保存第一个点可能有的三种状态，然后继续递归探索第二个点的三种状态。然而，本题的递归深度其实是有限的，因为我们只会将IP地址分成四段，从而用for循环可以以更好的时间复杂度以及空间复杂度解决本问题。本题的难点主要在于界定每个点的范围，也就是for循环中的循环次数。

```java
import java.util.*;
// O(1) time O(1) space
class Program {

    public ArrayList<String> validIPAddresses(String string) {
        ArrayList<String> ipAddressesFound = new ArrayList<>();

        for (int i = 1; i < Math.min(string.length(), 4); i++) {
            String[] currentIPAddressesParts = {"", "", "", ""};

            currentIPAddressesParts[0] = string.substring(0, i);
            if (!isValidPart(currentIPAddressesParts[0]))
                continue;

            for (int j = i + 1; j < i + Math.min(string.length() - i, 4); j++) {
                currentIPAddressesParts[1] = string.substring(i, j);
                if (!isValidPart(currentIPAddressesParts[1]))
                    continue;

                for (int k = j + 1; k < j + Math.min(string.length() - j, 4); k++) {
                    currentIPAddressesParts[2] = string.substring(j, k);
                    currentIPAddressesParts[3] = string.substring(k);

                    if ((isValidPart(currentIPAddressesParts[2]) && isValidPart(currentIPAddressesParts[3])))
                        ipAddressesFound.add(String.join(".", currentIPAddressesParts));
                }
            }
        }
        return ipAddressesFound;
    }

    private boolean isValidPart(String str) {
        int stringToInt = Integer.parseInt(str);
        if (stringToInt > 255)
            return false;
        // 检查是否str以0开头
        return str.length() == String.valueOf(stringToInt).length();
    }
}
```

```java
import java.util.*;
// 让代码更容易理解
class Program {

    public ArrayList<String> validIPAddresses(String string) {
        ArrayList<String> ipAddressesFound = new ArrayList<>();

        for (int i = 1; i < 4; i++) {
            String[] currentIPAddressesParts = {"", "", "", ""};
            // 每个点都有可能包含1-3个字符，但在遍历的过程中，可能已经到达了string末尾
            // 所以需要限制循环的次数
            if (i == string.length())
                break;
            currentIPAddressesParts[0] = string.substring(0, i);
            if (!isValidPart(currentIPAddressesParts[0]))
                continue;

            for (int j = i + 1; j < i + 4; j++) {
                if (j == string.length())
                    break;
                currentIPAddressesParts[1] = string.substring(i, j);
                if (!isValidPart(currentIPAddressesParts[1]))
                    continue;

                for (int k = j + 1; k < j + 4; k++) {
                    if (k == string.length())
                        break;
                    currentIPAddressesParts[2] = string.substring(j, k);
                    currentIPAddressesParts[3] = string.substring(k);

                    if ((isValidPart(currentIPAddressesParts[2]) && isValidPart(currentIPAddressesParts[3])))
                        ipAddressesFound.add(String.join(".", currentIPAddressesParts));
                }
            }
        }
        return ipAddressesFound;
    }

    private boolean isValidPart(String str) {
        int stringToInt = Integer.parseInt(str);
        if (stringToInt > 255)
            return false;
        // 检查是否str以0开头
        return str.length() == String.valueOf(stringToInt).length();
    }
}
```

## 12.Reverse Words In String(Blue)

> Write a function that takes in a string of words separated by one or more whitespaces and returns a string that has these words in reverse order. For example, given the string "tim is great", your function should return "great is tim".
>
> For this problem, a word can contain special characters, punctuation, and numbers. The words in the string will be separated by one or more whitespaces, and the reversed string must contain the same whitespaces as the original string. For example, given the string "whitespaces    4" you would be expected to return "4    whitespaces".
>
> Note that you're **not** allowed to to use any built-in split or reverse methods/functions. However, you **are** allowed to use a built-in join method/function.
>
> Also note that the input string isn't guaranteed to always contain words.
>
> ### Sample Input
>
> ```
> string = "AlgoExpert is the best!"
> ```
>
> ### Sample Output
>
> ```
> "best! the is AlgoExpert"
> ```

<img src="/assets/algo-exp-note.assets/image-20220521142054654.png" alt="image-20220521142054654" style="zoom:50%;" />

```java
import java.util.*;
// O(N) time O(N) space
class Program {

    public String reverseWordsInString(String string) {
        ArrayList<String> words = new ArrayList<>();
        int startIdx = 0;

        for (int i = 0; i < string.length(); i++) {
            char letter = string.charAt(i);

            // 遇到' '空格，将[startIdx, i)之间的内容加入words
            // 更新startIdx
            if (letter == ' ') {
                words.add(string.substring(startIdx, i));
                startIdx = i;
            }
            // 遇到字母, 需要判断其前方是否有空格
            else if (string.charAt(startIdx) == ' ') {
                words.add(" ");
                startIdx = i;
            }
        }

        words.add(string.substring(startIdx));
        Collections.reverse(words);
        return String.join("", words);
    }
}
```

```java
import java.util.*;
// O(N) time O(N) space
class Program1 {

    public String reverseWordsInString(String string) {
        char[] characters = string.toCharArray();
        reverseListInRange(characters, 0, characters.length - 1);

        int startOfWord = 0;
        for (int i = 0; i < characters.length; i++) {
            if (characters[i] == ' ') {
                reverseListInRange(characters, startOfWord, i);
                startOfWord = i;
            } else if (characters[startOfWord] == ' ') {
                startOfWord = i;
            }
        }

        reverseListInRange(characters, startOfWord, characters.length - 1);
        return new String(characters);
    }

    private void reverseListInRange(char[] characters, int left, int right) {
        while (left < right) {
            char temp = characters[left];
            characters[left] = characters[right];
            characters[right] = temp;
            left++;
            right--;
        }
    }
}
```

## 13.Minimum Characters For Words(Blue)

> Write a function that takes in an array of words and returns the smallest array of characters needed to form all of the words. The characters don't need to be in any particular order.
>
> For example, the characters ["y", "r", "o", "u"] are needed to form the words ["your", "you", "or", "yo"].
>
> Note: the input words won't contain any spaces; however, they might contain punctuation and/or special characters.
>
> ### Sample Input
>
> ```
> words = ["this", "that", "did", "deed", "them!", "a"]
> ```
>
> ### Sample Output
>
> ```
> ["t", "t", "h", "i", "s", "a", "d", "d", "e", "e", "m", "!"]
> // The characters could be ordered differently.
> ```



# Tries

## 1.Suffix Trie Construction(Blue)

> Write a `SuffixTrie` class for a `Suffix-Trie-like` data structure. The class should have a root property set to be the root node of the trie and should support:
>
> - Creating the trie from a string; this will be done by calling the `populateSuffixTrieFrom` method upon class instantiation, which should populate the root of the class.
> - Searching for strings in the trie.
>
> Note that every string added to the trie should end with the special `endSymbol` character: "*".
>
> If you're unfamiliar with Suffix Tries, we recommend watching the Conceptual Overview section of this question's video explanation before starting to code.
>
> ### Sample Input (for creation)
>
> ```
> string = "babc"
> ```
>
> ### Sample Output (for creation)
>
> ```
> The structure below is the root of the trie.
> {
>   "c": {"*": true},
>   "b": {
>     "c": {"*": true},
>     "a": {"b": {"c": {"*": true}}},
>   },
>   "a": {"b": {"c": {"*": true}}},
> }
> ```
>
> ### Sample Input (for searching in the suffix trie above)
>
> ```
> string = "abc"
> ```
>
> ### Sample Output (for searching in the suffix trie above)
>
> ```
> true
> ```

<img src="/assets/algo-exp-note.assets/image-20220522221826181.png" alt="image-20220522221826181" style="zoom:50%;" />

```java
import java.util.*;

class Program {
    // Do not edit the class below except for the
    // populateSuffixTrieFrom and contains methods.
    // Feel free to add new properties and methods
    // to the class.
    static class TrieNode {
        Map<Character, TrieNode> children = new HashMap<Character, TrieNode>();
    }

    static class SuffixTrie {
        TrieNode root = new TrieNode();
        char endSymbol = '*';

        public SuffixTrie(String str) {
            populateSuffixTrieFrom(str);
        }

        // O(N^2) time O(N^2) space
        public void populateSuffixTrieFrom(String str) {
            for (int i = 0; i < str.length(); i++)
                insertSubStringStartingAt(i, str);
        }

        private void insertSubStringStartingAt(int i, String str) {
            TrieNode node = root;
            for (int j = i; j < str.length(); j++) {
                char letter = str.charAt(j);
                if (!node.children.containsKey(letter)) {
                    TrieNode newNode = new TrieNode();
                    node.children.put(letter, newNode);
                }

                node = node.children.get(letter);
            }

            node.children.put(endSymbol, null);
        }

        // O(M) time O(1) space
        public boolean contains(String str) {
            TrieNode node = root;
            for (int i = 0; i < str.length(); i++) {
                char letter = str.charAt(i);
                if (!node.children.containsKey(letter)) {
                    return false;
                }
                node = node.children.get(letter);
            }

            return node.children.containsKey(endSymbol);
        }
    }
}
```

