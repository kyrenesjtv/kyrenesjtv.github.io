#### 数据库单表取数据的时候(例如日志表)，ID一样，但是时间不一样，去时间最大或者最小的一条。作为一张新表

~~~
select t1.*
from globl_log t1
left join globl_log t2 on  t1.user_id = t2.user_id and t1.last_login_time < t2.last_login_time
where t2.id is null

~~~