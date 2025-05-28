# mysqlKill

## 使用场景

循环查杀符合某些特征的mysql会话。

## 参数说明

```azure
Usage of ./
mysqlKill: -busy-time int
        语句执行秒数大于等于此参数值的会话会被杀掉 (default 60)
  -commands string
        会话的command（可以指定多个，逗号分隔）, 举例： 3,2
  -dry-run
        模拟运行模式，此模式下只打印符合kill的会话，不会执行kill操作。用于验证参数设置是否正确
  -dsn string
        dsn, 举例：127.0.0.1:3306
  -event string
        搜索等待事件名称中的字符串
  -interval int
        多久执行一次检查，单位：秒 (default 10)
  -password string
        登录密码
  -run-time string
        运行时间，举例：1d,1h,1m,1s (default "365d")
  -sqlid string
        SQLID
  -user string
        登录用户
  -username string
        会话的username
```

> user/password: 登录用户和密码,省略时，使用dba_monitor进行查杀，需要保证有kill权限。     
> dsn：数据库连接字符串，格式：ip:port/service_name  
> busy-time: 执行时间（秒）大于等于此参数值的会话会被杀掉。        
> commands: 会话的command类型,具体参考：SELECT command_type,command_name FROM v$sqlcommand; 可以指定多个值，逗号分隔。比如：3,2,6,7       
> 表示查增改删操作将会被杀掉。        
> username：会话的username等于此参数值的会话会被杀掉。        
> sqlid: SQLID等于此参数值的会话会被杀掉。        
> event: 等待事件名称中包含此字符串的会话会被杀掉。      
> interval：多久执行一次查杀，单位：秒，默认10秒。     
> run-time：运行时间超过此参数值程序会退出，默认365天。支持指定天、小时、分钟、秒，举例：1h30m 表示程序运行1小时30分钟后退出。      
> dry-run: 模拟运行模式，此模式下只打印符合kill的会话，不会执行kill操作。用于验证参数设置是否正确。     

## 使用方法

1. 先在dry-run模式下，调整好最佳参数

```azure
./orakill -dsn=172.22.110.12:1521/rmsdbtst -run-time 1s -interval=1 -dry-run
时间 | SID | SERIAL | 状态 | 用户 | 命令类型 | 客户端 | 程序 | SQLID | 运行时间 | 等待事件
2024-08-28 15:09:33 | 131 | 28829 | ACTIVE | RMS | 3 | cn-shenzhen.533818709361184.1654673938957 | JDBC Thin Client | auc3ub6rucgn9 | 26186 | db file sequential read
2024-08-28 15:09:33 | 1105 | 26157 | INACTIVE | MOMAPI | 0 | erp-mom-hlj-soa-deploy-5f9584d597-624fh | JDBC Thin Client | NULL | 2014 | SQL*Net message from client
2024-08-28 15:09:33 | 3908 | 55756 | INACTIVE | MOMAPI | 0 | erp-mom-hlj-soa-deploy-5f9584d597-624fh | JDBC Thin Client | NULL | 2014 | SQL*Net message from client
2024-08-28 15:09:33 | 3667 | 47241 | INACTIVE | RMS | 0 | erp-gsc-mom-transfer-server-deploy-79fc595cd8-tjw6w | JDBC Thin Client | NULL | 1729 | SQL*Net message from client
2024-08-28 15:09:33 | 1106 | 6731 | INACTIVE | RMS | 0 | erp-gsc-mom-transfer-server-deploy-79fc595cd8-tjw6w | JDBC Thin Client | NULL | 1654 | SQL*Net message from client
2024-08-28 15:09:33 | 3790 | 58411 | INACTIVE | RMS | 0 | erp-gsc-mom-transfer-server-deploy-79fc595cd8-tjw6w | JDBC Thin Client | NULL | 1641 | SQL*Net message from client
2024-08-28 15:09:33 | 11 | 23859 | INACTIVE | RMS | 0 | erp-gsc-mom-transfer-server-deploy-79fc595cd8-tjw6w | JDBC Thin Client | NULL | 1583 | SQL*Net message from client
2024-08-28 15:09:33 | 127 | 26878 | INACTIVE | RMS | 0 | erp-esb-mom-soa-deploy-66584b86b-bwwg8 | JDBC Thin Client | NULL | 1540 | SQL*Net message from client
```

根据打印出会话信息调整busy-time、commands、username、sqlid、event等参数，直至打印的会话全部是计划杀掉的会话。

2. 去掉-dry-run参数，执行kill操作

```azure
# 这里是示例，请根据实际情况调整参数
nohup ./orakill -dsn=172.22.110.12:1521/rmsdbtst -run-time=1d -interval=5 -username=RMS -commands=3 -busy-time=600 &>172.22.110.12_1521.log &
```

> 文件 orakill_<ip>_<port>.hist 记录杀掉的会话信息


