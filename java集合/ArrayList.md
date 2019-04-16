ArrayList 1.8
---
继承实现结构<br>
![image](https://github.com/wangda7/77/blob/master/picture/1.png)<br>
<br>
底层是一个Obejct数组，默认大小为10，size为数组中元素的个数<br>
![image](https://github.com/wangda7/77/blob/master/picture/2.png)<br>

有三种构造方法<br>
第一种：<br>
![image](https://github.com/wangda7/77/blob/master/picture/3.png)<br>
这种构造方法会创建一个空的Object数组<br>
第二种：<br>
![image](https://github.com/wangda7/77/blob/master/picture/4.png)<br>
如果initialCapacity>0,则根据initialCapacity的值创建对应大小的Object数组；如果initialCapacity=0的话，会创建一个空的Object数组；initialCapacity小于0的话会抛出非法参数异常<br>
第三种：<br><br>
![image](https://github.com/wangda7/77/blob/master/picture/5.png)<br>
第三种则是在ArrayList初始化时添加一个集合，先将集合变成数组，如果数组的长度不为0，并且数组的类型不为Obejct，则用Arrays.copyOf方法创建一个Object类型的数组；如果一开始数组的长度就为0的话，就创建一个空的数组<br>
查询元素下标位置，可以看出ArrayList允许null值，如果要找的值为空，则找出第一个值为null的元素的小标，否则就for循环找到要找的值，没有的话返回-1；可以看出时间复杂度为O(n)
```
public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }
```
从后先前找元素值，与indexOf原理一致
```
public int lastIndexOf(Object o) {
        if (o == null) {
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }
```
查找对应下标的元素  时间复杂度为O(1)
```
public E get(int index) {
        rangeCheck(index);

        return elementData(index);
    }
 E elementData(int index) {
        return (E) elementData[index];
    }
 ```
 根据下标设置元素，首先检查下标是否越界，然后找到原下标上的值，在该下标上赋新值，返回旧值
 ```
 public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
 ``` 
 <br><br>
 添加元素:<br>
 首先是扩容的代码:可以看出新容量是旧容量的1.5倍(不一定，还要另外两种情况)，并且扩容时用的是Arrays.copyOf()方法
 ```
 private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
 ```
 该方法直接从末尾添加元素，添加前将当前元素个数+1与现在的数组大小比较，如果大于零则进行扩容
 ```
 public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
 ```
 在指定位置添加元素：首先判断下标是否越界，接着判断是否需要扩容，用System.arraycopy()方法复制数组，添加元素
 ```
 public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
            ，             size - index);
        elementData[index] = element;
        size++;
    }
 ``` 
 <br>
 根据下标移除元素:先判断下标是否越界，修改modCoundt值，取得原下标的值，判断是否为要删除的值是否为最后一个，不是的话用System.arraycopy()方法创建一个新数组，去掉要删除的元素，然后将数组最后一个元素置空
 ```
  public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
 ```   
 根据元素移除下标：如果元素为null，则移除第一个为null值的元素；否则移除对应值的元素
```
 public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }
```
 
 
 




















