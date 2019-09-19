## 第一章：JDBC必知必会

### 1.如何配置单数据源

- 配置文件中写的通用的（非嵌入式数据库）：

  ```properties
  # 初始数据库的链接的URL
  spring.datasource.url=jdbc:mysql://localhost/test
  # 数据库登录的用户名
  spring.datasource.username=dbuser
  # 数据库登录的密码
  spring.datasource.password=dbpass
  # 数据库的驱动(可选)
  spring.datasource.driver-class-name=com.mysql.jdbc.Driver
  ```

- 配置嵌入式的数据库的：

  ```properties
  # 数据库初始化的模式
  # always为始终执行初始化，
  # embedded只初始化内存数据库（默认值）,如h2等，
  # never为不执行初始化。
  spring.datasource.initialization-mode=embedded|always|never
  # 初始化的建表语句
  spring.datasource.schema=classpath:schema.sql
  # 初始化的语句
  spring.datasource.data=classpath:data.sql
  # 初始化的数据库
  spring.datasource.platform=hsqldb|h2|oracle|mysql|postgresql
  ```

- Spring-Boot做了哪些配置

  - DataSourceAutoConfiguration
    - 配置DataSource
  - DataSourceTransactionManagerAutoConfiguration
    - 配置DataSourceTransactionManager
  - JdbcTemplateAutoConfiguration
    - 配置JdbcTemplate
  - 注意：符合条件才进行配置

- code：

  - application.properties

  ```properties
  # 开启服务器的监控，可以通过URL查看服务器的状态
  management.endpoints.web.exposure.include=*
  # 设置彩色输出会让日志更具可读性
  # NEVER：禁用ANSI-colored输出（默认项）
  # DETECT：会检查终端是否支持ANSI，是的话就采用彩色输出（推荐项）
  # ALWAYS：总是使用ANSI-colored格式输出，若终端不支持的时候，会有很多干扰信息，不推荐使用
  spring.output.ansi.enabled=ALWAYS
  # 数据库的相关配置
  spring.datasource.url=jdbc:h2:mem:testdb
  spring.datasource.username=sa
  spring.datasource.password=
  # HikariCP配置
  spring.datasource.hikari.maximumPoolSize=5
  spring.datasource.hikari.minimumIdle=5
  spring.datasource.hikari.idleTimeout=600000
  spring.datasource.hikari.connectionTimeout=30000
  spring.datasource.hikari.maxLifetime=1800000
  ```

- pom.xml

  ```xml
      <dependencies>
          <!--监控服务器的状态-->
        <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-jdbc</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
          <dependency>
              <groupId>com.h2database</groupId>
              <artifactId>h2</artifactId>
              <scope>runtime</scope>
          </dependency>
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <optional>true</optional>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
      </dependencies>
  ```
  
- DatasourceDemoApplication.java

  ```java
  import lombok.extern.slf4j.Slf4j;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.CommandLineRunner;
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.jdbc.core.JdbcTemplate;
  
  import javax.sql.DataSource;
  import java.sql.Connection;
  
  @SpringBootApplication
  //打印日志的注解，lombok中的注解
  @Slf4j
  public class DatasourceDemoApplication implements CommandLineRunner {
  
      @Autowired
      private DataSource dataSource;
  
      @Autowired
      private JdbcTemplate jdbcTemplate;
  
      public static void main(String[] args) {
          SpringApplication.run(DatasourceDemoApplication.class, args);
      }
  
      @Override
      public void run(String... args) throws Exception {
          showConnection();
          showData();
      }
  
      private void showConnection() throws Exception {
          //打印数据源的信息
          log.info(dataSource.toString());
          Connection conn = dataSource.getConnection();
          //打印连接的信息
          log.info(conn.toString());
          conn.close();
      }
  
      private void showData() {
          //查询Foo表，并将所有的信息打印出来
          //类似foreach增强for循环
          jdbcTemplate.queryForList("SELECT * FROM FOO")
                  .forEach(row -> log.info(row.toString()));
      }
  }
  ```

### 2.配置多数据源

- 手动配置两组 DataSource及相关内容

- 与spring boot协同工作(二选一)

  - 配置@Primary类型的Bean
  - 排除spring boot的自动配置
    - DataSourceAutoConfiguration
    - DataSourceTransactionManagerAutoConfiguration
    - JdbcTemplateAutoConfiguration

- 注意事项：

  - 不同数据源的配置要分开
  - 关注每次使用的数据源
    - 有多个DataSource时系统如何判断
    - 对应的设施(事务，ORM等)如何选择DataSource

