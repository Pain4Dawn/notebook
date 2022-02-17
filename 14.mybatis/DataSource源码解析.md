# Mybatis源码解析



+ SqlSessionFactoryBuilder:构件sqlSessionFactory，主要是解析xml
+ DefaultSqlSessionFactory：SqlSessionFactory的接口实现方式，创建sqlSession

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



