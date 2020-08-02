# 2. 编辑已有脚本

在你的账户创建时，你的家目录已经有一些脚本了，用于配置你在电脑上的会话行为。
在会话期间，系统中有名为 **环境变量** 的信息。其中包含了你的系统路径、你的用户名等。你可以用 `set` 命令来设置。
环境变量中通常包含两类命令：**别名** 和 **shell 函数**。

## 环境是如何建立的？
当你登录系统时，bash 程序启动，并读取一系列名为 **启动文件** 的配置脚本。这些脚本定义了所有用户共同的环境。更多的启动文件在你的家目录中，定义了你私人的环境。会话分为两类：**登录 shell 会话** 和 **未登录 shell 会话**。例如直接在终端中输入 bash 就是启动了一个未i登录 shell 会话。
登录会话读取文件如下：

| 文件 | 内容 |
| --- | --- |
| */etc/profile* | 应用到所有用户的全局配置 |
| *~/.bash_profile* | 用户的私人启动文件。可用于覆盖全部配置脚本中的设置 |
| *~/.bash_login* | 若未找到 *~/.bash_profile*，则尝试读取该脚本 |
| *~/.profile* | 若 *~/.bash_profile* 和 *~/.bash_login* 都未找到，则尝试读取本文件。是基于 Debain 发行版，如 Ubuntu 的默认文件 |

未登录会话读取以下启动文件：

| 文件 | 内容 |
| --- | --- |
| */etc/bash.bashrc* | 应用到所有用户的全局配置 |
| *~/.bashrc* | 用户的私人启动文件。可用于覆盖全部配置脚本中的设置 |

为了读取所有以上启动文件，未登录 shell 也会继承其父进程的环境，通常是一个登录 shell。
以普通用户的角度来说，*~/.bashrc* 通常是最重要的启动脚本，因为它最常被读取。未登录 shell 默认读取它，并且大多数登录 shell 的启动文件某种程度上也是读取它。
例如，一个典型的 *.bash_profile* 的内容如下：

```sh
# .bash_profile
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
 . ~/.bashrc
fi

# User specific environment and startup programs
PATH=$PATH:$HOME/bin
export PATH
```

上面的意思是说，如果 *.bashrc* 文件存在，则读取 *.bashrc* 文件。
`export` 命令告诉 shell 将 PATH 变量的内容对 shell 的子进程可用。


## 别名
别名用于给很长的命令一个简写的命令。语法如下：

```sh
$ alias name=value
```

name 为新命令的名称，value 为命令行输入 name 时实际执行的文本。
例如：

```sh
$ alias l='ls -l'
```

```sh
$ alias today='date +"%A, %B %-d, %Y"'
```

输出示例：`星期日, 二月 16, 2020`。
注意 `alias` 是 shell 内置命令，如果你在命令行中执行，则该别名只会在当前会话中生效。


## shell 函数

```sh
today() {
    echo -n "Today's date is: "
    date +"%A, %B %-d, %Y"
}
```

