---
title: oracle 锁表查询和解锁
date: 2018-06-07 15:34:04
tags: oracle
categories: 数据库
copyright: true
top: 5
---
# 1.查看里面的锁 #
```
SELECT b. OWNER, b.object_name, l.session_id, l.locked_mode
FROM v$locked_object l, dba_objects b
WHERE b.object_id = l.object_id;

SELECT t2.username, t2.sid, t2.serial #, t2.logon_time
FROM v$locked_object t1, v$session t2
WHERE t1.session_id = t2.sid
ORDER BY t2.logon_time
```
<!-- more -->
# 2.解锁 #
```
alter system kill session 'sid,serial#'
```

如：
```
alter system kill session '111,222'
```


# 3.查询当前用户的所有活动的session #
```
select t.SID,t.SERIAL#,t.STATUS,t.STATE,t.SQL_ID
from v$session t
where t.USERNAME = 'OCN_TDS_DB'
and t.STATUS = 'ACTIVE'
and t.MACHINE = 'localhost.localdomain';
```
# 4.分析session执行的SQL，尤其是sql_id相同的 #
```
#7ykv5kcc4paz2表示当前重复较高的SQL，查询出来发现该SQL主要是用来刷新工单数的。
select * from v$sql s where s.SQL_ID='7ykv5kcc4paz2'
```
# 5.删除当前应用连接的所有活动session，释放资源 #
```
#停止Mobile应用，清除所有获取工单数的SQL
select 'alter system kill session '''||t.SID||','||t.SERIAL#||''';'
from v$session t
where t.USERNAME = 'OCN_TDS_DB'
and t.STATUS = 'ACTIVE'
and t.SQL_ID='7ykv5kcc4paz2'
and t.MACHINE = 'localhost.localdomain';
```
# 6.根据session_id查询执行的SQL #
```
select s.SAMPLE_TIME,
sq.SQL_TEXT,
sq.DISK_READS,
sq.BUFFER_GETS,
 
sq.CPU_TIME,
sq.ROWS_PROCESSED,
--sq.SQL_FULLTEXT,
sq.SQL_ID
from v$sql sq, v$active_session_history s
where s.SQL_ID = sq.SQL_ID
and s.SESSION_ID = 190
order by s.SAMPLE_TIME desc;
```