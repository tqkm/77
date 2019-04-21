LinkedList  1.8
---

LinkedList是一个双向链表,成员变量如下(仅讨论作为链表，不讨论作为队列)
```
transient int size = 0;//所含Node的个数，也就是当前集合的大小
transient Node<E> first;//头结点
transient Node<E> last;//尾结点
```
Node为其静态内部类，操纵LinkedList其实就是操纵的Node节点,含有其上一个与下一个Node节点的引用，item为真正存储的值
```
 private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
 ``` 
 构造方法有两个,不带参数的什么也没做，另一个带参数的将集合添加进来
 ```
  public LinkedList() {
  }
  public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
  }
 ```
 看一下addAll()方法,两个重载方法，size是当前LinkedList集合的大小，其实就是从当前集合的末尾逐一将要添加的集合添加进去；先把要添加的集合转换成
 数组，数组长度为零也就意味着集合为空，返回false；接下来就是循环遍历数组，将Node一个一个添加到末尾
 ```
 public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
 }
  public boolean addAll(int index, Collection<? extends E> c) {
        checkPositionIndex(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        if (numNew == 0)
            return false;

        Node<E> pred, succ;
        if (index == size) {
            succ = null;
            pred = last;
        } else {
            succ = node(index);
            pred = succ.prev;
        }

        for (Object o : a) {
            @SuppressWarnings("unchecked") E e = (E) o;
            Node<E> newNode = new Node<>(pred, e, null);
            if (pred == null)
                first = newNode;
            else
                pred.next = newNode;
            pred = newNode;
        }

        if (succ == null) {
            last = pred;
        } else {
            pred.next = succ;
            succ.prev = pred;
        }

        size += numNew;
        modCount++;
        return true;
    }
 ```
 从头结点添加节点，首先拿到头节点first，然后创建一个新节点让其next的指向first，prev为null，这样first就成了新节点的next节点了，此时再将first指向
 创建的节点；如果此前集合没有任何节点的话，需将last节点也指向刚创建的节点，否则就将f节点的prev指向newNode即可。size++
 ```
 public void addFirst(E e) {
        linkFirst(e);
 }
 private void linkFirst(E e) {
        final Node<E> f = first;
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;
        if (f == null)
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
 }
 ```
 从末尾添加节点,先取得最后一个节点赋值给l，创建新节点newNode，其prev引用为l，将last指向newNode，如果此时集合中还没有节点，需将first节点的引用也指向
 newNode,否则只需要l节点的next引用指向newNode即可；
 ```
 public void addLast(E e) {
        linkLast(e);
 }
 void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
 }
 ```
 添加节点,就是调用了linkLast方法
 ```
 public boolean add(E e) {
        linkLast(e);
        return true;
 }
 ```
 从任意位置添加节点,首先要对下标做检查呢，不能大于size也不能小于0；当index==size时，直接调用linkLast方法从末尾添加节点，否则调用LinkBefore()方法
 先调用node方法找到对应下标的Node节点，当index小于size一半时，从头开始找，否则从尾开始找；找到后就是正常的链表操作
 ```
 public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
 }
 Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
 void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
        final Node<E> pred = succ.prev;
        final Node<E> newNode = new Node<>(pred, e, succ);
        succ.prev = newNode;
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }
 ```
 get方法,调用node方法，比较简单
 ```
 public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
 }
 ```
 set方法,检查index坐标是否越界，调用node方法，将新值赋给item，同时返回旧值
 ```
  public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
 }
 ```
 remove(int index)方法,当前节点x，前节点prev，后节点next，主要就是prev.next = next,next.prev = prev，x.prev == null,x.next == null这样x节点就被移除了；需
 注意的是某些特殊情况时，不要引用空指针；
 ```
 public E remove(int index) {
        checkElementIndex(index);
        return unlink(node(index));
 }
 E unlink(Node<E> x) {
        // assert x != null;
        final E element = x.item;
        final Node<E> next = x.next;
        final Node<E> prev = x.prev;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;
        size--;
        modCount++;
        return element;
    }
 ```
 remove(Object o)方法,当o为null时，遍历集合，找到第一个为x.item = null值的的o，调用unlink方法移除;否则找到对应的item，移除
 ```
  public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
 }
 ```
 
