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
