# 获取数据库连接

## 方式一

~~~java
import org.testng.annotations.Test;
import java.sql.Connection;
import java.sql.Driver;
import java.sql.SQLException;
import java.util.Properties;

public class ConnectionTest {
    @Test
    public void connection1() throws SQLException {
        Driver driver = new com.mysql.jdbc.Driver();

        String s = "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8";
        //将用户名封装在Properties中
        Properties properties = new Properties();
        properties.setProperty("user","root");
        properties.setProperty("password","abc123");
        Connection connect = driver.connect(s, properties);
        System.out.println(connect);
    }
}
~~~

## 方式二

对于方式一：不出现第三方API，使程序具有可移植性。

~~~java
import org.testng.annotations.Test;
import java.sql.Connection;
import java.sql.Driver;
import java.util.Properties;

public class ConnectionTest {
    @Test
    public void connection2() throws Exception {
        Class clazz = Class.forName("com.mysql.jdbc.Driver");
        Driver driver  = (Driver) clazz.newInstance();

        String s = "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8";
        //将用户名封装在Properties中
        Properties properties = new Properties();
        properties.setProperty("user","root");
        properties.setProperty("password","abc123");
        Connection connect = driver.connect(s, properties);
        System.out.println(connect);
    }
}
~~~

## 方式三

使用DriverManager替换Driver。

~~~java
import org.testng.annotations.Test;
import java.sql.Connection;
import java.sql.Driver;
import java.sql.DriverManager;

public class ConnectionTest {
    @Test
    public void connection3() throws Exception {
        //1.获取Driver实现类的对象。
        Class clazz = Class.forName("com.mysql.jdbc.Driver");
        Driver driver  = (Driver) clazz.newInstance();
        
        //2.提供另外三个连接的基本信息。
        String url = "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8";
        String user = "root";
        String password = "abc123";
        
        //3.注册驱动。
        DriverManager.registerDriver(driver);
        
        //4.获取连接
        Connection connection = DriverManager.getConnection(url, user, password);
        System.out.println(connection);

    }
}
~~~

## 方式四

较方式三：只是加载驱动，省略注册驱动，在Driver声明了注册驱动的操作。

~~~java
import org.testng.annotations.Test;
import java.sql.Connection;
import java.sql.Driver;
import java.sql.DriverManager;

public class ConnectionTest {
    @Test
    public void connection4() throws Exception {
        //1.获取Driver实现类的对象。
        Class clazz = Class.forName("com.mysql.jdbc.Driver");
        Driver driver  = (Driver) clazz.newInstance();
        
        //2.提供另外三个连接的基本信息。
        String url = "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8";
        String user = "root";
        String password = "abc123";
        
        //3.获取连接
        Connection connection = DriverManager.getConnection(url, user, password);
        System.out.println(connection);
    }
}
~~~

## 方式五

将数据库连接需要的4个基本信息声明在配置文件中，通过读取配置文件的方式，获取连接。

配置文件：

~~~
user=root
password=abc123
url=jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8
driverClass=com.mysql.jdbc.Driver
~~~

~~~java
import org.testng.annotations.Test;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.util.Properties;

public class ConnectionTest {
    @Test
    public void connection5() throws Exception {
        //1.读取配置文件的4个基本信息。
        InputStream is = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");

        Properties pros = new Properties();
        pros.load(is);

        String user = pros.getProperty("user");
        String password = pros.getProperty("password");
        String url = pros.getProperty("url");
        String driverClass = pros.getProperty("driverClass");

        //2.加载驱动
        Class.forName(driverClass);

        //3.获取连接
        Connection connection = DriverManager.getConnection(url, user, password);
        System.out.println(connection);
    }
}
~~~

优势：实现了数据与代码的分离，实现了耦合。

# PreparedStatement的使用

## PreparedStatement实现数据库的添加操作

~~~java
package PreparedStatementTest;

import org.testng.annotations.Test;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Properties;

