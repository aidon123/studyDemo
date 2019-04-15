#这里记录restfuldemo搭建过程

1：Transational事务配置记录
1-1：主启动文件添加@EnableTransactionManagement注解开启事务
1-2：对应service的方法上添加@Transational注解（一般推荐在service.方法上）
事务不起作用记得检查MySQL的引擎是否是innnoDB，方法是否public，异常是否是运行时异常or error。

2：多环境配置
添加application-xxx.yml配置文件，在主application.yml添加spring.profiles.active(这里可以${}动态读取开发工具的变量)
例如idea可以直接在启动配置里添加--DEPLOY=dev变量

3：集成mybatis
3-1：pom文件添加mybatis-spring-boot-starter依赖jar
3-2：启动类添加@MapperScan("com.example.restdemo.mapper")//此处添加注解后dao接口无需再添加@mapper（该注解不需要xml文件可以直接方法上写sql） @repository注解
3-3：#mybatis配置。通常，若mybatis配置信息较少，只是针对基本配置无需复杂配置，则只需在application.yml文件中配置即可，否则最好配置在 mybatis-config.xml中
    mybatis:
      typeAliasesPackage: com.example.restdemo.entity
      mapperLocations: classpath:mapper/*.xml
      #configLocation: classpath:/mybatis-config.xml
3-4：配置文件添加数据源配置
    spring:
      #数据源
      datasource:
        url: jdbc:mysql://localhost:3306/test??useUnicode=true&amp;characterEncoding=UTF-8
        username: root
        password:
        driver-class-name: com.mysql.jdbc.Driver
 
 4：添加logback.xml日志记录
 添加依赖jar包
 <!-- lombok依赖-->
<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok</artifactId>
</dependency>
<!--Slf4j 依赖-->
<dependency>
   <groupId>org.slf4j</groupId>
   <artifactId>jcl-over-slf4j</artifactId>
</dependency>
<!-- logback 依赖 是slf4j的实现-->
<dependency>
   <groupId>ch.qos.logback</groupId>
   <artifactId>logback-classic</artifactId>
</dependency>
使用类添加@Slf4j注解，idea添加lombok插件可以直接log.使用
 配置文件添加 #控制台打印sql
     logging:
         level:
           com:
             example:
               restdemo:
                 mapper: DEBUG

5:配置德鲁伊数据源
5-1：添加依赖
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.18</version>
</dependency>
5-2：配置文件添加参数
#数据源
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=UTF-8
    username: root
    password:
    driverClassName: com.mysql.jdbc.Driver
  ###################以下为druid增加的配置###########################
    type: com.alibaba.druid.pool.DruidDataSource
  # 下面为连接池的补充设置，应用到上面所有数据源中
  # 初始化大小，最小，最大
    initialSize: 5
    minIdle: 5
    maxActive: 20
  # 配置获取连接等待超时的时间
    maxWait: 60000
  # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
    timeBetweenEvictionRunsMillis: 60000
  # 配置一个连接在池中最小生存的时间，单位是毫秒
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
  # 打开PSCache，并且指定每个连接上PSCache的大小
    poolPreparedStatements: true
    maxPoolPreparedStatementPerConnectionSize: 20
  # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
    filters: stat,wall
  # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
  # 合并多个DruidDataSource的监控数据
  #spring.datasource.useGlobalDataSourceStat: true
  ###############以上为配置druid添加的配置########################################
5-3:添加DruidDBConfig配置文件和DruidConfig配置文件