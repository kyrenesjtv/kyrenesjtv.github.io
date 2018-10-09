## springboot多数据源切换,这个版本基于springboot2_withSQL

### 接下来的就是相差的文件,只要在上述版本当中替换这些文件就行,但是要注意withSQL中的原来的数据源连接的Config

#### application.properties , 在这里把最上面的mapper扫描路径给注释掉了

```
#mybatis.mapper-locations: classpath:mapper/*/*.xml
# 数据库访问配置
# 主数据源，默认的 carmon
spring.datasource.carmon.type = com.alibaba.druid.pool.DruidDataSource
spring.datasource.carmon.driverClassName = com.mysql.jdbc.Driver
spring.datasource.carmon.url = jdbc:mysql://127.0.0.1:3306/zjgwyc6?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true
#spring.datasource.carmon.url = jdbc:mysql://127.0.0.1:3306/zjgwc?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true
spring.datasource.carmon.username = root
spring.datasource.carmon.password = root


spring.datasource.gwyc.type = com.alibaba.druid.pool.DruidDataSource
spring.datasource.gwyc.driverClassName = com.mysql.jdbc.Driver
#spring.datasource.gwyc.url = jdbc:mysql://172.17.116.14:3306/gwyc?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true
spring.datasource.gwyc.url = jdbc:mysql://127.0.0.1:3306/zjgwyc6?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true
spring.datasource.gwyc.username = root
spring.datasource.gwyc.password = root


```


#### 两个数据源连接池的config。 如果是主数据源的话,需要在每个bean上面加@Primary

```
@Configuration
@MapperScan(basePackages = CarmonDatasourceConfig.PACKAGE, sqlSessionFactoryRef = "carmonSqlSessionFactory")
public class CarmonDatasourceConfig {
    // mysqldao扫描路径
    static final String PACKAGE = "com.github.kyrenesjtv.springboot2.springboot2_withsql.DAO.carmon";
    // mybatis mapper扫描路径
    static final String MAPPER_LOCATION = "classpath:mapper/carmon/*.xml";

    @Primary
    @Bean(name = "carmondatasource")
    @ConfigurationProperties(prefix ="spring.datasource.carmon")
    public DataSource carmonDataSource() {
        return DruidDataSourceBuilder.create().build();
    }

    @Bean(name = "carmonTransactionManager")
    @Primary
    public DataSourceTransactionManager mysqlTransactionManager() {
        return new DataSourceTransactionManager(carmonDataSource());
    }

    @Bean(name = "carmonSqlSessionFactory")
    @Primary
    public SqlSessionFactory carmonSqlSessionFactory(@Qualifier("carmondatasource") DataSource dataSource)
            throws Exception {
        final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(dataSource);
        //如果不使用xml的方式配置mapper，则可以省去下面这行mapper location的配置。
        sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources(CarmonDatasourceConfig.MAPPER_LOCATION));
        return sessionFactory.getObject();
    }
}

```


```
@Configuration
@MapperScan(basePackages = GwycDatasourceConfig.PACKAGE, sqlSessionFactoryRef = "gwycSqlSessionFactory")
public class GwycDatasourceConfig {
    // mysqldao扫描路径
    static final String PACKAGE = "com.github.kyrenesjtv.springboot2.springboot2_withsql.DAO.gwyc";
    // mybatis mapper扫描路径
    static final String MAPPER_LOCATION = "classpath:mapper/gwyc/*.xml";

    @Bean(name = "gwycdatasource")
    @ConfigurationProperties(prefix ="spring.datasource.gwyc")
    public DataSource gwycDataSource() {
        return DruidDataSourceBuilder.create().build();
    }

    @Bean(name = "gwycTransactionManager")
    public DataSourceTransactionManager mysqlTransactionManager() {
        return new DataSourceTransactionManager(gwycDataSource());
    }

    @Bean(name = "gwycSqlSessionFactory")
    public SqlSessionFactory gwycSqlSessionFactory(@Qualifier("gwycdatasource") DataSource dataSource)
            throws Exception {
        final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(dataSource);
        //如果不使用xml的方式配置mapper，则可以省去下面这行mapper location的配置。
        sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources(GwycDatasourceConfig.MAPPER_LOCATION));
        return sessionFactory.getObject();
    }
}
```


#### 上述配置完成之后,springboot自然而然就会在你使用某一个DAO的bean的时候会自动去切换数据源
