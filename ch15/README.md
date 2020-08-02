# 15. 错误、信号和陷阱（二）

发生错误并不一定是脚本退出的唯一方式。你也应该考虑信号。考虑以下程序：

```sh
#!/bin/bash

echo "this script will endlessly loop until you stop it"
while true; do
 : # Do nothing
done
```

该程序一运行就进入了死循环，除非收到信号来终止它。你可以通过按下 Ctrl-c 键来发送 SIGINT（SIGnal INTerrupt 的缩写）信号。


## 自行清理
忽略信号可能没有关系，但在某些情况下是有关系的：
看看另一个脚本：

```sh
#!/bin/bash

# Program to print a text file with headers and footers

TEMP_FILE=/tmp/printfile.txt

pr $1 > $TEMP_FILE

echo -n "Print file? [y/n]: "
read
if [ "$REPLY" = "y" ]; then
 lpr $TEMP_FILE
fi
```

脚本功能：从命令行中读取文本文件并输入到一个临时文件，如果用户输入 y 则通过 `lpr` 命令打印该文件（如果你的系统中没有打印机可使用 `less` 命令代替）。
首先，这个脚本本身存在很多问题，如没有检查命令行参数是否输入了文件名，以及输入的文件是否存在等。但是这里重点要讲的是，脚本运行结束后，没有对临时文件进行处理。
最佳实践是脚本运行结束后将临时文件删除：

```sh
rm $TEMP_FILE
```

看起来这样就能解决问题了。但是如果用户在提示「Print file? [y/n]:」的时候按下 ctrl-c 终止了脚本的运行呢？这就会终止 `read` 命令的执行，且 `rm` 命令永远也不会执行。很明显，当按下 ctrl-c 时，我们要对 SIGINT 信号进行处理。
bash 提供了这样的方法，当接收到信号时执行命令。


## trap
`trap` 命令允许你在接收到信号的时候执行命令。其使用格式如下：

```sh
trap arg signals
```

其中 signals 是要接收的信号列表，arg 是接收到信号列表中任一信号时所要执行的命令。对于上面的打印脚本，我们可以这样处理信号：

```sh
#!/bin/bash

# Program to print a text file with headers and footers

TEMP_FILE=/tmp/printfile.txt

trap "rm $TEMP_FILE; exit" SIGHUP SIGINT SIGTERM

pr $1 > $TEMP_FILE

echo -n "Print file? [y/n]: "
read
if [ "$REPLY" = "y" ]; then
 lpr $TEMP_FILE
fi
rm $TEMP_FILE
```

你可以使用 `trap -l` 查看 `trap` 支持的信号列表。另外，你也可以用信号数字值来代替信号名。

> **信号 9**
> `SIGKILL` 或信号 9 不能使用 `trap` 捕获。内核会直接终止脚本进程，没有信号处理。发送信号 9：

```sh
kill -9
```

> 通常情况下直接发送信号 9 是没有问题的，但是也有很多脚本会有问题。例如，很多复杂的程序（也有一些不复杂的）会使用 **锁文件** 来防止同一程序的多个拷贝同时运行。当这种程序接收到 SIGKILL 信号，脚本终止时没有机会移除锁文件。这时锁文件的存在会导致无法重启程序，除非手动移除锁文件。
> 注意，到最后再采取 SIGKILL 信号来终止程序。


## 一个清理函数
当使用 `trap` 需要执行多个清理命令时，你可以使用 `;` 号将多个命令放在一起，这样很丑，你可以将这些命令写入一个函数中。

```sh
#!/bin/bash

# Program to print a text file with headers and footers

TEMP_FILE=/tmp/printfile.txt

clean_up() {

 # Perform program exit housekeeping
 rm $TEMP_FILE
 exit
}

trap clean_up SIGHUP SIGINT SIGTERM

pr $1 > $TEMP_FILE

echo -n "Print file? [y/n]: "
read
if [ "$REPLY" = "y" ]; then
 lpr $TEMP_FILE
fi
clean_up
```

结合信号处理的最终版本：

```sh
#!/bin/bash

# Program to print a text file with headers and footers

# Usage: printfile file

# Create a temporary file name that gives preference
# to the user's local tmp directory and has a name
# that is resistant to "temp race attacks"

if [ -d "~/tmp" ]; then
 TEMP_DIR=~/tmp
else
 TEMP_DIR=/tmp
fi
TEMP_FILE=$TEMP_DIR/printfile.$$.$RANDOM
PROGNAME=$(basename $0)

usage() {

 # Display usage message on standard error
 echo "Usage: $PROGNAME file" 1>&2
}

clean_up() {

 # Perform program exit housekeeping
 # Optionally accepts an exit status
 rm -f $TEMP_FILE
 exit $1
}

error_exit() {

 # Display error message and exit
 echo "${PROGNAME}: ${1:-"Unknown Error"}" 1>&2
 clean_up 1
}

trap clean_up SIGHUP SIGINT SIGTERM

if [ $# != "1" ]; then
 usage
 error_exit "one file to print must be specified"
fi
if [ ! -f "$1" ]; then
 error_exit "file $1 cannot be read"
fi

pr $1 > $TEMP_FILE || error_exit "cannot format file"

echo -n "Print file? [y/n]: "
read
if [ "$REPLY" = "y" ]; then
 lpr $TEMP_FILE || error_exit "cannot print file"
fi
clean_up
```


## 创建安全临时文件
Unix 中常使用 */tmp* 目录来存放程序使用的临时文件。每个人都可以往该目录中写入文件。这通常会导致安全问题，建议避免使用 */tmp* 目录。首选做法是在本地目录创建一个临时目录，如 *~/tmp*。如果非要往 */tmp* 中写文件，你必须保证文件名是不可预测的。可预测的文件名将会使攻击者创建链接符号到其他文件。创建随机文件名示例：

```sh
TEMP_FILE=$TEMP_DIR/printfile.$$.$RANDOM
```

`$$` 表示程序的进程 ID（PID）。这有助于知道是哪个进程产生的文件。`$RANDOM` 产生随机的数字。

