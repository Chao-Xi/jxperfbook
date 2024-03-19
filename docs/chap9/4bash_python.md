# 4 在Python中执行Linux Shell脚本

## 为什么在Python中执行Shell脚本？

Python 不仅仅是一种强大的编程语言，而且它的易用性和灵活性也使得它成为自动化任务的理想选择。在 Python 中执行 shell 脚本，有以下好处：

* 使得复杂的 shell 脚本更容易理解和维护；
* 支持更强大的编程结构；
* 可以完美地与 Python 程序集成

## 如何在Python中执行Shell脚本？

在 Python 中执行 Shell 脚本的一种简单方法是使用 os 模块的 `system()` 函数。

示例：

```
import os
os.system('bash shell_script.sh')
```

`os.system()` 函数会将返回值设置为脚本的退出状态码，成功执行为0，没成功则为其他值。


## 使用 subprocess 模块执行 Shell 脚本


相比os模块，subprocess模块提供了更多的功能和灵活性，是Python标准库中推荐用来执行shell命令的模块。

示例：

```
import subprocess

subprocess.run(['bash', 'shell_script.sh'])
```


在这个例子中，`subprocess.run()` 将创建一个新的进程，运行 shell 脚本，等待其完成，然后返回一个 `CompletedProcess` 实例。

## 获取 Shell 脚本的输出


使用 subprocess，我们还可以捕获和获取 shell 脚本的输出。**这是通过使用 `run()` 函数的 `capture_output ` 参数实现的。**

```
import subprocess

completed_proc = subprocess.run(['bash', 'shell_script.sh'], capture_output=True, text=True)

print(completed_proc.stdout)
```

**示例中，通过设置`capture_output=True`和`text=True`，可以捕获命令的输出，并将输出的字节类型自动转成字符串**。