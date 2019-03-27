## springboot多数据源切换,这个版本基于springboot2_withSQL

### 接下来的就是相差的文件,只要在上述版本当中替换这些文件就行,但是要注意withSQL中的原来的数据源连接的Config

#### application.properties , 在这里把最上面的mapper扫描路径给注释掉了

```
#mybatis.mapper-locations: classpath:mappers/**/*.xml 配置多数据源关闭
# 数据库访问配置
# 主数据源，默认的
#spring.mysql.datasource.type = com.alibaba.druid.pool.DruidDataSource
spring.read.mysql.datasource.driverClassName = com.mysql.jdbc.Driver
# serverTimezone=GMT%2B8 为什么要加这个？不加这个时区连接会报错
spring.read.mysql.datasource.url = jdbc:mysql://127.0.0.1:3306/blackbox?useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&serverTimezone=GMT%2B8
spring.read.mysql.datasource.username = root
spring.read.mysql.datasource.password = root


#spring.write.mysql.datasource.driverClassName = com.mysql.jdbc.Driver
# serverTimezone=GMT%2B8 为什么要加这个？不加这个时区连接会报错
#spring.write.mysql.datasource.url = jdbc:mysql://127.0.0.1:3306/demo?useUnicode=true&zeroDateTimeBehavior=convertToNull&serverTimezone=GMT%2B8
#spring.write.mysql.datasource.username = root
#spring.write.mysql.datasource.password = root


# 下面为连接池的补充设置，应用到上面所有数据源中
# 初始化大小，最小，最大
spring.mysql.datasource.initialSize = 50
spring.mysql.datasource.minIdle = 100
spring.mysql.datasource.maxActive = 200
# 配置获取连接等待超时的时间
spring.mysql.datasource.maxWait = 60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.mysql.datasource.timeBetweenEvictionRunsMillis = 60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.mysql.datasource.minEvictableIdleTimeMillis = 300000
#spring.mysql.datasource.validationQuery = select 1
spring.mysql.datasource.testWhileIdle = true
spring.mysql.datasource.testOnBorrow = false
spring.mysql.datasource.testOnReturn = false
# 打开PSCache，并且指定每个连接上PSCache的大小
spring.mysql.datasource.poolPreparedStatements = true
spring.mysql.datasource.maxPoolPreparedStatementPerConnectionSize = 20
# 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
#spring.mysql.datasource.filters = stat, wall, log4j
# 通过connectProperties属性来打开mergeSql功能；慢SQL记录
#spring.mysql.datasource.connectionProperties = druid.stat.mergeSql = true; druid.stat.slowSqlMillis = 5000
# 合并多个DruidDataSource的监控数据
# spring.mysql.datasource.useGlobalDataSourceStat = true




#spring.oracle.datasource.type = com.alibaba.druid.pool.DruidDataSource
spring.read.oracle.datasource.driverClassName = com.mysql.jdbc.Driver
spring.read.oracle.datasource.url = jdbc:mysql://127.0.0.1:3306/demo?useUnicode=true&zeroDateTimeBehavior=convertToNull&serverTimezone=GMT%2B8
spring.read.oracle.datasource.username = root
spring.read.oracle.datasource.password = root
# 下面为连接池的补充设置，应用到上面所有数据源中
# 初始化大小，最小，最大
spring.oracle.datasource.initialSize = 50
spring.oracle.datasource.minIdle = 100
spring.oracle.datasource.maxActive = 200
# 配置获取连接等待超时的时间
spring.oracle.datasource.maxWait = 60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.oracle.datasource.timeBetweenEvictionRunsMillis = 60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.oracle.datasource.minEvictableIdleTimeMillis = 300000
#spring.oracle.datasource.validationQuery = select 1
spring.oracle.datasource.testWhileIdle = true
spring.oracle.datasource.testOnBorrow = false
spring.oracle.datasource.testOnReturn = false
# 打开PSCache，并且指定每个连接上PSCache的大小
spring.oracle.datasource.poolPreparedStatements = true
spring.oracle.datasource.maxPoolPreparedStatementPerConnectionSize = 20
# 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
#spring.oracle.datasource.filters = stat, wall, log4j
# 通过connectProperties属性来打开mergeSql功能；慢SQL记录
#spring.oracle.datasource.connectionProperties = druid.stat.mergeSql = true; druid.stat.slowSqlMillis = 5000
# 合并多个DruidDataSource的监控数据
# spring.oracle.datasource.useGlobalDataSourceStat = true


```

#### 在application这个类上上面需要把
```
@ServletComponentScan
@EnableTransactionManagement//事务注解
//@MapperScan("com.dingdang.system.dao")//扫描dao接口 配置多数据源关闭
```

