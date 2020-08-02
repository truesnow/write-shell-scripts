# 9. 远离问题

本节指出一些写 shell 脚本时常犯的错误。先创建一个 *trouble.bash* 脚本：

```sh
#!/bin/bash

number=1

if [ $number = "1" ]; then
    echo "Number equals 1"
else
    echo "Number does not equal 1"
fi

```

运行以上脚本输出：「"Number equals 1"」。

## 空变量
修改第三行代码：

```sh
number=1
```

为：

```sh
number=
```

并再次执行脚本，结果：

```
[me@linuxbox me]$ ./trouble.bash
/trouble.bash: [: =: unary operator expected.
Number does not equal 1
```

可以看到报错了，你可能以为错误是在第三行，但是根据错误提示，错误发生在第五行，`test` 命令的 `[` 写法这里。
首先，`number=` 是没有语法错误的，你可以定义一个变量为空。你可以在命令行输入 `number=` 来验证它是没错的。
那么，第五行有什么错呢？我们尝试用 shell 的运行原理去解释。将第五行的 `$number` 用其值去代替，先前的脚本得到：

```sh
if [ 1 = "1" ]; then
```

number 为空时得到：

```sh
if [ = "1" ]; then
```

这样就错误了。因为 `=` 是一个二元运算符，等号两边要有两个选项。而 shell 发现只有一个选项，所有错误提示「期望是一个一元运算符」。
要解决这个错误，我们可以将第五行改成：

```sh
if [ "$number" = "1" ]; then
```

替换后就是：

```sh
if [ "" = "1" ]; then
```

这样就正确了。
这就提示我们在写脚本时需要重视一个重要的东西：**考虑变量设为空的情况**。


## 缺少引号
修改第六行，去掉后面的引号：

```sh
   echo "Number equals 1
```

并再次运行脚本，得到：

```
[me@linuxbox me]$ ./trouble.bash
./trouble.bash: line 8: unexpected EOF while looking for matching "
./trouble.bash: line 10 syntax error: unexpected end of file
```

这里就引出了另一个常见的错误，shell 会通过首尾双引号来获取一个字符串，这里从第六行开始直到脚本运行结束都没有找到末尾的双引号，所以报错了。
这种错误在很长的脚本里找起来很痛苦。这就是为什么你需要边写脚本边测试，写一点测一点。当然，使用带有语法高亮的文本编辑器也可以轻松找出错误。


## 隔离问题
定位问题的几个方法：
**通过注释来隔离代码块**。例如：

```sh
#!/bin/bash

number=1

if [ $number = "1" ]; then
    echo "Number equals 1
#else
# echo "Number does not equal 1"
fi
```

通过注释掉 `else` 部分的代码块，运行脚本，发现仍然报错，就可以知道错误（双引号未关闭）不在 `else` 代码块中

**使用 `echo` 命令来验证你的猜测**。你可以通过 `echo` 命令来输出你的脚本运行预期。来证明脚本运行到了那一块。类似之前说过的 **存根** 代码。


## 监控你的脚本执行
如果你要 bash 向你显示其正在进行的工作（执行了什么代码），你可以在第一行使用 `-x` 参数：

```sh
#!/bin/bash -x
```

此时运行你的脚本，bash 会在执行代码的同时显示正在执行的每一行代码。这个技术叫做 **追踪**。
同时，你也可以用 `set` 命令来开启和关闭追踪。例如：

```sh
#!/bin/bash

number=1

set -x
if [ $number = "1" ]; then
    echo "Number equals 1"
else
    echo "Number does not equal 1"
fi
set +x
```