- code：

  - application.properties

    ```properties
    # 开启服务器的监控，可以通过URL查看服务器的状态
    management.endpoints.web.exposure.include=*
    # 设置彩色输出会让日志更具可读性
    # NEVER：禁用ANSI-colored输出（默认项）
    # DETECT：会检查终端是否支持ANSI，是的话就采用彩色输出（推荐项）
    # ALWAYS：总是使用ANSI-colored格式输出，若终端不支持的时候，会有很多干扰信息，不推荐使用
    spring.output.ansi.enabled=always
    
    # 其中一个数据库的配置信息
    foo.datasource.url=jdbc:h2:mem:foo
    foo.datasource.username=sa
    foo.datasource.password=
    # 另一个数据库的配置信息
    bar.datasource.url=jdbc:h2:mem:bar
    bar.datasource.username=sa
    bar.datasource.password=
    ```

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-jdbc</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    
    ```

  - MultiDatasourceDemoApplication.java

    ```java
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
    import org.springframework.boot.autoconfigure.jdbc.DataSourceProperties;
    import org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration;
    import org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Bean;
    import org.springframework.jdbc.datasource.DataSourceTransactionManager;
    import org.springframework.transaction.PlatformTransactionManager;
    
    import javax.annotation.Resource;
    import javax.sql.DataSource;
    
    /*
    * 排除了数据源自动配置，数据源事务自动配置，jdbc模板自动配置
    * */
    @SpringBootApplication(exclude = {DataSourceAutoConfiguration.class,
            DataSourceTransactionManagerAutoConfiguration.class,
            JdbcTemplateAutoConfiguration.class})
    @Slf4j
    public class MultiDatasourceDomeApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(MultiDatasourceDomeApplication.class, args);
        }
    
        //构建其中一个数据源的配置
        @Bean
        @ConfigurationProperties("foo.datasource")
        public DataSourceProperties fooDataSourceProperties() {
            return new DataSourceProperties();
        }
    
        //生成数据源
        @Bean
        public DataSource fooDataSource() {
            DataSourceProperties dataSourceProperties = fooDataSourceProperties();
            log.info("foo datasource:{}", dataSourceProperties.getUrl());
            return dataSourceProperties.initializeDataSourceBuilder().build();
        }
    
        //生成事务管理
        @Bean
        @Resource
        public PlatformTransactionManager fooTxManager(DataSource fooDataSource) {
            return new DataSourceTransactionManager(fooDataSource);
        }
    
        //构建另一个数据源的配置
        @Bean
        @ConfigurationProperties("bar.datasource")
        public DataSourceProperties barDataSourceProperties() {
            return new DataSourceProperties();
        }
    	
        //生成数据源
        @Bean
        public DataSource barDataSource() {
            DataSourceProperties dataSourceProperties = barDataSourceProperties();
            log.info("bar datasource: {}", dataSourceProperties.getUrl());
            return dataSourceProperties.initializeDataSourceBuilder().build();
        }
    	
        //生成事务管理
        @Bean
        @Resource
        public PlatformTransactionManager barTxManager(DataSource barDataSource) {
            return new DataSourceTransactionManager(barDataSource);
        }
    }
    ```

### 3.HikariCP连接池

- 快的原因

  1. 字节码级别优化(很多方法通过JavaAssist生成)
  2. 大量的小改进
     - 用 FastStatementList代替ArrayList
     - 无锁集合ConcurrentBag
     - 代理类的优化

- 常用配置

  ```properties
  # 池中最大连接数，包括闲置和使用中的连接
  spring.datasource.hikari.maximumPoolSize=10
  # 池中维护的最小空闲连接数
  spring.datasource.hikari.minimumldle=10
  # 连接允许在池中闲置的最长时间
  spring.datasource.hikari.idleTimeout=600000
  # 等待来自池的连接的最大毫秒数
  spring.datasource.hikari.connectionTimeout=30000
  # 池中连接最长生命周期
  spring.datasource.hikari.maxLifetime=1800000
  ```

  |          配置项           |                             描述                             |          构造器默认值          | 默认配置validate之后的值 |                         validate重置                         |
  | :-----------------------: | :----------------------------------------------------------: | :----------------------------: | :----------------------: | :----------------------------------------------------------: |
  |        autoCommit         |                   自动提交从池中返回的连接                   |              true              |           true           |                              -                               |
  |     connectionTimeout     |                 等待来自池的连接的最大毫秒数                 |  SECONDS.toMillis(30) = 30000  |          30000           |               如果小于250毫秒，则被重置回30秒                |
  |        idleTimeout        |                 连接允许在池中闲置的最长时间                 | MINUTES.toMillis(10) = 600000  |          600000          | 如果idleTimeout+1秒>maxLifetime 且 maxLifetime>0，则会被重置为0（代表永远不会退出）；如果idleTimeout!=0且小于10秒，则会被重置为10秒 |
  |        maxLifetime        |                     池中连接最长生命周期                     | MINUTES.toMillis(30) = 1800000 |         1800000          |           如果不等于0且小于30秒则会被重置回30分钟            |
  |    connectionTestQuery    |   如果您的驱动程序支持JDBC4，我们强烈建议您不要设置此属性    |              null              |           null           |                              -                               |
  |        minimumIdle        |                   池中维护的最小空闲连接数                   |               -1               |            10            |    minIdle<0或者minIdle>maxPoolSize,则被重置为maxPoolSize    |
  |      maximumPoolSize      |            池中最大连接数，包括闲置和使用中的连接            |               -1               |            10            | 如果maxPoolSize小于1，则会被重置。当minIdle<=0被重置为DEFAULT_POOL_SIZE则为10;如果minIdle>0则重置为minIdle的值 |
  |      metricRegistry       | 该属性允许您指定一个 Codahale / Dropwizard MetricRegistry 的实例，供池使用以记录各种指标 |              null              |           null           |                              -                               |
  |    healthCheckRegistry    | 该属性允许您指定池使用的Codahale / Dropwizard HealthCheckRegistry的实例来报告当前健康信息 |              null              |           null           |                              -                               |
  |         poolName          | 连接池的用户定义名称，主要出现在日志记录和JMX管理控制台中以识别池和池配置 |              null              |       HikariPool-1       |                              -                               |
  | initializationFailTimeout |   如果池无法成功初始化连接，则此属性控制池是否将 fail fast   |               1                |            1             |                              -                               |
  |  isolateInternalQueries   |     是否在其自己的事务中隔离内部池查询，例如连接活动测试     |             false              |          false           |                              -                               |
  |    allowPoolSuspension    |               控制池是否可以通过JMX暂停和恢复                |             false              |          false           |                              -                               |
  |         readOnly          |             从池中获取的连接是否默认处于只读模式             |             false              |          false           |                              -                               |
  |      registerMbeans       |                是否注册JMX管理Bean（MBeans）                 |             false              |          false           |                              -                               |
  |          catalog          |         为支持 catalog 概念的数据库设置默认 catalog          |         driver default         |           null           |                              -                               |
  |     connectionInitSql     | 该属性设置一个SQL语句，在将每个新连接创建后，将其添加到池中之前执行该语句。 |              null              |           null           |                              -                               |
  |      driverClassName      | HikariCP将尝试通过仅基于jdbcUrl的DriverManager解析驱动程序，但对于一些较旧的驱动程序，还必须指定driverClassName |              null              |           null           |                              -                               |
  |   transactionIsolation    |             控制从池返回的连接的默认事务隔离级别             |              null              |           null           |                              -                               |
  |     validationTimeout     |                 连接将被测试活动的最大时间量                 |   SECONDS.toMillis(5) = 5000   |           5000           |               如果小于250毫秒，则会被重置回5秒               |
  |  leakDetectionThreshold   |    记录消息之前连接可能离开池的时间量，表示可能的连接泄漏    |               0                |            0             | 如果大于0且不是单元测试，则进一步判断：(leakDetectionThreshold < SECONDS.toMillis(2) or (leakDetectionThreshold > maxLifetime && maxLifetime > 0)，会被重置为0 . 即如果要生效则必须>0，而且不能小于2秒，而且当maxLifetime > 0时不能大于maxLifetime |
  |        dataSource         | 这个属性允许你直接设置数据源的实例被池包装，而不是让HikariCP通过反射来构造它 |              null              |           null           |                              -                               |
  |          schema           |           该属性为支持模式概念的数据库设置默认模式           |         driver default         |           null           |                              -                               |
  |       threadFactory       | 此属性允许您设置将用于创建池使用的所有线程的java.util.concurrent.ThreadFactory的实例。 |              null              |           null           |                              -                               |
  |     scheduledExecutor     | 此属性允许您设置将用于各种内部计划任务的java.util.concurrent.ScheduledExecutorService实例 |              null              |           null           |                              -                               |

- 连接：http://brettwooldridge.github.io/HikariCP/

### 4.Alibaba Druid 连接池

- 数据源的配置

  ```properties
  # Filter配置，全局使用默认值
  spring.datasource.druid.filters=stat,config,wall,log4j
  # 密码加密
  spring.datasource.password=<加密密码>
  spring.datasource.druid.filter.config.enabled=true
  spring.datasource.druid.connection-properties=config.decrypt=true;config.decrypt.key=<public-key>
  # SQL防注入
  spring.datasource.druid.filter.wall.enabled=true
  spring.datasource.druid.filter.wall.db-type=h2
  spring.datasource.druid.filter.wall.config.delete-allow=false
  spring.datasource.druid.filter.wall.config.drop-table-allow=false
  ```

- ```properties
  # 系统属性配置
  druid.stat.logSlowSql=true
  druid.stat.slowSqlMillis=3000
  # Spring Boot
  # 开启过滤
  spring.datasource.druid.filter.stat.enabled=true
  # 开启慢查询
  spring.datasource.druid.filter.stat.log-slow-sql=true
  # 超过多长时间的慢查询
  spring.datasource.druid.filter.stat.slow-sql-millis=3000
  ```

- Druid_Filter

  - 用于定制连接池操作的各种环节
  - 可以继承FilterEventAdapter以便方便地实现Filter
  - 修改META-INF/druid-filter.properties增加Filter配置

- Druid的一些注意事项

  - 没有特殊情况，不要在生产环境打开监控的Servlet
  - 没有连接泄漏可能的情况下，不要开启removeAbandoned
  - testXxx的使用需要注意
  - 务必配置合理的超时时间

- 连接：https://github.com/alibaba/druid/wiki

- code

  - pom.xml

    ```xml
          <dependencies>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-actuator</artifactId>
              </dependency>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-jdbc</artifactId>
                  <!--HikariCP从SpringBoot中排除-->
                  <exclusions>
                      <exclusion>
                          <artifactId>HikariCP</artifactId>
                          <groupId>com.zaxxer</groupId>
                      </exclusion>
                  </exclusions>
              </dependency>
              <dependency>
                  <groupId>com.h2database</groupId>
                  <artifactId>h2</artifactId>
                  <scope>runtime</scope>
              </dependency>
              <dependency>
                  <groupId>org.projectlombok</groupId>
                  <artifactId>lombok</artifactId>
                  <optional>true</optional>
              </dependency>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-test</artifactId>
                  <scope>test</scope>
              </dependency>
           <!--引入Alibaba-Druid-->
              <dependency>
                  <groupId>com.alibaba</groupId>
                  <artifactId>druid-spring-boot-starter</artifactId>
                  <version>1.1.10</version>
              </dependency>
          </dependencies>
    ```

  - application.properties

    ```properties
    # 彩色打印
    spring.output.ansi.enabled=always
    # 连接的URL
    spring.datasource.url=jdbc:h2:mem:foo
    # 连接的用户名
    spring.datasource.username=sa
    # 连接的加密后的密码
    # 生成的方式:java -cp druid-1.0.16.jar com.alibaba.druid.filter.config.ConfigTools you_password
    spring.datasource.password=n/z7PyA5cvcXvs8px8FVmBVpaRyNsvJb3X7YfS38DJrIg25EbZaZGvH4aHcnc97Om0islpCAPc3MqsGvsrxVJw==
    # 初始化连接池的大小
    spring.datasource.druid.initial-size=5
    # 最大连接池数量
    spring.datasource.druid.max-active=5
    # 最小连接池数量
    spring.datasource.druid.min-idle=5
    # 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有： 监控统计用的filter:stat日志用的filter:log4j防御sql注入的filter:wall
    spring.datasource.druid.filters=conn,config,stat,slf4j
    # 连接的配置
    spring.datasource.druid.connection-properties=config.decrypt=true;config.decrypt.key=${public-key}
    # 过滤打开
    spring.datasource.druid.filter.config.enabled=true
    # 测试打开
    spring.datasource.druid.test-on-borrow=true
    spring.datasource.druid.test-on-return=true
    spring.datasource.druid.test-while-idle=true
    # 公钥
    public-key=MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBALS8ng1XvgHrdOgm4pxrnUdt3sXtu/E8My9KzX8sXlz+mXRZQCop7NVQLne25pXHtZoDYuMh3bzoGj6v5HvvAQ8CAwEAAQ==
    
    ```

  - META-INF/druid-filter.properties
  
    ```properties
    # 定义过滤打印的日志
    druid.filters.conn=geektime.spring.data.druiddemo.ConnectionLogFilter
    ```
  
  - ConnectionLogFilter.java
  
    ```java
    package geektime.spring.data.druiddemo;
    
    import com.alibaba.druid.filter.FilterChain;
    import com.alibaba.druid.filter.FilterEventAdapter;
    import com.alibaba.druid.proxy.jdbc.ConnectionProxy;
    import lombok.extern.slf4j.Slf4j;
    
    import java.util.Properties;
    
    @Slf4j
    //自定义过滤类，要继承FilterEventAdapter
    public class ConnectionLogFilter extends FilterEventAdapter {
    	
       	//连接前打印的信息
        @Override
        public void connection_connectBefore(FilterChain chain, Properties info) {
            log.info("BEFORE CONNECTION!");
        }
    	
        //连接后打印的信息
        @Override
        public void connection_connectAfter(ConnectionProxy connection) {
            log.info("AFTER CONNECTION!");
        }
    }
    ```
  
  - DruidDemoApplication.java
  
    ```java
    package geektime.spring.data.druiddemo;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.jdbc.core.JdbcTemplate;
    
    import javax.sql.DataSource;
    
    @SpringBootApplication
    @Slf4j
    public class DruidDemoApplication implements CommandLineRunner {
    
        @Autowired
        private DataSource dataSource;
    
        @Autowired
        private JdbcTemplate jdbcTemplate;
    
        public static void main(String[] args) {
            SpringApplication.run(DruidDemoApplication.class, args);
        }
    
        //打印数据源的信息
        @Override
        public void run(String... args) throws Exception {
            log.info(dataSource.toString());
        }
    }
    
    ```

### 5.通过spring-jdbc访问数据库

- spring-jdbc.jar

  - core,JdbcTemplate等相关核心接口和类
  - datasource,数据源相关的辅助类
  - object，将基本的JDBC操作封装成对象
  - support，错误码等其他辅助工具

- spring-jdbc简单操作

  - JdbcTemplate
    - query(查询的方法)
    - queryForObject(查询的方法)
    - queryForList(查询的方法)
    - updata(更新的方法)
    - execute(执行SQL语句的方法)

- spring-jdbc中的SQL批处理

  - JdbcTemplate
    - batchUpdate
      - BatchPreparedStatemenetSetter
  - NamedParameterJdbcTemplate
    - batchUpdate
      - SqlParameterSourceUtils.createBatch

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-jdbc</artifactId>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - application.properties

    ```properties
    spring.output.ansi.enabled=always
    ```

  - Foo.java

    ```java
    package geektime.spring.data.simplejdbcdemo;
    
    import lombok.Builder;
    import lombok.Data;
    
    @Data
    @Builder
    public class Foo {
        private Long id;
        private String bar;
    }
    
    ```

  - FooDao.java

    ```java
    package geektime.spring.data.simplejdbcdemo;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.jdbc.core.RowMapper;
    import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
    import org.springframework.stereotype.Repository;
    
    import java.sql.ResultSet;
    import java.sql.SQLException;
    import java.util.Arrays;
    import java.util.HashMap;
    import java.util.List;
    
    @Slf4j
    @Repository
    public class FooDao {
    
        @Autowired
        private JdbcTemplate jdbcTemplate;
    
        @Autowired
        private SimpleJdbcInsert simpleJdbcInsert;
    
        public void insertData() {
            //生成集合b,c的流对象，将数据进行插入
            Arrays.asList("b", "c").forEach(bar -> {
                //update方法，既可以更新，也可以插入
                jdbcTemplate.update("INSERT INTO FOO (BAR) VALUES (?)", bar);
            });
            HashMap<String, String> row = new HashMap<>();
            row.put("BAR", "d");
            //执行插入语句并返回行对应的id
            Number id = simpleJdbcInsert.executeAndReturnKey(row);
            log.info("ID of d: {}", id.longValue());
        }
    
        public void listData() {
            //queryForObject返回的是一个对象
            //第一个参数是执行的语句
            //第二个参数是返回的类型
            log.info("Count: {}", jdbcTemplate.queryForObject("SELECT COUNT(*) FROM FOO", Long.class));
    
            //返回一个list对象，并放着表中所有的Bar的值
            //第一个参数是执行的语句
            //第二个参数是返回的集合中的单个对象订单类型
            List<String> list = jdbcTemplate.queryForList("SELECT BAR FROM FOO", String.class);
            list.forEach(s -> log.info("Bar: {}", s));
    
           /* List<Foo> fooList = jdbcTemplate.query("SELECT * FROM FOO", new RowMapper<Foo>() {
                @Override
                public Foo mapRow(ResultSet resultSet, int i) throws SQLException {
                    return Foo.builder()
                            .id(resultSet.getLong(1))
                            .bar(resultSet.getString(2))
                            .build();
                }
            });*/
            List<Foo> fooList = jdbcTemplate.query("SELECT * FROM FOO",
                    (resultSet, i) -> Foo.builder()
                            .id(resultSet.getLong(1))
                            .bar(resultSet.getString(2))
                            .build());
            fooList.forEach(f -> log.info("Foo : {}", f));
        }
    }
    
    ```

  - BatchFooDao.java

    ```java
    package geektime.spring.data.simplejdbcdemo;
    
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jdbc.core.BatchPreparedStatementSetter;
    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
    import org.springframework.jdbc.core.namedparam.SqlParameterSourceUtils;
    import org.springframework.stereotype.Repository;
    
    import java.sql.PreparedStatement;
    import java.sql.SQLException;
    import java.util.ArrayList;
    import java.util.List;
    
    @Repository
    public class BatchFooDao {
    
        @Autowired
        private JdbcTemplate jdbcTemplate;
    
        @Autowired
        private NamedParameterJdbcTemplate namedParameterJdbcTemplate;
    
        public void batchInsert() {
            //批处理的方法
            //第一个参数是执行的批处理的语句
            //第二个参数是为语句赋值，并决定执行的次数
            jdbcTemplate.batchUpdate("INSERT INTO FOO (BAR) values (?)", new BatchPreparedStatementSetter() {
                //每次为SQL语句执行设置值
                @Override
                public void setValues(PreparedStatement preparedStatement, int i) throws SQLException {
                    preparedStatement.setString(1, "b-" + i);
                }
    
                //执行的次数
                @Override
                public int getBatchSize() {
                    return 2;
                }
            });
    
            List<Foo> list = new ArrayList<>();
            list.add(Foo.builder().id(100L).bar("b-100").build());
            list.add(Foo.builder().id(101L).bar("b-101").build());
            //执行批处理的方法
            //第一个参数是执行的语句
            //第二个参数的根据list集合生成批处理的SQL语句
            namedParameterJdbcTemplate
                    .batchUpdate("INSERT INTO FOO (ID, BAR) VALUES (:id, :bar)",
                            SqlParameterSourceUtils.createBatch(list));
        }
    }
    
    ```

  - SimpleJdbcDemoApplication.java

    ```java
    package geektime.spring.data.simplejdbcdemo;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.Bean;
    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
    import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
    
    import javax.sql.DataSource;
    
    @SpringBootApplication
    @Slf4j
    public class SimpleJdbcDemoApplication implements CommandLineRunner {
        @Autowired
        private FooDao fooDao;
        @Autowired
        private BatchFooDao batchFooDao;
    
        public static void main(String[] args) {
            SpringApplication.run(SimpleJdbcDemoApplication.class, args);
        }
    
        //别的地方用到了这个，所以要创建
        @Bean
        @Autowired
        public SimpleJdbcInsert simpleJdbcInsert(JdbcTemplate jdbcTemplate) {
            //进行还数据库中的表和主键进行绑定
            return new SimpleJdbcInsert(jdbcTemplate)
                    .withTableName("FOO").usingGeneratedKeyColumns("ID");
        }
    
        @Bean
        @Autowired
        public NamedParameterJdbcTemplate namedParameterJdbcTemplate(DataSource dataSource) {
            return new NamedParameterJdbcTemplate(dataSource);
        }
    
        @Override
        public void run(String... args) throws Exception {
            fooDao.insertData();
            batchFooDao.batchInsert();
            fooDao.listData();
        }
    }
    
    ```

### 6.spring事务的抽象

- 事务抽象的核心的接口

  - PlatformTransactionManager
    - DataSourceTransactionManager
    - HibernateTransactionManger
    - JtaTransactionManager
  - TransactionDefinition
    - Propagation
    - Isolation
    - Timeout
    - Read-only status

- 事务的传播特性：

  |          传播性           |  值  |                 描述                 |
  | :-----------------------: | :--: | :----------------------------------: |
  |    POPAGATION_REQUIRED    |  0   |  当前有事务就用当前的，没有就用新的  |
  |    PROPAGTION_SUPPORTS    |  1   |       事务可有可无，不是必须的       |
  |   PROPAGTION_MANDATIORY   |  2   |   当前一定要有事务，不然就抛出异常   |
  |  PROPAGTION_REQUIRES_NEW  |  3   |    无论是否有事务，都起个新的事务    |
  | PROPAGATION_NOT_SUPPORTED |  4   |     不支持事务，按非事务方式运行     |
  |     PROPAGATION_NEVER     |  5   |   不支持事务，如果有事务则抛出异常   |
  |     PROPAGTION_NESTED     |  6   | 当前有事务就在当前事务里再起一个事务 |

- 事务的隔离特性：

  |           隔离性           |  值  | 脏读 | 不可重复读 | 幻读 |
  | :------------------------: | :--: | :--: | :--------: | :--: |
  | ISOLATION_READ_UNCOMMITTED |  1   |  √   |     √      |  √   |
  |  ISOLATION_READ_COMMITTED  |  2   |  ×   |     √      |  √   |
  | ISOLATION_REPEATABLE_READ  |  3   |  ×   |     ×      |  √   |
  |   ISOLATION_SERIALIZABLE   |  4   |  ×   |     ×      |  ×   |

- 编程式事务

  - TransactionTemplate
    - TransactionCallback
    - TransactionCallbackWithoutResult
  - PlatformTransactionManager
    - 可以传入TransactionDefinition进行定义

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-jdbc</artifactId>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - ProgrammaticTransactionDemoApplication.java

    ```java
    package geektime.spring.data.programmatictransactiondemo;
    
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.transaction.TransactionStatus;
    import org.springframework.transaction.support.TransactionCallbackWithoutResult;
    import org.springframework.transaction.support.TransactionTemplate;
    
    @SpringBootApplication
    @Slf4j
    public class ProgrammaticTransactionDemoApplication implements CommandLineRunner {
    
        @Autowired
        private TransactionTemplate transactionTemplate;
    
        @Autowired
        private JdbcTemplate jdbcTemplate;
    
        public static void main(String[] args) {
            SpringApplication.run(ProgrammaticTransactionDemoApplication.class, args);
        }
    
        @Override
        public void run(String... args) throws Exception {
            log.info("COUNT BEFORE TRANSACTION ： {}", getCount());
            //执行的SQL语句加上的事务
            //TransactionCallbackWithoutResult事务回调类，并且没有返回值
            transactionTemplate.execute(new TransactionCallbackWithoutResult() {
                @Override
                protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                    jdbcTemplate.execute("INSERT INTO FOO (ID, BAR) VALUES (1, 'aaa')");
                    log.info("COUNT IN TRANSACTION: {}", getCount());
                    //设置事务的状态，一般正常情况下根据执行的SQL的情况，来决定事务的状态
                    transactionStatus.setRollbackOnly();
                }
            });
            log.info("COUNT AFTER TRANSACTION: {}", getCount());
        }
    
        private long getCount() {
            return (long) jdbcTemplate.queryForList("SELECT COUNT(*) AS CNT FROM FOO")
                    .get(0).get("CNT");
        }
    }
    
    ```

  - spring声明式事务

    - spring的声明式事务本质上是通过AOP来增强了类的功能
    - spring的AOP本质就是为类做了一个代理
      - 看似在调用自己写的类，实际用的是增强后的代理类

  - spring声明式事务的注解

    - 开启事务注解的方式
      - @EnableTransactionManagement
      - <tx:annotation-driven/>
    - 一些配置
      - proxyTargetClass
      - mode
      - order
    - @Transactional
      - transactionManager
      - propagation
      - isolation
      - timeout
      - readOnly
      - 怎么判断回滚

  - code

    - pom.xml

      ```xml
          <dependencies>
      		<!--声明式事务要导入的jar包，aop-->
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-aop</artifactId>
              </dependency>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-jdbc</artifactId>
              </dependency>
              <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-aspects</artifactId>
              </dependency>
              <dependency>
                  <groupId>com.h2database</groupId>
                  <artifactId>h2</artifactId>
                  <scope>runtime</scope>
              </dependency>
              <dependency>
                  <groupId>org.projectlombok</groupId>
                  <artifactId>lombok</artifactId>
                  <optional>true</optional>
              </dependency>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-test</artifactId>
                  <scope>test</scope>
              </dependency>
      
          </dependencies>
      
      ```

    - FooService.java

      ```java
      package geektime.spring.data.declarativetransactiondemo;
      
      public interface FooService {
          void insertRecord();
      
          void insertThenRollback() throws RollbackException;
      
          void invokeInsertThenRollback() throws RollbackException;
      }
      
      ```

    - FooServiceImpl.java

      ```java
      package geektime.spring.data.declarativetransactiondemo;
      
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.jdbc.core.JdbcTemplate;
      import org.springframework.stereotype.Component;
      import org.springframework.transaction.annotation.Transactional;
      
      @Component
      public class FooServiceImpl implements FooService{
      
          @Autowired
          private JdbcTemplate jdbcTemplate;
      
          @Autowired
          private FooService fooService;
      
          @Override
          @Transactional
          public void insertRecord() {
              jdbcTemplate.execute("INSERT INTO FOO (BAR) VALUES ('AAA')");
          }
      
          @Override
          //当出现了RollbackException异常，事务回滚
          @Transactional(rollbackFor = RollbackException.class)
          public void insertThenRollback() throws RollbackException {
              jdbcTemplate.execute("INSERT INTO FOO (BAR) VALUES ('BBB')");
              throw new RollbackException();
          }
      
          @Override
          public void invokeInsertThenRollback() throws RollbackException {
              //必须创建这个类然后通过这个类调用这个方法，事务才能生效
              //因为调用的是增强后的代理类中的方法，事务才能生效
              fooService.insertThenRollback();
          }
      }
      
      ```

    - RollbackException.java

      ```java
      package geektime.spring.data.declarativetransactiondemo;
      
      public class RollbackException extends Exception{
      }
      
      ```

    - DeclarativeTransactionDemoApplication.java

      ```java
      package geektime.spring.data.declarativetransactiondemo;
      
      import lombok.extern.slf4j.Slf4j;
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.boot.CommandLineRunner;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.annotation.AdviceMode;
      import org.springframework.jdbc.core.JdbcTemplate;
      import org.springframework.transaction.annotation.EnableTransactionManagement;
      
      @SpringBootApplication
      //开启事务管理
      @EnableTransactionManagement(mode = AdviceMode.PROXY)
      @Slf4j
      public class DeclarativeTransactionDemoApplication implements CommandLineRunner {
      
          @Autowired
          private FooService fooService;
      
          @Autowired
          private JdbcTemplate jdbcTemplate;
      
          public static void main(String[] args) {
              SpringApplication.run(DeclarativeTransactionDemoApplication.class, args);
          }
      
          @Override
          public void run(String... args) throws Exception {
              fooService.insertRecord();
              log.info("AAA {}", jdbcTemplate.queryForObject("SELECT COUNT(*) FROM FOO WHERE BAR='AAA'", Long.class));
      
              try {
                  fooService.insertThenRollback();
              } catch (Exception e) {
                  log.info("BBB {}",
                          jdbcTemplate
                                  .queryForObject("SELECT COUNT(*) FROM FOO WHERE BAR='BBB'", Long.class));
              }
      
              try {
                  fooService.invokeInsertThenRollback();
              } catch (Exception e) {
                  log.info("BBB {}",
                          jdbcTemplate
                                  .queryForObject("SELECT COUNT(*) FROM FOO WHERE BAR='BBB'", Long.class));
              }
          }
      }
      
      ```

### 7.spring-jdbc异常抽象

- 如何处理

  - spring会将数据操作的异常转换为DataAccessException
  - 无论使用何种数据访问方式，都能使用一样的异常

- 如何认识错误码

  - 通过SQLErrorCodeSQLExceptionTranslator解析错误码
  - ErrorCode定义
    - org/springframework/jdbc/support/sql-error-codes.xml
    - Classpath 下的 sql-error-codes.xml

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-jdbc</artifactId>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - sql-error-codes.xml

    ```xml-dtd
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN" "http://www.springframework.org/dtd/spring-beans-2.0.dtd">
    <beans>
        <bean id="H2" class="org.springframework.jdbc.support.SQLErrorCodes">
            <property name="badSqlGrammarCodes">
                <value>42000,42001,42101,42102,42111,42112,42121,42122,42132</value>
            </property>
            <property name="duplicateKeyCodes">
                <value>23001,23505</value>
            </property>
            <property name="dataIntegrityViolationCodes">
                <value>22001,22003,22012,22018,22025,23000,23002,23003,23502,23503,23506,23507,23513</value>
            </property>
            <property name="dataAccessResourceFailureCodes">
                <value>90046,90100,90117,90121,90126</value>
            </property>
            <property name="cannotAcquireLockCodes">
                <value>50200</value>
            </property>
    		<!--自己定义的错误码 -->
            <property name="customTranslations">
                <bean class="org.springframework.jdbc.support.CustomSQLErrorCodesTranslation">
                    <property name="errorCodes" value="23001,23505"/>
    			   <!--捕获的异常-->
                    <property name="exceptionClass"
                         value="geektime.spring.data.errorcodedemo.CustomDuplicatedKeyException"/>
                </bean>
            </property>
    
        </bean>
    
    </beans>
    
    ```

  - CustomDuplicatedKeyException.java

    ```java
    package geektime.spring.data.errorcodedemo;
    
    import org.springframework.dao.DuplicateKeyException;
    
    //重写了主键重复的异常
    public class CustomDuplicatedKeyException extends DuplicateKeyException {
        public CustomDuplicatedKeyException(String msg) {
            super(msg);
        }
    
        public CustomDuplicatedKeyException(String msg, Throwable cause) {
            super(msg, cause);
        }
    }
    
    ```

  - ErrorcodeDemoApplicationTests.java

    ```java
    package geektime.spring.data.errorcodedemo;
    
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.test.context.junit4.SpringRunner;
    
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class ErrorcodeDemoApplicationTests {
    
        @Autowired
        private JdbcTemplate jdbcTemplate;
    
        //测试捕获到的异常
        @Test(expected = CustomDuplicatedKeyException.class)
        public void testThrowingCustomException() {
            jdbcTemplate.execute("INSERT INTO FOO (ID, BAR) VALUES (1, 'a')");
            jdbcTemplate.execute("INSERT INTO FOO (ID, BAR) VALUES (1, 'b')");
        }
    
    }
    ```

### 8.拓展小知识

- Actuator提供的一些好用的Endpoint

  |        URL        |         作用         |
  | :---------------: | :------------------: |
  | /actuator/health  |       健康检查       |
  |  /actuator/beans  | 查看容器中的所有Bean |
  | /actuator/mapping |   查看Web的URL映射   |
  |   /actuator/env   |     查看环境信息     |

- 如何解禁Endpoint

  - 默认
    - /actuator/health 和 /actuator/info 可web访问
  - 解禁所有Endpoint
    - application.properties 或application.yml
      - management.endpoints.web.exposure.include=*
  - **生产环境需谨慎**

- 注解相关
  
  - java Config相关注解
    - @Configuration 配置类
    - @ImportResource 注入配置类以外的配置
    - @ComponentScan 注解扫描
    - @Bean 添加到容器
    - @ConfigurationProperties 配置绑定
  - 定义相关注解
    - @Component 添加到容器
    - @Repository 数据库访问层的bean
    - @Service 服务层的bean
    - @Controller web层的bean
    - @RestController web层的bean
    - @RequestMapping 定义URL
  - 注入相关注解
    - @Autowired 按照类型查找自动注入
    - @Qualifier 指定类名字注入
    - @Resource 根据名字注入
    - @Value 在bean中注入常量，spel表达式
  
- Project-Lombok常用的注解

  - @Getter / @Setter
  - @ToString
  - @NoArgsConstructor / @RequireddArgsConstructor / @AllArgsConstructor
  -  @Data
  - @Builder
  - @Slf4j / @CommonsLog / @Log4j2
  - 链接：https://www.jianshu.com/p/365ea41b3573

- REQUIRES_NEW与NESTED

  - REQUIRES_NEW，始终启动一个新事务
    - 两个事务没有关联
  - NESTED，在原事务内启动一个内嵌事务
    - 两个事务有关联
    - 外部事务回滚，内嵌事务也会回滚

## 第二章：O/R Mapping实践

### 1.认识Spring Data JPA

- 对象与关系的范式不匹配

  |          |       Object       |    RDBMS    |
  | :------: | :----------------: | :---------: |
  |   粒度   |         类         |     表      |
  |   继承   |         有         |    没有     |
  |  唯一性  | a == b a.equals(b) |    主键     |
  |   关联   |        引用        |    外键     |
  | 数据访问 |      逐级访问      | SQL数量要少 |

### 2.定义JPA的实体对象

- 常用的JPA注解
  - 实体
    - @Entity、@MappedSuperclass
    - @Table(name)
  - 主键
    - @Id
      - @GeneratedValue(strategy,generator)
      - @SequenceGenerator(name,sequenceName)
  - 映射
    - @Column(name, nullable, length, insertable, updateable)
    - @JoinTable(name)、@JoinColumn(name)
  - 关系
    - @OneToOne、@OneToMany、@ManyToOne、@ManyToMany
    - @OrderBy

### 3.Spring Data JPA操作数据库

- Repository
  - @EnablejpaRepositories
  - Repository<T,ID> 接口
    - CrudRepository<T,ID>
    - PagingAndSortingRepository<T,ID>
    - JpaRepository<T,ID>
- 定义查询
  - 根据方法名定义查询
    - find...By...  / read...By...  /  query...By...  / get... By ...
    - count...By...
    - ...OrderBy...[Asc  / Desc]
    - And  /  Or  /  IgnoreCase
    - Top  /  First  /  Distinct 
- 分页查询
  - PagingAndSortRepository<T, ID>
  - Pageable  /  Sort
  - Slice<T>  /  Page<T>

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-jpa</artifactId>
            </dependency>
            <!--金钱处理的类-->
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>1.0.1</version>
            </dependency>
            <dependency>
                <groupId>org.jadira.usertype</groupId>
                <artifactId>usertype.core</artifactId>
                <version>6.0.1.GA</version>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    
    ```

  - application.properties

    ```properties
    # 创建和删除
    spring.jpa.hibernate.ddl-auto=create-drop
    # 显示SQL语句
    spring.jpa.properties.hibernate.show_sql=true
    # 格式胡SQL语句
    spring.jpa.properties.hibernate.format_sql=true
    ```

  - BaseEntity.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.hibernate.annotations.CreationTimestamp;
    import org.hibernate.annotations.UpdateTimestamp;
    
    import javax.persistence.Column;
    import javax.persistence.GeneratedValue;
    import javax.persistence.Id;
    import javax.persistence.MappedSuperclass;
    import java.io.Serializable;
    import java.util.Date;
    
    //其他的类要继承这个类，必须加上这个注解
    @MappedSuperclass
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    // 必须实现Serializable接口，一个序列化的接口
    public class BaseEntity implements Serializable {
    
        @Id
        @GeneratedValue
        private Long id;
    
        //该列不可以更新
        @Column(updatable = false)
        //创建的时间戳
        @CreationTimestamp
        private Date createTime;
    
        //更新的时间戳
        @UpdateTimestamp
        private Date updateTime;
    }
    
    ```

  - Coffee.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo.model;
    
    import lombok.*;
    import org.hibernate.annotations.Type;
    import org.joda.money.Money;
    
    import javax.persistence.Entity;
    import javax.persistence.Table;
    import java.io.Serializable;
    
    //实体类
    @Entity
    //表名
    @Table(name = "T_MENU")
    @Builder
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    public class Coffee extends BaseEntity implements Serializable {
    
        private String name;
    
        @Type(type = "org.jadira.usertype.moneyandcurrency.joda.PersistentMoneyAmount",
                parameters = {@org.hibernate.annotations.Parameter(name = "currencyCode", value = "CNY")})
        private Money price;
    }
    
    ```

  - CoffeeOrder.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo.model;
    
    import lombok.*;
    
    import javax.persistence.*;
    import java.io.Serializable;
    import java.util.List;
    
    @Entity
    @Table(name = "T_ORDER")
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public class CoffeeOrder extends BaseEntity implements Serializable {
    
        private String customer;
    
        //映射的关系
        @ManyToMany
        //中间的表名
        @JoinTable(name = "T_ORDER_COFFEE")
        //排序方式
        @OrderBy("id")
        private List<Coffee> items;
    
        //枚举
        @Enumerated
        //该列不能为空
        @Column(nullable = false)
        private OrderState state;
    }
    
    ```

  - OrderState.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo.model;
    
    public enum OrderState {
        INIT, PAID, BREWING, BREWED, TAKEN, CANCELLED
    }
    ```

  - BaseRepository.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo.repository;
    
    import org.springframework.data.repository.NoRepositoryBean;
    import org.springframework.data.repository.PagingAndSortingRepository;
    
    import java.util.List;
    //父类不需要加入到容器中
    @NoRepositoryBean
    public interface BaseRepository<T, Long> extends PagingAndSortingRepository<T, Long> {
    
        List<T> findTop3ByOrderByUpdateTimeDescIdAsc();
    }
    ```

  - CoffeeRepository.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo.repository;
    
    import geektime.spring.springbucks.jpacomplexdemo.model.Coffee;
    
    public interface CoffeeRepository extends BaseRepository<Coffee, Long> {
    }
    ```

  - CoffeeOrderRepository.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo.repository;
    
    import geektime.spring.springbucks.jpacomplexdemo.model.CoffeeOrder;
    
    import java.util.List;
    
    public interface CoffeeOrderRepository extends BaseRepository<CoffeeOrder, Long> {
    
        //按照customer查找，并按照id升序排列
        List<CoffeeOrder> findByCustomerOrderById(String customer);
    
        // 根剧name查询中间表
        List<CoffeeOrder> findByItems_Name(String name);
    }
    ```

  - JpaComplexDemoApplication.java

    ```java
    package geektime.spring.springbucks.jpacomplexdemo;
    
    import geektime.spring.springbucks.jpacomplexdemo.model.Coffee;
    import geektime.spring.springbucks.jpacomplexdemo.model.CoffeeOrder;
    import geektime.spring.springbucks.jpacomplexdemo.model.OrderState;
    import geektime.spring.springbucks.jpacomplexdemo.repository.CoffeeOrderRepository;
    import geektime.spring.springbucks.jpacomplexdemo.repository.CoffeeRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.data.domain.Sort;
    import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
    import org.springframework.transaction.annotation.EnableTransactionManagement;
    
    import javax.transaction.Transactional;
    import java.util.Arrays;
    import java.util.Collections;
    import java.util.List;
    import java.util.stream.Collectors;
    
    @SpringBootApplication
    //开启jpa的支持
    @EnableJpaRepositories
    //开启事务管理
    @EnableTransactionManagement
    @Slf4j
    public class JpaComplexDemoApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeRepository coffeeRepository;
    
        @Autowired
        private CoffeeOrderRepository orderRepository;
    
        public static void main(String[] args) {
            SpringApplication.run(JpaComplexDemoApplication.class, args);
        }
    
        @Override
        @Transactional
        public void run(ApplicationArguments args) throws Exception {
            initOrders();
            findOrders();
        }
    
        private void initOrders() {
            //构建咖啡信息
            Coffee latte = Coffee.builder().name("latte")
                    .price(Money.of(CurrencyUnit.of("CNY"), 30.0))
                    .build();
            //保存一条咖啡信息
            coffeeRepository.save(latte);
            log.info("Coffee: {}", latte);
    
            Coffee espresso = Coffee.builder().name("espresso")
                    .price(Money.of(CurrencyUnit.of("CNY"), 20.0))
                    .build();
            coffeeRepository.save(espresso);
            log.info("Coffee: {}", espresso);
    	    //构造咖啡订单信息
            CoffeeOrder order = CoffeeOrder.builder()
                    .customer("Li Lei")
                    .items(Collections.singletonList(espresso))
                    .state(OrderState.INIT)
                    .build();
            //保存咖啡订单信息
            orderRepository.save(order);
            log.info("Order: {}", order);
    
            order = CoffeeOrder.builder()
                    .customer("Li Lei")
                    .items(Arrays.asList(espresso, latte))
                    .state(OrderState.INIT)
                    .build();
            orderRepository.save(order);
            log.info("Order: {}", order);
        }
    
        private void findOrders() {
            //查询所有的咖啡信息，按照id降序排列，然后打印
            coffeeRepository
                    .findAll(Sort.by(Sort.Direction.DESC, "id"))
                    .forEach(c -> log.info("Loading {}", c));
    		
            //查询开始的三条记录，并按照更新时间降序，按照id升序排列
            List<CoffeeOrder> list = orderRepository.findTop3ByOrderByUpdateTimeDescIdAsc();
            log.info("findTop3ByOrderByUpdateTimeDescIdAsc: {}", getJoinedOrderId(list));
    	
            //按照customer查询，并且按照id升序排列
            list = orderRepository.findByCustomerOrderById("Li Lei");
            log.info("findByCustomerOrderById: {}", getJoinedOrderId(list));
    
            list.forEach(o -> {
                log.info("Order {}", o.getId());
                o.getItems().forEach(i -> log.info(" Item {}", i));
            });
    
            //按照name查询中间表
            list = orderRepository.findByItems_Name("latte");
            log.info("findByItems_Name: {}", getJoinedOrderId(list));
        }
    
        private String getJoinedOrderId(List<CoffeeOrder> list) {
            //list转成流对象，然后按逗号拼接
            return list.stream().map(o -> o.getId().toString())
                    .collect(Collectors.joining(","));
        }
    }
    ```

### 4.Repository如何将接口变成Bean的

- RepositoryBean的创建
  - JpaRepositoriesRegistrar
    - 激活了@EnableJpaRepositories
    - 返回了JpaRepositoryConfigExtension
  - RepositoryBeanDefinitionRegistrarSupport.registerBeanDefinitions
    - 注册Repository Bean(类型是JpaRepositoryFactoryBean)
  - RepositoryConfigurationExtensionSupport.getRepositoryConfigurations
    - 取得Repository配置
  - JpaRepositoryFactory.getTargetRepos
    - 创建Repository
- Repository接口的方法是如何被解释的
  - RepositoryFactorySupport.getRepository添加Advice
    - DefaultMethodInvokingMethodInterceptor
    - QueryExecutorMethodInterceptor
  - AbstractJpaQuery.execute执行具体的查询
  - 语法解析在Part中

### 5.通过MyBatis操作数据库

- mybatis简单的配置

  ```properties
  mybatis.mapper-locations=classpath*:mapper/**/*.xml
  mybatis.type-aliases-package=类型别名的包名
  mybatis.type-handlers-package=TypeHander扫描包名
  # 下划线与驼峰对应起来
  mybatis.configuration.map-underscore-to-camel-case=true
  ```

- Mapper的定义与扫描

  - @MapperScan 配置扫描位置
  - @Mapper 定义接口
  - 映射的定义---XML与注解

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
            </dependency>
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>LATEST</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.1.0</version>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - application.properties

    ```properties
    mybatis.type-handlers-package=geektime.spring.data.mybatisdemo.handler
    mybatis.configuration.map-underscore-to-camel-case=true
    ```

  - Coffee.java

    ```java
    package geektime.spring.data.mybatisdemo.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Builder;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.joda.money.Money;
    
    import java.util.Date;
    
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    @Builder
    public class Coffee {
        private Long id;
        private String name;
        private Money price;
        private Date createTime;
        private Date updateTime;
    }
    ```

  - CoffeeMapper.java

    ```java
    package geektime.spring.data.mybatisdemo.mapper;
    
    import geektime.spring.data.mybatisdemo.model.Coffee;
    import org.apache.ibatis.annotations.*;
    //让注解扫描的到
    @Mapper
    public interface CoffeeMapper {
    
        @Insert("insert into t_coffee (name, price, create_time, update_time)"
                + "values (#{name}, #{price}, now(), now())")
        //版本的问题，必须要加上keyColumn = "id", keyProperty = "id"
        @Options(useGeneratedKeys = true, keyColumn = "id", keyProperty = "id")
        int save(Coffee coffee);
    
        @Select("select * from t_coffee where id = #{id}")
        @Results({
                @Result(id = true, column = "id", property = "id"),
                @Result(column = "create_time", property = "createTime"),
                // map-underscore-to-camel-case = true 可以实现一样的效果
                // @Result(column = "update_time", property = "updateTime"),
        })
        Coffee findById(@Param("id") Long id);
    }
    
    ```

  - MonyTypeHandler.java

    ```java
    package geektime.spring.data.mybatisdemo.handler;
    
    import org.apache.ibatis.type.BaseTypeHandler;
    import org.apache.ibatis.type.JdbcType;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    
    import java.sql.CallableStatement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.SQLException;
    
    /**
     * 在 Money 与 Long 之间转换的 TypeHandler，处理 CNY 人民币
     */
    public class MoneyTypeHandler extends BaseTypeHandler<Money> {
        @Override
        public void setNonNullParameter(PreparedStatement ps, int i, Money parameter, JdbcType jdbcType) throws SQLException {
            ps.setLong(i, parameter.getAmountMajorLong());
        }
    
        @Override
        public Money getNullableResult(ResultSet rs, String columnName) throws SQLException {
            return parseMoney(rs.getLong(columnName));
        }
    
        @Override
        public Money getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
            return parseMoney(rs.getLong(columnIndex));
        }
    
        @Override
        public Money getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
            return parseMoney(cs.getLong(columnIndex));
        }
    
        private Money parseMoney(Long value) {
            return Money.of(CurrencyUnit.of("CNY"), value / 100.0);
        }
    }
    ```

  - MybatisDemoApplication.java

    ```java
    package geektime.spring.data.mybatisdemo;
    
    import geektime.spring.data.mybatisdemo.mapper.CoffeeMapper;
    import geektime.spring.data.mybatisdemo.model.Coffee;
    import lombok.extern.slf4j.Slf4j;
    import org.apache.ibatis.annotations.Many;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    import org.mybatis.spring.annotation.MapperScan;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    @Slf4j
    //Mapper扫描的路径
    @MapperScan("geektime.spring.data.mybatisdemo.mapper")
    public class MybatisDemoApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeMapper coffeeMapper;
    
        public static void main(String[] args) {
            SpringApplication.run(MybatisDemoApplication.class, args);
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            //构建一个咖啡对象
            Coffee c = Coffee.builder().name("espreso")
                    .price(Money.of(CurrencyUnit.of("CNY"), 20.0)).build();
    		//进行保存
            int count = coffeeMapper.save(c);
            log.info("Save {} Coffee: {}", count, c);
    
            c = Coffee.builder().name("latte")
                    .price(Money.of(CurrencyUnit.of("CNY"), 25.0)).build();
    
            count = coffeeMapper.save(c);
            log.info("Save {} Coffee: {} ", count, c);
    		
            //按id查询一个对象并返回
            c = coffeeMapper.findById(c.getId());
            log.info("Find Coffee: {}", c);
    
        }
    }
    
    ```

### 6.MyBatis Generator

- 配置Mybatis-Generator

  - generatorConfiguration
  - context
    - jdbcConnection 配置连接信息
    - javaModelGenerator  javaModel生成的具体配置
    - sqlMapGenerator  SQLMap生成的配置
    - javaClientGenerator(ANNOTATEDMAPPER  / XMLMAAPPER  /  MIXEDMAPPER)
    - table 相关的表的配置，给那些表生成映射

- 生成时可以使用的插件

  - 内置插件都在org.mybatis.generator.plugins包中
    - FluentBuilderMethodsPlugin
    - ToStringPlugin
    - SerializablePlugin
    - RowBoundsPlugin
    - ......

- 使用生成的对象

  - 简单操作，直接使用生成的xxxMapper的方法
  - 复杂查询，使用生成的xxxExample对象

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.1.0</version>
            </dependency>
    		<!--导入相应的jar包-->
            <dependency>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-core</artifactId>
                <version>1.3.7</version>
            </dependency>
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>LATEST</version>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    
    ```

  - application.properties

    ```properties
    mybatis.mapper-locations=classpath*:/mapper/**/*.xml
    mybatis.type-aliases-package=geektime.spring.data.mybatisgeneratordemo.model
    mybatis.type-handlers-package=geektime.spring.data.mybatisgeneratordemo.handler
    mybatis.configuration.map-underscore-to-camel-case=true
    ```

  - generatorConfig.xml

    ```xml-dtd
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE generatorConfiguration
            PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
            "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
    
    <generatorConfiguration>
        <context id="H2Tables" targetRuntime="Mybatis3">
    		<!--顺序要求的-->
            <plugin type="org.mybatis.generator.plugins.FluentBuilderMethodsPlugin"/>
            <plugin type="org.mybatis.generator.plugins.ToStringPlugin"/>
            <plugin type="org.mybatis.generator.plugins.SerializablePlugin"/>
            <plugin type="org.mybatis.generator.plugins.RowBoundsPlugin"/>
    		<!--数据库连接的配置-->
            <jdbcConnection driverClass="org.h2.Driver"
                            connectionURL="jdbc:h2:mem:testdb"
                            userId="sa"
                            password="">
            </jdbcConnection>
    		<!--生成的model的配置-->
            <javaModelGenerator targetPackage="geektime.spring.data.mybatisgeneratordemo.model"
                                targetProject="./src/main/java">
                <property name="enableSubPackages" value="true"/>
                <property name="trimStrings" value="true"/>
            </javaModelGenerator>
    		<!--生成的SQLmapper的配置-->
            <sqlMapGenerator targetPackage="geektime.spring.data.mybatisgeneratordemo.mapper"
                             targetProject="./src/main/resources/mapper">
                <property name="enableSubPackages" value="true" />
            </sqlMapGenerator>
    		<!--使用混合配置，注解加上xml-->
            <javaClientGenerator type="MIXEDMAPPER"
                                 targetPackage="geektime.spring.data.mybatisgeneratordemo.mapper"
                                 targetProject="./src/main/java">
                <property name="enableSubPackages" value="true"/>
            </javaClientGenerator>
     		<!--映射的表格-->
            <table tableName="t_coffee" domainObjectName="Coffee">
                <generatedKey column="id" sqlStatement="CALL IDENTITY()" identity="true"/>
                <columnOverride column="price" javaType="org.joda.money.Money" jdbcType="BIGINT"
                                typeHandler="geektime.spring.data.mybatisgeneratordemo.handler.MoneyTypeHandler"/>
            </table>
    
        </context>
    </generatorConfiguration>
    ```

  - MoneyTypeHandler.java

    ```java
    package geektime.spring.data.mybatisgeneratordemo.handler;
    
    import org.apache.ibatis.type.BaseTypeHandler;
    import org.apache.ibatis.type.JdbcType;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    
    import java.sql.CallableStatement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.SQLException;
    
    /**
     * 在 Money 与 Long 之间转换的 TypeHandler，处理 CNY 人民币
     */
    public class MoneyTypeHandler extends BaseTypeHandler<Money> {
        @Override
        public void setNonNullParameter(PreparedStatement ps, int i, Money parameter, JdbcType jdbcType) throws SQLException {
            ps.setLong(i, parameter.getAmountMinorLong());
        }
    
        @Override
        public Money getNullableResult(ResultSet rs, String columnName) throws SQLException {
            return parseMoney(rs.getLong(columnName));
        }
    
        @Override
        public Money getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
            return parseMoney(rs.getLong(columnIndex));
        }
    
        @Override
        public Money getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
            return parseMoney(cs.getLong(columnIndex));
        }
    
        private Money parseMoney(Long value) {
            return Money.of(CurrencyUnit.of("CNY"), value / 100.0);
        }
    }
    ```

  - MybatisGeneratorDemoApplication.java

    ```java
    package geektime.spring.data.mybatisgeneratordemo;
    
    import geektime.spring.data.mybatisgeneratordemo.mapper.CoffeeMapper;
    import geektime.spring.data.mybatisgeneratordemo.model.Coffee;
    import geektime.spring.data.mybatisgeneratordemo.model.CoffeeExample;
    import lombok.extern.slf4j.Slf4j;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    import org.mybatis.generator.api.MyBatisGenerator;
    import org.mybatis.generator.config.Configuration;
    import org.mybatis.generator.config.xml.ConfigurationParser;
    import org.mybatis.generator.internal.DefaultShellCallback;
    import org.mybatis.spring.annotation.MapperScan;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;
    
    @SpringBootApplication
    @Slf4j
    @MapperScan("geektime.spring.data.mybatisgeneratordemo.mapper")
    public class MybatisGeneratorDemoApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeMapper coffeeMapper;
    
        public static void main(String[] args) {
            SpringApplication.run(MybatisGeneratorDemoApplication.class, args);
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            //generateArtifacts();
            playWithArtifacts();
        }
    
        //生成的方法
        private void generateArtifacts() throws Exception {
            List<String> warnings = new ArrayList<>();
            //解析器
            ConfigurationParser cp = new ConfigurationParser(warnings);
            //读取配置文件
            Configuration config = cp.parseConfiguration(
                    this.getClass().getResourceAsStream("/generatorConfig.xml"));
            //默认的回调函数
            DefaultShellCallback callback = new DefaultShellCallback(true);
            //传入参数，执行生成方法
            MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
            myBatisGenerator.generate(null);
        }
    
        private void playWithArtifacts() {
            //生成Coffee对象
            Coffee espresso = new Coffee()
                    .withName("espresso")
                    .withPrice(Money.of(CurrencyUnit.of("CNY"), 20.0))
                    .withCreateTime(new Date())
                    .withUpdateTime(new Date());
            //执行插入的方法
            coffeeMapper.insert(espresso);
    
            Coffee latte = new Coffee()
                    .withName("latte")
                    .withPrice(Money.of(CurrencyUnit.of("CNY"), 30.0))
                    .withCreateTime(new Date())
                    .withUpdateTime(new Date());
            coffeeMapper.insert(latte);
    
            //按照主键的查询
            Coffee s = coffeeMapper.selectByPrimaryKey(1L);
            log.info("Coffee {}", s);
    
            CoffeeExample example = new CoffeeExample();
            //传入条件执行查询
            example.createCriteria().andNameEqualTo("latte");
            //按照条件进行查询
            List<Coffee> list = coffeeMapper.selectByExample(example);
            list.forEach(e -> log.info("selectByExample: {}", e));
        }
    }
    
    ```

