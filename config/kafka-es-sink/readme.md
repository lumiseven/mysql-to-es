### kafka-elasticsearch-connect

将数据从kafka直接放入elasticsearch 但是由于 kafka 中的数据是binlog 其中存在部分逻辑 不能直接放入es，需要进行etl操作 否则不能使用
