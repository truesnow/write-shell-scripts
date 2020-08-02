# 8. 流程控制（一）

本节学习：
- `if`
- `test`
- `exit`

## if
`if` 根据退出状态（exit status）来判断。`if` 语法如下：

```sh
if commands; then
commands
[elif commands; then
commands...]
[else
commands]
fi
```

commands 是命令列表。


## 退出状态
命令（包括脚本和 shell 函数）执行结束时会返回一个值给系统，叫做 **退出状态**。其值是在 0 到 255 之间的整数。0 表示成功，其他值表示失败。shell 提供了一个参数来检查退出状态。


```sh
echo $?
```

shell 提供了两个极其简单的内建命令只用于表示 0 和 1 退出状态。`true` 命令总是执行成功，而 `false` 命令总是执行失败。
示例：

```sh
if true; then echo "It's true."; fi
if false; then echo "It's true."; fi
```


## test
`test` 命令经常和 `if` 命令一起使用。它有两种语法：

```sh
# 第一种形式
test expression

# 第二种形式
[ expression ]
```

`test` 命令原理很简单。如果表达式返回 true，`test` 返回状态 0，否则以状态 1 退出。
示例：

```sh
if [ -f .bash_profile ]; then
    echo "You have a .bash_profile. Things are fine."
else
    echo "Yikes! You have no .bash_profile!"
fi
```

示例说明：
- `-f .bash_profile` 表示「.bash_profile 是否是一个文件」。
- `[ expression ]` 是 `test` 的另一种写法，注意 `[` 和 `]` 两边的空格是必须的。
- 分号 `;` 是命令分隔符。使用分号允许你在同一行写多个命令。例如：

```sh
clear; ls
```

- `if [ -f .bash_profile ]; then` 这么写是为了好看，更易读。
- `echo` 前面的空格没有实际意义，是为了使脚本更易读。

我们也可以写成下面这样：

```sh
# 一种形式

if [ -f .bash_profile ]
then
    echo "You have a .bash_profile. Things are fine."
else
    echo "Yikes! You have no .bash_profile!"
fi

# 另一种形式

if [ -f .bash_profile ]
then echo "You have a .bash_profile. Things are fine."
else echo "Yikes! You have no .bash_profile!"
fi
```

以下是一些 `test` 可以判断的列表，因为 `test` 是一个 shell 内置命令，所以你也可以用 `help test` 来查看其帮助文档。

| 表达式 | 描述 |
| --- | --- |
| -d file | 如果 file 为目录则为 true |
| -e file | 如果 file 存在则为 true |
| -f file | 如果 file 存在且是一个普通文件则为 true |
| -L file | 如果 file 是一个符号链接则为 true |
| -r file | 如果你有 file 的可读权限则为 true |
| -w file | 如果你有 file 的可写权限则为 true |
| -x file | 如果你有 file 的可执行权限则为 true |
| file1 -nt file2 | 如果 file1 比 file2 更新（根据修改时间）则为 true |
| file1 -ot file2 | 如果 file1 比 file2 更旧（根据修改时间）则为 true |
| -z string | 如果 string 为空则为 true |
| -n string | 如果 string 不为空则为 true |
| string1 = string2 | 如果 string1 和 string2 相等则为 true |
| string1 != string2 | 如果 string1 和 string2 不相等则为 true |


## exit
`exit` 命令可以使脚本立即终止并设置退出状态。例如：

```sh
exit 0
```

代表设置退出状态为 0（成功），而：

```sh
exit 1
```

退出脚本并设置退出状态为 1（失败）。


## 检查是否是 root 用户
当一个脚本需要超级用户才能运行时，如果我们用普通用户去执行这个脚本会发生什么？会产生很多很丑的错误信息。那么我们可以在脚本中添加什么代码，来阻止普通用户执行呢？
`id` 命令可以告诉我们当前用户是谁。当使用 `-u` 选项执行 `id` 命令时，会返回当前用户的数字 ID。

```sh
id -u
```

当切换为超级用户后：`id -u` 返回 0。于是，我们的脚本可以加上：

```sh
if [ $(id -u) = "0" ]; then
    echo "superuser"
fi
```

而我们的真正目的是阻止用户执行，所以脚本可以这样写：

```sh
if [ $(id -u) != "0" ]; then
    echo "You must be the superuser to run this script" >&2
    exit 1
fi
```

注意 `>&2` 是 I/O 重定向的另一种形式，表示将输出重定向到标准错误。因为我们的脚本执行结果最终是想输出到一个普通文件的，这样做可以将错误信息与正常执行结果区分开来。
所以我们最开始的示例中的函数可以写成：

```sh
function home_space
{
    # Only the superuser can get this information

    if [ "$(id -u)" = "0" ]; then
        echo "<h2>Home directory space by user</h2>"
        echo "<pre>"
        echo "Bytes Directory"
            du -s /home/* | sort -nr
        echo "</pre>"
    fi

} # end of home_space

```

