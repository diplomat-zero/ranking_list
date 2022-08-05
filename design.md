
数据库表结构
| id  | user_id | score | time | create_time| update_time |
|-------|----|-------|----|----|----|
| 自增id  | 用户id  | 获得的总分     | 获得分数的时间  | 创建时间| 更新时间|

每一行存储用户的userid和对应的活动总分以及获得分数的时间

```
缓存结构
用zset存储
zset的key是一个个活动总分，member是用户的userid，score是获得分数的时间
```