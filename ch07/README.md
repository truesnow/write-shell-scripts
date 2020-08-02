# 7. 系统信息

## show_uptime
显示 `uptime` 的输出，包括系统自上次重启后的运行时间，用户数以及最近的系统负载。

```sh
show_uptime()
{
    echo "<h2>System uptime</h2>"
    echo "<pre>"
    uptime
    echo "</pre>"
}
```


## drive_space
使用 `df` 命令来得到所有装载到文件系统的磁盘空间使用情况。

```sh
drive_space()
{
    echo "<h2>Filesystem space</h2>"
    echo "<pre>"
    df
    echo "</pre>"
}
```


## home_space
显示用户的家目录空间使用情况。按使用空间大小倒序显示文件和目录列表。

```sh
home_space()
{
    echo "<h2>Home directory space by user</h2>"
    echo "<pre>"
    echo "Bytes Directory"
    du -s /home/* | sort -nr
    echo "</pre>"
}
```

注意为了使此函数成功运行，本脚本必须以超级用户身份运行，因为 `du` 命令要超级用户权限来检视 */home* 目录。


## system_info
我们还没准备好写这个函数，使用存根代码来代替：

```sh
system_info()
{
    echo "<h2>System release info</h2>"
    echo "<p>Function not yet implemented</p>"
}
```

