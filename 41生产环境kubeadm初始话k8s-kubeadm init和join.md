### 用kubeadm初始化master节点和node节点
#### 1、在k8s-master01上，创建kubeadm-config.yaml文件
```shell
[root@k8s-master01 ~]# vim kubeadm-config.yaml
```
```yaml
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: v1.20.6		#k8s版本设置
controlPlaneEndpoint: 172.16.120.100:16443	  #vip地址+端口，如果是单节点，写控制节点ip
imageRepository: registry.aliyuncs.com/google_containers		#镜像源是阿里云的
apiServer:
 certSANs:
 - 172.16.120.101
 - 172.16.120.102
 - 172.16.120.111
 - 172.16.120.100
networking:
  podSubnet: 10.244.0.0/16
  serviceSubnet: 10.10.0.0/16
---
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind:  KubeProxyConfiguration
mode: ipvs
```
#### 2、在k8s-master01上用kubeadm init初始化
```shell
[root@k8s-master01 ~]# kubeadm init --config kubeadm-config.yaml
```
#### 3、在k8s-master01上，配置kubectl的配置文件config，相当于对kubectl进行授权，这样kubectl命令可以使用这个证书对k8s集群进行管理
```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
```shell
mkdir -p $HOME/.kube && sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config && sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
#### 4、扩容master节点
##### 4.1 在master01上，把k8s-master01节点的证书拷贝到k8s-master02上
```shell
[root@k8s-master02 ~]# cd /root && mkdir -p /etc/kubernetes/pki/etcd &&mkdir -p ~/.kube/
```
```shell
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/ca.crt k8s-master02:/etc/kubernetes/pki/
ca.crt                                                                     100% 1066   973.2KB/s   00:00    
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/ca.key k8s-master02:/etc/kubernetes/pki/
ca.key                                                                     100% 1675     1.7MB/s   00:00    
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/sa.key k8s-master02:/etc/kubernetes/pki/
sa.key                                                                     100% 1679     1.6MB/s   00:00    
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/sa.pub k8s-master02:/etc/kubernetes/pki/
sa.pub                                                                     100%  451   590.1KB/s   00:00    
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/front-proxy-ca.crt k8s-master02:/etc/kubernetes/pki/
front-proxy-ca.crt                                                         100% 1078     1.4MB/s   00:00    
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/front-proxy-ca.key k8s-master02:/etc/kubernetes/pki/
front-proxy-ca.key                                                         100% 1675     1.5MB/s   00:00    
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/etcd/ca.crt k8s-master02:/etc/kubernetes/pki/etcd/
ca.crt                                                                     100% 1058   854.4KB/s   00:00    
[root@k8s-master01 ~]# scp /etc/kubernetes/pki/etcd/ca.key k8s-master02:/etc/kubernetes/pki/etcd/
ca.key                                                                     100% 1679     1.5MB/s   00:00    
[root@k8s-master01 ~]# 
```
##### 4.2 拷贝完成之后，在master01节点，刷新token
```shell
[root@k8s-master01 ~]# kubeadm token create --print-join-command
kubeadm join 172.16.120.100:16443 --token ij6pzz.coon8qsx8aniftq9     --discovery-token-ca-cert-hash sha256:46c42bc063dc6c3c6c15100fdd8784a64ed31616ecaf6aef497acd79f6fbda9e 
```
##### 4.3 刷新token之后生成的命令，在master02上输入，后面加控制节点后缀即可
```shell
[root@k8s-master02 ~]# kubeadm join 172.16.120.100:16443 --token ij6pzz.coon8qsx8aniftq9     --discovery-token-ca-cert-hash sha256:46c42bc063dc6c3c6c15100fdd8784a64ed31616ecaf6aef497acd79f6fbda9e --control-plane
```
##### 4.4 在master01上，查看nodes
```shell
[root@k8s-master01 ~]# kubectl get nodes
NAME           STATUS     ROLES                  AGE    VERSION
k8s-master01   NotReady   control-plane,master   112m   v1.20.6
k8s-master02   NotReady   control-plane,master   26s    v1.20.6
```
#### 5、给k8s集群扩容node节点
##### 5.1 在master01节点，刷新token
```shell
[root@k8s-master01 ~]# kubeadm token create --print-join-command
kubeadm join 172.16.120.100:16443 --token wuptwt.z3bfetx22fd9lk8x     --discovery-token-ca-cert-hash sha256:46c42bc063dc6c3c6c15100fdd8784a64ed31616ecaf6aef497acd79f6fbda9e 
```
##### 5.2 在node节点上，输入master01刷新的token
```shell
[root@k8s-node01 ~]# kubeadm join 172.16.120.100:16443 --token wuptwt.z3bfetx22fd9lk8x     --discovery-token-ca-cert-hash sha256:46c42bc063dc6c3c6c15100fdd8784a64ed31616ecaf6aef497acd79f6fbda9e 
```
##### 5.3 在master01上查看node几点加入情况
```shell
[root@k8s-master01 ~]# kubectl get nodes                        
NAME           STATUS     ROLES                  AGE    VERSION
k8s-master01   NotReady   control-plane,master   114m   v1.20.6
k8s-master02   NotReady   control-plane,master   3m5s   v1.20.6
k8s-node01     NotReady   <none>                 6s     v1.20.6
```
##### 5.4 修改node节点的角色
```shell
[root@k8s-master01 ~]# kubectl label node k8s-node01 node-role.kubernetes.io/worker=worker
node/k8s-node01 labeled
[root@k8s-master01 ~]# kubectl get nodes
NAME           STATUS     ROLES                  AGE    VERSION
k8s-master01   NotReady   control-plane,master   115m   v1.20.6
k8s-master02   NotReady   control-plane,master   4m9s   v1.20.6
k8s-node01     NotReady   worker                 70s    v1.20.6
```
#### 6、安装calico网络插件
##### 6.1 calico的作用
```shell
1 可以做网络策略
2 分配规定的pod的ip地址(此项目是10.244.0.0/16)
```
##### 6.2 在master01下载calico的yaml文件
```shell
[root@k8s-master01 ~]# wget  https://docs.projectcalico.org/manifests/calico.yaml
```
##### 6.3 安装calico插件
```shell
[root@k8s-master01 ~]# kubectl apply -f  calico.yaml
```
##### 6.4 安装成功之后，再查看pod
```shell
[root@k8s-master01 ~]# kubectl get pod -A -o wide
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE     IP               NODE           NOMINATED NODE   READINESS GATES
kube-system   calico-kube-controllers-5f6cfd688c-2vkwl   1/1     Running   0          8m6s    10.244.32.130    k8s-master01   <none>           <none>
kube-system   calico-node-7n4hs                          1/1     Running   0          8m6s    172.16.120.101   k8s-master01   <none>           <none>
kube-system   calico-node-jrchv                          1/1     Running   0          8m6s    172.16.120.111   k8s-node01     <none>           <none>
kube-system   calico-node-thdbn                          1/1     Running   0          8m6s    172.16.120.102   k8s-master02   <none>           <none>
kube-system   coredns-7f89b7bc75-gr6hc                   1/1     Running   0          3h43m   10.244.32.129    k8s-master01   <none>           <none>
kube-system   coredns-7f89b7bc75-s4tpb                   1/1     Running   0          3h43m   10.244.85.193    k8s-node01     <none>           <none>
kube-system   etcd-k8s-master01                          1/1     Running   0          3h43m   172.16.120.101   k8s-master01   <none>           <none>
kube-system   etcd-k8s-master02                          1/1     Running   0          111m    172.16.120.102   k8s-master02   <none>           <none>
kube-system   kube-apiserver-k8s-master01                1/1     Running   0          3h43m   172.16.120.101   k8s-master01   <none>           <none>
kube-system   kube-apiserver-k8s-master02                1/1     Running   0          111m    172.16.120.102   k8s-master02   <none>           <none>
kube-system   kube-controller-manager-k8s-master01       1/1     Running   1          3h43m   172.16.120.101   k8s-master01   <none>           <none>
kube-system   kube-controller-manager-k8s-master02       1/1     Running   1          111m    172.16.120.102   k8s-master02   <none>           <none>
kube-system   kube-proxy-2b4cx                           1/1     Running   0          111m    172.16.120.102   k8s-master02   <none>           <none>
kube-system   kube-proxy-n8vqr                           1/1     Running   0          3h43m   172.16.120.101   k8s-master01   <none>           <none>
kube-system   kube-proxy-p8rn8                           1/1     Running   0          108m    172.16.120.111   k8s-node01     <none>           <none>
kube-system   kube-scheduler-k8s-master01                1/1     Running   1          3h43m   172.16.120.101   k8s-master01   <none>           <none>
kube-system   kube-scheduler-k8s-master02                1/1     Running   1          111m    172.16.120.102   k8s-master02   <none>           <none>
```