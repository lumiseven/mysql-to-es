# Mysql
mysql 考虑用作测试，直接搭建单机mysql服务器，使用系统包管理工具下载安装并启动 不再赘述操作

```bash
# 安装
apt install mysqld

# 启动
systemctl start mysql

# 查看
systemctl status mysql
```

# ElasticSearch
es存在分区和副本的概念 为模拟正式环境 考虑使用集群环境搭建 使用3台服务器的es集群
host: es0 es1 es2

1. 下载es 后解压
    https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.1-linux-x86_64.tar.gz 
2. 创建用户
    安全问题,Elasticsearch 不允许 root 用户直接运行,所以要在每个节点中创建新用
户

```bash
useradd es #新增 es 用户
passwd es #为 es 用户设置密码
userdel -r es #如果错了,可以删除再加
chown -R es:es /opt/module/es #文件夹所有者

### 当期测试集群存在用户 hadoop 考虑直接使用该用户
```

3. 修改 `elasticsearch.yml` 分发

```yaml
# 加入如下配置
#集群名称
cluster.name: cluster-es-1
#节点名称,每个节点的名称不能重复
node.name: es-0
#ip 地址,每个节点的地址不能重复
network.host: hadoop0
#是不是有资格主节点 es8使用 node.roles
# node.master: true
# node.data: true
node.roles: [ master, data ]
http.port: 9200
# head 插件需要这打开这两个配置
http.cors.allow-origin: "*"
http.cors.enabled: true
http.max_content_length: 200mb
#es7.x 之后新增的配置,初始化一个新的集群时需要此配置来选举 master
cluster.initial_master_nodes: ["es-2"]
#es7.x 之后新增的配置,节点发现
discovery.seed_hosts: ["hadoop0:9300","hadoop1:9300","hadoop2:9300"]
# gateway.recover_after_nodes: 2 es8 使用 gateway.recover_after_data_nodes
gateway.recover_after_data_nodes: 2
network.tcp.keep_alive: true
network.tcp.no_delay: true
# transport.tcp.compress: true # es8使用 transport.compress
transport.compress: true
#集群内同时启动的数据任务个数,默认是 2 个
cluster.routing.allocation.cluster_concurrent_rebalance: 2
#添加或删除节点及负载均衡时并发恢复的线程个数,默认 4 个
cluster.routing.allocation.node_concurrent_recoveries: 4
#初始化数据恢复时,并发恢复线程的个数,默认 4 个
cluster.routing.allocation.node_initial_primaries_recoveries: 4
xpack.security.enabled: false
xpack.security.transport.ssl.enabled: false
#屏蔽自动下载geopip
ingest.geoip.downloader.enabled: false
```

可以选择配置jvm参数调整es占用资源多少
```bash
## 限制 es 内存2g
echo $'-Xms2g\n-Xmx2g' > /opt/module/es/config/jvm.options.d/2gb.options
```

配置系统资源使用上限，需要根据系统配置相关项:
`/etc/security/limits.conf`: 
```conf
# 在文件末尾中增加下面内容
es soft nofile 65536
es hard nofile 65536
```

`/etc/security/limits.d/20-nproc.conf`: 
```conf
# 在文件末尾中增加下面内容
es soft nofile 65536
es hard nofile 65536
* hard nproc 4096
# 注:* 带表 Linux 所有用户名称
```

`/etc/sysctl.conf`: 
```conf
# 在文件中增加下面内容
vm.max_map_count=655360
```

4. 重新加载 `sysctl -p`
5. 启动
```bash
cd /opt/module/es
#启动
bin/elasticsearch
#后台启动
bin/elasticsearch -d
```

6. 测试集群
```bash
# curl 访问es 集群基本信息
curl -X GET hadoop0:9200/_cat/nodes
# 172.16.3.237  6 65 3 0.09 0.60 0.38 dm - es-1
# 172.16.3.236  7 69 4 0.04 0.43 0.33 dm - es-0
# 172.16.3.238 10 76 7 0.11 0.77 0.85 dm * es-2
```