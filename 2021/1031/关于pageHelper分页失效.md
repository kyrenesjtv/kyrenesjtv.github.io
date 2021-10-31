### pageHelper分页引起失效

#### 如果我们的SQL语句打印出来，只有count(),而没有limit的话,就证明分页已经失效了。

```java
[10-19 20:11:03.003] DEBUG[DubboServerHandler-172.16.0.247:20880-thread-10]-Slf4jLogFilter(134)-> {conn-610010, pstmt-620001} created. SELECT count(0) FROM jdp_tb_trade WHERE seller_nick = ? AND jdp_modified >= ? AND jdp_modified < ? AND buyer_nick = ?
```

#### 然后我们检查一个pageHelper的版本，是否是5.2.0以上的。
#### 如果是5.2.0以上的版本，我们就降版本到5.1.2就行了

