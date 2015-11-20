#Spring with Oracle
##설정파일
###OracleConfiguration.java
```java
import oracle.jdbc.pool.OracleDataSource;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import javax.sql.DataSource;
import java.sql.SQLException;

@Configuration
@ConfigurationProperties("oracle")
public class OracleConfiguration {
    private String username;
    private String password;
    private String url;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    @Bean
    DataSource dataSource() throws SQLException {
        OracleDataSource dataSource = new OracleDataSource();
        dataSource.setUser(username);
        dataSource.setPassword(password);
        dataSource.setURL(url);
        dataSource.setImplicitCachingEnabled(true);
        dataSource.setFastConnectionFailoverEnabled(true);
        return dataSource;
    }
}
```
####@ConfigurationProperties("prefix")
properties 파일에서 prefix._{property name}_을 key로 하여 value를 매핑
###application.properties
```
#Oracle connection
oracle.username={user name}
oracle.password={password}
oracle.url=jdbc:oracle:thin:@//{host:port}/{database}
```
####Hibernate로 JPA구현 하는 데 Oracle 사용
```
//For hibernate
spring.jpa.database-platform=org.hibernate.dialect.Oracle10gDialect
```