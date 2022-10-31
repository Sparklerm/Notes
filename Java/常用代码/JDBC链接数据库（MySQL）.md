# JDBC 连接数据库（MySQL）

## 准备测试数据库

```sql
/*
SQLyog Ultimate v13.1.1 (64 bit)
MySQL - 5.7.25-log : Database - demo
*********************************************************************
*/

/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`demo` /*!40100 DEFAULT CHARACTER SET latin1 */;

USE `demo`;

/*Table structure for table `demo_table` */

DROP TABLE IF EXISTS `demo_table`;

CREATE TABLE `demo_table` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) CHARACTER SET latin1 DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

INSERT INTO demo_table(`name`,`age`) VALUES('Mark',14),('Bob',33),('Alex',21);

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

```

## 创建数据库连接对象

```java
package com.demo;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.sql.DataSource;

import com.mysql.cj.jdbc.MysqlDataSource;

/**
 * 以单例的方式创建数据库链接
 * 
 * @author Sparkler
 *
 */
public class DataSourceUtil {
	
	
	/**
	 * jdbc:mysql : 表示用JDBC连接MySQL数据库;
	 * 127.0.0.1 : 表示本机服务器默认地址(localhost也可以);
	 * 3306 : MySQL默认端口号，端口号可以修改;
	 * demo : 数据库名字;
	 */
	private static final String URL = "jdbc:mysql://127.0.0.1:3306/demo?characterEncoding=utf8&useSSL=false&serverTimezone=UTC";
	/**
	 * 数据库登录账号
	 */
	private static final String USERNAME = "root";
	/**
	 * 数据库登录密码
	 */
	private static final String PASSWORD = "123456";

	private static volatile DataSource dataSource = null;

	public static DataSource getDataSource() {
		if (dataSource == null) {
			synchronized (DataSourceUtil.class) {
				if (dataSource == null) {
					// 加载JDBC数据库驱动
					dataSource = new MysqlDataSource();
					// 设置数据库属性
					((MysqlDataSource) dataSource).setURL(URL);
					((MysqlDataSource) dataSource).setUser(USERNAME);
					((MysqlDataSource) dataSource).setPassword(PASSWORD);
				}
			}
		}
		return dataSource;
	}

	/**
	 * 获取数据库链接
	 * 
	 * 数据库连接失败是很常见的问题. 如果失败, 后续的操作肯定也是失败的. 如果发现 Connect 为 null, 就说明数据库连接失败,
	 * 就需要查看错误信息(Tomcat 的日志) 常见的问题就是, url, username, password 等信息写错了, 或者数据库没有启动.
	 * 
	 * @return
	 */
	public static Connection getConnection() {
		try {
			return getDataSource().getConnection();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		System.out.println("数据库连接失败, 请检查数据库是否启动正确, url 是否正确");
		return null;
	}

	/**
	 * 关闭数据库链接
	 * 
	 * @param connection
	 * @param statement
	 * @param resultSet
	 */
	public static void close(Connection connection, PreparedStatement statement, ResultSet resultSet) {
		try {
			if (resultSet != null) {
				resultSet.close();
			}
			if (statement != null) {
				statement.close();
			}
			if (connection != null) {
				connection.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

}
```

## 使用数据库连接操作数据库

```java
package com.demo;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

/**
 * 通过数据库链接操作数据库
 * 
 * @author Sparkler
 *
 */
public class ExecuteSQL {

	/**
	 * 获取数据库信息
	 */
	private static Connection connection = DataSourceUtil.getConnection();

	/**
	 * 查询数据库
	 * 
	 * @throws SQLException
	 */
	private static ResultSet query(String sql) throws SQLException {
		Statement statement = connection.createStatement();
		return statement.executeQuery(sql);
	}

	/**
	 * 修改数据库 DML语句 insert,update,delete
	 * 
	 * @throws SQLException
	 */
	private static Integer update(String sql) throws SQLException {
		Statement statement = connection.createStatement();
		return statement.executeUpdate(sql);
	}

	public static void main(String[] args) {
		try {
			String querySQL = "SELECT `id`,`name`,`age` FROM demo_table";
			ResultSet resultSet = query(querySQL);
			while (resultSet.next()) {
				System.out.println("Id : " + resultSet.getInt("id") + " , Name : " + resultSet.getString("name")
						+ " , Age : " + resultSet.getInt("age"));
			}

			String updateSQL = "UPDATE demo_table SET `age` = 88 WHERE name = 'Bob'";
			Integer result = update(updateSQL);
			if (result > -1) {
				System.out.println("Update Successful");
				String query2 = "SELECT `id`,`name`,`age` FROM demo_table WHERE `name` = 'Bob'";
				ResultSet queryUpdateResult = query(query2);
				while (queryUpdateResult.next()) {
					System.out.println("Id : " + queryUpdateResult.getInt("id") + " , Name : "
							+ queryUpdateResult.getString("name") + " , Age : " + queryUpdateResult.getInt("age"));
				}
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
```

