### 1. 数据切片的区间是**前闭后开**：
```python
list[:3]
```
代表list从索引0开始取，直到索引3为止，但不包括索引3。即索引0，1，2，正好是3个元素。
```python
list[-1]
```
记住倒数第一个元素的索引是-1。
```python
L[-10:]
```
代表取后10个索引。
```python
L[:]
```
可以原样复制一个list。

要注意，**列表切片是临时产生一个新的对象，对列表切片进行操作并不能影响原列表**。
### 2. Python中除法有两种运算符：’/’和’//’. <br>

X / Y类型：<br>

在Python2.6或者之前，这个操作对于整数运算会省去小数部分，而对于浮点数运算会保持小数部分。<br>

在Python3.0中变成真除法（无论任何类型都会保持小数部分，即使整除也会表示为浮点数形式）。<br>

X // Y 类型：<br>

在Python 2.2中新增的操作，在Python2.6和Python3.0中均能使用，这个操作不考虑操作对象的类型，总是省略小数部分，剩下最小的能整除的整数部分。<br>

### 3. python的可变类型和不可变类型，以及他们与函数参数调用的关系

对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。而对可变对象进行操作时，会改变对象自身的内容。

1) 数字类型（例如 int） 不可变

    尝试修改数字对象，实际上是新建一个数字对象。

2) 字符串类型str 不可变

3) 元组(tuple) 不可变

4) 集合(set) 可变

5) 列表(list) 可变

6) 字典(dict) 可变

**1. 不可变对象作为函数参数，Python通过值传递，不会改变原变量的值。**

**2. 可变对象作为函数参数，Python通过引用传递，会改变原变量的值。**

   * 特殊情况: 对于可变对象作为函数参数，且参数不指向其他对象时，相当于引用传递；否则，若参数指向其他对象，则对参数变量的操作并不影响原变量的对象值
```python
def change(val):
    newval = [10]
    val= val + newval
nums = [0, 1]
change(nums)
print(nums)
```
  * 特殊情况: 可以通过global来改变全局变量的值
```python
def foo():
    global a
    a = 200

if __name__ == '__main__':
    a = 100
    foo()
    print(a)  # 200
```

输出结果为
```
[0,1]
```
### 4. map() reduce() lambda()

####  map()函数

map()函数接收两个参数，一个是函数，一个是序列，map将传入的函数依次作用到序列的每个元素，并把结果作为新的list返回。
  
 ```python
def f(x):
    return x * x
print(map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9]))    #[1, 4, 9, 16, 25, 36, 49, 64, 81]
```
#### reduce()函数

reduce把一个函数作用在一个序列[x1, x2, x3...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是：reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
```python
def fn(x, y):
     return x * 10 + y
print(reduce(fn, [1, 3, 5, 7, 9]))  ####13579
```
#### 匿名函数 lambda

```python
map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9])   #[1, 4, 9, 16, 25, 36, 49, 64, 81]
```
匿名函数lambda x: x * x实际上就是：
```python
def f(x):
    return x * x
```
关键字lambda表示匿名函数，冒号前面的x表示函数参数。匿名函数有个限制，就是只能有一个表达式，不用写return，返回值就是该表达式的结果。

此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数：
```python
f = lambda x: x * x
print(f(5))    #25
```
### 5. 用集合set来获取非重复元素的数量
```python
    s = input()
    st = set(s)
    print(len(s))    #
```
顺手总结一下list和set的区别:
  * list有序，通过append()或者insert()来添加元素，通过pop()删除元素。
  * set无序，通过add和remove来添加、删除元素（保持不重复），访问一个set的意义就仅仅在于查看某个元素是否在这个集合里面。set的计算效率比list高，所以如果我们要判断一个元素是否在一些不同的条件内符合，用set是最好的选择
### 6. sort()排序函数

1) 列表.sort(),作用是将原来的列表正序排序，所以它是对原来的列表进行的操作，不会产生一个新列表

**sort()方法语法：**
```python
list.sort(cmp=None, key=None, reverse=False)
```
**参数**
```
cmp -- 可选参数, 如果指定了该参数会使用该参数的方法进行排序。
key -- 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
reverse -- 排序规则，reverse = True 降序， reverse = False 升序（默认）。
```
2) sorted(),是Python内置函数，该函数对原列表不会产生影响，只是在原来列表的基础上，产生一个有序的新列表，可以复制一个列表名

### 7. 字符串处理

1) strip([chars])

在字符串上执行 lstrip()和 rstrip()。 ps:无法切除中间的空格。

lstrip() :截掉字符串左边的空格或指定字符。

rstrip() :截掉字符串右边的空格或指定字符。

返回移除字符串头尾指定的字符序列生成的新字符串。

2) split() 

```python
str.split(str="", num=string.count(str))
```

通过指定分隔符对字符串进行切片，如果第二个参数 num 有指定值，则分割为 num+1 个子字符串，返回分割后的字符串列表。

3) 列表中的数字组合成一个数字

```python
a = [1, 3, 5, 3, 2, 1]
b = [str(x) for x in a]
print(int(''.join(b)))  #输出结果135321
```

**一个例子**：
```
6 5    #这是输入值
787585
```
```python
print(list(map(int, input().split())))
print(list(map(int, input().strip())))
```
```
    6 5 [7, 8, 7, 5, 8, 5]   
#输出结果
```
**如果将input换成sys.stdin.readline()**
```python
print(list(map(int, sys.stdin.readline().split())))
print(list(map(int, sys.stdin.readline().strip())))
```
结果不变
```
6 5 [7, 8, 7, 5, 8, 5]   
```
**结果分析**

第一行，split()将6 5 分割成了字符串列表["6","5"]，然后使用map进行**类型转换**之后。

第二行，strip()处理之后，返回了字符串"787585"，然后将每一个字符转化为int，并**构建列表**。

### 8. floate类型保留对应的小数点
1. '%.2f' %f 方法  四舍五入
```python
f = 1.23456
print('%.4f' % f) # 1.2346
print('%.3f' % f) # 1.235
print('%.2f' % f) # 1.23
```
2. round函数 不推荐
3. 自己写一个函数，实现舍弃小数点
```python
def get_two_float(f_str, n):
    f_str = str(f_str)      # f_str = '{}'.format(f_str) 也可以转换为字符串
    a, b, c = f_str.partition('.')
    c = (c+"0"*n)[:n]       # 如论传入的函数有几位小数，在字符串后面都添加n为小数0
    return ".".join([a, c])
num = 123.4567
print(get_two_float(num, 2))   #123.45
num2 = 123.4
print(get_two_float(num2, 2))  #123.40
``` 
   * Python partition() 方法: partition() 方法用来根据指定的分隔符将字符串进行分割。如果字符串包含指定的分隔符，则返回一个3元的元组，第一个为分隔符左边的子串，第二个为分隔符本身，第三个为分隔符右边的子串。
### 9. ord()与chr()
ord()函数主要用来返回对应字符的ascii码，chr()主要用来表示ascii码对应的字符他的输入时数字，可以用十进制，也可以用十六进制。
print ord('a') #97
print chr(97) #a
