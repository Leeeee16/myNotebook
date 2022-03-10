## MySQL基础学习笔记

主要是SQL语句的学习

----------------

### 0. SQL语言分类

* DDL(Data Definition Language)：数据定义语言，用来定义数据库对象：库、表、列等；如：create、drop、alter

* **DML**(Data Manipulation Language)：数据操作语言，用来定义数据库记录(数据)；如：insert、update、delete
* DCL(Data Control Language)：数据控制语言，用来定义访问权限和安全级别；
* **DQL**(Data Query Language)：数据查询语言。如：select

### 1. DQL语言

#### 1.1 基础查询

##### 1.1.1 语法

查询列表可以是：字段、表达式、常量、函数等

```sql
select 查询列表
from 表名;

select 字段1,字段2,表达式
from 表名;
```

##### 1.1.2 特点

查询结果是一个虚表

##### 1.1.3 执行顺序

from -> select

##### 1.1.4 查询函数

1. `select Database();`：查询当前数据库
2. `select Version();`：版本
3. `select USER();`：查看用户

##### 1.1.5 起别名

1. 方式一：使用`AS`

   ```sql
   select User() AS 用户名;
   select USER() AS "用户名";
   select USER() AS '用户名';
   # 以下情况必须加引号
   select last_name AS '姓  名' from employees;
   ```

2. 方式二：使用`空格`：直接将`AS`替换为`空格`

##### 1.1.6 拼接

1. MySQL中`+`的作用
   1. 两个都是数值型就直接计算，如`1 + 1`

   2. 其中一个操作数为字符型，将字符型强转为数值型，如无法转换，直接当成0，如

      ```sql
      SELECT 'aa' + 100;
      # 结果为 100
      ```

   3. 其中一个操作数为`null`，结果都为`null`

2. 拼接函数：CONCAT

   ```sql
   # SELECT CONCAT(str1,str2,...)
   
   SELECT CONCAT(last_name,first_name) AS full_name
   FROM employees;
   ```

##### 1.1.7 distinct的使用

结果去重

```sql
# 查询员工涉及到的部门编号有哪些
SELECT DISTINCT department_id
FROM employees;
```

##### 1.1.8 查询表的结构

1. `DESC employees;`

2. `SHOW COLUMNS FROM employees;`

   <img src="D:/Program%20Data/Typora/typora-user-images/image-20210602103325297.png" alt="image-20210602103325297" style="zoom:50%;" />

##### 1.1.9 ifnull

```sql
/* ifnull(表达式1, 表达式2)
	表达式1：可能为null的字段或表达式
	表达式2：如果表达式1为null，则最终结果显示的值
*/
SELECT commission_pct, IFNULL(commission_pct,'空') FROM employees;
```

<img src="D:/Program%20Data/Typora/typora-user-images/image-20210602103643347.png" alt="image-20210602103643347" style="zoom:50%;" />

#### 1.2 条件查询

##### 1.2.1 语法

```sql	
SELECT 查询列表
FROM 表名
WHERE 筛选条件;
```

##### 1.2.2 执行顺序

from -> where -> select

##### 1.2.3 按条件表达式筛选

关系运算符基本与Java一样；不等于用`<>`，可以用`!=`但不建议

栗子：查询工资大于15000的员工

```sql
SELECT *
FROM employees
WHERE salary > 15000;
```

##### 1.2.4按逻辑表达式筛选

逻辑运算符：AND、OR、NOT

栗子：查询工资在10000-15000的员工信息

```sql
SELECT *
FROM employees
WHERE salary >= 10000 AND salary <= 15000;
```

##### 1.2.5 模糊查询

LIKE、IN、Between AND、IS NULL

###### 1.2.5.1 LIKE

用于字符型字段进行模糊查询，一般和通配符搭配使用。

* 通配符：`_`：任意单个字符；`%`：任意多个字符(0到任意多个)

 栗子：

```sql
SELECT *
FROM employees
WHERE first_name LIKE '%a_b%';
```

