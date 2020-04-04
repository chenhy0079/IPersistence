#### 1、Mybatis动态sql是做什么的？都有哪些动态sql？简述一下动态sql的执行原理？

​		传统的使用JDBC的方法，在组合复杂的的SQL语句的时候，需要去拼接，如果拼接错去，如空格，分隔符等写错都会导致错误。Mybatis的动态SQL功能正通过 if, choose, when, otherwise, trim, where, set, foreach标签，可组合成非常灵活的SQL语句，从而提高开发人员的效率。

​		实现原理：通过XMLScriptBuilder解析Mapper中拿到的sql，针对不同的标签节点使用对应的nodeHandler生成XNode对象，如IfSqlNode，TrimSqlNode，StaticTextSqlNode，DynamicSqlSource对解析完成sql进行拼接，并生成BoundSql；

#### 2、Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

​	mybatis支持延迟加载，延迟加载主要功能是在进行多表联查时，对关联表数据先不查询，等到真正调用时才会查询。延迟加载的作用是减少多表联查，提升响应速度。但是延迟加载的作用范围是当前session会话，如果会话结束前为调用关联表数据，之后再调用将获取不到数据。延迟加载是基于动态代理实现的，通过代理连接指定方法，然后进行数据查询。

#### 3、Mybatis都有哪些Executor执行器？它们之间的区别是什么？

​		a.SimpleExecutor 简单执行器，默认执行器，每次执行都会创建新的statement对象，执行后续流程；

​		b.ReuseExecutor 区别于SimpleExecutor ，会将statement对象进行缓存，如果同一个会话中执行相同的	     BoundSql，第二次执行就不会创建新的statement，直接使用第一次创建过的statement。作用是减少sql编译，提升失踪效率，建议生产环境使用。

​		c.BatchExecutor 批处理执行器，针对更新操作会调用statement.addBatch，对更新操作进行缓存，然后批量插入数据库，但是大量插入数据时也会出现性能问题。

​		d.CachingExecutor 直接实现Exceutor接口，当开启二级缓存时使用，先查询缓存数据，然后再调用以上三种执行器执行。

#### 4、简述下Mybatis的一级、二级缓存（分别从存储结构、范围、失效场景。三个方面来作答）？

​		一级缓存：默认开启，数据结构为MAP，缓存范围当前sqlsession会话，会话结束或者会话中执行update操作缓存会被清空；

​		二级缓存：默认关闭，可在mybatis注配置文件中配置开启，数据结构MAP, 缓存范围sqlsessionFactory级别，缓存对象存放在MappedStatement对象中，如果执行update操作，会进行清空

#### 5、简述Mybatis的插件运行原理，以及如何编写一个插件？

​	mybati启动时会把实现Interceptor 接口的类，提取成一个拦截器处理连。当Executor,StatementHandler,ParamterHandler,ResultSetHandler四大接口生成对象，会经过拦截器连进行处理，进行方法增强。

​	mybatis实现插件的方式是继承Interceptor 接口，实现接口的三个方法。连接器实现类添加@Interceptors，指定拦截的接口和方法，最后再mybatis配置文件中通过Plugins标签指定插件实现类全限定名， 当mybatis扫描到实现类时，会通过Plugin 为实现类创建代理对象加入到拦截器处理链；