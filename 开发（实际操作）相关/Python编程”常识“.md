1. 用下面的方式判断逻辑“真”或“假”。(扔掉"！""和==")

   ```Python
   if x:
   if not x:
   ```
   
2. 不使用临时变量交换两个值。（python不是C++！）

   ```Python
   a, b = b, a
   ```

3. 用生成式生成列表。（别再单独写一个遍历循环啦！）

   ```Python
   data = [7, 20, 3, 15, 11]
   result = [num * 3 for num in data if num > 10]
   print(result)  # [60, 45, 33]
   ```

4. 让代码既可以被导入又可以被执行。

   ```Python
   if __name__ == '__main__':
   ```
5. 使用_来命名需要保护的元素，使用property装饰器来完成getter和setter方法。
```python
class Someclass(object):

    def __init__(self, member1, member2):
        self._member1 = member1
        self._member2 = member2

    # 访问器 - getter方法
    @property
    def member1(self):
        return self._member1

    # 访问器 - getter方法
    @property
    def member2(self):
        return self._member2

    # 修改器 - setter方法
    @member2.setter
    def member2(self, member2):
        if member2 > 10: #member2元素需要符合的条件
            self._member2 = member2
        else:
            print("member2必须大于10！")
def main():
    temp = Someclass('NAME', 12)
    print(temp.member1) #NAME
    print(temp.member2) #12
    temp.member1 = 'Othername'  # AttributeError: can't set attribute
    temp.member2 = 9 #member2必须大于10！
    temp.member2 = 12
    print(temp.member2) #12

if __name__ == '__main__':
    main()
```
6. 字符串格式化:%和.format 他们的区别在于？

   format在许多方面看起来更便利.对于`%`最烦人的是它无法同时传递一个变量和元组.你可能会想下面的代码不会有什么问题:

```python
"hi there %s" % name
```

   但是,如果name恰好是\(1,2,3\),它将会抛出一个TypeError异常.为了保证它总是正确的,你必须这样做:

```python
"hi there %s" % (name,)   # 提供一个单元素的数组而不是一个参数
```

   但是有点丑..format就没有这些问题.
```python 
print("{} {}".format("hello", "world"))   # 不设置指定位置，按默认顺序 'hello world'

print("{0} {1}".format("hello", "world")) # 设置指定位置 'hello world'
'hello world'
 
print("{1} {0} {1}".format("hello", "world"))  # 设置指定位置 'world hello world'
```
