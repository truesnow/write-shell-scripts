# 12. 位置参数

目前为止，我们的脚本示例长这样：

```sh
#!/bin/bash

# sysinfo_page - A script to produce a system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW=$(date +"%x %r %Z")
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{
    echo "<h2>System release info</h2>"
    echo "<p>Function not yet implemented</p>"

} # end of system_info


show_uptime()
{
    echo "<h2>System uptime</h2>"
    echo "<pre>"
    uptime
    echo "</pre>"

} # end of show_uptime


drive_space()
{
    echo "<h2>Filesystem space</h2>"
    echo "<pre>"
    df
    echo "</pre>"

} # end of drive_space


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



##### Main

cat <<- _EOF_
  <html>
  <head>
      <title>$TITLE</title>
  </head>
  <body>
      <h1>$TITLE</h1>
      <p>$TIME_STAMP</p>
      $(system_info)
      $(show_uptime)
      $(drive_space)
      $(home_space)
  </body>
  </html>
_EOF_

```

现在，我们想增加以下功能：
- 在命令行中指定要输出的文件名，如果未指定，则使用默认的文件名称
- 提供一个交互模式提示用户输入文件名，并且在文件名已经存在的时候提醒用户将覆盖其内容
- 自然地，我们希望能提供一个帮助选项来显示使用信息

以上所有功能都和选项和参数有关。要处理这些命令行选项，我们需要用到 shell 中的 **位置参数** 功能。位置参数是一系列特殊变量（`$0` 到 `$9`)，其中包含了命令行的内容。
例如：

```sh
some_program word1 word2 word3
```

如果 some_program 是一个脚本，我们可以这样使用位置参数读取其中的每一个项目：
- `$0` 包含 some_program
- `$1` 包含 word1
- `$2` 包含 word2
- `$3` 包含 word3

你可以使用下面的脚本进行验证：

```sh
#!/bin/bash

echo "Positional Parameters"
echo '$0 = ' $0
echo '$1 = ' $1
echo '$2 = ' $2
echo '$3 = ' $3
```


## 检测命令行参数
你可以通过几种方式来检查你的命令行参数，第一种，检查 `$1`，示例：

```sh
#!/bin/bash

if [ "$1" != "" ]; then
    echo "Positional parameter 1 contains something"
else
    echo "Positional parameter 1 is empty"
fi
```

第二种，shell 中维护了一个名为 `$#` 的变量，其中包含命令行的项目数，此外，`$0` 表示命令名称。

```sh
#!/bin/bash

if [ $# -gt 0 ]; then
    echo "Your command line contains $# arguments"
else
    echo "Your command line contains no arguments"
fi
```


## 命令行选项
如前面所提到过的，大部分程序，尤其是属于 GNU 项目的，都支持短选项和长选项。例如，要显示命令的帮助，你可以使用 `-h` 选项或 `--help` 选项。长选项通常是两个短横线。我们也将在我们的脚本中采用此惯例。
示例：

```sh
interactive=
filename=~/sysinfo_page.html

while [ "$1" != "" ]; do
    case $1 in
        -f | --file ) shift
                                filename=$1
                                ;;
        -i | --interactive ) interactive=1
                                ;;
        -h | --help ) usage
                                exit
                                ;;
        * ) usage
                                exit 1
    esac
    shift
done
```

脚本中的 `shift` 是 shell 中处理位置参数的一个内置命令。每次调用 `shift`，它将会使位置参数减 1。`$2` 变成 `$1`，`$3` 变成 `$2`，`$4` 变成 `$3` 等等。示例：

```sh
#!/bin/bash

echo "You start with $# positional parameters"

# Loop until all parameters are used up
while [ "$1" != "" ]; do
    echo "Parameter 1 equals $1"
    echo "You now have $# positional parameters"

    # Shift all the parameters down by one
    shift

done
```


## 获取选项参数
`-f` 选项要求输入一个有效的文件名作为参数。我们使用 `shift` 来从命令行中获取，并将其赋值给 filename。


## 将命令行处理器集成到脚本

```sh
#!/bin/bash

# sysinfo_page - A script to produce a system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW=$(date +"%x %r %Z")
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{
    echo "<h2>System release info</h2>"
    echo "<p>Function not yet implemented</p>"

} # end of system_info


show_uptime()
{
    echo "<h2>System uptime</h2>"
    echo "<pre>"
    uptime
    echo "</pre>"

} # end of show_uptime


drive_space()
{
    echo "<h2>Filesystem space</h2>"
    echo "<pre>"
    df
    echo "</pre>"

} # end of drive_space


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


write_page()
{
    cat <<- _EOF_
    <html>
        <head>
        <title>$TITLE</title>
        </head>
        <body>
        <h1>$TITLE</h1>
        <p>$TIME_STAMP</p>
        $(system_info)
        $(show_uptime)
        $(drive_space)
        $(home_space)
        </body>
    </html>
_EOF_

}

usage()
{
    echo "usage: sysinfo_page [[[-f file ] [-i]] | [-h]]"
}


##### Main

interactive=
filename=~/sysinfo_page.html

while [ "$1" != "" ]; do
    case $1 in
        -f | --file ) shift
                                filename=$1
                                ;;
        -i | --interactive ) interactive=1
                                ;;
        -h | --help ) usage
                                exit
                                ;;
        * ) usage
                                exit 1
    esac
    shift
done


# Test code to verify command line processing

if [ "$interactive" = "1" ]; then
 echo "interactive is on"
else
 echo "interactive is off"
fi
echo "output file = $filename"


# Write page (comment out until testing is complete)

# write_page > $filename
```


## 添加交互模式
交互模式通过以下代码实现：

```sh
if [ "$interactive" = "1" ]; then

    response=

    echo -n "Enter name of output file [$filename] > "
    read response
    if [ -n "$response" ]; then
        filename=$response
    fi

    if [ -f $filename ]; then
        echo -n "Output file exists. Overwrite? (y/n) > "
        read response
        if [ "$response" != "y" ]; then
            echo "Exiting program."
            exit 1
        fi
    fi
fi

```

首先，检查是否开启了交互模式。然后，让用户输入文件名。注意我们提示语的格式：

```sh
echo -n "Enter name of output file [$filename] > "
```

我们将 filename 的默认值显示出来，以防用户直接按下了 enter 键时，使用默认文件名。

