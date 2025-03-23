
```sql
<![CDATA[<=]]>    小于等于

date_part( 'day', ( CAST ( reg_date AS TIMESTAMP ) - CAST ( now( ) AS TIMESTAMP ) ) )    日期加减   

 ORDER BY LPAD(s.num, 10)   字符串排序
 
s.unsure LIKE CONCAT('%',#{vo.unsure},'%')   模糊搜索

参数是集合
<if test="null!=standardIds">
    AND rel.standard_Id  IN
    <foreach collection="standardIds" open="(" close=")" index="index" item="item" separator=",">
        #{item}
    </foreach>
</if>    


@SqlParser(filter = true）  //过滤企业id



//if else 判断
 <where>
            <choose>
                <when test="0==vo.type">
                    extract(year from i.ctime)  = #{vo.time}
                </when>
                <when test="1==vo.type">
                    extract(month from i.ctime)   IN
                    <foreach collection="vo.months"  index="index" item="item" open="(" separator="," close=")"  >
                        #{item}
                    </foreach>
                </when>
                <when test="2==vo.type">
                    extract(month from i.ctime)    = #{vo.time}
                </when>
            </choose>
        </where>

```
