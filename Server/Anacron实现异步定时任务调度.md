# Anacron实现异步定时任务调度

## Anacron是什么

    众所周知，cron是*nix系统下的一种定时任务调度器，可以实现任意用户周期性运行特定任务的需要，常用在自动维护和管理中。而anacron是一种异步的定时任务调度器，大家都知道cron是Linux系统下的定时任务调度器，可以实现异步的任务调度。对于24小时开机运行的服务器可能用不到这种东西，但是非24小时开机，又想定时运行任务的机器还是十分有用的，可以实现异步的调用。

目前很多发行版中的cron是由cronie实现的，而cronie中集成了anacron。
## anacron介绍
1. anacron 用于以天为单位的频率运行命令。它的工作与 cron 稍有不同，它假设机器不会一直开机。
2. cron 也适合在那些不会 24X7 运行如笔记本以及桌面电脑的机器上运行每日、每周以及每月的计划任务（LCTT 译注：不适合按小时、分钟执行任务）。
3. 假设你有一个计划任务（比如备份脚本）要使用 cron 在每天半夜运行，也许你以及睡着，那时你的桌面/笔记本电脑已经关机。你的备份脚本就不会被运行。
4. 如果你使用 anacron，你可以确保在你下次开启桌面/笔记本电脑的时候，备份脚本会被执行。

## Anacron配置
anacron的配置与cron类似，也是要首先配置一个叫anacrontab的文件，其文件格式说明一般在manual里有man anacrontab。
```sh
# /etc/anacrontab: anacron 配置文件

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
RANDOM_DELAY=55 # 表示定时触发后随机延迟55分钟以内的时间再启动应用（主要是防止一开机多任务同时启动造成负载过重）
START_HOURS_RANGE=23-24 # 表示程序在23时至24时之间会启动

#period in days   delay in minutes   job-identifier   command
1                 5                  cron.daily       nice run-parts /etc/cron.daily
7                 25                 cron.weekly      nice run-parts /etc/cron.weekly
@monthly          45                 cron.monthly     nice run-parts /etc/cron.monthly
```

period  - 这是任务的频率，以天来指定，或者是@daily 、@weekly、@monthly 代表每天、每周、每月一次， 你也可以使用数字：1 - 每天、7 - 每周、30- 每月，或者N - 几天。
delay   - 这是在执行一个任务前等待的分钟数。
job-id  - 这是写在日志文件中任务的独特名字。
command - 这是要执行的命令或 shell 脚本。

## 如何看示列文件
> ls -l /var/spool/anacron/

## cron 以及 anacron 的比较
cron 和 anacron 主要的区别在于 cron 能在那些持续运行的机器上有效地运行，而 anacron 是针对那些会在一天内或者一周内会关机的机器。

cron:
它是守候进程 适合服务器 可以让你分钟级运行计划任务 关机时不会执行计划任务 普通用户和root用户都可以使用
anacron:
它不是守候进程 适合桌面/笔记本电脑 只能让你一天为基础来运行计划任务 如果计划任务到期，机器是关的，那么它会在机器下次开机后执行计划任务 只有root用户可以使用（使用特定的配置启动普通任务）

由于Cron是Linux的内置服务，可以用以下的方法启动.关闭这个服务:
```sh
systemctl start crond.service        # 启动服务
systemctl stop crond.service         # 关闭服务
systemctl restart crond.service      # 重启服务
systemctl reload crond.service       # 重新载入配置
```
