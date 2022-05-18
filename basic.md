### sql基础

#### day1选择

##### 题目1：where or and 练习

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220515184539285.png" alt="image-20220515184539285" style="zoom:80%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220515184604983.png" alt="image-20220515184604983" style="zoom:80%;" />

```sql
select name,population,area from World where area>=3000000 or population>=25000000;
```

##### 题目2

![image-20220516173052000](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220516173052000.png)

sql判断某变量是否等于某值的时候用单等号，并且如果这个值是一个字符串需要用引号括起来

```sql
select product_id from Products where low_fats = "Y" and recyclable = "Y"
```

##### 题目3：where中比较字段是null

![image-20220516173824379](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220516173824379.png)

- 在sql中不等于可以使用<>这样的符号进行操作
- sql判断是否为null需要使用is null或者is not null进行判断

```sql
select name from customer where referee_id!=2 or referee_id IS NULL
不等于也可以这样写
select name from customer where referee_id<>2 or referee_id IS NULL
```

##### 题目4：where的not in的使用

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220516175300332.png" alt="image-20220516175300332" style="zoom:80%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220516175310191.png" alt="image-20220516175310191" style="zoom:80%;" />

相当于查找一个表中的某一列是否在另一个表中的某一列中出现。

```sql
select customers.name as 'Customers'
from customers
where customers.id not in
(
    select customerid from orders
);
```

#### day2：排序修改

##### 题目1：1873

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220517175429264.png" alt="image-20220517175429264" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220517175504555.png" alt="image-20220517175504555" style="zoom:50%;" />

根据条件查询出表中没有的列：

如这道题所示，我们需要的bouns是在原来的表格中没有的，所以我们需要将其创造出来，创造出来的方法就是在select字段中写上if或case，根据条件填充这一列的值。

###### **case 的使用**

case语句相当于（if-then-else语句，if后面的条件为真即返回then后面的值，end后面为取别名）

这个case语句用于创造一个新的列，根据已知条件

```sql
SELECT
    employee_id, 
CASE WHEN 
    MOD(employee_id, 2) = 1 AND name not rlike '^M' THEN salary ELSE 0 END AS bonus 
FROM
	Employees 
ORDER BY 
    employee_id;
```

###### rlike正则匹配

A RLIKE B ，表示B是否在A里面即可。而A LIKE B,则表示B是否是A.

使用RLIKE可以使用正则表达式去匹配



###### **if的使用**

expr1如果是true返回expr2，如果是false则返回expr3

```
IF( expr1 , expr2 , expr3 )
```

```sql
select 
    employee_id,
    if (employee_id %2 = 1 and left(name,1)!='M', salary, 0) as bonus
from Employees
```

###### left函数

`LEFT()`函数从提供的字符串的左侧提取给定数量的字符。

```sql
SELECT LEFT('SQL Server',3) result_string;
结果
result_string
-------------
SQL
```

##### 题目2：627变更性别

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220518171047675.png" alt="image-20220518171047675" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220518171113390.png" alt="image-20220518171113390" style="zoom:50%;" />

###### update语句的使用

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

update字段后面写要改变哪个表的内容，set是将内容改成什么，where是在哪里改



###### 在update语句中使用case

当我们想要对整个表的列按照某个逻辑统一修改的时候，我们不需要where语句，所以对于这道题来说，直接在set后面跟上需要update的内容即可

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220518170717515.png" alt="image-20220518170717515" style="zoom: 50%;" />

```
UPDATE salary
SET
    sex = CASE sex
        WHEN 'm' THEN 'f'
        ELSE 'm'
    END;

```

##### 题目3：196

###### delete语句

一定要注意delete语句如果不使用where的话表中记录的所有东西都会被删除

```sql
DELETE FROM table_name WHERE condition;
```

###### delete from where语句的应用

我们可以将一个表做一个内连接，让其自己和自己形成一张表，在新表中使用where语句进行要删除行的判断，这样可以来删除自己表中的一些重复信息。

这种delete的写法跟select的写法类似，它涉及到了两张表。delete p1表示要删除p1的跟p2表无法匹配上的记录，所以就是表示从p1中删除where条件中的记录，并不用去管笛卡尔积的问题。

整体过程：

```
a. 从表p1取出3条记录；
b. 拿着第1条记录去表p2查找满足WHERE的记录，代入该条件p1.Email = p2.Email AND p1.Id > p2.Id后，发现没有满足的，所以不用删掉记录1；
c. 记录2同理；
d. 拿着第3条记录去表p2查找满足WHERE的记录，发现有一条记录满足，所以要从p1删掉记录3；
e. 3条记录遍历完，删掉了1条记录，这个DELETE也就结束了。
```

```sql
DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id
```