### 7.MyBatis PageHelper

- 链接：https://github.com/pagehelper/Mybatis-PageHelper/blob/master/README_zh.md

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.1.0</version>
            </dependency>
    
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>LATEST</version>
            </dependency>
            <!--引入分页的插件-->
            <dependency>
                <groupId>com.github.pagehelper</groupId>
                <artifactId>pagehelper-spring-boot-starter</artifactId>
                <version>1.2.10</version>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - application.properties

    ```properties
    mybatis.type-handlers-package=geektime.spring.data.mybatispagehelperdemo.handler
    mybatis.configuration.map-underscore-to-camel-case=true
    
    pagehelper.offset-as-page-num=true
    pagehelper.reasonable=true
    pagehelper.page-size-zero=true
    pagehelper.support-methods-arguments=true
    ```

  - Coffee.java

    ```java
    package geektime.spring.data.mybatispagehelperdemo.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Builder;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.joda.money.Money;
    
    import java.util.Date;
    
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    @Builder
    public class Coffee {
    
        private Long id;
        private String name;
        private Money price;
        private Date createTime;
        private Date updateTime;
    }
    ```

  - CoffeeMapper.java

    ```java
    package geektime.spring.data.mybatispagehelperdemo.mapper;
    
    import geektime.spring.data.mybatispagehelperdemo.model.Coffee;
    import org.apache.ibatis.annotations.Mapper;
    import org.apache.ibatis.annotations.Param;
    import org.apache.ibatis.annotations.Select;
    import org.apache.ibatis.session.RowBounds;
    
    import java.util.List;
    
    @Mapper
    public interface CoffeeMapper {
    
        @Select("select * from t_coffee order by id")
        List<Coffee> findAllWithRowBounds(RowBounds rowBounds);
    
        @Select("select * from t_coffee order by id")
        List<Coffee> findAllWithParam(@Param("pageNum") int pageNum,
                                      @Param("pageSize") int pageSize);
    }
    ```

  - MonyTypeHandler.java

    ```java
    package geektime.spring.data.mybatispagehelperdemo.handler;
    
    import org.apache.ibatis.type.BaseTypeHandler;
    import org.apache.ibatis.type.JdbcType;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    
    import java.sql.CallableStatement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.SQLException;
    
    /**
     * 在 Money 与 Long 之间转换的 TypeHandler，处理 CNY 人民币
     */
    public class MoneyTypeHandler extends BaseTypeHandler<Money> {
        @Override
        public void setNonNullParameter(PreparedStatement ps, int i, Money parameter, JdbcType jdbcType) throws SQLException {
            ps.setLong(i, parameter.getAmountMinorLong());
        }
    
        @Override
        public Money getNullableResult(ResultSet rs, String columnName) throws SQLException {
            return parseMoney(rs.getLong(columnName));
        }
    
        @Override
        public Money getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
            return parseMoney(rs.getLong(columnIndex));
        }
    
        @Override
        public Money getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
            return parseMoney(cs.getLong(columnIndex));
        }
    
        private Money parseMoney(Long value) {
            return Money.of(CurrencyUnit.of("CNY"), value / 100.0);
        }
    }
    ```

  - MybatisPagehelperDemoApplication.java

    ```java
    package geektime.spring.data.mybatispagehelperdemo;
    
    import com.github.pagehelper.PageInfo;
    import geektime.spring.data.mybatispagehelperdemo.mapper.CoffeeMapper;
    import geektime.spring.data.mybatispagehelperdemo.model.Coffee;
    import lombok.extern.slf4j.Slf4j;
    import org.apache.ibatis.session.RowBounds;
    import org.mybatis.spring.annotation.MapperScan;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    import java.util.List;
    
    @SpringBootApplication
    @Slf4j
    @MapperScan("geektime.spring.data.mybatispagehelperdemo.mapper")
    public class MybatisPagehelperDemoApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeMapper coffeeMapper;
    
        public static void main(String[] args) {
            SpringApplication.run(MybatisPagehelperDemoApplication.class, args);
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            //查询第一页的三条记录
            coffeeMapper.findAllWithRowBounds(new RowBounds(1,3))
                    .forEach(c -> log.info("Page(1) Coffee {}", c));
    	   //查询第二页的三条记录
            coffeeMapper.findAllWithRowBounds(new RowBounds(2,3))
                    .forEach(c -> log.info("Page(2) Coffee {}", c));
    
            log.info("===================");
    		//查询所有的记录
            coffeeMapper.findAllWithRowBounds(new RowBounds(1,0))
                    .forEach(c -> log.info("Page(1) Coffee {}", c));
    
            log.info("===================");
    		//根据参数来查询的
            coffeeMapper.findAllWithParam(1, 3)
                    .forEach(c -> log.info("Page(1) Coffee {}", c));
            List<Coffee> list = coffeeMapper.findAllWithParam(2, 3);
            //打印PageInfo类
            PageInfo page = new PageInfo(list);
            log.info("PageInfo: {}", page);
        }
    }
    
    ```

