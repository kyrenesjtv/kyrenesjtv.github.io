#### 数据库排序的时候遇到NULL值，默认NULL值为最小。只需要在order by 的时候给字段套上IFNULL。就会对非NULL数据进行排序，NULL的数据默认放在最尾部

~~~
select *****
order ifnull(**) asc

~~~