# select语句的执行流程


- 1. from, join
    - 如果没有多表联查，就直接from table拿到原始表数据
    - 如果涉及多表联查
        - 先cross join表，得到虚拟表v1-1
        - 再通过on条件筛选数据，得到虚拟表v1-2
        - 根据是left join / right join，得到表v1-3 
- 2. where
    - 根据原始数据表v1，过滤数据，得到v2
- 3. group by,having
    - 进行分组和分组过滤，得到v3
- 4. select, distinct
    - 数据过滤完了，选取字段，进行字段数据去重，得到v4
- 5. order by
    - 进行字段排序，得到v5
- 6. limit
    - 根据limit关键字进行行数过滤，得到v6，并返回