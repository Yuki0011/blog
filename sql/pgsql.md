##pgsql

1.多行数据用逗号拼接
```
SELECT
  列1,string_agg(列2,',') AS 别名
FROM
  表名
GROUP BY,列1;
```

2.两个日期之间的天数
```
extract(day from (implement_date-now()))
```

3.计算两个时间之间的小时数
```
select extract(day from (end-start)) * 24 + extract(hour from (end-start)) from a 
```

4.查询一段时间内的所有日期 (统计用)
```
-- 1.序列查询
select date(t) as day
from 
generate_series('2020-06-09'::date,'2020-11-03', '1 days') as t;
-- 2.递归查询:查询效率比序列快一点
with recursive t(n) as (
    select date('2020-06-09')
    union all 
    select n+1 from t where n < date('2020-11-03')
)select n as day from t;
```
5.数据中排序字段既有数字也有非数字，要求数字排在前面且正序排，非数字排在后面正序排
直接order by 是有问题的，带小数点的位置不固定，还是要将符合数字类型的转换成数字进行排序

```
直接转换 非数字 会报错 ，需要一个判断是否是数字的函数
CREATE
OR REPLACE FUNCTION isnumeric (txtStr VARCHAR) RETURNS BOOLEAN AS $$
BEGIN
	RETURN txtStr ~ '^([0-9]+[.]?[0-9]*|[.][0-9]+)$' ;
END ; $$ LANGUAGE 'plpgsql';

where isnumeric(code) = true  order by  cast(code,numeric) asc 
union all
where  isnumeric(code) = false order by code asc  //分别排序
```
6.一个条件，多个值的排序 用 order by case when 

```
根据一个条件的多个值，进行排序。 

     order by
     case when status=1 then 0
             when status=0 then 1
             when status=4 then 2
             when status=3 then 3
             when status=2 then 4 ,
    add_time desc

此句sql的意思是： 先根据用户状态排序：   状态为1的排在最前面  >其次是状态为0  >状态为4  >状态为3  >状态为2 ; 再根据添加时间降序排序。

```

7.在聚合函数中使用case when  得到符合要求的数量
```
 sum(case when s.status  <![CDATA[<]]> 300 then 1 else 0 end ) as num
```

8.近 一段时间

```
--近一周
  select now() - interval '1 week';

--近一月
 select now() - interval '1 month';

--近一年
 select now() - interval '1 year';
```
  
  
9.当天，周，月
```
            <choose>
                <when test="type == 0">
                    AND DATE_TRUNC('day', ctime) = CURRENT_DATE
                </when>
                <when test="type == 1">
                    AND ctime >= (SELECT CURRENT_DATE -(EXTRACT (DOW FROM CURRENT_DATE) - 1 || 'day')::interval)
                    AND ctime <![CDATA[<]]> (SELECT CURRENT_DATE -(EXTRACT (DOW FROM CURRENT_DATE) - 8
                    ||'day')::INTERVAL)
                </when>
                <when test="type == 2">
                    AND TO_CHAR(ctime,'YYYY-MM') = TO_CHAR(CURRENT_DATE,'YYYY-MM')
                </when>
                <otherwise>
                    AND DATE_TRUNC('day', ctime) = CURRENT_DATE
                </otherwise>
            </choose>
```

10.除法保留小数

```
select round(1::numeric/4::numeric,2);  //不转换类型的话，结果保留整数
```

11.格式化日期 到 小时

```
to_char(time, 'HH24:MI:SS')   //正确
to_char(time, 'hh:mm:ss')    //错误

HH24	一天的小时数(00-23)
MI	   分钟(00-59)


```