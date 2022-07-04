# Mysql 开启 binlog

0. 检查当期 mysql 配置
```sql
# 查看当前mysql是否开启binlog
show variables like '%log_bin%';

# 查看当期 binlog 类型
show variables like 'binlog_format';
```

## 修改mysql配置
mysql 8.0 默认开启 binlog 默认 `binlog_format=ROW`
1. 修改my.cnf(/etc/mysql/mysql.conf.d/mysqld.cnf)
```cnf
[mysqld]
...
## 当前为默认值
log-bin=/var/lib/mysql/binlog
binlog_format=ROW
```

2. 重启服务
```bash
# 使用 systemd 管理的 mysqld 重启
systemctl restart mysql.service
```

3. 登录mysql 查看是否开启
```sql
show variables like '%log_bin%';
```
