# 10. 键盘输入和算术

目前为止，我们的脚本都没有交互。也就是说，它们都不需要用户的输入。本节，我们将学习脚本如何问问题，并获取以及使用其响应。

## read
要从键盘读取输入，使用 `read` 命令。`read` 命令从键盘读取输入并将其赋值给一个变量。例如：

```sh
#!/bin/bash

echo -n "Enter some text > "
read text
echo "You entered: $text"
```

注意 `echo` 命令的 `-n` 参数用于将光标保留在同一行，也就是说，它使得 `echo` 输出的提示后面不会加上换行。
接着，调用了 `read` 命令，并以 `text` 为参数。该命令的作用是等待用户输入并以换行（enter）结束，并将输入内容赋值给 `text`。
如果你不给 `read` 命令指定参数接收，那么将赋值给环境变量 `REPLY`。
`read` 命令也有一些命令行选项。最有趣的是 `-t` 和 `-s` 选项。`-t` 选项后跟一个数值，给 `read` 命令提供一个以秒为单位的自动超时时间。也就是说，如果用户在给定时间内没有输入内容，那么 `read` 会放弃读取用户输入。这个选项可以在用户没有根据提示进行输入而脚本必须继续的情况下使用（超时时脚本给定一个默认响应）。`-t` 选项使用示例：

```sh
#!/bin/bash

echo -n "Hurry up and type something! > "
if read -t 3 response; then
    echo "Great, you made it in time!"
else
    echo "Sorry, you are too slow!"
fi
```

`-s` 选项使得用户的输入不会显示。这在你需要用户输入密码或其他保密信息时很有用。


## 算术
shell 提供了 **整数运算** 的功能。
如果你想进行小数运算，你可以使用一个叫 `bc` 的程序，它可以提供任意精度的计算。
示例：

```sh
echo $((2+2))
```

如你所见，当你将表达式放在两层小括号中时，shell 就会将其进行算术展开。
注意空格符会被忽略，以下命令都是一样的：

```sh
echo $((2+2))
echo $(( 2+2 ))
echo $(( 2 + 2 ))
```

shell  可以执行各种各种的常见运算，例如：

```sh
#!/bin/bash

first_num=0
second_num=0

echo -n "Enter the first number --> "
read first_num
echo -n "Enter the second number -> "
read second_num

echo "first number + second number = $((first_num + second_num))"
echo "first number - second number = $((first_num - second_num))"
echo "first number * second number = $((first_num * second_num))"
echo "first number / second number = $((first_num / second_num))"
echo "first number % second number = $((first_num % second_num))"
echo "first number raised to the"
echo "power of the second number = $((first_num ** second_num))"

```

注意，在算术表达式中，参数不需要 `$` 符。
注意，过大的整数会变成负数。以 0 做除数会抛出异常。
`%` 号表示取余运算（也叫取模运算）。例如：

```sh
#!/bin/bash

number=0

echo -n "Enter a number > "
read number

echo "Number is $number"
if [ $((number % 2)) -eq 0 ]; then
    echo "Number is even"
else
    echo "Number is odd"
fi
```

利用取余来将秒转换为时分秒的示例：

```sh
#!/bin/bash

seconds=0

echo -n "Enter number of seconds > "
read seconds

hours=$((seconds / 3600))
seconds=$((seconds % 3600))
minutes=$((seconds / 60))
seconds=$((seconds % 60))

echo "$hours hour(s) $minutes minute(s) $seconds second(s)"

```

