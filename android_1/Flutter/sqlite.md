

 <br/>

> 在一个查询中，HAVING 子句必须放在 GROUP BY 子句之后，必须放在 ORDER BY 子句之前。下面是包含 HAVING 子句的 SELECT 语句的语法：

  <br/>

```
SELECT column1, column2

FROM table1, table2

WHERE [ conditions ]

GROUP BY column1, column2

HAVING [ conditions ]

ORDER BY column1, column2
```

 <br/>

>  NULL：空值相当于Java中的null
>
>  INTEGER：带符号的整型，相当于Java中的int型
>
>  REAL：浮点数字，相当于Java中float/double型
>
>  TEXT/VARCHAR：字符串文本，相当于Java中String类
>
>  BLOB：二进制对象，相当于Java中的byte数组，用于存放图片、声音等文件