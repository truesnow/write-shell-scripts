# 3. HERE 脚本

## 使用脚本写一个 HTML 文件
HTML 示例：

```html
<html>
<head>
    <title>
    The title of your page
    </title>
</head>

<body>
    Your page content goes here.
</body>
</html>
```

写一个生成以上文本的脚本：

```sh
#!/bin/bash

# sysinfo_page - A script to produce an html file

echo "<html>"
echo "<head>"
echo " <title>"
echo " The title of your page"
echo " </title>"
echo "</head>"
echo ""
echo "<body>"
echo " Your page content goes here."
echo "</body>"
echo "</html>"
```

上述脚本使用方法：

```sh
$ sysinfo_page > sysinfo_page.html
```

可以将系统信息写入到一个 HTML 文件。
上述脚本太难写了，有很多 echo，我们使用双引号改进一下：

```sh
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

echo "<html>
 <head>
   <title>
   The title of your page
   </title>
 </head>

 <body>
   Your page content goes here.
 </body>
 </html>"
```

使用双引号，可以在文本中嵌入换行符，使其参数可以换行。
但是这种做法有限制。但是大部分 HTML 标签本身需要使用引号，这就会因为冲突导致写的很麻烦：每个引号都要使用反斜杠转义。
我们可以使用一种叫 **here 脚本** 的方式来避免上述情况。

```sh
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

cat << _EOF_
<html>
<head>
    <title>
    The title of your page
    </title>
</head>

<body>
    Your page content goes here.
</body>
</html>
_EOF_
```

here 脚本（有时也称为 here 文档）是 I/O 重定向的一部分。
here 脚本的格式如下：

```sh
command << token
content to be used as command's standard input
token
```

token 可以是任意字符串，只要不跟 bash 保留字冲突就行。结尾的 token 必须和开始的 token 一样。
上述方式会忽略文本中的空格和换行符，如果想要保留，你可以改写脚本如下：

```sh
#!/bin/bash

# sysinfo_page - A script to produce an HTML file

cat <<- _EOF_
    <html>
    <head>
        <title>
        The title of your page
        </title>
    </head>

    <body>
        Your page content goes here.
    </body>
    </html>
_EOF_
```

将 `<<` 替换为 `<<-` 就可以了。

