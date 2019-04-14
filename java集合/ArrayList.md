ArrayList 1.8
---
继承实现结构<br>
![image](https://github.com/wangda7/77/blob/master/picture/1.png)<br>
<br>
底层是一个Obejct数组，默认大小为10<br>
![image](https://github.com/wangda7/77/blob/master/picture/2.png)<br>

有三种构造方法<br>
第一种：<br>
![image](https://github.com/wangda7/77/blob/master/picture/3.png)<br>
这种构造方法会创建一个空的Object数组<br>
第二种：<br>
![image](https://github.com/wangda7/77/blob/master/picture/4.png)<br>
如果initialCapacity>0,则根据initialCapacity的值创建对应大小的Object数组；如果initialCapacity=0的话，会创建一个空的Object数组；initialCapacity小于0的话会抛出非法参数异常<br>
第三种：<br>
![image](https://github.com/wangda7/77/blob/master/picture/5.png)<br>
第三种则是在ArrayList初始化时添加一个集合，先将集合变成数组，如果数组的长度不为0，并且数组的类型不为Obejct，则用Arrays.copyOf方法创建一个Object类型的数组；如果一开始数组的长度就为0的话，就创建一个空的数组<br>
