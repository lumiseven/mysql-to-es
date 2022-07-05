# 集群机器分配说明

| IP | hostname |
| --- | --- |
| 172.16.x.x (外网ip 121.199.36.88) | hzconsole(console) |
| 172.16.3.236 | hadoop0 |
| 172.16.3.237 | hadoop1 |
| 172.16.3.238 | hadoop2 |

| server | console | hadoop0 | hadoop1 | hadoop2 |
| -- | -- | -- | -- | -- |
| 外网访问 | &check; | | | |
| nginx | &check; | | | |
| mysql | &check; | | | |
| es(master&data) | | &check; | &check; | &check; |
| canal(deployer adapter admin) | | | &check; | |
| maxwell | | | &check; | |