#### 两个数据源连接池的config。 如果是主数据源的话,需要在每个bean上面加@Primary

```
@Configuration
@MapperScan(basePackages = MysqlDataSourceConfig.PACKAGE, sqlSessionFactoryRef = "mysqlSqlSessionFactory")
//开启事务注解
@EnableTransactionManagement
public class MysqlDataSourceConfig implements EnvironmentAware {
    // mysqldao扫描路径
    static final String PACKAGE = "com.dingdang.system.dao.mysql";
    // mybatis mapper扫描路径
    static final String MAPPER_LOCATION = "classpath:mappers/mysql/**/*.xml";
    //2.0之后用这种方法，直接getProperty
    private  Environment environment;

    @Override
    public void setEnvironment(Environment environment) {
        this.environment=environment;
    }

    @Primary
    @Bean(name = "mysqlDatasource")
    public DataSource mysqlDataSource() {
        DruidDataSource datasource = new DruidDataSource();
        datasource.setUrl(environment.getProperty("spring.read.mysql.datasource.url"));
        datasource.setDriverClassName(environment.getProperty("spring.read.mysql.datasource.driverClassName"));
        datasource.setUsername(environment.getProperty("spring.read.mysql.datasource.username"));
        datasource.setPassword(environment.getProperty("spring.read.mysql.datasource.password"));

        datasource.setInitialSize(Integer.valueOf(environment.getProperty("spring.mysql.datasource.initialSize")));
        datasource.setMinIdle(Integer.valueOf(environment.getProperty("spring.mysql.datasource.minIdle")));
        datasource.setMaxWait(Long.valueOf(environment.getProperty("spring.mysql.datasource.maxWait")));
        datasource.setMaxActive(Integer.valueOf(environment.getProperty("spring.mysql.datasource.maxActive")));
        datasource.setTimeBetweenEvictionRunsMillis(Long.valueOf(environment.getProperty("spring.mysql.datasource.timeBetweenEvictionRunsMillis")));
        datasource.setMinEvictableIdleTimeMillis(Long.valueOf(environment.getProperty("spring.mysql.datasource.minEvictableIdleTimeMillis")));
        datasource.setTestWhileIdle(Boolean.parseBoolean(environment.getProperty("spring.mysql.datasource.testWhileIdle")));
        datasource.setTestOnBorrow(Boolean.parseBoolean(environment.getProperty("spring.mysql.datasource.testOnBorrow")));
        datasource.setTestOnReturn(Boolean.parseBoolean(environment.getProperty("spring.mysql.datasource.testOnReturn")));
        datasource.setPoolPreparedStatements(Boolean.parseBoolean(environment.getProperty("spring.mysql.datasource.poolPreparedStatements")));
        try {
            datasource.setFilters("stat,wall");
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return datasource;
    }

    @Bean(name = "mysqlTransactionManager")
    @Primary
    public DataSourceTransactionManager mysqlTransactionManager() {
        return new DataSourceTransactionManager(mysqlDataSource());
    }

    @Bean(name = "mysqlSqlSessionFactory")
    @Primary
    public SqlSessionFactory mysqlSqlSessionFactory(@Qualifier("mysqlDatasource") DataSource dataSource)
            throws Exception {
        final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(dataSource);
        //如果不使用xml的方式配置mapper，则可以省去下面这行mapper location的配置。
        sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources(MysqlDataSourceConfig.MAPPER_LOCATION));
        //开启驼峰
        sessionFactory.getObject().getConfiguration().setMapUnderscoreToCamelCase(true);
        return sessionFactory.getObject();
    }

    @Bean
    public ServletRegistrationBean druidServlet() {
        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean();
        servletRegistrationBean.setServlet(new StatViewServlet());
        servletRegistrationBean.addUrlMappings("/druid/*");
        Map<String, String> initParameters = new HashMap<String, String>();
         initParameters.put("loginUsername", "druid");// 用户名
         initParameters.put("loginPassword", "druid");// 密码
        initParameters.put("resetEnable", "false");// 禁用HTML页面上的“Reset All”功能
                initParameters.put("allow", "127.0.0.1"); // IP白名单 (没有配置或者为空，则允许所有访问)
         initParameters.put("deny", "192.168.20.38");// IP黑名单
        // (存在共同时，deny优先于allow)
        servletRegistrationBean.setInitParameters(initParameters);
        return servletRegistrationBean;
    }

    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setFilter(new WebStatFilter());
        filterRegistrationBean.addUrlPatterns("/*");
        filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.bmp,*.png,*.css,*.ico,/druid/*");
        return filterRegistrationBean;
    }


}


```


