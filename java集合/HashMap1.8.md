HashMap1.8
---
HashMap1.8中改动很大，底层数据结构变成了数组+链表+红黑树，链表中个数大于8个时,转换成红黑树，小于6个时,再转成链表；链表是Node,红黑树是TreeNode<br>
Node<br>
![image](https://github.com/wangda7/77/blob/master/picture/14.png)<br>
TreeNode<br>
![image](https://github.com/wangda7/77/blob/master/picture/15.png)<br>
<br>
几点注意：<br>
1.红黑树的根结点一定是索引位置的头结点（也就是链表的头结点），通过moveRootToFront方法来维持<br>
2.转为红黑树节点后，链表的结构还存在，通过next属性维持，红黑树节点在进行操作时都会维护链表的结构，并不是转为红黑树节点，链表结构就不存在了。<br>
3.在红黑树上，叶子节点也可能有next节点，因为红黑树的结构跟链表的结构是互不影响的，不会因为是叶子节点就说该节点已经没有next节点。<br>
4.Node是单向链表，移除操作只需用prev.next = next这一模式即可<br>
5.TreeNode是红黑树结构，同时多了一个prev引用，移除时需按照双向链表移除的方式移除，另外，这只是红黑树维护链表结构的移除，红黑树还需单独的移除操作。<br>
