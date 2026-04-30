# JPA 配置
jpa:
generate-ddl: false
properties:
hibernate:
naming:
physical-strategy:
jdbc:
batch_size: 10
fetch_size: 30
max_fetch_depth: 3
show-sql: true
format-sql: false
use_sql_comments: false
hbm2ddl:
auto: none
