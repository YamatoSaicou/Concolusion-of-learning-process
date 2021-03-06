## 备忘录
1. group by 与 order by
   * group by 中一定会含有聚合函数
2. having 与 where
   * HAVING短语与WHERE子句的区别：作用对象不同。
    * WHERE子句作用于基表或视图，从中选择满足条件的元组。
    * HAVING短语作用于组，从中选择满足条件的组。
3. distinct 只能用于表名
4. 子查询 (经常可以被内联结代替)
5. 计算字段
6. 内联结:
  * 使用where连接两个表（简单语法）
  * inner join  on
7. 左连接和右连接
  * SELECT * from A LEFT JOIN B ON A.id= B.id and A.`name` = B.`name`  会查询出什么结果？ 会查询出 A 表中所有的数据消息和满足 B 表中的 数据消息
  * 在使用left jion时，on和where条件的区别如下：
    * on条件是在生成临时表时使用的条件，它不管on中的条件是否为真，都会返回左边表中的记录。
    * where条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有left join的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。
8. 自联结(from自己两次，起不同的别名)
9. 外联结(LEFT OUTER JOIN )(RIGHT OUTER JOIN)
10. UNION 连接两个selcet语句，并把输出组合成一个结果集。
  * 会自动去除重复的行，如果不想去掉，可以 UNION ALL
  * 在最后一个SELECT语句后加ORDER BY，可以对所有结果进行排序
11. 插入数据 
```SQL
INSERT INTO XXXX
VALUES("",
       "",
       );
```
   更加安全的版本
```SQL
INSERT INTO XXXX(XXXX,
                 XXXX,
)
VALUES("",
       "",
       );
```
12. INSERT INTO 与 SELECT结合
```SQL
INSERT INTO Customers(cust_id, 
                     cust_contact,  
                     cust_email, 
                     cust_name, 
                     cust_address,  
                     cust_city, 
                     cust_state, 
                     cust_zip,  
                     cust_country) 
SELECT cust_id, 
         cust_contact,   # 注意，列名无关紧要，只关心顺序
         cust_email, 
         cust_name,  
         cust_address, 
         cust_city, 
         cust_state, 
         cust_zip, 
         cust_country 
FROM CustNew; 
```
13. SELECT INTO 从一个表复制到另一个表
```SQL
SELECT * 
INTO CustCopy FROM Customers; 
```
 MYSQL中语法不同，
 ```SQL
 CREATE TABLE CustCopy 
 SELECT * FROM Customers; 
 ```
14. UPDATE更新语句，由三部分构成
  * 要更新的表
  * 列名和他们的新值
  * 确定要更新哪些行的过滤条件
```SQL
UPDATE Customers 
SET cust_email = 'kim@thetoystore.com' 
WHERE cust_id = '1000000005'; 
```
15. 删除数据
```SQL
DELETE FROM Customers 
WHERE cust_id = '1000000006'; 
```
 * DELETE 是删除整行，如果想要删除指定列，要使用UPDATE
16. 使用DELETE 和 UPDATE时需要注意的原则:
  * 别忘记where
  * 保证每个表都有主键
  * 先用SELECT进行测试
  * 使用强制实施引用完整性的数据库
17. 创建表:
  * 新表的名字
  * 表列的名字和定义
  * 指定表的位置（可选）
```SQL
CREATE TABLE Products
( 
     prod_id    CHAR(10)     NOT NULL  PRIMARY KEY, # 主键
     vend_id    CHAR(10)     NOT NULL  REFERENCE Customers(cust_id), # 外键
     prod_desc  TEXT(1000)   (NULL)  DEFAULT a  # 默认值通常用于时间戳，DEFAULT CURRENT_DATE()
     quantity   INTEGER      NOT NULL CHECK (quantity > 0),
);
```
18. 更新表：
```SQL
ALTER TABLE Vendors   #ALTER是对表列进行操作，更行行值的时候使用UPDATE
ADD vend_phone CHAR(20); 
```
```SQL 
ALTER TABLE Orders 
ADD CONSTRAINT 
FOREIGN KEY (cust_id) REFERENCES Customers (cust_id)  # 在表中添加一个外键
```
  * 对于复杂的表，通常手动建立一个新的表来代替它
19. 删除表：
```SQL
DROP TABLE CustCopy;  #不可撤销，谨慎操作，DROP是删除数据库的对象，DELETE是删除表中的行
```
20. 视图
  * 创建视图:
```SQL
CREATE VIEW ProductCustomers AS 
SELECT cust_name, cust_contact, prod_id FROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id 
```
  * 使用视图:要注意视图本身并不存储数据，每次使用视图的时候，都必须处理查询执行时需要的所有检索。
 ```SQL
SELECT cust_name, cust_contact 
FROM ProductCustomers 
WHERE prod_id = 'RGAN01'; 
 ```
  * 整理数据格式（复用SQL语句）:
```SQL
CREATE VIEW VendorLocations AS 
SELECT RTRIM(vend_name) + ' (' + RTRIM(vend_country) + ')' 
AS vend_title 
FROM Vendors; 
```
  * 视图过滤数据:
```SQL
CREATE VIEW CustomerEMailList AS 
SELECT cust_id, cust_name, cust_email FROM Customers 
WHERE cust_email IS NOT NULL; 
```
21. 存储过程:
```SQL
EXECUTE AddNewProduct( 'JTS01', 
                       'Stuffed Eiffel Tower',  
                        6.49, 
                        'Plush stuffed toy with the text La Tour Eiffel in red white and blue' 
                        )
```
  * 例子中EXECUTE代表执行，AddNewProduct为所执行的存储过程名，后面是参数。
  * 创建存储过程和调用存储过程的简单例子:
```SQL 
CREATE PROCEDURE MailingListCount  
AS 
DECLARE @cnt INTEGER   #DECLARE的作用是声明变量
SELECT @cnt = COUNT(*) 
FROM Customers 
WHERE NOT cust_email IS NULL; RETURN @cnt; 
```
```SQL
DECLARE @ReturnValue INT 
EXECUTE @ReturnValue=MailingListCount; SELECT @ReturnValue; 
```
  * 第二个例子
```SQL
CREATE PROCEDURE NewOrder @cust_id CHAR(10)  #所需求的参数是顾客的id
AS 
DECLARE @order_num INTEGER 
SELECT @order_num=MAX(order_num) 
FROM Orders 
SELECT @order_num=@order_num+1 
INSERT INTO Orders(order_num, order_date, cust_id) 
VALUES(@order_num, GETDATE(), @cust_id) 
RETURN @order_num; 
```
```SQL
CREATE PROCEDURE NewOrder @cust_id CHAR(10)  #另一个版本
AS 
INSERT INTO Orders(cust_id) 
VALUES(@cust_id) 
SELECT order_num = @@IDENTITY; 
```
21. 事务，保留点，回滚，
```SQL
BEGIN TRANSACTION   # @@ERROR为0说明有错误发生
INSERT INTO Customers(cust_id, cust_name) 
VALUES('1000000010', 'Toys Emporium'); 
SAVE TRANSACTION StartOrder; 
INSERT INTO Orders(order_num, order_date, cust_id)
VALUES(20100,'2001/12/1','1000000010'); 
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder; 
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity, item_price)
VALUES(20100, 1, 'BR01', 100, 5.49);
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder;
INSERT INTO OrderItems(order_num, order_item, prod_id, quantity,item_price)
VALUES(20100, 2, 'BR03', 100, 10.99);
IF @@ERROR <> 0 ROLLBACK TRANSACTION StartOrder;
COMMIT TRANSACTION
```
22. 游标
23. 唯一约束 
  * UNIQUE (列名)，创建表时使用
  * 创建之后再添加约束
  ```SQL
  ALTER TABLE Persons
  ADD UNIQUE (列名)
  ```
24. 检查约束 CHECK
  * 创建时直接CHECK (条件)
  * 船舰之后添加约束
```SQL
ADD CONSTRAINT CHECK (gender LIKE '[MF]')  #性别只能是M或者F
```
25. 索引
```SQL
CREATE INDEX prod_name_ind ON Products (prod_name); 
```
26. 触发器
  * 触发器是特殊的存储过程，在特定的数据库活动发生时自动执行。
  * 触发器与单个表关联，INSERT，DELETE，UPDATE执行时触发。
  * 触发器的代码具有以下数据的使用权：
     * INSERT操作中的所有新数据
     * UPDATA中所有的新数据和旧数据
     * DELETE操作中删除的数据
  * 触发器可以在特定操作之前或之后执行
  * 触发器的常见作用:
     * 保持数据一致，例如人名大写等
     * 基于某个表的活动，在其他表上进行操作。例如记录更改
     * 进行额外的验证
     * 计算列值或者更新时间戳
```SQL
CREATE TRIGGER customer_state  #SQL server
ON Customers 
FOR INSERT, UPDATE 
AS 
UPDATE Customers 
SET cust_state = Upper(cust_state) 
WHERE Customers.cust_id = inserted.cust_id; 
```
```SQL
CREATE TRIGGER tr_testb_insert AFTER INSERT ON testb FOR EACH ROW   #MYSQL
BEGIN
	INSERT INTO testb_log (TESTB_ID,`NAME`, AGE,`STATUS`,`ACTION`,TIME)
VALUES
	(NEW.ID, NEW.NAME, NEW.AGE, NEW.STATUS,'INSERT',NOW());
END;
```