public class PreparedStatementTest {
    @Test
    public void test() throws Exception {
        //1.获取数据库连接
        InputStream is = PreparedStatementTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
        Properties pros = new Properties();
        pros.load(is);
        String user = pros.getProperty("user");
        String password = pros.getProperty("password");
        String url = pros.getProperty("url");
        String driverClass = pros.getProperty("driverClass");
        Class.forName(driverClass);
        Connection connection = DriverManager.getConnection(url, user, password);
        //2.预编译sql语句，返回PreparedStatement的实例。
        String sql = "insert into customers(name,email,birth)values(?,?,?)";//?为占位符
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        //3.填充占位符。
        preparedStatement.setString(1,"哪吒");
        preparedStatement.setString(2,"nezha@gmail.com");
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        Date date = simpleDateFormat.parse("1000-01-01");
        preparedStatement.setDate(3, new java.sql.Date(date.getTime()));
        //4.执行操作。
        preparedStatement.execute();
        //5.关闭资源，应该用try-catch。
        preparedStatement.close();
        connection.close();
    }
}
~~~

## 封装数据库连接和资源关闭操作

~~~java
package PreparedStatementTest;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.Properties;

public class JDBCutils {
    public static Connection getConnection() throws SQLException, ClassNotFoundException, IOException {
        InputStream is = PreparedStatementTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
        Properties pros = new Properties();
        pros.load(is);
        String user = pros.getProperty("user");
        String password = pros.getProperty("password");
        String url = pros.getProperty("url");
        String driverClass = pros.getProperty("driverClass");
        Class.forName(driverClass);
        Connection connection = DriverManager.getConnection(url, user, password);
        return connection;
    }
    public static void closeResource(Connection connection, PreparedStatement preparedStatement){
        try {
            preparedStatement.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            connection.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
~~~

## PreparedStatement实现数据库的更新操作

~~~java
package PreparedStatementTest;

import org.testng.annotations.Test;
import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class PrepareStatementUpdate {
    @Test
    public void update() {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            connection = JDBCutils.getConnection();
            String s = "update customers set name = ? where id = ?";
            preparedStatement = connection.prepareStatement(s);
            preparedStatement.setObject(1,"莫扎特");
            preparedStatement.setObject(2,18);
            preparedStatement.execute();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            JDBCutils.closeResource(connection,preparedStatement);
        }

    }
}
~~~

## PreparedStatement实现数据库的删除操作

~~~java
package PreparedStatementTest;

import org.testng.annotations.Test;

import java.io.IOException;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class PrepareStatementDetele {
    @Test
    public void test(){
        detele("delete from customers where id = ?",22);
    }

    public static void detele(String s,Object ...args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            connection = JDBCutils.getConnection();
            String sql = s;
            preparedStatement = connection.prepareStatement(s);
            for (Object obj : args) {
                int i = 1;
                preparedStatement.setObject(i,obj);
            }
            preparedStatement.execute();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            JDBCutils.closeResource(connection,preparedStatement);
        }
    }
}
~~~

## PreparedStatement实现数据库的查询操作

~~~java
package PreparedStatementTest;

import org.testng.annotations.Test;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class SelectTest {
    @Test
    public void selectTest(){
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            //获取数据库连接
            Properties properties = new Properties();
            InputStream resourceAsStream = SelectTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
            properties.load(resourceAsStream);
            String user = properties.getProperty("user");
            String password = properties.getProperty("password");
            String url = properties.getProperty("url");
            String driverClass = properties.getProperty("driverClass");
            Class.forName(driverClass);
            connection = DriverManager.getConnection(url, user, password);
            //预编译sql语句，获取PrepareStatement的实例。
            String sql = "select id,name,email,birth from customers where id = ?";
            preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setInt(1,1);
            //执行返回结果集。
            resultSet = preparedStatement.executeQuery();
            //对结果集进行处理。
            while (resultSet.next()){
                int anInt = resultSet.getInt(1);
                String string = resultSet.getString(2);
                String string1 = resultSet.getString(3);
                Date date = resultSet.getDate(4);
                Person person = new Person(anInt, string, string1, date);
                System.out.println(person);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                preparedStatement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        //资源的关闭

    }
}

class  Person{
    private int id;
    private String name;
    private String email;
    private Date birth;

    public Person() {
    }

    public Person(int id, String name, String email, Date birth) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.birth = birth;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Date getBirth() {
        return birth;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", birth=" + birth +
                '}';
    }
}
~~~

查询相关类和方法：

* ResultSet类：通过PreparedStatement类的executeQuery();返回得到一个实例，是查询结果的结果集。
  * next(); 判断下一个指针是否有数据，如果有返回true并且指针下移，如果没有返回false。
  * getMetaData(); 获取结果集的元数据的一个实例：ResultSetMetaData
* ResultSetMetaData类：
  * getColumnName(); 获取结果集对应的列名。
  * getColumnLabel(); 获取结果集对应的别名，如果没有别名返回列名。
  * getColumnCount(); 获取结果集的列数。



# 数据库事务

## 事务的处理原则

* 事务：一组逻辑操作单元（一个或多个DML操作），使数据从一种状态到另一种状态。
* 事务处理原则：保证所有的事务都作为一个工作单元来执行，即使出现了故障，都不能改变这种执行方式。
* 当在一个事务中执行多个操作时，要么所有的数据都提交（commit），这些数据将永久的保存下来；要么数据库管理系统将放弃所做的所有修改，整个事务回滚（rollback）到最初状态；数据一旦提交，就不可回改。
* 以下操作会导致数据自动提交：
  * DDL操作一旦执行，都会自动提交。
  * DML默认情况下，一旦执行，就会自动提交。
    * 可以通过 set autocommit = false;的方式取消DML操作的自动提交。
  * 默认在关闭数据库连接时，会自动的提交数据。

## 考虑数据库事务后的代码执行

Connection类中关于事务的两个方法：

* setAutoCommit(false); 取消DML操作时的自动提交（commit）
* getAutoCommit(); 获取是否设置为自动提交。
* commit; 让数据库进行一次提交操作。
* rollback(); 让数据库进行一次回滚操作。

~~~java
package com.shy.TCL;

import com.shy.PreparedStatement.InsertUpdateDeleteUtils;
import com.shy.PreparedStatement.JDBCutils;
import org.testng.annotations.Test;
import java.sql.Connection;
import java.sql.SQLException;

/**
 * @author shy
 * @create 2022-03-15-15:30
 */
public class TCLTest {
    @Test
    public void tclTest()  {
        //操作user_table表中的AA和BB，让AA给BB转账100.
        Connection connection = null;
        try {
            connection = JDBCutils.getConnection();
            connection.setAutoCommit(false);
            System.out.println(connection.getAutoCommit());
            String sql1 = "update user_table set balance = balance - 100 where user = ?";
            String sql2 = "update user_table set balance = balance + 100 where user = ?";
            InsertUpdateDeleteUtils.util2(connection,sql1,"AA");
            System.out.println(100/0);
            InsertUpdateDeleteUtils.util2(connection,sql2,"BB");
            System.out.println("转账成功");
            connection.commit();
        } catch (Exception e) {
            try {
                connection.rollback();
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
            e.printStackTrace();
        } finally {
            if(connection!=null){
                try {
                    connection.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
~~~

util2方法的具体代码：

~~~java
public static void util2(Connection connection,String s,Object ...args) {
        PreparedStatement preparedStatement = null;
        try {
            String sql = s;
            preparedStatement = connection.prepareStatement(s);
            for (Object obj : args) {
                int i = 1;
                preparedStatement.setObject(i, obj);
            }
            preparedStatement.execute();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCutils.closeConnection(null, preparedStatement);
        }
    }
~~~

## 事务的ACID属性

* A原子性：事务是一个不可分割的工作单位，事务中的操作要么发生，要么不发生。
* C一致性：事务必须使数据库从一个一致性状态换到另外一个一致性状态。
* I隔离性：一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是个例的，并发执行的各个事务之间不能互相干扰。
* D持久性：一个事务一旦被提交，它对数据库中数据的改变就是永久的，接下来的其他操作和数据故障不应该对其有任何影响。

### 数据库的并发问题

- 对于同时运行的多个事务, 当这些事务访问数据库中相同的数据时, 如果没有采取必要的隔离机制, 就会导致各种并发问题:
  - **脏读**: 对于两个事务 T1, T2, T1 读取了已经被 T2 更新但还**没有被提交**的字段。之后, 若 T2 回滚, T1读取的内容就是临时且无效的。
  - **不可重复读**: 对于两个事务T1, T2, T1 读取了一个字段, 然后 T2 **更新**了该字段。之后, T1再次读取同一个字段, 值就不同了。
  - **幻读**: 对于两个事务T1, T2, T1 从一个表中读取了一个字段, 然后 T2 在该表中**插入**了一些新的行。之后, 如果 T1 再次读取同一个表, 就会多出几行。
- **数据库事务的隔离性**: 数据库系统必须具有隔离并发运行各个事务的能力, 使它们不会相互影响, 避免各种并发问题。
- 一个事务与其他事务隔离的程度称为隔离级别。数据库规定了多种事务隔离级别, 不同隔离级别对应不同的干扰程度, **隔离级别越高, 数据一致性就越好, 但并发性越弱。**

### 四种隔离级别

mysql提供的4中事务隔离级别：

![1555586275271](JDBC.assets/1555586275271.png)

mysql默认的隔离级别为：repeatable read

### 在mysql中操作隔离级别

* 查看当前隔离级别：

```mysql
select @@tx_isolation;
```

* 设置当前mysql连接的隔离级别：

```mysql
set transaction isolation level read committed;
```

* 设置数据库系统的全局的隔离级别：

```mysql
set global transaction isolation level read committed;
```

# DAO

* DAO类封装了针对数据表的通用操作。

# 数据库连接池

## C3P0数据库连接池

* 创建配置文件 c3p0-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>

    <named-config name="helloc3p0">
<!-- 提供获取连接的4个基本信息 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:13306/test?useUnicode=true&amp;characterEncoding=utf8</property>
        <property name="user">root</property>
        <property name="password">abc123</property>
<!-- 进行数据库连接池管理的基本信息 -->
        <!--当数据库连接池中连接数不够时，一次申请的连接数-->
        <property name="acquireIncrement">50</property>
        <!--初始化连接的个数-->
        <property name="initialPoolSize">100</property>
        <!--最少连接数-->
        <property name="minPoolSize">50</property>
        <!--最多连接数-->
        <property name="maxPoolSize">1000</property>
        <!--最多维护的Statement数-->
        <property name="maxStatements">50</property>
        <!--每个连接最多可以使用的Statement的个数-->
        <property name="maxStatementsPerConnection">5</property>
    </named-config>
</c3p0-config>
```

```java
package com.shy.JDBC;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import java.sql.Connection;
import java.sql.SQLException;

public class C3P0Test {
    public static void main(String[] args) throws SQLException {
        ComboPooledDataSource cpds = new ComboPooledDataSource("helloc3p0");
        Connection connection = cpds.getConnection();
        System.out.println(connection);
    }
}
```

## DBCP数据库连接池

* dbcp连接池常用基本配置属性
  * initialSize ：连接池启动时创建的初始化连接数量（默认值为0）
  * maxActive ：连接池中可同时连接的最大的连接数（默认值为8，调整为20，高峰单机器在20并发左右，自己根据应用场景定）
  * maxIdle：连接池中最大的空闲的连接数，超过的空闲连接将被释放，如果设置为负数表示不限制（默认为8个，maxIdle不能设置太小，因为假如在高负载的情况下，连接的打开时间比关闭的时间快，会引起连接池中idle的个数 上升超过maxIdle，而造成频繁的连接销毁和创建，类似于jvm参数中的Xmx设置)
  * minIdle：连接池中最小的空闲的连接数，低于这个数量会被创建新的连接（默认为0，调整为5，该参数越接近maxIdle，性能越好，因为连接的创建和销毁，都是需要消耗资源的；但是不能太大，因为在机器很空闲的时候，也会创建低于minidle个数的连接，类似于jvm参数中的Xmn设置）
  * maxWait  ：最大等待时间，当没有可用连接时，连接池等待连接释放的最大时间，超过该时间限制会抛出异常，如果设置-1表示无限等待（默认为无限，调整为60000ms，避免因线程池不够用，而导致请求被无限制挂起）
  * poolPreparedStatements：开启池的prepared（默认是false，未调整，经过测试，开启后的性能没有关闭的好。）
  * maxOpenPreparedStatements：开启池的prepared 后的同时最大连接数（默认无限制，同上，未配置）
  * minEvictableIdleTimeMillis  ：连接池中连接，在时间段内一直空闲， 被逐出连接池的时间
  * removeAbandonedTimeout  ：超过时间限制，回收没有用(废弃)的连接（默认为 300秒，调整为180）
  * removeAbandoned  ：超过removeAbandonedTimeout时间后，是否进 行没用连接（废弃）的回收（默认为false，调整为true)

* 创建配置文件

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:13306/test?useUnicode=true&amp;characterEncoding=utf8
username=root
password=abc123

initialSize=10
```

* 创建数据库连接池

```java
package com.shy.JDBC;

import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.sql.Connection;
import java.util.Properties;

public class DBCPTest {
    public static void main(String[] args) throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\dbcp.properties"));
        DataSource dataSource = BasicDataSourceFactory.createDataSource(properties);
        Connection connection = dataSource.getConnection();
        System.out.println(connection);

    }
}
```

## Druid数据库连接池

- 配置参数

| **配置**                      | **缺省** | **说明**                                                     |
| ----------------------------- | -------- | ------------------------------------------------------------ |
| name                          |          | 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。   如果没有配置，将会生成一个名字，格式是：”DataSource-” +   System.identityHashCode(this) |
| url                           |          | 连接数据库的url，不同数据库不一样。例如：mysql :   jdbc:mysql://10.20.153.104:3306/druid2      oracle :   jdbc:oracle:thin:@10.20.149.85:1521:ocnauto |
| username                      |          | 连接数据库的用户名                                           |
| password                      |          | 连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用ConfigFilter。详细看这里：<https://github.com/alibaba/druid/wiki/%E4%BD%BF%E7%94%A8ConfigFilter> |
| driverClassName               |          | 根据url自动识别   这一项可配可不配，如果不配置druid会根据url自动识别dbType，然后选择相应的driverClassName(建议配置下) |
| initialSize                   | 0        | 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时 |
| maxActive                     | 8        | 最大连接池数量                                               |
| maxIdle                       | 8        | 已经不再使用，配置了也没效果                                 |
| minIdle                       |          | 最小连接池数量                                               |
| maxWait                       |          | 获取连接时最大等待时间，单位毫秒。配置了maxWait之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置useUnfairLock属性为true使用非公平锁。 |
| poolPreparedStatements        | false    | 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。 |
| maxOpenPreparedStatements     | -1       | 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100 |
| validationQuery               |          | 用来检测连接是否有效的sql，要求是一个查询语句。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会其作用。 |
| testOnBorrow                  | true     | 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。 |
| testOnReturn                  | false    | 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能 |
| testWhileIdle                 | false    | 建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。 |
| timeBetweenEvictionRunsMillis |          | 有两个含义： 1)Destroy线程会检测连接的间隔时间2)testWhileIdle的判断依据，详细看testWhileIdle属性的说明 |
| numTestsPerEvictionRun        |          | 不再使用，一个DruidDataSource只支持一个EvictionRun           |
| minEvictableIdleTimeMillis    |          |                                                              |
| connectionInitSqls            |          | 物理连接初始化的时候执行的sql                                |
| exceptionSorter               |          | 根据dbType自动识别   当数据库抛出一些不可恢复的异常时，抛弃连接 |
| filters                       |          | 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有：   监控统计用的filter:stat日志用的filter:log4j防御sql注入的filter:wall |
| proxyFilters                  |          | 类型是List，如果同时配置了filters和proxyFilters，是组合关系，并非替换关系 |

* 创建配置文件

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:13306/test?useUnicode=true&amp;characterEncoding=utf8
username=root
password=abc123

initialSize=5
```

* 创建数据库连接池

```java
package com.shy.JDBC;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.sql.Connection;
import java.util.Properties;

public class DruidTest {
    public static void main(String[] args) throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));

        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
    }
}
```

# 使用DButils实现对数据库的CRUD操作

* 增删改

```java
package com.shy.JDBC;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.apache.commons.dbutils.QueryRunner;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

public class QueryRunnerTest {
    public static void main(String[] args) {
        Connection connection = null;
        try {
            Properties properties = new Properties();
            properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));

            DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
            connection = dataSource.getConnection();
            //对数据库test的插入操作：
            QueryRunner queryRunner = new QueryRunner();
            String sql = "insert into customers(name,email,birth)values(?,?,?)";
            int update = queryRunner.update(connection, sql, "caixukun", "caixukun@123.com", "1999-01-01");
            System.out.println("添加了"+update+"条记录");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if(connection!=null){
                try {
                    connection.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

* 查询

  * BeanHander:是ResultSetHandler接口的实现类，用于封装表中的一条记录。

  ```java
  package com.shy.JDBC;
  
  import com.alibaba.druid.pool.DruidDataSourceFactory;
  import org.apache.commons.dbutils.QueryRunner;
  import org.apache.commons.dbutils.handlers.BeanHandler;
  
  import javax.sql.DataSource;
  import java.io.FileInputStream;
  import java.sql.Connection;
  import java.util.Properties;
  
  public class QueryRunnerTest {
      public static void main(String[] args) throws Exception {
          Properties properties = new Properties();
          properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));
  
          DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
          Connection connection = dataSource.getConnection();
          //对数据库test的插入操作：
          QueryRunner queryRunner = new QueryRunner();
          BeanHandler<Customer> handler = new BeanHandler<>(Customer.class);
          String sql = "select id,name,email,birth from customers where id = ?";
          Customer customer = queryRunner.query(connection, sql, handler,10);
          System.out.println(customer);
          connection.close();
      }
  }
  ```

  * BeanListHandler：是ResultSetHandler接口的实现类，用于封装表中的多条记录构成的集合。

  ```java
  package com.shy.JDBC;
  
  import com.alibaba.druid.pool.DruidDataSourceFactory;
  import org.apache.commons.dbutils.QueryRunner;
  import org.apache.commons.dbutils.handlers.BeanListHandler;
  
  import javax.sql.DataSource;
  import java.io.FileInputStream;
  import java.sql.Connection;
  import java.util.Iterator;
  import java.util.List;
  import java.util.Properties;
  
  public class QueryRunnerTest {
      public static void main(String[] args) throws Exception {
          Properties properties = new Properties();
          properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));
  
          DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
          Connection connection = dataSource.getConnection();
          //对数据库test的插入操作：
          QueryRunner queryRunner = new QueryRunner();
          BeanListHandler<Customer> beanListHandlerHandler = new BeanListHandler<>(Customer.class);
          String sql = "select id,name,email,birth from customers where id < ?";
          List<Customer> query = queryRunner.query(connection, sql, beanListHandlerHandler, 10);
          Iterator<Customer> iterator = query.iterator();
          while (iterator.hasNext()){
              System.out.println(iterator.next());
          }
          connection.close();
      }
  }
  ```

  

# JDBCTemplate的简单使用

使用：

* 创建JDBCTemplate的对象，需要一个数据库连接池DataSource作为参数：
  * JdbcTemplate template = new JdbcTemplate(ds);
* 调用JdbcTemplate的方法来实现CRUD操作。
  * update(); 实现增删改操作。
  * queryForMap(); 查询结果封装为Map集合，结果集长度只能是一。
  * queryForList(); 查询结果的每一条结果封装为一个Map集合，将这些Map集合封装在List集合中。
  * query(); 查询结果封装为javabean对象。
  * queryForObject(); 查询结果封装为对象,一般查询聚合函数。

实现增删改操作：

```java
package com.shy.jdbctemplate;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.springframework.jdbc.core.JdbcTemplate;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.util.Properties;

public class JdbcTemplateTest {
    public static void main(String[] args) throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));

        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        String sql = "insert into user(name,password,address,phone)values(?,?,?,?)";
        jdbcTemplate.update(sql,"caixukun",123456,"shanghai",2131241213);

    }
}
```

* 查询

```java
package com.shy.jdbctemplate;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.springframework.jdbc.core.JdbcTemplate;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.util.Iterator;
import java.util.List;
import java.util.Map;
import java.util.Properties;

public class JdbcTemplateTest {
    public static void main(String[] args) throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));

        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        String sql = "select * from user";
        List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql);
        Iterator<Map<String, Object>> iterator = maps.iterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }
    }
}
```

```java
package com.shy.jdbctemplate;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.util.Iterator;
import java.util.List;
import java.util.Properties;

public class JdbcTemplateTest {
    public static void main(String[] args) throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));

        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        String sql = "select * from user";
        List<user> query = jdbcTemplate.query(sql, new BeanPropertyRowMapper<user>(user.class));
        Iterator<user> iterator = query.iterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }

    }
}

class user{
    private int id;
    private String name;
    private String password;
    private String address;
    private String phone;

    public user() {
    }

    public user(int id, String name, String password, String address, String phone) {
        this.id = id;
        this.name = name;
        this.password = password;
        this.address = address;
        this.phone = phone;
    }

    @Override
    public String toString() {
        return "user{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
                ", address='" + address + '\'' +
                ", phone='" + phone + '\'' +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }
}
```

```java
package com.shy.jdbctemplate;

import com.alibaba.druid.pool.DruidDataSourceFactory;
import org.springframework.jdbc.core.JdbcTemplate;
import javax.sql.DataSource;
import java.io.FileInputStream;
import java.util.Properties;

public class JdbcTemplateTest {
    public static void main(String[] args) throws Exception {
        Properties properties = new Properties();
        properties.load(new FileInputStream("D:\\code\\JDBC\\pool\\src\\druid.properties"));

        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
        JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
        String sql = "select count(*) from user";
        Long aLong = jdbcTemplate.queryForObject(sql, long.class);
        System.out.println(aLong);

    }
}
```

