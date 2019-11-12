#### 数据库排序的时候遇到NULL值，默认NULL值为最小。然后需要把NULL的放到最后，不为NULL的从小到大(如果想相反，只需将0 1互换位置，asc变成DESC即可)。就可以以下代码实现。

~~~
select *****
order by if(ISNULL(xxx),0,1) asc

~~~