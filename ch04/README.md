# 4. 变量

```sh
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

cat <<- _EOF_
    <html>
    <head>
        <title>
        My System Information
        </title>
    </head>

    <body>
    <h1>My System Information</h1>
    </body>
    </html>
_EOF_
```

上述脚本中「My System Information」重复了，我们可以改进如下：

```sh
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

title="My System Information"

cat <<- _EOF_
    <html>
    <head>
        <title>
        $title
        </title>
    </head>

    <body>
    <h1>$title</h1>
    </body>
    </html>
_EOF_
```

## 变量
上述示例中使用了 **变量**，几乎在所有语言中都有这个概念。变量可以存储信息，可以通过一个名字来引用。
shell 在任何时候碰到 `$` 符，都会视为变量并用变量值代替它。


## 如何创建一个变量
定义一个变量名，后面跟 `=` 号，`=` 号后面跟要赋予的值。
变量命名规则如下：
- 变量名以字母开头
- 不可以嵌套空格。使用下划线代替
- 不能使用标点符号


## 环境变量
当你开启一个 shell 会话时，启动文件已经帮你设置好了一些变量。使用 `printenv` 查看你的环境变量。环境变量 `HOSTNAME` 存储了你的主机名。我们可以将这个变量添加到上面的脚本中：

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
    </body>
    </html>
_EOF_
```

注意，为了方便，环境变量都是大写的。