## 第三章：NoSQL实践

### 1.通过Docker辅助开发

- Docker：容器管理引擎

- 容器的定义：

  - 应用层的抽象，标准化的单元，容器和虚拟机不同，容器中不包含操作系统的细节的内容。

- 开发眼中的Docker

  - 简化了重复搭建开发环境的工作

- 运维眼中的Docker

  - 交付系统更为流畅
  - 伸缩性更好

- docker常用的命令

  - 镜像相关

    - docker pull <image> 拉去镜像
    - docker search <image> 搜索镜像

  - 容器相关

    - docker run 运行容器
    - docker statrt/stop <容器名>  启动或者停止容器
    - docker ps <容器名>  查看容器启动的情况
    - docker logs <容器名>  查看容器的日志

  - docker run

    - 选项说明
      - -d 后台运行容器
      - -e 设置环境变量
      - --expose / -p 宿主端口:容器端口
      - --name  指定容器名称
      - --link 链接不同的容器
      - -v 宿主目录:容器目录，挂载磁盘卷

  - win10下安装docker和运行MongoDB

    - 链接：http://www.moguf.com/post/windockerrunmongo

    - 获取镜像

      - docker pull mongo

    - 运行MongoDB

      ```powershell
      # 该命令在Linux和macOS有用
      docker run --name mongo -p 27017:27017 -v ~/docker-data/mongo:/data/db -e MONGO_INITDB_ROOT-USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=admin -d mongo
      ```

    - 登录到MongoDB容器中

      - docker exec -it mongo bash

    - 通过Shell连接MongoDB

      - mongo -u admin -p admin

