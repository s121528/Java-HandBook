# 1 分页查询

## 1.1 总条数

关于pageHeler这个插件很早就用过,但总有些问题,比如说:

pageInfo.getTotal()获取的总是分页当前的数据条数,

今天抽空研究了下发现**使用这个插件中间只能有一次进行查询的操作!!**

**如果进行了两次查询操作就会让pageInfo.getTotal()获得的是当前查询的当前页的数据总条数**,

所以谨记!!

## 1.2 全部数据



# 2 条件查询

queryWrapper