![image-20210602105024135](D:/Program%20Data/Typora/typora-user-images/image-20210602105024135.png)

转译字符

```sql
# 设置$为转译字符，可以任意设置，一般设置为$
SELECT *
FROM employees
WHERE last_name LIKE '_$_%' ESCAPE '$';
```

![image-20210602105249211](D:/Program%20Data/Typora/typora-user-images/image-20210602105249211.png)

###### 1.2.5.2 IN / not in

查询某字段的值是否属于指定的列表之内

`IN(常量值1, 常量值2, 常量值3, ...)`

相反的：`NOT IN(常量值1, 常量值2, 常量值3, ...)`

栗子：查询部门编号是30/50/90的员工名、部门编号

```sql
SELECT last_name, department_id
FROM employees
WHERE department_id IN(30,50,90);
```

###### 1.2.5.3 between in / not between in

判断某个字段的值是否介于xxx之间

栗子1：查询部门编号是30-90之间的部门编号、员工姓名

```sql
SELECT last_name, department_id
FROM employees
WHERE department_id BETWEEN 30 AND 90;
```

等价于

```sql
SELECT last_name, department_id
FROM employees
WHERE department_id >= 30 AND department_id <= 90;
```

栗子2：查询年薪不是100000-200000之间的员工的姓名、工资、年薪

```sql
SELECT last_name,salary,salary*12*(1+IFNULL(commission_pct,0)) AS 年薪
FROM employees
WHERE salary*12*(1+IFNULL(commission_pct,0)) NOT BETWEEN 100000 AND 200000;
```

<img src="D:/Program%20Data/Typora/typora-user-images/image-20210602110446300.png" alt="image-20210602110446300" style="zoom:67%;" />

###### 1.2.5.4 is null / is not null

栗子：查询奖金率为空(没有奖金)的员工信息

```sql
SELECT *
FROM employees
WHERE commission_pct IS NULL;
# 这里不能用 WHERE commission_pct = NULL;
```

`=`：只能判断普通的内容。

`IS`：只能判断`NULL`值。`xx IS 100;`会报错

`<=>`：安全等于，既能判断普通内容，又能判断`NULL`值。但很少用，可读性差

```sql	
SELECT *
FROM employees
WHERE salary <=> 10000 AND commission_pct <=> NULL;
```

![image-20210602111031359](D:/Program%20Data/Typora/typora-user-images/image-20210602111031359.png)

#### 1.3 排序查询

##### 1.3.1 语法

```sql	
SELECT 查询列表
FROM 表名
[WHERE 筛选条件]
ORDER BY 排序列表
```

##### 1.3.2 执行顺序

FROM -> WHERE -> SELECT -> ORDER BY

##### 1.3.3 特点

1. 排序列表可以是单个字段、多个字段、表达式、函数、列数、以及以上的组合
2. 升序：ASC（默认）；降序：DESC

##### 1.3.4 按单个字段排序

栗子：

```sql
SELECT last_name,salary
FROM employees
WHERE salary > 10000
ORDER BY salary DESC;
```

##### 1.3.5 按表达式排序

栗子：将有奖金的员工按年薪降序

```sql
SELECT *,salary*12*(1+commission_pct) 年薪
FROM employees
WHERE commission_pct IS NOT NULL
ORDER BY salary*12*(1+commission_pct) DESC;
```

##### 1.3.6 按别名排序

```sql
SELECT *,salary*12*(1+commission_pct) 年薪
FROM employees
WHERE commission_pct IS NOT NULL
ORDER BY 年薪 DESC;
```

##### 1.3.7 按函数的结果排序

栗子：按姓名的字数长度进行升序排序

```sql
SELECT last_name, LENGTH(last_name) 姓名长度
FROM employees
ORDER BY 姓名长度;
```

##### 1.3.8 按多个字段排序

栗子：查员工姓名、工资、部门编号，先按工资升序，再按部门编号降序

```sql
SELECT last_name,salary,department_id
FROM employees
ORDER BY salary,department_id DESC;
```

##### 1.3.9 按列数排序

