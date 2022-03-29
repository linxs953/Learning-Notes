## mysql count函数


- count(*)
  - mysql特殊优化，不取值
  - 不是null，按行累加
- count(1)
  - 获取每一行记录，往里面添加“1”
- count(primary id)
  - 通过最小的索引树取获取主键id
  - 获取每一行记录，解析记录拿到id
  - 判断id是否是null，不是null再累加
- count(field)
  - 如果没有索引，根据选主键索引
  - 获取每一行记录
  - 判断是否是null，不是null就累加


- 效率对比（从高到低）
  - count(*)=count(1)(约等于)
  - count(1) > count(primary id) > count(field)
