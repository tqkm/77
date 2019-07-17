Mysql
---
视图：根据编写的查询sql语句所建立的一张数据表，但并不存储数据，对该视图可通过sql直接进行查询，可以减少代码中的sql复杂度。基本格式为：
create view as 视图名（列名，列名）as select...(sql查询语句)   
