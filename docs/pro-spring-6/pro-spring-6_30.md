# datasource config
spring:
datasource:
driverClassName: org.mariadb.jdbc.Driver
url: jdbc:mariadb://localhost:3306/musicdb?useSSL=false
username: prospring6
password: prospring6
hikari:
maximum-pool-size: 25
jpa:
generate-ddl: false
properties:
hibernate:
jdbc:
batch_size: 10
show-sql: true
format-sql: false
use_sql_comments: false
hbm2ddl:
auto: none