```
@Configuration
@MapperScan(basePackages = OracleDataSourceConfig.PACKAGE, sqlSessionFactoryRef = "oracleSqlSessionFactory")
//开启事务注解
@EnableTransactionManagement
public class OracleDataSourceConfig implements EnvironmentAware {
    // mysqldao扫描路径
    static final String PACKAGE = "com.dingdang.system.dao.oracle";
    // mybatis mapper扫描路径
    static final String MAPPER_LOCATION = "classpath:mappers/oracle/**/*.xml";
    //2.0之后用这种方法，直接getProperty
    private Environment environment;

    @Override
    public void setEnvironment(Environment environment) {
        this.environment=environment;
    }

    @Bean(name = "oracleDatasource")
    @ConfigurationProperties(prefix ="spring.datasource.oracle")
    public DataSource oracleDataSource() {
        DruidDataSource datasource = new DruidDataSource();
        datasource.setUrl(environment.getProperty("spring.read.oracle.datasource.url"));
        datasource.setDriverClassName(environment.getProperty("spring.read.oracle.datasource.driverClassName"));
        datasource.setUsername(environment.getProperty("spring.read.oracle.datasource.username"));
        datasource.setPassword(environment.getProperty("spring.read.oracle.datasource.password"));

        datasource.setInitialSize(Integer.valueOf(environment.getProperty("spring.oracle.datasource.initialSize")));
        datasource.setMinIdle(Integer.valueOf(environment.getProperty("spring.oracle.datasource.minIdle")));
        datasource.setMaxWait(Long.valueOf(environment.getProperty("spring.oracle.datasource.maxWait")));
        datasource.setMaxActive(Integer.valueOf(environment.getProperty("spring.oracle.datasource.maxActive")));
        datasource.setTimeBetweenEvictionRunsMillis(Long.valueOf(environment.getProperty("spring.oracle.datasource.timeBetweenEvictionRunsMillis")));
        datasource.setMinEvictableIdleTimeMillis(Long.valueOf(environment.getProperty("spring.oracle.datasource.minEvictableIdleTimeMillis")));
        datasource.setTestWhileIdle(Boolean.parseBoolean(environment.getProperty("spring.oracle.datasource.testWhileIdle")));
        datasource.setTestOnBorrow(Boolean.parseBoolean(environment.getProperty("spring.oracle.datasource.testOnBorrow")));
        datasource.setTestOnReturn(Boolean.parseBoolean(environment.getProperty("spring.oracle.datasource.testOnReturn")));
        datasource.setPoolPreparedStatements(Boolean.parseBoolean(environment.getProperty("spring.oracle.datasource.poolPreparedStatements")));
        try {
            datasource.setFilters("stat,wall");
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return datasource;
    }

    @Bean(name = "oracleTransactionManager")
    public DataSourceTransactionManager oracleTransactionManager() {
        return new DataSourceTransactionManager(oracleDataSource());
    }

    @Bean(name = "oracleSqlSessionFactory")
    public SqlSessionFactory oracleSqlSessionFactory(@Qualifier("oracleDatasource") DataSource dataSource)
            throws Exception {
        final SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(dataSource);
        //如果不使用xml的方式配置mapper，则可以省去下面这行mapper location的配置。
        sessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources(OracleDataSourceConfig.MAPPER_LOCATION));
        //开启驼峰
        sessionFactory.getObject().getConfiguration().setMapUnderscoreToCamelCase(true);
        return sessionFactory.getObject();
    }

//    @Bean
//    public ServletRegistrationBean druidServlet() {
//        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean();
//        servletRegistrationBean.setServlet(new StatViewServlet());
//        servletRegistrationBean.addUrlMappings("/druid/*");
//        Map<String, String> initParameters = new HashMap<String, String>();
//        initParameters.put("loginUsername", "druid");// 用户名
//        initParameters.put("loginPassword", "druid");// 密码
//        initParameters.put("resetEnable", "false");// 禁用HTML页面上的“Reset All”功能
//        initParameters.put("allow", "127.0.0.1"); // IP白名单 (没有配置或者为空，则允许所有访问)
//        initParameters.put("deny", "192.168.20.38");// IP黑名单
//        // (存在共同时，deny优先于allow)
//        servletRegistrationBean.setInitParameters(initParameters);
//        return servletRegistrationBean;
//    }
//
//    @Bean
//    public FilterRegistrationBean filterRegistrationBean() {
//        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
//        filterRegistrationBean.setFilter(new WebStatFilter());
//        filterRegistrationBean.addUrlPatterns("/*");
//        filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.bmp,*.png,*.css,*.ico,/druid/*");
//        return filterRegistrationBean;
//    }

}

```


#### 上述配置完成之后,springboot自然而然就会在你使用某一个DAO的bean的时候会自动去切换数据源
