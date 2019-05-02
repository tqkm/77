HashMap 1.7
---
底层是一个Entry<K,V>数组，采用的数据结构为数组加链表的形式，它是线程不安全的
![image](https://github.com/wangda7/77/blob/master/picture/6.png)<br>
可以看到默认大小为16，最大容量为2^30，当当前的容量*加载因子 < 哈希表中的条目时，就会进行rehash<br>
<br>
![image](https://github.com/wangda7/77/blob/master/picture/7.png)<br>
带有容量和加载因子的构造函数<br><br>
![image](https://github.com/wangda7/77/blob/master/picture/8.png)<br>
只带有初始容量的构造函数，加载因子为默认的0.75<br><br>
![image](https://github.com/wangda7/77/blob/master/picture/9.png)<br>
不带任何参数的构造方法，其值都为默认值<br><br><br>


![image](https://github.com/wangda7/77/blob/master/picture/10.png)<br>
Entry静态内部类部分参数<br><br>


indexFor()方法，hashmap中计算数组下标的方法,先把key计算hash得到h，再与数组长度进行位于，由于hashmap中数组长度总是2的n次方，所以这种计算方法
等价于h%length，但是&比%具有更高的效率。
```
static int indexFor(int h, int length) {
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        return h & (length-1);
 }
 ```

在put()方法前，先看一下hashmap的扩容问题，当当前的size即Entry的个数大于阈值threshold时，便会进行扩容
```
void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);  //此处可知，每次扩容数组会扩大一倍
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }
```
具体的resize方法,当容量达到Integer.MAX_VALUE时，便不会扩容了；否则，会创建一个新数组，将原数组的内容全部转移到新数组中,initHashSeedAsNeeded()方法
会判断是否需要重新进行hash计算
```
void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }
```
对所有数据重新进行hash计算，重新确定所在新数组中的位置，以及发生hash冲突的则生成链表结构

```
void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }
 ```
![image](https://github.com/wangda7/77/blob/master/picture/11.png)<br>
put方法，先对key进行hash，然后用hash后的值与表长度去模运算找到对应的下标，如果当前的key已存在，那么就会将新值赋给key，将旧值返回；如果key不存在，
会创建一个Entry，并且将其next引用指向之前该下标的值，并把当前下标值赋给Entry;具体如下createEntry()方法,还有扩容问题<br><br>

![image](https://github.com/wangda7/77/blob/master/picture/12.png)<br><br>

在上述put方法中，有一个putForNullKey()方法，就是给map中放一个key为null的值,可以看出key为null的键值是放在数组下标为0的链表上的
```
private V putForNullKey(V value) {
        for (Entry<K,V> e = table[0]; e != null; e = e.next) {
            if (e.key == null) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;
        addEntry(0, null, value, 0);
        return null;
    }
```

remove()方法,首先要找到key所对应的entry，根据hash和数组长度确定下标i，找到entry，在从链表中拿到对应的entry；具体操作中要注意的是若table[i]对应
的entry即为要找的entry的话，table[i]要指向下一个entry，否则就是正常的链表移除操作
```
public V remove(Object key) {
        Entry<K,V> e = removeEntryForKey(key);
        return (e == null ? null : e.value);
}
final Entry<K,V> removeEntryForKey(Object key) {
        if (size == 0) {
            return null;
        }
        int hash = (key == null) ? 0 : hash(key);
        int i = indexFor(hash, table.length);
        Entry<K,V> prev = table[i];
        Entry<K,V> e = prev;

        while (e != null) {
            Entry<K,V> next = e.next;
            Object k;
            if (e.hash == hash &&
                ((k = e.key) == key || (key != null && key.equals(k)))) {
                modCount++;
                size--;
                if (prev == e)
                    table[i] = next;
                else
                    prev.next = next;
                e.recordRemoval(this);
                return e;
            }
            prev = e;
            e = next;
        }
        return e;
}       
```

get()方法,和remove方法类似，根据key去寻找对应的entry，若是找到了则返回对应的value，若是没有则返回null
```
public V get(Object key) {
        if (key == null)
            return getForNullKey();
        Entry<K,V> entry = getEntry(key);

        return null == entry ? null : entry.getValue();
}
final Entry<K,V> getEntry(Object key) {
        if (size == 0) {
            return null;
        }

        int hash = (key == null) ? 0 : hash(key);
        for (Entry<K,V> e = table[indexFor(hash, table.length)];
             e != null;
             e = e.next) {
            Object k;
            if (e.hash == hash &&
                ((k = e.key) == key || (key != null && key.equals(k))))
                return e;
        }
        return null;
}
```
