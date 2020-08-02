# 6. Shell 函数

当程序越来越长、越来越复杂的时候，就会变得越来越难设计、编码和维护。这时，我们通常会将单个巨大的任务拆分为一系列小的任务。
例如，假设我们要向一个外星人描述如何去一家超市买菜，我们最开始可能这样描述：
- 离开家
- 开车到超市
- 停车
- 进入超市
- 买菜
- 开车回家
- 停车
- 回家

然而，对于一个外星人来说，我们可能对「停车」需要更详细的说明：
- 寻找停车位
- 开车进入停车位
- 关闭马达
- 拉手刹
- 下车
- 锁车

对于「关闭马达」这一步可能又有更详细的说明，如「关掉引擎」、「拔出钥匙」等。
上面的步骤我们称为 **自上而下设计**。这个技术可以让你将大的复杂的任务拆为很多小的简单的任务。
对示例来说，我们可以使用 **shell 函数** 来做。

```sh
#!/bin/bash

# sysinfo_page - A script to produce an system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW=$(date +"%x %r %Z")
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{

}


show_uptime()
{

}


drive_space()
{

}


home_space()
{

}

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

函数要注意的几个事项：
- 第一，必须在使用前声明。
- 第二，函数体（在 `{}` 之间的）必须至少含有一个有效的命令（像上面的脚本运行是会报错的，因为函数体都是空的，最简单的解决方法是在每一个函数体内加一个 `return` 声明）

当你开发脚本程序时，写一部分代码后，运行脚本，然后再写更多的代码，运行脚本，如此。这样，你可以尽早地发现脚本中的错误并改正。
在写脚本时，你可以使用 **存根** 技巧来暂时替代函数体。例如：

```sh
system_info()
{
    # Temporary function stub
    echo "function system_info"
}

```

这样，我们的脚本就可以成功执行，即使我们还没有写完 system_info() 函数。我们可以在之后替换掉存根代码。
使用 `echo` 的目的是我们可以在脚本运行时从结果中获得反馈（知道哪里还没完成）。
给上面的示例加上存根，让示例可以运行：

```sh
#!/bin/bash

# sysinfo_page - A script to produce an system information HTML file

##### Constants

TITLE="System Information for $HOSTNAME"
RIGHT_NOW=$(date +"%x %r %Z")
TIME_STAMP="Updated on $RIGHT_NOW by $USER"

##### Functions

system_info()
{
    # Temporary function stub
    echo "function system_info"
}


show_uptime()
{
    # Temporary function stub
    echo "function show_uptime"
}


drive_space()
{
    # Temporary function stub
    echo "function drive_space"
}


home_space()
{
    # Temporary function stub
    echo "function home_space"
}


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

