## 备忘录
1. group by 与 order by
2. having 与 where
3. distinct 只能用于表名
4. 子查询 (经常可以被内联结代替)
5. 计算字段
6. 内联结:
  * 使用where连接两个表（简单语法）
  * inner join  on
7. 笛卡尔积(叉联结)
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
     prod_id    CHAR(10)     NOT NULL,
     vend_id    CHAR(10)     NOT NULL,
     prod_desc  TEXT(1000)   (NULL)  DEFAULT a, # 默认值通常用于时间戳，DEFAULT CURRENT_DATE()
);
```
18. 更新表：
```SQL
ALTER TABLE Vendors 
ADD vend_phone CHAR(20); 
```
  * 对于复杂的表，通常手动建立一个新的表来代替它
19. 删除表：
```SQL
DROP TABLE CustCopy;  #不可撤销，谨慎操作
```
20. 视图
