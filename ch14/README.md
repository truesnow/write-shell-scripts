# 14. 错误、信号和陷阱（一）

本节学习如何处理脚本运行中发生的错误。
好程序和坏程序之间的一个标志就是程序的 **健壮性**。

## 退出状态
正如前面课程所说，好的程序退出时都会返回一个退出状态。脚本执行成功，则退出状态为 0，如果为非 0，则代表脚本执行失败。
检查你所调用的程序的退出状态是很重要的。同时，当你的脚本结束时返回一个有意义的状态码也是很重要的。例如以下代码，曾经在某个生产环境的 Linux 服务器上：

```sh
# Example of a really bad idea

cd $some_directory
rm *
```

为什么这个脚本不好呢？如果它没有错误，那么它是好的。脚本首先切换目录到 `$some_directory`，然后删除该目录下的文件。但是如果 `$some_directory` 变量中的目录不存在呢？此时，`cd` 命令执行失败，`rm` 会删除当前工作目录下的所有文件。这不是本意行为！
这里的问题是在执行 `rm` 命令之前，没有检查 `cd` 命令的退出状态。


## 检查退出状态
有几种检查退出状态的方法。一种是检查 `$?` 环境变量。`$?` 环境变量包含了上一个执行命令的退出状态。示例：

```sh
true; echo $?
false; echo $?
```

`true` 命令直接返回退出状态 0，`false` 命令直接返回退出状态 1。所以我们可以改造开头那个脚本如下：

```sh
# Check the exit status

cd $some_directory
if [ "$?" = "0" ]; then
 rm *
else
 echo "Cannot change directory!" 1>&2
 exit 1
fi
```

这个版本中，我们检查了 `cd` 命令的退出状态，如果不为 0 则打印错误信息，并以非 0 状态退出，若退出状态为 0 则执行 `rm` 命令。
我们可以使用 `if` 简化上述脚本，`if` 可以直接判断命令的退出状态，如下：

```sh
# A better way

if cd $some_directory; then
 rm *
else
 echo "Could not change directory! Aborting." 1>&2
 exit 1
fi
```


## 错误退出函数
我们可以提供一个退出函数来打印错误信息及退出，以减少代码的书写。

```sh
# An error exit function

error_exit()
{
 echo "$1" 1>&2
 exit 1
}

# Using error_exit

if cd $some_directory; then
 rm *
else
 error_exit "Cannot change directory! Aborting."
fi
```


## AND 和 OR 命令列表
最后，我们可以使用 `AND` 和 `OR` 来简化我们的脚本。
`AND` 命令列表：

```sh
command1 && command2
```

command2 只会在 command1 退出状态为 0 时才会执行。
`OR` 命令列表：

```sh
command1 || command2
```

command2 只会在 command1 退出状态非 0 时才会执行。`AND` 和 `OR` 命令列表的退出状态为列表中最后一个执行命令的退出状态。
同样地，我们可以使用 `true` 和 `false` 命令来验证：

```sh
true || echo "echo executed"
false || echo "echo executed"
true && echo "echo executed"
false && echo "echo executed"
```

利用这个技巧，我们可以用更简洁的方法重写开头的示例：

```sh
# Simplest of all

cd $some_directory || error_exit "Cannot change directory! Aborting"
rm *
```

而且如果发生了错误时不需要退出脚本，可以简写如下：

```sh
# Another way to do it if exiting is not desired

cd $some_directory && rm *
```


## 改进退出函数
我们可以将程序名称添加到错误信息中，以提示错误具体来自哪里。这在脚本复杂或脚本中引用了其他脚本时很有用。注意引入的 `LINENO` 环境变量，它可以帮你定位脚本发生错误的具体行数。

```sh
#!/bin/bash

# A slicker error handling routine

# I put a variable in my scripts named PROGNAME which
# holds the name of the program being run. You can get this
# value from the first item on the command line ($0).

PROGNAME=$(basename $0)

error_exit()
{

#	----------------------------------------------------------------
#	Function for exit due to fatal program error
#	Accepts 1 argument:
#	string containing descriptive error message
#	----------------------------------------------------------------


 echo "${PROGNAME}: ${1:-"Unknown Error"}" 1>&2
 exit 1
}

# Example call of the error_exit function. Note the inclusion
# of the LINENO environment variable. It contains the current
# line number.

echo "Example of error with line number and message"
error_exit "$LINENO: An error has occurred."
```

错误函数中使用的大括号用于 **参数扩展**。你可以在变量名两边加上大括号来跟其周边的字符分开（如：`${PROGNAME}`）。有些人有在每个变量两边都加上大括号的习惯。第二个用法 `${1:-"Unknown Error"}` 表示当第一个命令行参数未定义时，使用「Unknown Error」作为默认值代替。你可以在 BASH 的帮助文档中的 `EXPANSIONS` 一节找到更多关于参数扩展的信息。

