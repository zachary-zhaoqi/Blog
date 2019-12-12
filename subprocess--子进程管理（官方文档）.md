# subprocess --- 子进程管理

源代码: [Lib/subprocess.py](https://github.com/python/cpython/tree/3.8/Lib/subprocess.py)

subprocess 模块允许你生成新的进程，连接它们的输入、输出、错误管道，并且获取它们的返回码。此模块打算代替一些老旧的模块与功能：

```python
os.system
os.spawn*
```

## 使用 subprocess 模块
推荐使用`run()`函数。  
进阶推荐使用更底层的`Popen`接口。

```python
subprocess.run(args, *,
                stdin=None,
                input=None,
                stdout=None,
                stderr=None,
                capture_output=False,
                shell=False,
                cwd=None,
                timeout=None,
                check=False,
                encoding=None,
                errors=None,
                text=None,
                env=None,
                universal_newlines=None
            )
```

运行arg描述的指令。等待指令完成，然后返回一个`completedProcess`示例。
