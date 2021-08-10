# 项目规划
## 1、ip地址规划和硬件配置
#### 172.16.120.11 k8s-master
2核cpu，4G内存，50G硬盘，不拆分
#### 172.16.120.22 k8s-node01,jenkins-agent01
2核cpu，4G内存，60G硬盘，不拆分
#### 172.16.120.33 k8s-node02,jenkins-agent02
2核cpu，4G内存，60G硬盘，不拆分
#### 172.16.120.44 harbor,jenkins-master
2核cpu，4G内存，100G硬盘，不拆分
#### 172.16.120.55 ansible,gitlab
2核cpu，4G内存，50G硬盘，不拆分
#### 172.16.120.66 rancher
4核cpu，4G内存，20G硬盘，不拆分
#### 81.70.88.52 postgresql,mongodb
1核cpu，2G内存，50G硬盘，1M带宽，云上数据库
#### 172.16.120.77 k8s-master-binary
6核cpu，8G内存，50G硬盘，不拆分
#### 172.16.120.88 k8s-node-binary
6核cpu，8G内存，50G硬盘，不拆分

## 2、域名解析
```shell script
172.16.120.55 gitlab.liuxiang.com
```


## 3、端口暴露
```shell script
gitlab的http连接:
    172.16.120.55:13800 gitlab.liuxiang.com
gitlab的ssh连接
    172.16.120.55:13822 
jenkins:
    172.16.120.44:8880
harbor:
    172.16.120.44:80  harbor.liuxing.com
rancher:
    http://172.16.120.66
```
## 4、内部用户和密码
```shell script
gitlab的root用户:
    用户名:root
    密码:ZH....88
gitlab的普通用户:
    用户名:liuxiang
    密码:ZH...88
harbor的root用户:
    用户名:admin
    密码:Harbor12345
Jenkins的root用户:
    用户名:admin
    密码:123
rancher:
    用户名:admin
    密码:123
```