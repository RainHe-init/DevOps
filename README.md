# 项目规划
## ip地址规划和硬件配置
#### 172.16.120.11 k8s-master
2核cpu，4G内存，50G硬盘，不拆分
#### 172.16.120.22 k8s-node01,jenkins-agent01
2核cpu，4G内存，60G硬盘，不拆分
#### 172.16.120.33 k8s-node02,jenkins-agent02
2核cpu，4G内存，60G硬盘，不拆分
#### 172.16.120.44 harbor,jenkins-master
2核cpu，4G内存，100G硬盘，不拆分
#### 172.16.120.55 ansible,git,rancher
2核cpu，4G内存，50G硬盘，不拆分
#### 81.70.88.52 postgresql,mongodb
1核cpu，2G内存，50G硬盘，1M带宽，云上数据库