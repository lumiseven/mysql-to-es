### 在分布式模式下使用Kafka Connect时，我们需要使用REST API创建连接器

```bash
curl -XPOST -H 'Content-type:application/json' 'localhost:8083/connectors' -d '{
    "name" : "second_es_sink",
    "config" : {
        "connector.class" : "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
        "tasks.max" : "1",
        "topics" : "console_mysql_binlog_maxwell", 
        "connection.url" : "http://localhost:9200",
        "key.ignore" : "true",
        "schema.ignore" : "true"
    }
}'
```
