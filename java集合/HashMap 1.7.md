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

![image](https://github.com/wangda7/77/blob/master/picture/11.png)<br>
put方法，先对key进行hash，然后用hash后的值与表长度去模运算找到对应的下标，如果当前的key已存在，那么就会将新值赋给key，将旧值返回；如果key不存在，
会创建一个Entry，并且将其next引用指向之前该下标的值，并把当前下标值赋给Entry;具体如下createEntry()方法,还有扩容问题<br><br>

![image](https://github.com/wangda7/77/blob/master/picture/12.png)