可读性较差

```sql
# 按第二列排序
SELECT last_name,salary
FROM employees
ORDER BY 2 DESC;
```

#### 1.4 常见函数

相当于Java里的方法。

通常分为：字符函数、数学函数、日期函数和流程控制函数

##### 1.4.1 字符函数

###### 1.4.1.1 CONCAT 拼接字符

```sql
SELECT CONCAT('hello:  ',first_name,last_name)
FROM employees;
```

###### 1.4.1.2 LENGTH 获取字节长度

```sql	
SELECT LENGTH('hello,哥哥');  # 12,中文占3个字节
```

###### 1.4.1.3 CHAR_LENGTH 获取字符长度

```sql
SELECT CHAR_LENGTH('hello,哥哥'); # 8
```

###### 1.4.1.4 SUBSTR  截取子串

SUBSTR(原串，起始索引，截取的长度)，**注意起始索引从1开始**

SUBSTR(原串，起始索引)：从起始开始，后面都截取

```sql
SELECT SUBSTR('2020年6月2日',1,7); # 2020年6月
SELECT SUBSTR('2020年6月2日',8); # 2日
```

###### 1.4.1.5 INSTR

获取字符第一次出现的索引

```sql
SELECT INSTR('阿真爱上了阿强','阿强'); # 6
```

###### 1.4.1.6 TRIM 

去前后的指定字符（默认是空格）

```sql
SELECT TRIM('    阿  珍  ') AS az;
SELECT TRIM('x' FROM 'xxxxx阿  珍xxxxxxxxxx') AS az;
```

###### 1.4.1.7 LPAD/RPAD 左填充和右填充

```sql
SELECT LPAD('柯南',5,'呆'); # 呆呆呆柯南
SELECT RPAD('柯南',5,'呆'); # 柯南呆呆呆
```

###### 1.4.1.8 UPPER/LOWER 变大写/变小写

* 栗子：查询员工表的姓名，要求格式：姓首字符大写，其他字符小写，名所有字符大写，且姓和名之间用`_`分割，别名"OUTPUT"

  ```sql
  SELECT CONCAT(UPPER(SUBSTR(first_name,1,1)),LOWER(SUBSTR(first_name,2)),'_',UPPER(last_name)) "OUTPUT"
  FROM employees;
  ```

  <img src="D:/Program%20Data/Typora/typora-user-images/image-20210602202533829.png" alt="image-20210602202533829" style="zoom:50%;" />

###### 1.4.1.9 STRCMP 比较两个字符大小

```sql
SELECT STRCMP('aaa','avc'); # -1
```

###### 1.4.1.10 LEFT/RIGHT 截取子串

```sql
SELECT LEFT('工藤新一',2); # 工藤
SELECT RIGHT('工藤新一',2); # 新一
```

##### 1.4.2 数学函数

###### 1.4.2.1 ABS 绝对值

```sql
SELECT ABS(-100); # 100
```

###### 1.4.2.2 CEIL/FLOOR 向上/下取整

```sql
SELECT CEIL(3.14); # 4
SELECT FLOOR(3.14); # 3
```

###### 1.4.2.3 ROUND 四舍五入

```sql
SELECT ROUND(3.14); # 3
SELECT ROUND(3.88); # 4
SELECT ROUND(3.881223414123,3); # 小数点保留3位，3.881
```

###### 1.4.2.4 TRUNCATE 截断

```sql
SELECT TRUNCATE(3.881223414123,1); # 3.8
```

###### 1.4.2.5 MOD 取余

```sql
SELECT MOD(100,3); # 1
```

##### 1.4.3 日期函数

###### 1.4.3.1 NOW 获取当前时间

```sql
SELECT NOW(); # 2021-06-03 09:35:23
```

###### 1.4.3.2 CURDATE/CURTIME 获取当前日期/时间

```sql
SELECT CURDATE(); # 2021-06-03
SELECT CURTIME(); # 09:37:09
```



###### 1.4.3.3 DATEDIFF 时差

