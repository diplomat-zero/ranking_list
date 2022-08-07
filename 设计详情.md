
数据库表结构
| id  | user_id | score | time | create_time| update_time |
|-------|----|-------|----|----|----|
| 自增id  | 用户id  | 获得的总分     | 获得分数的时间  | 创建时间| 更新时间|

每一行存储用户的userid和对应的活动总分以及获得分数的时间

```
缓存结构
用zset存储
zset的key是一个个活动总分，member是用户的userid，score是获得分数的时间

用list存储
存储每个总分的总人数，按照0-10000的顺序插入，总人数为0的插入0，保证list里有10000个元素
```

```
查询名次伪代码

输入查询的user_id
根据user_id从mysql中查询获得的分数
通过list拉出大于等于该分数的所有元素，将大于的算总和sum_m,当前分数总人数为n
再通过分数对应的zset获取该user_id对应的名次
if 名次 > 10 && n - 名次 > 10 {
    直接在同一个zset中拉取上下十名的user_id
} else if 名次 <= 10 &&  n - 名次 > 10{
    在同一个zset中拉取后十名user_id
    在同一个zset中拉取所有前面的user_id
    在该分数的上一个分数的最后拉取剩余的几名user_id
} else if 名次 > 10 && n - 名次 <= 10 {
    在同一个zset中拉取前十名user_id
    在同一个zset中拉取所有后面的user_id
    在该分数的下一个分数的开始拉取剩余的几名user_id
} else if 名次 <= 10 && n - 名次 <= 10 {
    在同一个zset中拉取全部的user_id
    在该分数的上一个分数的最后拉取剩余的几名user_id
    在该分数的下一个分数的开始拉取剩余的几名user_id
}
最后的名次都要加上之前的sum_m
```