# 11. 流程控制（二）

## 更多分支：case
之前学过了 `if` 命令。本节学习的叫做 **分支**。使用 `case` 命令。
你也可以使用多个 `if` 来构建分支。例如：

```sh
#!/bin/bash

echo -n "Enter a number between 1 and 3 inclusive > "
read character
if [ "$character" = "1" ]; then
    echo "You entered one."
elif [ "$character" = "2" ]; then
    echo "You entered two."
elif [ "$character" = "3" ]; then
    echo "You entered three."
else
    echo "You did not enter a number between 1 and 3."
fi
```

这样的代码不太好看。shell 提供了更优雅的方式。它提供了一个叫 `case` 的内建命令，使用 `case` 改写上面的示例如下：

```sh
#!/bin/bash

echo -n "Enter a number between 1 and 3 inclusive > "
read character
case $character in
    1 ) echo "You entered one."
        ;;
    2 ) echo "You entered two."
        ;;
    3 ) echo "You entered three."
        ;;
    * ) echo "You did not enter a number between 1 and 3."
esac
```

`case` 命令的格式如下：

```sh
case word in
    patterns ) commands ;;
esac
```

patterns 可以是纯文本或者通配符。多个模式可用 `|` 符分开。下面是一个更高级点的例子：

```sh
#!/bin/bash

echo -n "Type a digit or a letter > "
read character
case $character in
                                # Check for letters
    [[:lower:]] | [[:upper:]] ) echo "You typed the letter $character"
                                ;;

                                # Check for digits
    [0-9] ) echo "You typed the digit $character"
                                ;;

                                # Check for anything else
    * ) echo "You did not type a letter or a digit"
esac
```

注意特殊的模式符号 `*`。该模式会匹配任意的东西，以防前面的情况都无法匹配。一般将其用作最后一个情况，来检测无效地输入。


## 循环
shell 提供了三个循环命令：`while`、`until` 和 `for`。
以下是一个使用 `while` 命令从 0 数到 9 的示例：

```sh
#!/bin/bash

number=0
while [ "$number" -lt 10 ]; do
    echo "Number = $number"
    number=$((number + 1))
done
```

这里 `while` 后的表达式退出状态为 0 时，则会运行其内的代码块，大部分情况下必须有终止循环的代码，来改变退出状态，否则就会导致 **无限循环**。
`until` 命令工作原理一样。下面使用 `until` 的示例结果和上面的一样。

```sh
#!/bin/bash

number=0
until [ "$number" -ge 10 ]; do
    echo "Number = $number"
    number=$((number + 1))
done
```


## 创建一个菜单
基于文本交互的最常用表现形式就是使用 **菜单**。
以下示例实现了一个简单的菜单：

```sh
#!/bin/bash

selection=
until [ "$selection" = "0" ]; do
    echo "
    PROGRAM MENU
    1 - Display free disk space
    2 - Display free memory

    0 - exit program
"
    echo -n "Enter selection: "
    read selection
    echo ""
    case $selection in
        1 ) df ;;
        2 ) free ;;
        0 ) exit ;;
        * ) echo "Please enter 1, 2, or 0"
    esac
done
```

这里 `until` 的目的是为了在每次选择菜单后重新显示菜单，直到用户输入 0 为止，即「退出」选项。
为了使程序执行起来更好看，我们在脚本中加入一个要求用户再选择的选项执行完毕后需输入 enter，并在显示菜单前清除屏幕。以下是强化版：

```sh
#!/bin/bash

press_enter()
{
    echo -en "\nPress Enter to continue"
    read
    clear
}

selection=
until [ "$selection" = "0" ]; do
    echo "
    PROGRAM MENU
    1 - display free disk space
    2 - display free memory

    0 - exit program
"
    echo -n "Enter selection: "
    read selection
    echo ""
    case $selection in
        1 ) df ; press_enter ;;
        2 ) free ; press_enter ;;
        0 ) exit ;;
        * ) echo "Please enter 1, 2, or 0"; press_enter
    esac
done
```

> 当计算机宕住了时…
> 优秀的软件会使用 **超时** 来避免程序的挂起状态。这就意味着循环里有计算尝试次数，或尝试计算等待的时间。如果尝试次数或等待时间到达上限，那么循环就会退出并终止，程序会抛出一个错误。

