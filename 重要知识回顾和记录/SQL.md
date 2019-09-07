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
SELECT xxx FUNCTION(xxxx) FROM xxxx (WHERE)  (GROUP BY) (HAVING) (ORDER BY)
