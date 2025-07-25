---
layout: post
title: "时实文件同步：从 rsync 到 lsyncd 的抓腾之路"
date: 2024-10-06 00:00:00 +0800
categories: [devops]
tags: [rsync, lsyncd, linux]
---

当需要在多台服务器之间实时同步文件时，传统的 `rsync` 命令往往
不能满足秒级的更新需求。 这篇文章讲述了我在寻找实时同步方案
过程中的一些心得，从使用 `inotify` 触发 `rsync` 到最终选择
**lsyncd** 的抓腾之路。

## rsync 与 inotify

起初我使用 `inotify-tools` 监听目录变化，并在检测到文件修改时
调用 `rsync` 将更改同步到目标服务器。 尤直这种方案简单，但
存在两大问题：首先，如果短时间内有大量文件变动，`rsync`
会被频繁触发，导致系统引方过大；其次，`inotify` 无法递归
监听所有子目录，实际配置较为紫红【922107329369592†L44-L75】。

## lsyncd – 更好的选择

后来我发现了 **lsyncd**（Live Syncing Daemon），它编合了
`inotify` 的实时监控与 `rsync` 的数据传输能力。 lsyncd
支持递归监听目录，并通过事件合并减少同步次数。 配置文件允许
设置 SSH 密钥、日志路径等参数，易于调优。例如：

```lua
settings {
    logfile    = "/var/log/lsyncd.log",
    statusFile = "/var/log/lsyncd.status"
}

sync {
    default.rsyncssh,
    source = "/data/src",
    host   = "user@example.com",
    targetdir = "/data/dst",
    rsync = {
        compress = true,
        rsh = "/usr/bin/ssh -i /home/user/.ssh/id_rsa"
    }
}
```

通过这种方式，我实现了秒级文件同步，既可靠又节省系统资源。
如果你也在寻找实时同步方案，推荐尝试 lsyncd【922107329369592†L44-L75】。
