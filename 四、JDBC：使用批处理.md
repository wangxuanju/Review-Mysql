# 场景四：批处理
将大量的数据录入到数据库中，会有大量的数据需要插入；通过批处理方式，插入一次SQL就可以插入多条数据；
用Statement方法中的是三个函数：addBatch()（打包成单元）、executeBatch()（加入到batch中）、clearBatch()（清空batch中的语句，准备下次执行）
```java
//使用批处理插入数据
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.HashSet;
import java.util.Set;

public class JDBC3 {
	static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
	static final String DB_URL = "jdbc:mysql://localhost:3306/imooc1";
	static final String USER = "root";
	static final String PASSWORD = "wang";
	public static void insertUsers(Set<String> users) throws ClassNotFoundException {
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		//1.加载驱动程序
		Class.forName(JDBC_DRIVER);
		//2.建立数据库连接
		try {
			conn = DriverManager.getConnection(DB_URL,USER,PASSWORD);
			//3执行SQL语句
			stmt = conn.createStatement();
			//4.获取执行结果
			for(String user:users){
				stmt.addBatch("insert into user(useName) values('" + user + "')");
			}
			stmt.executeBatch();
			stmt.clearBatch();//清空
		} catch (SQLException e) {
			// 异常处理
			e.printStackTrace();
		}finally{
			//5.清理环境
				try {
					if(conn!=null)
					    conn.close();
					if(stmt!=null)
						stmt.close();
					if(rs!=null)
						rs.close();
				} catch (SQLException e) {
					// ignore
				}
			
			
		}
		
	}

	public static void main(String[] args) throws ClassNotFoundException {	
		Set<String> users = new HashSet<String>();
		users.add("guo");
		users.add("wang");
		insertUsers(users);
	}
}
```
