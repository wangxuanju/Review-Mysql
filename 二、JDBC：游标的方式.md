过滤条件太弱，一次读入过多的信息；
# 场景二：使用游标
海量数据的读取过程中，极有可能产生溢出，因为JVM有内存大小限制；所以每次读入一部分数据，处理完后处理另一部分数据...；
这种处理方式成为游标，而JDBC支持游标；游标提供一种客户端读取部分服务器端结果集的机制；每一批数据称为FetchSize；

1.开启游标；

2.PreparedStatement有一个SetFetchSize()接口；
```java
//使用游标读取数据
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class JDBC {
	static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
	static String DB_URL = "jdbc:mysql://localhost:3306/imooc1";//此次final去掉了
	static final String USER = "root";
	static final String PASSWORD = "wang";
	public static void helloword() throws ClassNotFoundException {
		Connection conn = null;
		PreparedStatement ptmt = null;
		ResultSet rs = null;
		//1加载驱动程序
		Class.forName(JDBC_DRIVER);
		//2建立数据库连接
		try {
			conn = DriverManager.getConnection(DB_URL,USER,PASSWORD);
			DB_URL = DB_URL + "userCursorFetch = true";//增加游标
			//3执行SQL语句
			ptmt = conn.prepareStatement("select user_name from imooc_goddess");
			ptmt.setFetchSize(1);//每次读入一条记录；结束后再读另一条记录
			rs = ptmt.executeQuery();
			//4获取执行结果
			while(rs.next()){
				System.out.println("hello"+rs.getString("user_name"));
			}
		} catch (SQLException e) {
			// 异常处理
			e.printStackTrace();
		}finally{
			//5清理环境
				try {
					if(conn!=null)
					conn.close();
					if(ptmt!=null)
						ptmt.close();
					if(rs!=null)
						rs.close();
				} catch (SQLException e) {
					// ignore
				}
			
			
		}
		
	}

	public static void main(String[] args) throws ClassNotFoundException {	
		helloword();
	}
}
```
