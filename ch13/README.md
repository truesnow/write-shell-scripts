# 13. 流程控制（三）

本节讲 `for` 的使用。语法：

```sh
for variable in words; do
    commands
done
```

本质上，`for` 是将一个列表中的值依次赋值给一个指定的变量，执行命令，直到列表中的值使用完毕。示例：

```sh
#!/bin/bash

for i in word1 word2 word3; do
    echo $i
done
```

`for` 的有趣之处在于构建列表的方式。所有扩展符都可以使用。以下是使用命令替换的示例：

```sh
#!/bin/bash

count=0
for i in $(cat ~/.bash_profile); do
    count=$((count + 1))
    echo "Word $count ($i) contains $(echo -n $i | wc -c) characters"
done
```

位置参数示例：

```sh
#!/bin/bash

for i in "$@"; do
    echo $i
done
```

shell 变量 `$@` 包含命令行参数。另一个示例：

```sh
#!/bin/bash

for filename in "$@"; do
    result=
    if [ -f "$filename" ]; then
        result="$filename is a regular file"
    else
        if [ -d "$filename" ]; then
            result="$filename is a directory"
        fi
    fi
    if [ -w "$filename" ]; then
        result="$result and it is writable"
    else
        result="$result and it is not writable"
    fi
    echo "$result"
done
```

尝试运行该脚本，使用 `*` 等通配符来观察其结果。
以下是另一个示例脚本，该脚本对比两个目录，并列出在第一个目录而第二个目录中没有的文件。

```sh
#!/bin/bash

# cmp_dir - program to compare two directories

# Check for required arguments
if [ $# -ne 2 ]; then
    echo "usage: $0 directory_1 directory_2" 1>&2
    exit 1
fi

# Make sure both arguments are directories
if [ ! -d $1 ]; then
    echo "$1 is not a directory!" 1>&2
    exit 1
fi

if [ ! -d $2 ]; then
    echo "$2 is not a directory!" 1>&2
    exit 1
fi

# Process each file in directory_1, comparing it to directory_2
missing=0
for filename in $1/*; do
    fn=$(basename "$filename")
    if [ -f "$filename" ]; then
        if [ ! -f "$2/$fn" ]; then
            echo "$fn is missing from $2"
            missing=$((missing + 1))
        fi
    fi
done
echo "$missing files missing"
```

现在来改善之前的打印系统信息的脚本：

```sh
home_space()
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

一个新版本：

```sh
home_space()
{
    echo "<h2>Home directory space by user</h2>"
    echo "<pre>"
    format="%8s%10s%10s %-s\n"
    printf "$format" "Dirs" "Files" "Blocks" "Directory"
    printf "$format" "----" "-----" "------" "---------"
    if [ $(id -u) = "0" ]; then
        dir_list="/home/*"
    else
        dir_list=$HOME
    fi
    for home_dir in $dir_list; do
        total_dirs=$(find $home_dir -type d | wc -l)
        total_files=$(find $home_dir -type f | wc -l)
        total_blocks=$(du -s $home_dir)
        printf "$format" $total_dirs $total_files $total_blocks
    done
    echo "</pre>"

} # end of home_space
```

该版本引入了一个新的命令 `printf`。用于格式化输出字符串。格式字符串的格式可以参考：
- [GNU Awk User's Guide - Control Letters](http://www.gnu.org/software/gawk/manual/html_node/Control-Letters.html#Control-Letters)
- [GNU Awk User's Guide - Format Modifiers](http://www.gnu.org/software/gawk/manual/html_node/Format-Modifiers.html#Format-Modifiers)

同时引入了 `find` 命令，用于查找符合标准的文件或目录。
`wc` 命令，用于统计出现的文件和目录数量。
`id` 命令用于检查用户是否是超级用户。
在另一个函数中使用 `for`：

```sh
system_info()
{
    # Find any release files in /etc

    if ls /etc/*release 1>/dev/null 2>&1; then
        echo "<h2>System release info</h2>"
        echo "<pre>"
        for i in /etc/*release; do

            # Since we can't be sure of the
            # length of the file, only
            # display the first line.

            head -n 1 $i
        done
        uname -orp
        echo "</pre>"
    fi

} # end of system_info
```

