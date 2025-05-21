---
date: 2024-10-09 18:10
tags:
  - DB
  - Connection
  - JDBC
  - Java
  - 커넥션풀
aliases:
  - DB 연결
  - JDBC 커넥션
  - DB Connection
  - Connection
---

# DB 커넥션

**DB에서의 커넥션**은 애플리케이션 서버와 DB 간의 **논리적 연결**을 의미합니다. 커넥션을 통해 애플리케이션 서버는 DB에 쿼리를 전달하고, 그 결과를 받아오며, **INSERT**, **UPDATE**, **DELETE** 등의 작업을 수행할 수 있습니다.

Java에서는 **JDBC**(Java Database Connectivity)라는 라이브러리를 사용하여 DB와 통신하고, 커넥션을 맺고 끊습니다.

---

## 예제 코드

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;

public class DatabaseConnectionExample {

    private static final String JDBC_URL = "jdbc:mysql://localhost:3306/mydatabase";
    private static final String DB_USER = "root";
    private static final String DB_PASSWORD = "password";

    public static void main(String[] args) {
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn = DriverManager.getConnection(JDBC_URL, DB_USER, DB_PASSWORD);
            stmt = conn.createStatement();
            String sql = "SELECT id, name FROM users";
            rs = stmt.executeQuery(sql);

            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("ID: " + id + ", Name: " + name);
            }

        } catch (SQLException | ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close(); // 커넥션 끊기
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

---

## 커넥션 자원의 관리

커넥션은 **DB에서 관리하는 유한한 자원**이므로, **사용이 끝난 후 반드시 자원을 반환**(즉, 커넥션을 닫는 작업)을 해주어야 합니다. 이를 소홀히 할 경우, 커넥션이 계속해서 열려 있는 상태로 남아 자원이 고갈될 수 있습니다.

> [!danger] Error
> `java.sql.SQLException: Too many connections`  
> 👉 자세한 원인은 [[MySQL Too Many Connections 에러]] 문서를 참고하세요.

---

## 커넥션 부족 시 발생할 수 있는 문제

- 새로운 요청이 와도 커넥션 풀이 비어 있으면 **대기 상태** 발생
- 애플리케이션의 **응답 지연** 또는 **시스템 장애**
- DB 연결 자원이 고갈되면 심각한 **성능 저하**

---

## 관련 문서

- [[MySQL Too Many Connections 에러]]
- [[Connection Pool]]
- [[JDBC]]
