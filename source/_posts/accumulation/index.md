---
title: 批量删除redis中key对应的value
tags:
  - redis  
categories:
  - 缓存
---
# 日常积累

## 批量删除redis中指定key

```sh
redis-cli -h yourhost -p 6379 -a password -n 0 keys "nlas:*" | xargs -r -t -n1 redis-cli -h yourhost -p 6379 -a password -n  0 del

redis-cli 命令解释：
-h　　host
-p　　port
-a　　auth
-n　　选择库，-n 1 相当于 select 1
这就登录了，然后执行 keys "nlas:*" 查询符合条件的 key。
xargs 命令参数解释:
-r　　no-run-if-empty，如果参数为空不执行后面的命令。
-t　　先打印执行命令，然后再执行。
-n1　 传入的参数没批执行几条，-n1，1 就是每次传入 1 个值执行一次。

```