```sql
SELECT DATEDIFF(CURDATE(),'1998-1-6'); # 8549
```

###### 1.4.3.4 DATE_FORMAT

```sql
SELECT DATE_FORMAT('1998-1-6 10:00:00','%Y年%m月%d日 %H小时%i分钟%s秒') 出生日期;  # 1998年01月06日 10小时00分钟00秒
```

```sql	
SELECT DATE_FORMAT(hiredate,'%Y年%m月%d日 %H小时%i分钟%s秒') 入职日期
FROM employees;
```

###### 1.4.3.5 STR_TO_DATE 按指定格式解析字符串为日期类型

```sql
SELECT STR_TO_DATE('3/16 1998','%m/%d %Y'); # 1998-03-16
SELECT *
FROM employees
WHERE hiredate < STR_TO_DATE('3/16 1998','%m/%d %Y');
```

##### 1.4.4 流程控制函数

###### 1.4.4.1 IF函数

```sql
SELECT IF(100>9,'good','坏'); # good，类似三元运算符
```

栗子：如果有奖金，显示奖金，如果没有则显示0

```sql
SELECT IF(commission_pct IS NULL,0,salary*12*commission_pct) 奖金,commission_pct
FROM employees
```

<img src="D:/Program%20Data/Typora/typora-user-images/image-20210603094924460.png" alt="image-20210603094924460" style="zoom:67%;" />

###### 1.4.4.2 CASE

1. 情况1：类似switch语句，可以实现等值判断

```sql
CASE 表达式
WHEN 值1 THEN 结果1
WHEN 值2 THEN 结果2
...
ELSE 结果n
END
```

栗子：部门编号是30，工资显示为3倍；部门编号为50，工资显示为5倍；部门编号为60，工资显示为6倍；否则不变

```sql
SELECT department_id,salary,
CASE department_id
	WHEN 30 THEN salary*3
	WHEN 50 THEN salary*5
	WHEN 60 THEN salary*6
	ELSE
		salary
END 新工资
FROM employees;
```

2. 情况2：类似于多重if语句，实现区间判断

   ```sql
   CASE
   WHEN 条件1 THEN 结果1
   WHEN 条件2 THEN 结果2
   ...
   ELSE 结果n
   END
   ```

   栗子：如果工资>20000，显示级别A；如果工资>15000，显示级别B；如果工资>10000，显示级别C，否则显示D

   ```sql
   SELECT salary,
   CASE 
   	WHEN salary > 20000 THEN 'A'
   	WHEN salary > 15000 THEN 'B'
   	WHEN salary > 10000 THEN 'C'
   	ELSE 'D'
   END 级别
   FROM employees;
   
   ```

   <img src="D:/Program%20Data/Typora/typora-user-images/image-20210603095726485.png" alt="image-20210603095726485" style="zoom:67%;" />

##### 1.4.5 聚合(分组)函数

前面的1.4.1到1.4.4都是单行函数，而聚合函数用于将一组数据进行统计计算，最终得到一个值。

SUM(字段名)、AVG(字段名)、MIN(字段名)/MAX(字段名)

COUNT(字段名)  计算非空字段的个数

```sql
SELECT COUNT(salary),SUM(salary),MAX(salary),MIN(salary),AVG(salary)
FROM employees;
```

<img src="D:/Program%20Data/Typora/typora-user-images/image-20210603102336766.png" alt="image-20210603102336766" style="zoom: 67%;" />

* COUNT(*)用于查询结果集的总行数

```sql
SELECT COUNT(*) FROM employees; # 107
```

* 搭配`distinct`实现去重统计

  栗子：查询有员工的部门个数

  ```sql
  SELECT COUNT(DISTINCT department_id)
  FROM employees;	# 11
  ```

#### 1.5 分组查询

栗子：查询每个部门的工资情况

```sql
SELECT department_id,SUM(salary),AVG(salary),MAX(salary),MIN(salary)
FROM employees
GROUP BY department_id;
```





### 2. DML语言

### 3. DDL语言

### 4. 