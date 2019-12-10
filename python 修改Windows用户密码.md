# 使用python修改Windows用户密码

## python调用CMD命令

有三种方法

### os.system()  

- 返回0 表示命令执行成功，其他表示失败。  
- 用法：os.system("command")
- *注意：使用该函数经常会莫名其妙地出现错误，但是直接执行命令并没有问题，所以一般建议不要使用。*

### os.popen()

- 这种调用方式是通过管道的方式来实现，函数返回是 file read 的对象，对其进行读取read、readlines等操作可以看到执行的输出。
- 注意：如果命令执行失败，就读取不到内容。
- 用法：os.popen("command")

### subprocess.Popen()

subprocess模块被推荐用来替换一些老的模块和函数，如：os.system、os.spawn*、os.popen*等

#### SUBPROCESS.POPEN（）详解  

---  

注解（赵奇）：subprocess.Popen()可以直接执行shell语句，然后若stdout是PIPE文件流格式，则会将输出信息放在stdout中。

---  

This module defines one class called Popen:  
这个模块定义了一个叫做Popen的类:

```python
class Popen(args, bufsize=0, executable=None,
            stdin=None, stdout=None, stderr=None,
            preexec_fn=None, close_fds=False, shell=False,
            cwd=None, env=None, universal_newlines=False,
            startupinfo=None, creationflags=0):
```
