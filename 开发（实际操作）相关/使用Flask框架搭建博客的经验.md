1. 让代码既可以被导入又可以被执行。

   ```Python
   if __name__ == '__main__':
   ```

2. 使用虚拟环境，让部署变的方便和规范。可以借助第三方工具virtualenv很方便的搭建虚拟环境，开发完成后，使用pip来输出requirements文件
```Python
pip freeze >requirements.txt
```
3. 为了使得程序可以动态修改程序配置，使用程序工厂函数来搭建程序，使用蓝本Blueprint来定义路由。

4. MySql数据库：部署的时候，ubuntu系统自动安装的Mysql会出现默认无密码的情况，需要手动添加root密码后加入程序配置，否则python程序会提示权限不足。

5. 电子邮件支持
