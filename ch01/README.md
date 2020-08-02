# 1. 创建你的第一个脚本

学习路径：
- 写一个脚本
- 给 shell 执行脚本的权限
- 将脚本放到 shell 可以找到的地方

## 写一个脚本
shell 脚本是一个包含 ASCII 文本的文件。使用 **文本编辑器** 来创建脚本。文本编辑器用于读写 ASCII 文本文件。Linux 系统中有许多文本编辑器，包括支持命令行的，和支持 GUI 环境的。以下列出一些：

| 名称 | 描述 | 实现 |
| --- | --- | --- |
| vi，vim | 编辑器的祖先。比较难用，但是强大、轻量并且快速。在几乎所有 Unix 系统上都有，可以说是 Unix 学习的入门证书。在大部分 Linux 发行版中有一个名为 vim 的增强版 vi 编辑器。 | 命令行 |
| Emacs | Richard Stallman 开发。 | 命令行 |
| nano | pine 邮件程序提供的。易于使用但功能简陋。建议初学者使用。 | 命令行 |
| gedit | Gnome 桌面环境提供的编辑器 | 图形界面 |
| kwrite | KDE 提供的一个高级编辑器。有语法高亮。 | 图形界面 |

在文本编辑器中输入你的第一个脚本如下：

```sh
#!/bin/bash
# My first script

echo "Hello World!"
```

给它取个名字：*hello_world*。
第一行叫 shebang，告诉 shell 用什么程序来解释脚本。这里是 */bin/bash*。其他的脚本语言如 Perl、awk、tcl、Tk 和 python 也是用这个机制。
第二行是注释。bash 会忽略所有以 `#` 开头的行。
最后一行是 `echo` 命令。简单地在显示器中打印其参数。


## 设置权限
下一步要做的是给 shell 权限来执行脚本。使用 `chmod` 命令如下：

```sh
$ chmod 755 hello_world
```


## 将脚本放到系统路径中
此时你可以运行脚本：

```sh
$ ./hello_world
```

你可以看到显示了 「Hello World!」。
shell 维护了一个存放可执行文件（程序）的目录列表，在命令行输入命令时只会到这个列表中查找。如果没有找到对应的命令，那么就会有 *command not found* 的错误提示。
你可以通过以下命令获取这个目录列表：

```sh
$ echo $PATH
```

当你执行你的新脚本时，你需要在文件名前加上 `./` 前缀。
你也可以往系统路径中添加目录，使用以下命令：

```sh
$ export PAHT=$PATH:directory
```

更好的方式是编辑你的 *.bash_profile* 或 *.profile* 文件。这样你每次登陆的时候就会自动加载目录下的命令了。
一般我们将可执行程序放到 *bin* 目录下，你可以在你的家目录下创建它，并把它加入到系统路径中：

```sh
$ mkdir bin
```

将你的脚本移动到该 *bin* 目录下，然后只需要输入：

```sh
$ hello_world
```

你的脚本就可以执行了。注意你可能需要打开一个新的终端才能使其生效。或者在编辑系统路径后，执行：

```sh
$ bash ~/.bash_profile
```
