HashSet
---
HashSet是一个hash映射，底层是hashmap,hashset的主要作用就是去重，去重的原理利用的就是hashmap中key不重复，若是熟悉hashmap,hashset就很容易理解<br>
![image](https://github.com/wangda7/77/blob/master/picture/13.png)<br>
可以看到有一个hashmap和一个Object对象<br><br>
```
public HashSet() {
        map = new HashMap<>();
}
 public HashSet(Collection<? extends E> c) {
        map = new HashMap<>(Math.max((int) (c.size()/.75f) + 1, 16));
        addAll(c);
}
public HashSet(int initialCapacity, float loadFactor) {
        map = new HashMap<>(initialCapacity, loadFactor);
}
public HashSet(int initialCapacity) {
        map = new HashMap<>(initialCapacity);
}
```
从上面的部分构造方法中可以看出都是创造了hashmap<br><br>

```
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
}
```
add方法是将输入的e作为key，放入hashmap中，如果返回值为null,就说明之前map中没有该key的存在，添加成功；若是不为null，则说明map中已存在该key，添加失败

```
public boolean remove(Object o) {
        return map.remove(o)==PRESENT;
}
```
调用的map中的remove方法

```
public boolean contains(Object o) {
        return map.containsKey(o);
}
```
调用的map中的containsKey()方法
