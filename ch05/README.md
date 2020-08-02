# 5. 命令替换和常量

在上一节的基础上，我们给脚本加上更新时间及更新人信息：

```sh
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

title="System Information for"

cat <<- _EOF_
    <html>
    <head>
        <title>
        $title $HOSTNAME
        </title>
    </head>

    <body>
    <h1>$title $HOSTNAME</h1>
    <p>Updated on $(date +"%x %r %Z") by $USER</p>
    </body>
    </html>
_EOF_

```

这里我们使用了另一个环境变量 `USER`，来获取用户名。
另外，我们使用了 `$(date +"%x %r %Z")`。其中 `$()` 告诉 shell 「使用括号中的命令结果替换」。你可以通过如下方式查看 `date` 命令的帮助。
`$(command)` 也可以用反引号来代替：\`command\`。旧的形式是和 sh 兼容的。所以，以下命令是相等的：

```sh
$(command)
`command`
```


## 将命令结果赋予给一个变量

```sh
right_now=$(date +"%x %r %Z")
```

变量也可以嵌套，如：

```sh
right_now=$(date +"%x %r %Z")
time_stamp="Updated on $right_now by $USER"
```


## 常量
正如 **常量** 其名字所表明的，是用于存储会改变的东西。这意味着在脚本执行过程中，变量的内容可能会改变。
另一方面，可能存在只要设置后就不会再变的值。这种叫做 **常量**。大部分语言都有 **常量** 的用法，bash 也有，但很少看到。而且，如果一个值看起来像一个常数，那么程序员应该给他一个全大写的名字，即使没有强制要求。
环境变量通常是全大写的，因为它们通常不会改变。所以，建议在写脚本时，给变量小写名字，给常量大写名字。
例如：

```sh
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

title="System Information for $HOSTNAME"
RIGHT_NOW=$(date +"%x %r %Z")
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

cat <<- _EOF_
    <html>
    <head>
        <title>
        $title
        </title>
    </head>

    <body>
    <h1>$title</h1>
    <p>$TIME_STAMP</p>
    </body>
    </html>
_EOF_

```

