##部署数据库
###一、安装docker
####1、指定yum源
```shell script
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.cloud.tencent.com/repo/centos7_base.repo
```
####2、指定docker源
```shell script
wget -O /etc/yum.repos.d/docker-ce.repo https://download.docker.com/linux/centos/docker-ce.repo
```
####3、修改软件仓库地址
```shell script
sudo sed -i 's+download.docker.com+mirrors.cloud.tencent.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```
####5、清理缓存，并重新建立缓存
```shell script
yum clean all
yum makecache
```
####6、查看docker-ce的可安装版本
```shell script
yum list docker-ce --showduplicates |sort -r
```
####7、安装docker-ce
```shell script
yum install -y docker-ce
```
####8、启动docker
```shell script
systemctl start docker && systemctl enable docker && systemctl status docker
```
###二、使用docker安装PostgreSQL数据库
####1、下载
```shell script
docker pull postgres:9.6
```
####2、创建持久卷
```shell script
docker volume create postgres-data
```
####3、启动postgresql:9.6的容器
```shell script
docker run -d --name postgres-server -p 5432:5432 -v postgres-data:/var/lib/postgresql/data -e "POSTGRES_PASSWORD=*********" postgres:9.6
-d:后台运行
-v:指定挂载点,主机挂载点:容器内路径
-e:指定参数
--name:指定名称
-p:指定映射端口,主机端口:容器端口
```

####4、查看容器运行状态
```shell script
docker ps -a 
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                       NAMES
e34902413f55   postgres:9.6   "docker-entrypoint.s…"   8 seconds ago   Up 7 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres-server
```
####5、用Navicat连接测试
