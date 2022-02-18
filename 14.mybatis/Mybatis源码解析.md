# Mybatis源码解析



+ SqlSessionFactoryBuilder:构件sqlSessionFactory，主要是解析xml
+ DefaultSqlSessionFactory：SqlSessionFactory的接口实现方式，创建sqlSession
+ 构造MapperRegistry，将配置文件的mapper类放到MapperRegistry构件MapperProxyFactory中
+ 生成Mapper代理
  + 通过MapperRegistry找到接口的代理工厂，由代理工厂创建Mapper代理
  + MapperProxyFactory创建实例化动态代理。引用MethodInvoker。


## DataSource

+ 在xml时，会根据xml的配置文件解析datasource

```java
 private DataSourceFactory dataSourceElement(XNode context) throws Exception {
    if (context != null) {
      String type = context.getStringAttribute("type");
      Properties props = context.getChildrenAsProperties();
      DataSourceFactory factory = (DataSourceFactory) resolveClass(type).getDeclaredConstructor().newInstance();
      factory.setProperties(props);
      return factory;
    }
    throw new BuilderException("Environment declaration requires a DataSourceFactory.");
  }
```

### 自定义连接池

```java
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="datasource.DBCPDataSourceFactory">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/blog_db?useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
                <property name="maxIdle" value="20"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

```java
package datasource;

import lombok.Data;
import org.apache.commons.dbcp2.BasicDataSource;
import org.apache.ibatis.datasource.DataSourceFactory;

import javax.sql.DataSource;
import java.util.Properties;

@Data
public class DBCPDataSourceFactory implements DataSourceFactory {

    private String username;
    private String password;
    private String driver;
    private String url;
    private int initialSize = 6;
    private int maxIdle = 8;
    private int minIdle = 6;

    @Override
    public void setProperties(Properties props) {
        username = props.getProperty("username");
        password = props.getProperty("password");
        driver = props.getProperty("driver");
        url = props.getProperty("url");

        initialSize = Integer.valueOf(props.getProperty("initialSize", ""+initialSize));
        maxIdle = Integer.valueOf(props.getProperty("maxIdle", ""+maxIdle));
        minIdle = Integer.valueOf(props.getProperty("minIdle", ""+minIdle));
    }

    @Override
    public DataSource getDataSource() {
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        dataSource.setUrl(url);
        dataSource.setDriverClassName(driver);
        dataSource.setInitialSize(initialSize);
        dataSource.setMaxIdle(maxIdle);
        dataSource.setMinIdle(minIdle);
        return dataSource;
    }

}
```



[framework-learning/Mybatis源码分析.md at dev · guang19/framework-learning (github.com)](https://github.com/guang19/framework-learning/blob/dev/orm-learning/Mybatis源码分析.md)

[MyBatis: 自定义连接池 - 乐天笔记 (letianbiji.com)](https://www.letianbiji.com/mybatis/mybatis-custom-conn-pool.html)