### 2.Spring中访问MongoDB

- Spring 对MongDB的支持

  - Spring Data MongoDB
    - MongoTemplate
    - Repository支持

- Spring Data MongoDB的基本用法

  - 注解
    - @Document
    - @Id
  - MongoTemplate
    - save  /  remove
    - Criteria  /  Query  /  Update

- code

  - 创建库

    - use springbucks;

  - 创建用户

    ```json
    db.createUser(
    	{
            user: "springbucks",
            pwd: "springbucks",
            roles:[
                {role: "readWrite", db: "springbucks"}
            ]
        }
    )
    ```

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-mongodb</artifactId>
            </dependency>
    
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>RELEASE</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - application.properties

    ```properties
    # 拿springbucks用户登录MongoDB，端口是27019
    spring.data.mongodb.uri=mongodb://springbucks:springbucks@localhost:27019/springbucks
    ```

  - Coffe.java

    ```java
    package geektime.spring.data.mongodemo.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Builder;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.joda.money.Money;
    import org.springframework.data.annotation.Id;
    import org.springframework.data.mongodb.core.mapping.Document;
    
    import java.util.Date;
    
    //标明与coffee的collection对应的
    @Document
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public class Coffee {
    
        @Id
        private String id;
        private String name;
        private Money price;
        private Date createTime;
        private Date updateTime;
    }
    ```

  - MoneyReadConverter.java

    ```java
    package geektime.spring.data.mongodemo.converter;
    
    import org.bson.Document;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    import org.springframework.core.convert.converter.Converter;
    
    //将Document转化成Money类型
    //序列化的时候已将将Money类型转成Document类型
    //在MongoDB中存储的格式是bson格式
    public class MoneyReadConverter implements Converter<Document, Money> {
        @Override
        public Money convert(Document source) {
            Document money = (Document) source.get("money");
            double amount = Double.parseDouble(money.getString("amount"));
            String currency = ((Document) money.get("currency")).getString("code");
            return Money.of(CurrencyUnit.of(currency), amount);
        }
    }
    ```

  - MongoDemoApplication.java

    ```java
    package geektime.spring.data.mongodemo;
    
    import com.mongodb.client.result.UpdateResult;
    import geektime.spring.data.mongodemo.converter.MoneyReadConverter;
    import geektime.spring.data.mongodemo.model.Coffee;
    import lombok.extern.slf4j.Slf4j;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.Bean;
    import org.springframework.data.mongodb.core.MongoTemplate;
    import org.springframework.data.mongodb.core.convert.MongoCustomConversions;
    import org.springframework.data.mongodb.core.query.Criteria;
    import org.springframework.data.mongodb.core.query.Query;
    import org.springframework.data.mongodb.core.query.Update;
    
    import java.util.Arrays;
    import java.util.Date;
    import java.util.List;
    
    import static org.springframework.data.mongodb.core.query.Criteria.where;
    import static org.springframework.data.mongodb.core.query.Query.query;
    
    @SpringBootApplication
    @Slf4j
    public class MongoDemoApplication implements ApplicationRunner {
    
        @Autowired
        private MongoTemplate mongoTemplate;
    
        public static void main(String[] args) {
            SpringApplication.run(MongoDemoApplication.class, args);
        }
    
        //让springboot读到自己定义的类型转换的类
        @Bean
        public MongoCustomConversions mongoCustomConversions() {
            return new MongoCustomConversions(Arrays.asList(new MoneyReadConverter()));
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            //创建一个咖啡对象
            Coffee espresso = Coffee.builder()
                    .name("espresso")
                    .price(Money.of(CurrencyUnit.of("CNY"), 20.0))
                    .createTime(new Date())
                    .updateTime(new Date())
                    .build();
            //保存咖啡对象
            Coffee saved = mongoTemplate.save(espresso);
            log.info("Coffee {}", saved);
    
            //查找coffee的名字是espresso的所有的咖啡
            List<Coffee> list = mongoTemplate.find(
                    query(where("name").is("espresso")), Coffee.class);
            log.info("Find {} Coffee", list.size());
            list.forEach(c -> log.info("Coffee {}", c));
    
            Thread.sleep(1000); //为了看更新时间
            //更新咖啡名字是espresso的第一条的咖啡信息，把价格更新成30
            UpdateResult result = mongoTemplate.updateFirst(query(where("name").is("espresso")),
                    new Update().set("price", Money.ofMajor(CurrencyUnit.of("CNY"), 30))
                            .currentDate("updateTime"), Coffee.class);
            log.info("Update Result: {}", result.getModifiedCount());
    		
            //查找到刚才保存的咖啡信息
            Coffee updateOne = mongoTemplate.findById(saved.getId(), Coffee.class);
            log.info("Update Result: {}", updateOne);
            //删除刚才查找的咖啡信息
            mongoTemplate.remove(updateOne);
        }
    }
    ```

  - Spring Data MongoDB 的 repository

    - @EnableMongoRepositories 开启Repository的支持

  - 对应接口

    - MongoRepository<T, ID>
    - PagingAndSortRepository<T,ID>
    - CrudRepository<T,ID>

  - code

    - pom.xml

      ```xml
          <dependencies>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-data-mongodb</artifactId>
              </dependency>
              <dependency>
                  <groupId>org.joda</groupId>
                  <artifactId>joda-money</artifactId>
                  <version>RELEASE</version>
              </dependency>
              <dependency>
                  <groupId>org.projectlombok</groupId>
                  <artifactId>lombok</artifactId>
                  <optional>true</optional>
              </dependency>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-test</artifactId>
                  <scope>test</scope>
              </dependency>
          </dependencies>
      ```

    - application.properties

      ```properties
      spring.data.mongodb.uri=mongodb://springbucks:springbucks@localhost:27019/springbucks
      ```

    - Coffee.java

      ```java
      package geektime.spring.data.mongodemo.model;
      
      import lombok.AllArgsConstructor;
      import lombok.Builder;
      import lombok.Data;
      import lombok.NoArgsConstructor;
      import org.joda.money.Money;
      import org.springframework.data.annotation.Id;
      import org.springframework.data.mongodb.core.mapping.Document;
      
      import java.util.Date;
      
      @Document
      @Data
      @Builder
      @AllArgsConstructor
      @NoArgsConstructor
      public class Coffee {
      
          @Id
          private String id;
          private String name;
          private Money price;
          private Date createTime;
          private Date updateTime;
      }
      ```

    - CoffeeRepository.java

      ```java
      package geektime.spring.data.mongodemo.repository;
      
      import geektime.spring.data.mongodemo.model.Coffee;
      import org.springframework.data.mongodb.repository.MongoRepository;
      
      import java.util.List;
      
      public interface CoffeeRepository extends MongoRepository<Coffee,String> {
          List<Coffee> findByName(String name);
      }
      ```

    - MoneyReadConverter.java

      ```java
      package geektime.spring.data.mongodemo.converter;
      
      import org.bson.Document;
      import org.joda.money.CurrencyUnit;
      import org.joda.money.Money;
      import org.springframework.core.convert.converter.Converter;
      import org.springframework.stereotype.Component;
      
      @Component
      public class MoneyReadConverter implements Converter<Document, Money> {
          @Override
          public Money convert(Document document) {
              Document money = (Document) document.get("money");
              double amount = Double.parseDouble(money.getString("amount"));
              String currency = ((Document) money.get("currency")).getString("code");
              return Money.of(CurrencyUnit.of(currency), amount);
          }
      }
      ```

    - MongoRepositoryDemoApplication.java

      ```java
      package geektime.spring.data.mongodemo;
      
      import geektime.spring.data.mongodemo.converter.MoneyReadConverter;
      import geektime.spring.data.mongodemo.model.Coffee;
      import geektime.spring.data.mongodemo.repository.CoffeeRepository;
      import lombok.extern.slf4j.Slf4j;
      import org.joda.money.CurrencyUnit;
      import org.joda.money.Money;
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.boot.CommandLineRunner;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.context.annotation.Bean;
      import org.springframework.data.domain.Sort;
      import org.springframework.data.mongodb.core.convert.MongoCustomConversions;
      import org.springframework.data.mongodb.repository.config.EnableMongoRepositories;
      
      import java.util.Arrays;
      import java.util.Date;
      
      @Slf4j
      @SpringBootApplication
      //开启MongoRepository的支持
      @EnableMongoRepositories
      public class MongoRepositoryDemoApplication implements CommandLineRunner {
      
          @Autowired
          private CoffeeRepository coffeeRepository;
      
          public static void main(String[] args) {
              SpringApplication.run(MongoRepositoryDemoApplication.class, args);
          }
      
          @Bean
          public MongoCustomConversions mongoCustomConversions() {
              return new MongoCustomConversions(Arrays.asList(new MoneyReadConverter()));
          }
      
          @Override
          public void run(String... args) throws Exception {
              Coffee espresso = Coffee.builder()
                      .name("espresso")
                      .price(Money.of(CurrencyUnit.of("CNY"), 20.0))
                      .createTime(new Date())
                      .updateTime(new Date())
                      .build();
              Coffee latte = Coffee.builder()
                      .name("latte")
                      .price(Money.of(CurrencyUnit.of("CNY"), 30.0))
                      .createTime(new Date())
                      .updateTime(new Date())
                      .build();
      
              //将两条记录插入进去
              coffeeRepository.insert(Arrays.asList(espresso, latte));
              //查找所有的记录，并且按照name排序
              coffeeRepository.findAll(Sort.by("name"))
                      .forEach(c -> log.info("Saved Coffee {}", c));
      
              Thread.sleep(1000);
              latte.setPrice(Money.of(CurrencyUnit.of("CNY"), 35.0));
              latte.setUpdateTime(new Date());
              //对象的属性修改，执行save方法的时候就会更新数据
              coffeeRepository.save(latte);
      
              coffeeRepository.findByName("latte")
                      .forEach(c -> log.info("Coffee {}", c));
      
              coffeeRepository.deleteAll();
          }
      }
      ```

