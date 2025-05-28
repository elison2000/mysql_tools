# mysqlKill

## 使用场景

循环查杀符合某些特征的mysql会话。

## 参数说明

```azure
Usage of ./mysqlKill:
  -P int
        数据库端口 (default 3306)
  -busy-time int
        语句执行秒数大于等于此参数值的会话会被杀掉 (default 60)
  -h string
        数据库地址
  -interval int
        多久执行一次检查，单位：秒 (default 10)
  -match-cmd string
        匹配Command状态
  -match-db string
        匹配数据库（可以指定多个，逗号分隔）
  -match-sql string
        搜索sql文本（支持%模糊匹配）
  -match-user string
        匹配用户（可以指定多个，逗号分隔）
  -mode string
        dry-run: 只打印匹配的线程，不会执行kill。
        kill: 终止匹配的线程。
        kill-query: 终止匹配的查询，线程不退出。 (default "dry-run")
  -p string
        登录密码
  -parallel int
        kill并发数 (default 8)
  -run-time string
        运行时间，举例：1d,1h,1m,1s (default "365d")
  -txn-time int
        事务执行时间大于等于此参数值的会话会被杀掉（默认不检查此参数）
  -u string
        登录用户
```