### 3.在spring中jedis的使用

- Spring对Redis的支持

  - Redis是一款开源的内存KV存储，支持多种数据结构
  - Spring Data Redis
    - 支持的客户端Jedis  / Lettuce
    - RedisTemplate
    - Repository支持
  - jedis的简单使用
    - Jedis不是线程安全的
    - 通过JedisPool获取Jedis实例
    - 直接使用Jedis中的方法
  - 通过docker启动Redis
    - 获取镜像
      - docker pull redis
    - 启动Redis
      - docker run --name redis -d -p 6379:6379 redis

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-jpa</artifactId>
            </dependency>
            <!--jedis的支持-->
            <dependency>
                <groupId>redis.clients</groupId>
                <artifactId>jedis</artifactId>
            </dependency>
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>1.0.1</version>
            </dependency>
            <dependency>
                <groupId>org.jadira.usertype</groupId>
                <artifactId>usertype.core</artifactId>
                <version>6.0.1.GA</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - schema.sql

    ```sql
    --自动执行的SQL语句
    drop table t_coffee if exists;
    drop table t_order if exists;
    drop table t_order_coffee if exists;
    
    create table t_coffee
    (
        id          bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        name        varchar(255),
        price       bigint,
        primary key (id)
    );
    
    create table t_order
    (
        id          bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        customer    varchar(255),
        state       integer not null,
        primary key (id)
    );
    
    create table t_order_coffee
    (
        coffee_order_id bigint not null,
        items_id        bigint not null
    );
    
    insert into t_coffee (name, price, create_time, update_time)
    values ('espresso', 2000, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('latte', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('capuccino', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('mocha', 3000, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('macchiato', 3000, now(), now());
    ```

  - application.properties

    ```properties
    spring.jpa.hibernate.ddl-auto=none
    spring.jpa.properties.hibernate.show_sql=true
    spring.jpa.properties.hibernate.format_sql=true
    
    # 下面的两种写法没有区别
    redis.host=localhost
    redis.maxTotal=5
    redis.maxIdle=5
    redis.testOnBorrow=true
    
    #redis.max-total=3
    #redis.max-idle=3
    #redis.test-on-borrow=true
    ```

  - BaseEntity.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.hibernate.annotations.CreationTimestamp;
    import org.hibernate.annotations.UpdateTimestamp;
    
    import javax.persistence.*;
    import java.io.Serializable;
    import java.util.Date;
    
    @MappedSuperclass
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public class BaseEntity implements Serializable {
    
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
    
        @Column(updatable = false)
        @CreationTimestamp
        private Date createTime;
    
        @UpdateTimestamp
        private Date updateTime;
    }
    ```

  - Coffee.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.*;
    import org.hibernate.annotations.Type;
    import org.joda.money.Money;
    
    import javax.persistence.Entity;
    import javax.persistence.Table;
    import java.io.Serializable;
    
    @Entity
    @Table(name = "T_COFFEE")
    @Builder
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    public class Coffee extends BaseEntity implements Serializable {
    
        private String name;
    
        @Type(type = "org.jadira.usertype.moneyandcurrency.joda.PersistentMoneyMinorAmount",
                parameters = {@org.hibernate.annotations.Parameter(name = "currencyCode", value = "CNY")})
        private Money price;
    }
    ```

  - CoffeeOrder.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeOrder;
    import geektime.spring.springbucks.model.OrderState;
    import geektime.spring.springbucks.repository.CoffeeOrderRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import javax.transaction.Transactional;
    import java.util.ArrayList;
    import java.util.Arrays;
    
    @Slf4j
    @Service
    @Transactional
    public class CoffeeOrderService {
    
        @Autowired
        private CoffeeOrderRepository orderRepository;
    
        public CoffeeOrder createOrder(String customer, Coffee... coffee) {
            CoffeeOrder order = CoffeeOrder.builder()
                    .customer(customer)
                    .items(new ArrayList<>(Arrays.asList(coffee)))
                    .state(OrderState.INIT)
                    .build();
            CoffeeOrder saved = orderRepository.save(order);
            log.info("New Order: {}", saved);
            return saved;
        }
    
        public boolean updateState(CoffeeOrder order, OrderState state) {
            if (state.compareTo(order.getState()) <= 0) {
                log.warn("Wrong State order: {}, {}", state, order.getState());
                return false;
            }
            order.setState(state);
            orderRepository.save(order);
            log.info("Updated Order: {}", order);
            return true;
        }
    }
    ```

  - OrderState.java

    ```java
    package geektime.spring.springbucks.model;
    
    public enum  OrderState {
        INIT, PAID, BREWING, BREWED, TAKEN, CANCELLED
    }
    ```

  - CoffeeRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.Coffee;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeRepository extends JpaRepository<Coffee, Long> {
    }
    ```

  - CoffeeOrderRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.CoffeeOrder;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeOrderRepository extends JpaRepository<CoffeeOrder, Long> {
    }
    ```

  - CoffeeService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.repository.CoffeeRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.data.domain.Example;
    import org.springframework.data.domain.ExampleMatcher;
    import org.springframework.stereotype.Service;
    
    import java.util.List;
    import java.util.Optional;
    
    import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.exact;
    
    @Slf4j
    @Service
    public class CoffeeService {
    
        @Autowired
        private CoffeeRepository coffeeRepository;
    
        public List<Coffee> findAllCoffee() {
            return coffeeRepository.findAll();
        }
    
        public Optional<Coffee> findOneCoffee(String name) {
            ExampleMatcher matcher = ExampleMatcher.matching()
                    .withMatcher("name", exact().ignoreCase());
    
            Optional<Coffee> coffee = coffeeRepository.findOne(
                    Example.of(Coffee.builder().name(name).build(), matcher));
    
            log.info("Coffee Found: {}", coffee);
            return coffee;
        }
    }
    ```

  - CoffeeOrderService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeOrder;
    import geektime.spring.springbucks.model.OrderState;
    import geektime.spring.springbucks.repository.CoffeeOrderRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import javax.transaction.Transactional;
    import java.util.ArrayList;
    import java.util.Arrays;
    
    @Slf4j
    @Service
    @Transactional
    public class CoffeeOrderService {
    
        @Autowired
        private CoffeeOrderRepository orderRepository;
    
        public CoffeeOrder createOrder(String customer, Coffee... coffee) {
            CoffeeOrder order = CoffeeOrder.builder()
                    .customer(customer)
                    .items(new ArrayList<>(Arrays.asList(coffee)))
                    .state(OrderState.INIT)
                    .build();
            CoffeeOrder saved = orderRepository.save(order);
            log.info("New Order: {}", saved);
            return saved;
        }
    
        public boolean updateState(CoffeeOrder order, OrderState state) {
            if (state.compareTo(order.getState()) <= 0) {
                log.warn("Wrong State order: {}, {}", state, order.getState());
                return false;
            }
            order.setState(state);
            orderRepository.save(order);
            log.info("Updated Order: {}", order);
            return true;
        }
    }
    ```

  - SpringbucksApplication.java

    ```java
    package geektime.spring.springbucks;
    
    import geektime.spring.springbucks.service.CoffeeService;
    import org.springframework.beans.factory.annotation.Value;
    import lombok.extern.slf4j.Slf4j;
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Bean;
    import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
    import org.springframework.transaction.annotation.EnableTransactionManagement;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisPool;
    import redis.clients.jedis.JedisPoolConfig;
    
    import java.util.Map;
    
    @Slf4j
    @EnableTransactionManagement
    @SpringBootApplication
    @EnableJpaRepositories
    public class SpringbucksApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeService coffeeService;
    
        @Autowired
        private JedisPool jedisPool;
    
        @Autowired
        private JedisPoolConfig jedisPoolConfig;
    
        public static void main(String[] args) {
            SpringApplication.run(SpringbucksApplication.class, args);
        }
    
        @Bean
        @ConfigurationProperties("redis")
        public JedisPoolConfig jedisPoolConfig() {
            return new JedisPoolConfig();
        }
    
        @Bean(destroyMethod = "close")
        public JedisPool jedisPool(@Value("${redis.host}") String host) {
            return new JedisPool(jedisPoolConfig(), host);
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            log.info(jedisPoolConfig.toString());
    
            //获取一个jedis对象
            try (Jedis jedis = jedisPool.getResource()) {
                coffeeService.findAllCoffee().forEach(c -> {
                    //将查到的价格的值插入到Redis数据库，并且键为springbucks-menu
                    jedis.hset("springbucks-menu",
                            c.getName(),
                            Long.toString(c.getPrice().getAmountMinorLong()));
                });
    		 
                //获取Redis中键为springbucks-menu的值
                Map<String, String> menu = jedis.hgetAll("springbucks-menu");
                log.info("Menu: {}", menu);
    		   
                //获取Redis中springbucks-menu中键为espresso的价格
                String price = jedis.hget("springbucks-menu", "espresso");
                log.info("espresso - {}",
                        Money.ofMinor(CurrencyUnit.of("CNY"), Long.parseLong(price)));
            }
        }
    }
    ```

### 4.Redis的哨兵模式和集群模式

- Redis的哨兵模式
  - Redis Sentinel 是Redis的一种高可用方案
    - 监控、通知、自动故障转移、服务发现
  - JedisSentinelPool
- Redis的集群模式
  - Redis Cluster
    - 数据自动分片(分成16384个Hash Slot)
    - 在部分节点失效时有一定可用性
  - JedisCluster
    - jedis只从Master读取数据，如果想要自动读写分离，可以定制

### 5.spring中的缓存抽象

- 为不同的缓存提供一层抽象

  - 为java方法增加缓存，缓存执行结果
  - 支持ConcurrentMap、EhCache、Caffeine、JCache(JSR-107)
  - 接口
    - org.springframework.cache.Cache
    - org.springframework.cache.CacheManager

- spring基于注解的缓存

  - @EnableCaching
    - @Cacheable
    - @CacheEvict
    - @CachePut
    - @Caching
    - @CacheConfig

- code

  - pom.xml

    ```xml
        <dependencies>
            <!--若使用Redis作为缓存，要引入下面的两个jar包-->
            <!--
    		<dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-cache</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-redis</artifactId>
            </dependency>
    		-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-jpa</artifactId>
            </dependency>
    
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>1.0.1</version>
            </dependency>
            <dependency>
                <groupId>org.jadira.usertype</groupId>
                <artifactId>usertype.core</artifactId>
                <version>6.0.1.GA</version>
            </dependency>
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - application.properties

    ```properties
    spring.jpa.hibernate.ddl-auto=none
    spring.jpa.properties.hibernate.show_sql=true
    spring.jpa.properties.hibernate.format_sql=true
    ######################################################
    # 使用下面的配置开启Redis缓存
    #缓存类型
    # spring.cache.type=redis
    #缓存的名字
    # spring.cache.cache-names=coffee
    #缓存过期时间
    # spring.cache.redis.time-to-live=5000
    # spring.cache.redis.cache-null-values=false
    
    # spring.redis.host=localhost
    ```

  - schema.sql

    ```sql
    drop table t_coffee if exists;
    drop table t_order if exists;
    drop table t_order_coffee if exists;
    
    create table t_coffee (
        id bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        name varchar(255),
        price bigint,
        primary key (id)
    );
    
    create table t_order (
        id bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        customer varchar(255),
        state integer not null,
        primary key (id)
    );
    
    create table t_order_coffee (
        coffee_order_id bigint not null,
        items_id bigint not null
    );
    
    insert into t_coffee (name, price, create_time, update_time) values ('espresso', 2000, now(), now());
    insert into t_coffee (name, price, create_time, update_time) values ('latte', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time) values ('capuccino', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time) values ('mocha', 3000, now(), now());
    insert into t_coffee (name, price, create_time, update_time) values ('macchiato', 3000, now(), now());
    ```

  - BaseEntity.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.hibernate.annotations.CreationTimestamp;
    import org.hibernate.annotations.UpdateTimestamp;
    
    import javax.persistence.Column;
    import javax.persistence.GeneratedValue;
    import javax.persistence.GenerationType;
    import javax.persistence.Id;
    import javax.persistence.MappedSuperclass;
    import java.io.Serializable;
    import java.util.Date;
    
    @MappedSuperclass
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public class BaseEntity implements Serializable {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
        @Column(updatable = false)
        @CreationTimestamp
        private Date createTime;
        @UpdateTimestamp
        private Date updateTime;
    }
    ```

  - Coffee.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Builder;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import lombok.ToString;
    import org.hibernate.annotations.Type;
    import org.joda.money.Money;
    
    import javax.persistence.Entity;
    import javax.persistence.Table;
    import java.io.Serializable;
    
    @Entity
    @Table(name = "T_COFFEE")
    @Builder
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    public class Coffee extends BaseEntity implements Serializable {
        private String name;
        @Type(type = "org.jadira.usertype.moneyandcurrency.joda.PersistentMoneyMinorAmount",
                parameters = {@org.hibernate.annotations.Parameter(name = "currencyCode", value = "CNY")})
        private Money price;
    }
    ```

  - CoffeeOrder.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Builder;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import lombok.ToString;
    
    import javax.persistence.Column;
    import javax.persistence.Entity;
    import javax.persistence.Enumerated;
    import javax.persistence.JoinTable;
    import javax.persistence.ManyToMany;
    import javax.persistence.OrderBy;
    import javax.persistence.Table;
    import java.io.Serializable;
    import java.util.List;
    
    @Entity
    @Table(name = "T_ORDER")
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public class CoffeeOrder extends BaseEntity implements Serializable {
        private String customer;
        @ManyToMany
        @JoinTable(name = "T_ORDER_COFFEE")
        @OrderBy("id")
        private List<Coffee> items;
        @Enumerated
        @Column(nullable = false)
        private OrderState state;
    }
    ```

  - OrderState.java

    ```java
    package geektime.spring.springbucks.model;
    
    public enum OrderState {
        INIT, PAID, BREWING, BREWED, TAKEN, CANCELLED
    }
    ```

  - CoffeeRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.Coffee;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeRepository extends JpaRepository<Coffee, Long> {
    }
    ```

  - CoffeeOrderRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.CoffeeOrder;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeOrderRepository extends JpaRepository<CoffeeOrder, Long> {
    }
    ```

  - CoffeeService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.repository.CoffeeRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.cache.annotation.CacheConfig;
    import org.springframework.cache.annotation.CacheEvict;
    import org.springframework.cache.annotation.Cacheable;
    import org.springframework.data.domain.Example;
    import org.springframework.data.domain.ExampleMatcher;
    import org.springframework.stereotype.Service;
    
    import java.util.List;
    import java.util.Optional;
    
    import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.exact;
    
    @Slf4j
    @Service
    //缓存配置的名字
    @CacheConfig(cacheNames = "coffee")
    public class CoffeeService {
        @Autowired
        private CoffeeRepository coffeeRepository;
    
        //将查询的结果放入到缓存中
        @Cacheable
        public List<Coffee> findAllCoffee() {
            return coffeeRepository.findAll();
        }
    
        //清除缓存
        @CacheEvict
        public void reloadCoffee() {
        }
    
        public Optional<Coffee> findOneCoffee(String name) {
            ExampleMatcher matcher = ExampleMatcher.matching()
                    .withMatcher("name", exact().ignoreCase());
            Optional<Coffee> coffee = coffeeRepository.findOne(
                    Example.of(Coffee.builder().name(name).build(), matcher));
            log.info("Coffee Found: {}", coffee);
            return coffee;
        }
    }
    ```

  - CoffeeOrderService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeOrder;
    import geektime.spring.springbucks.model.OrderState;
    import geektime.spring.springbucks.repository.CoffeeOrderRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import javax.transaction.Transactional;
    import java.util.ArrayList;
    import java.util.Arrays;
    
    @Slf4j
    @Service
    @Transactional
    public class CoffeeOrderService {
        @Autowired
        private CoffeeOrderRepository orderRepository;
    
        public CoffeeOrder createOrder(String customer, Coffee...coffee) {
            CoffeeOrder order = CoffeeOrder.builder()
                    .customer(customer)
                    .items(new ArrayList<>(Arrays.asList(coffee)))
                    .state(OrderState.INIT)
                    .build();
            CoffeeOrder saved = orderRepository.save(order);
            log.info("New Order: {}", saved);
            return saved;
        }
    
        public boolean updateState(CoffeeOrder order, OrderState state) {
            if (state.compareTo(order.getState()) <= 0) {
                log.warn("Wrong State order: {}, {}", state, order.getState());
                return false;
            }
            order.setState(state);
            orderRepository.save(order);
            log.info("Updated Order: {}", order);
            return true;
        }
    }
    ```

  - SpringbucksApplication.java

    ```java
    package geektime.spring.springbucks;
    
    import geektime.spring.springbucks.service.CoffeeService;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cache.annotation.EnableCaching;
    import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
    import org.springframework.transaction.annotation.EnableTransactionManagement;
    
    @Slf4j
    @EnableTransactionManagement
    @SpringBootApplication
    @EnableJpaRepositories
    //进行类的拦截，基于aop
    @EnableCaching(proxyTargetClass = true)
    public class SpringbucksApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeService coffeeService;
    
        public static void main(String[] args) {
            SpringApplication.run(SpringbucksApplication.class, args);
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            log.info("Count: {}", coffeeService.findAllCoffee().size());
    
            for (int i = 0; i < 10; i++) {
                log.info("Reading from cache.");
                coffeeService.findAllCoffee();
            }
    
            //清空缓存
            coffeeService.reloadCoffee();
    
            log.info("Reading after refresh");
            coffeeService.findAllCoffee().forEach(c -> log.info("Coffee {}",c.getName()));
        }
    }
    ```

### 6.spring中访问Redis

- 与Redis建立连接

  - 配置连接工厂
    - LettuceConnectionFactory与JedisConnectionFactory
      - RedisStandaloneConfiguration
      - RedisSentinelConfiguration
      - RedisClusterConfiguration

- Lettuce内置支持读写分离

  - 只读主、只读从
  - 优先读主、优先读从
  - LettuceClientConfiguration
  - LettucePoolingClientConfiguration
  - LettuceClientConfigurationBuilderCustomizer

- RedisTemplate

  - RedisTemplate<K, V>
    - opsForXxx()
  - StringRedisTemplate
  - **一定注意设置过期时间**

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-jpa</artifactId>
            </dependency>
            <!--引入Redis的支持-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-redis</artifactId>
            </dependency>
    		<!--要加入的依赖-->
            <dependency>
                <groupId>org.apache.commons</groupId>
                <artifactId>commons-pool2</artifactId>
            </dependency>
    
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>1.0.1</version>
            </dependency>
            <dependency>
                <groupId>org.jadira.usertype</groupId>
                <artifactId>usertype.core</artifactId>
                <version>6.0.1.GA</version>
            </dependency>
    
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - application.properties

    ```properties
    spring.jpa.hibernate.ddl-auto=none
    spring.jpa.properties.hibernate.show_sql=true
    spring.jpa.properties.hibernate.format_sql=true
    
    spring.redis.host=localhost
    spring.redis.lettuce.pool.max-active=5
    spring.redis.lettuce.pool.max-idle=5
    ```

  - schema.sql

    ```sql
    drop table t_coffee if exists;
    drop table t_order if exists;
    drop table t_order_coffee if exists;
    
    create table t_coffee
    (
        id          bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        name        varchar(255),
        price       bigint,
        primary key (id)
    );
    
    create table t_order
    (
        id          bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        customer    varchar(255),
        state       integer not null,
        primary key (id)
    );
    
    create table t_order_coffee
    (
        coffee_order_id bigint not null,
        items_id        bigint not null
    );
    
    insert into t_coffee (name, price, create_time, update_time)
    values ('espresso', 2000, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('latte', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('capuccino', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('mocha', 3000, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('macchiato', 3000, now(), now());
    ```

  - BaseEntity.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.hibernate.annotations.CreationTimestamp;
    import org.hibernate.annotations.UpdateTimestamp;
    
    import javax.persistence.*;
    import java.io.Serializable;
    import java.util.Date;
    
    @MappedSuperclass
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public class BaseEntity implements Serializable {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
    
        @Column(updatable = false)
        @CreationTimestamp
        private Date createTime;
    
        @UpdateTimestamp
        private Date updateTime;
    }
    ```

  - Coffee.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.*;
    import org.hibernate.annotations.Type;
    import org.joda.money.Money;
    
    import javax.persistence.Entity;
    import javax.persistence.Table;
    import java.io.Serializable;
    
    @Entity
    @Table(name = "T_COFFEE")
    @Builder
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    public class Coffee extends BaseEntity implements Serializable {
        private String name;
        @Type(type = "org.jadira.usertype.moneyandcurrency.joda.PersistentMoneyMinorAmount",
                parameters = {@org.hibernate.annotations.Parameter(name = "currencyCode", value = "CNY")})
        private Money price;
    }
    ```

  - CoffeeOrder.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.*;
    
    import javax.persistence.*;
    import java.io.Serializable;
    import java.util.List;
    
    @Entity
    @Table(name = "T_ORDER")
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public class CoffeeOrder extends BaseEntity implements Serializable {
        private String customer;
        @ManyToMany
        @JoinTable(name = "T_ORDER_COFFEE")
        @OrderBy("id")
        private List<Coffee> items;
        @Enumerated
        @Column(nullable = false)
        private OrderState state;
    }
    ```

  - OrderState.java

    ```java
    package geektime.spring.springbucks.model;
    
    public enum OrderState {
        INIT, PAID, BREWING, BREWED, TAKEN, CANCELLED
    }
    ```

  - CoffeeRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.Coffee;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeRepository extends JpaRepository<Coffee, Long> {
    }
    ```

  - CoffeeOrderRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.CoffeeOrder;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeOrderRepository extends JpaRepository<CoffeeOrder, Long> {
    }
    ```

  - CoffeeService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.repository.CoffeeRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.data.domain.Example;
    import org.springframework.data.domain.ExampleMatcher;
    import org.springframework.data.redis.core.HashOperations;
    import org.springframework.data.redis.core.RedisTemplate;
    import org.springframework.stereotype.Service;
    
    import java.util.Optional;
    import java.util.concurrent.TimeUnit;
    
    import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.exact;
    
    @Slf4j
    @Service
    public class CoffeeService {
    
        public static final String CACHE = "springbucks-coffee";
    
        @Autowired
        private CoffeeRepository coffeeRepository;
    
        @Autowired
        private RedisTemplate<String, Coffee> redisTemplate;
    
        public Optional<Coffee> findOneCoffee(String name) {
            //获取这个hash
            HashOperations<String, String, Coffee> hashOperations = redisTemplate.opsForHash();
            //Redis中存在这个键，这个hash中存在这个咖啡，直接读取Redis中数据
            if (redisTemplate.hasKey(CACHE) && hashOperations.hasKey(CACHE,name)){
                log.info("Get coffee {} from Redis.", name);
                return Optional.of(hashOperations.get(CACHE,name));
            }
    
            ExampleMatcher matcher = ExampleMatcher.matching()
                    .withMatcher("name", exact().ignoreCase());
            Optional<Coffee> coffee = coffeeRepository.findOne(
                    Example.of(Coffee.builder().name(name).build(), matcher));
            log.info("Coffee Found: {}", coffee);
    
            //查找的coffee的值存在的话，存入的Redis中
            if (coffee.isPresent()){
                log.info("Put coffee {} to Redis", name);
                hashOperations.put(CACHE, name, coffee.get());
                //设置过期的时间为1分钟
                redisTemplate.expire(CACHE, 1, TimeUnit.MINUTES);
            }
            return coffee;
        }
    }
    ```

  - CoffeeOrderService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeOrder;
    import geektime.spring.springbucks.model.OrderState;
    import geektime.spring.springbucks.repository.CoffeeOrderRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import javax.transaction.Transactional;
    import java.util.ArrayList;
    import java.util.Arrays;
    
    @Slf4j
    @Service
    @Transactional
    public class CoffeeOrderService {
    
        @Autowired
        private CoffeeOrderRepository orderRepository;
    
        public CoffeeOrder createOrder(String customer, Coffee... coffee){
            CoffeeOrder order = CoffeeOrder.builder()
                    .customer(customer)
                    .items(new ArrayList<>(Arrays.asList(coffee)))
                    .state(OrderState.INIT)
                    .build();
            CoffeeOrder saved = orderRepository.save(order);
            log.info("New Order: {}", saved);
            return saved;
        }
    
        public boolean updateState(CoffeeOrder order, OrderState state) {
            if (state.compareTo(order.getState()) <= 0){
                log.warn("Wrong State order: {}, {}", state, order.getState());
                return false;
            }
            order.setState(state);
            orderRepository.save(order);
            log.info("Updated Order: {}", order);
            return true;
        }
    }
    ```

  - SpringbucksApplication.java

    ```java
    package geektime.spring.springbucks;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeOrder;
    import geektime.spring.springbucks.model.OrderState;
    import geektime.spring.springbucks.repository.CoffeeRepository;
    import geektime.spring.springbucks.service.CoffeeOrderService;
    import geektime.spring.springbucks.service.CoffeeService;
    import io.lettuce.core.ReadFrom;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.autoconfigure.data.redis.LettuceClientConfigurationBuilderCustomizer;
    import org.springframework.context.annotation.Bean;
    import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
    import org.springframework.data.redis.connection.RedisConnectionFactory;
    import org.springframework.data.redis.core.RedisTemplate;
    import org.springframework.transaction.annotation.EnableTransactionManagement;
    
    import java.util.Optional;
    
    @Slf4j
    @EnableTransactionManagement
    @SpringBootApplication
    @EnableJpaRepositories
    public class SpringbucksApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeService coffeeService;
    
        public static void main(String[] args) {
            SpringApplication.run(SpringbucksApplication.class, args);
        }
    
        //将特殊的RedisTemplate构造出来
        @Bean
        public RedisTemplate<String, Coffee> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
            RedisTemplate<String, Coffee> template = new RedisTemplate<>();
            template.setConnectionFactory(redisConnectionFactory);
            return template;
        }
    
        //设置从主机上读
        @Bean
        public LettuceClientConfigurationBuilderCustomizer customizer() {
            return builder -> builder.readFrom(ReadFrom.MASTER_PREFERRED);
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            Optional<Coffee> c = coffeeService.findOneCoffee("mocha");
            log.info("Coffee {}", c);
    
            for (int i = 0; i < 5; i++) {
                c = coffeeService.findOneCoffee("mocha");
            }
    
            log.info("Value from Redis: {}", c);
        }
    }
    ```

- Redis-Repository

  - 实体注解
    - @RedisHash
    - @Id
    - @Indexed

- code

  - pom.xml

    ```xml
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-jpa</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-redis</artifactId>
            </dependency>
    
            <dependency>
                <groupId>org.apache.commons</groupId>
                <artifactId>commons-pool2</artifactId>
            </dependency>
            <dependency>
                <groupId>org.joda</groupId>
                <artifactId>joda-money</artifactId>
                <version>1.0.1</version>
            </dependency>
            <dependency>
                <groupId>org.jadira.usertype</groupId>
                <artifactId>usertype.core</artifactId>
                <version>6.0.1.GA</version>
            </dependency>
    
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    ```

  - application.properties

    ```properties
    spring.jpa.hibernate.ddl-auto=none
    spring.jpa.properties.hibernate.show_sql=true
    spring.jpa.properties.hibernate.format_sql=true
    
    spring.redis.host=localhost
    spring.redis.lettuce.pool.maxActive=5
    spring.redis.lettuce.pool.maxIdle=5
    ```

  - schema.sql

    ```sql
    drop table t_coffee if exists;
    drop table t_order if exists;
    drop table t_order_coffee if exists;
    
    create table t_coffee
    (
        id          bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        name        varchar(255),
        price       bigint,
        primary key (id)
    );
    
    create table t_order
    (
        id          bigint auto_increment,
        create_time timestamp,
        update_time timestamp,
        customer    varchar(255),
        state       integer not null,
        primary key (id)
    );
    
    create table t_order_coffee
    (
        coffee_order_id bigint not null,
        items_id        bigint not null
    );
    
    insert into t_coffee (name, price, create_time, update_time)
    values ('espresso', 2000, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('latte', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('capuccino', 2500, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('mocha', 3000, now(), now());
    insert into t_coffee (name, price, create_time, update_time)
    values ('macchiato', 3000, now(), now());
    ```

  - BaseEntity.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.hibernate.annotations.CreationTimestamp;
    import org.hibernate.annotations.UpdateTimestamp;
    
    import javax.persistence.*;
    import java.io.Serializable;
    import java.util.Date;
    
    @MappedSuperclass
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public class BaseEntity implements Serializable {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
    
        @Column(updatable = false)
        @CreationTimestamp
        private Date createTime;
    
        @UpdateTimestamp
        private Date updateTime;
    }
    ```

  - Coffee.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.*;
    import org.hibernate.annotations.Type;
    import org.joda.money.Money;
    
    import javax.persistence.Entity;
    import javax.persistence.Table;
    import java.io.Serializable;
    
    @Entity
    @Table(name = "T_COFFEE")
    @Builder
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    public class Coffee extends BaseEntity implements Serializable {
        private String name;
        @Type(type = "org.jadira.usertype.moneyandcurrency.joda.PersistentMoneyMinorAmount",
                parameters = {@org.hibernate.annotations.Parameter(name = "currencyCode", value = "CNY")})
        private Money price;
    }
    ```

  - CoffeeOrder.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.*;
    
    import javax.persistence.*;
    import java.io.Serializable;
    import java.util.List;
    
    @Entity
    @Table(name = "T_ORDER")
    @Data
    @ToString(callSuper = true)
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public class CoffeeOrder extends BaseEntity implements Serializable {
        private String customer;
        @ManyToMany
        @JoinTable(name = "T_ORDER_COFFEE")
        @OrderBy("id")
        private List<Coffee> items;
        @Enumerated
        @Column(nullable = false)
        private OrderState state;
    }
    ```

  - CoffeeCache.java

    ```java
    package geektime.spring.springbucks.model;
    
    import lombok.AllArgsConstructor;
    import lombok.Builder;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.joda.money.Money;
    import org.springframework.data.annotation.Id;
    import org.springframework.data.redis.core.RedisHash;
    import org.springframework.data.redis.core.index.Indexed;
    
    //设置在Redis中存储的键springbucks-coffee，过期时间为60秒
    @RedisHash(value = "springbucks-coffee",timeToLive = 60)
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public class CoffeeCache {
        @Id
        private Long id;
        //二级索引
        @Indexed
        private String name;
        private Money price;
    }
    ```

  - OrderState.java

    ```java
    package geektime.spring.springbucks.model;
    
    public enum OrderState {
        INIT, PAID, BREWING, BREWED, TAKEN, CANCELLED
    }
    ```

  - BytesToMoneyConverter.java

    ```java
    package geektime.spring.springbucks.converter;
    
    import org.joda.money.CurrencyUnit;
    import org.joda.money.Money;
    import org.springframework.core.convert.converter.Converter;
    
    import java.nio.charset.StandardCharsets;
    
    public class BytesToMoneyConverter implements Converter<byte[], Money> {
        @Override
        public Money convert(byte[] source) {
            String value = new String(source, StandardCharsets.UTF_8);
            return Money.ofMinor(CurrencyUnit.of("CNY"), Long.parseLong(value));
        }
    }
    ```

  - MoneyToBytesConverter.java

    ```java
    package geektime.spring.springbucks.converter;
    
    import org.joda.money.Money;
    import org.springframework.core.convert.converter.Converter;
    
    import java.nio.charset.StandardCharsets;
    
    public class MoneyToBytesConverter implements Converter<Money, byte[]> {
        @Override
        public byte[] convert(Money source) {
            String value = Long.toString(source.getAmountMinorLong());
            return value.getBytes(StandardCharsets.UTF_8);
        }
    }
    ```

  - CoffeeRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.Coffee;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeRepository extends JpaRepository<Coffee, Long> {
    }
    ```

  - CoffeeOrderRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.CoffeeOrder;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface CoffeeOrderRepository extends JpaRepository<CoffeeOrder, Long> {
    }
    ```

  - CoffeeCacheRepository.java

    ```java
    package geektime.spring.springbucks.repository;
    
    import geektime.spring.springbucks.model.CoffeeCache;
    import org.springframework.data.repository.CrudRepository;
    
    import java.util.Optional;
    
    public interface CoffeeCacheRepository extends CrudRepository<CoffeeCache, Long> {
        Optional<CoffeeCache> findOneByName(String name);
    }
    ```

  - CoffeeService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeCache;
    import geektime.spring.springbucks.repository.CoffeeCacheRepository;
    import geektime.spring.springbucks.repository.CoffeeRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.data.domain.Example;
    import org.springframework.data.domain.ExampleMatcher;
    import org.springframework.stereotype.Service;
    
    import java.util.List;
    import java.util.Optional;
    
    import static org.springframework.data.domain.ExampleMatcher.GenericPropertyMatchers.exact;
    
    @Slf4j
    @Service
    public class CoffeeService {
    
        @Autowired
        private CoffeeCacheRepository cacheRepository;
    
        @Autowired
        private CoffeeRepository coffeeRepository;
    
        public List<Coffee> findAllCoffee() {
            return coffeeRepository.findAll();
        }
    
        public Optional<Coffee> findSimpleCoffeeFromCache(String name) {
            Optional<CoffeeCache> cached = cacheRepository.findOneByName(name);
            //缓存中有数据
            if (cached.isPresent()) {
                //直接获取缓存中的数据
                CoffeeCache coffeeCache = cached.get();
                //构造咖啡对象用于返回
                Coffee coffee = Coffee.builder()
                        .name(coffeeCache.getName())
                        .price(coffeeCache.getPrice())
                        .build();
                log.info("Coffee {} found in cache.", coffeeCache);
                return Optional.of(coffee);
            } else {
                //如果缓存中没有数据直接从数据库中查询
                Optional<Coffee> raw = findOneCoffee(name);
                //查询到的结果存在的话，直接将数据保存到缓存中
                raw.ifPresent(c -> {
                    //构造一个缓存对象
                    CoffeeCache coffeeCache = CoffeeCache.builder()
                            .id(c.getId())
                            .name(c.getName())
                            .price(c.getPrice())
                            .build();
                    log.info("Save Coffee {} to cache.", coffeeCache);
                    cacheRepository.save(coffeeCache);
                });
                return raw;
            }
        }
    
        public Optional<Coffee> findOneCoffee(String name) {
            ExampleMatcher matcher = ExampleMatcher.matching()
                    .withMatcher("name", exact().ignoreCase());
            Optional<Coffee> coffee = coffeeRepository.findOne(
                    Example.of(Coffee.builder().name(name).build(), matcher));
            log.info("Coffee Found: {}", coffee);
            return coffee;
        }
    }
    ```

  - CoffeeOrderService.java

    ```java
    package geektime.spring.springbucks.service;
    
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeOrder;
    import geektime.spring.springbucks.model.OrderState;
    import geektime.spring.springbucks.repository.CoffeeOrderRepository;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    import javax.transaction.Transactional;
    import java.util.ArrayList;
    import java.util.Arrays;
    
    @Slf4j
    @Service
    @Transactional
    public class CoffeeOrderService {
    
        @Autowired
        private CoffeeOrderRepository orderRepository;
    
        public CoffeeOrder createOrder(String customer, Coffee... coffee){
            CoffeeOrder order = CoffeeOrder.builder()
                    .customer(customer)
                    .items(new ArrayList<>(Arrays.asList(coffee)))
                    .state(OrderState.INIT)
                    .build();
            CoffeeOrder saved = orderRepository.save(order);
            log.info("New Order: {}", saved);
            return saved;
        }
    
        public boolean updateState(CoffeeOrder order, OrderState state) {
            if (state.compareTo(order.getState()) <= 0){
                log.warn("Wrong State order: {}, {}", state, order.getState());
                return false;
            }
            order.setState(state);
            orderRepository.save(order);
            log.info("Updated Order: {}", order);
            return true;
        }
    }
    ```

  - SpringbucksApplication.java

    ```java
    package geektime.spring.springbucks;
    
    import geektime.spring.springbucks.converter.BytesToMoneyConverter;
    import geektime.spring.springbucks.converter.MoneyToBytesConverter;
    import geektime.spring.springbucks.model.Coffee;
    import geektime.spring.springbucks.model.CoffeeOrder;
    import geektime.spring.springbucks.model.OrderState;
    import geektime.spring.springbucks.repository.CoffeeRepository;
    import geektime.spring.springbucks.service.CoffeeOrderService;
    import geektime.spring.springbucks.service.CoffeeService;
    import io.lettuce.core.ReadFrom;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.ApplicationArguments;
    import org.springframework.boot.ApplicationRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.autoconfigure.data.redis.LettuceClientConfigurationBuilderCustomizer;
    import org.springframework.context.annotation.Bean;
    import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
    import org.springframework.data.redis.core.convert.RedisCustomConversions;
    import org.springframework.data.redis.repository.configuration.EnableRedisRepositories;
    import org.springframework.transaction.annotation.EnableTransactionManagement;
    
    import java.nio.Buffer;
    import java.util.Arrays;
    import java.util.Optional;
    
    @Slf4j
    @EnableTransactionManagement
    @SpringBootApplication
    @EnableJpaRepositories
    @EnableRedisRepositories
    public class SpringbucksApplication implements ApplicationRunner {
    
        @Autowired
        private CoffeeService coffeeService;
    
        //总主机中读取数据
        @Bean
        public LettuceClientConfigurationBuilderCustomizer customizer() {
            return builder -> builder.readFrom(ReadFrom.MASTER_PREFERRED);
        }
    
        //将自定义的类型转换的类传入到容器中
        @Bean
        public RedisCustomConversions redisCustomConversions() {
            return new RedisCustomConversions(Arrays.asList(new MoneyToBytesConverter(), new BytesToMoneyConverter()));
        }
    
        public static void main(String[] args) {
            SpringApplication.run(SpringbucksApplication.class, args);
        }
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            Optional<Coffee> c = coffeeService.findSimpleCoffeeFromCache("mocha");
            log.info("Coffee {}", c);
    
            for (int i = 0; i < 5; i++) {
                c = coffeeService.findSimpleCoffeeFromCache("mocha");
            }
    
            log.info("Value from Redis: {}", c);
        }
    }
    ```

  - 如何区分这些Repository

    - 根据实体的注解
    - 根据继承的接口类型
    - 扫描不同的包

## 第四章：数据方法进阶

### 1.Project Reactor介绍

- 在计算机中，响应式编程或反应式编程（英语： Reactive Programming）是⼀一种⾯面向数据流和变化传播的编程范式。这意味着可以在编程语⾔言中**很⽅方便便地表达静态或动态的数据流**，⽽而相关的计算模型会⾃自动将变化的值通过数据流进⾏行行传播。 

- 举例：在Excel表中，计算一列的和，当其中一行的数据改变的时候，这一列的和就发生了变化，这就是响应式编程。

- 一些核心的概念：

  - Operators - Publisher / Subscriber（发布者/订阅者）

    - Nothing Happens Until You subscribe()（在subscribe方法之前不会发生任何事情）
    - Flux [ 0..N ] - onNext()、 onComplete()、 onError() （n个元素的序列，在遇到下一个元素时执行方法，在遇到完成的时候该执行的方法，在遇到错误的时该执行的方法）
    - Mono [ 0..1 ] - onNext()、 onComplete()、 onError() （1个元素的序列，在遇到下一个元素时执行方法，在遇到完成的时候该执行的方法，在遇到错误的时该执行的方法）

  - Backpressure（反压力，生产过快，订阅者可以少点）

    - Subscription
    - onRequest()、 onCancel()、 onDispose() （每次订阅的人数，可以取消订阅，终止订阅）

  - 线程调度 Schedulers

    - immediate() / single() / newSingle() （在当前线程上做，独占一个线程，新起一个线程）
    -  elastic() / parallel() / newParallel() （缓存线程池（空闲60秒后被回收），创建和CPU核数一样的线程不会被回收，创建一个新的线程出来）

  - 错误处理理

    -  onError / onErrorReturn / onErrorResume （onError(try catch),onErrorReturn遇到异常返回一个特定的值，OnErrorResume用lambda处理异常）
    -  doOnError / doFinally （doOnError,doFinally出现异常也会finally处理）

  - code:

    - pom.xml

      ```xml
          <dependencies>
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter</artifactId>
              </dependency>
      
              <dependency>
                  <groupId>org.projectlombok</groupId>
                  <artifactId>lombok</artifactId>
                  <optional>true</optional>
              </dependency>
      		<!--引入projectreactor jar-->
              <dependency>
                  <groupId>io.projectreactor</groupId>
                  <artifactId>reactor-core</artifactId>
              </dependency>
      
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-test</artifactId>
                  <scope>test</scope>
              </dependency>
          </dependencies>
      ```

    - SimpleReactorDemoApplication.java

      ```java
      package geektime.spring.reactor.simplereactordemo;
      
      import lombok.extern.slf4j.Slf4j;
      import org.springframework.boot.ApplicationArguments;
      import org.springframework.boot.ApplicationRunner;
      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import reactor.core.publisher.Flux;
      import reactor.core.publisher.Mono;
      import reactor.core.scheduler.Schedulers;
      
      @SpringBootApplication
      @Slf4j
      public class SimpleReactorDemoApplication implements ApplicationRunner {
      
          public static void main(String[] args) {
              SpringApplication.run(SimpleReactorDemoApplication.class, args);
          }
      
          @Override
          public void run(ApplicationArguments args) throws Exception {
              //产生1-6的序列
              Flux.range(1, 6)
                      //订阅的时候执行
                      .doOnRequest(n -> log.info("Request {} number", n)) //注意顺序造成的影响
                      //放入那个线程池中
      //                .publishOn(Schedulers.elastic())
                      //创建完成的时候执行
                      .doOnComplete(() -> log.info("Publisher COMPLETE 1"))
                      //map方法中可以实现类型的转换
                      .map(i -> {
                          log.info("Publish {}, {}", Thread.currentThread(), i);
                          //return 10 / (i - 3);
                          return i;
                      })
                      //订阅完成后执行
                      .doOnComplete(() -> log.info("Publisher COMPLETE 2"))
                      //放入那个线程池
      //                .subscribeOn(Schedulers.single())
                      //异常处理
      //                .onErrorResume(e -> {
      //                    log.error("Exception {}", e.toString());
      //                    return Mono.just(-1);
      //                })
                      //异常不处理，直接返回一个值
      //                .onErrorReturn(-1)
                      //在调用该方法之前，所有的操作都是未执行，只是一个简单的封装
                      .subscribe(i -> log.info("Subscribe {} : {}", Thread.currentThread(), i),
                              e -> log.error("error {}", e.toString()),
                              //方法执行完了调用
                              () -> log.info("Subscriber COMPLETE")//,
      //                        s -> s.request(4)
                      );
              Thread.sleep(2000);
          }
      }
      ```

      

