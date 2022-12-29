---
title: Docker修改daemon.json后无法启动的问题
date: 2018-08-30 09:05
tag: 
keywords: docker, daemon.json
description: 本文介绍如何解决docker无法启动的错误。
---

> 本文的运行环境为Centos 7.3，Docker与Kubernetes的安装方式见[kubeadm安装kubernetes V1.11.1 集群](https://www.cnblogs.com/cocowool/p/kubeadm_install_kubernetes.html)。想要了解更多 Kubernetes 相关知识，可以阅读 [Kubernetes 系列学习文章](http://www.edulinks.cn/2020/10/16/20201016-kubernetes-articles/)。

最近在整理Docker和Kubernetes中的日志与相关配置，在尝试通过```/etc/docker/daemon.json```配置Docker的```log-driver```参数，遇到了Docker无法启动的错误。
配置信息如下
```javascript
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "1m",
        "max-file": "5"
    }
}
```
错误提示如下
```bash
[root@devops-101 ~]# journalctl -amu  docker
-- Logs begin at Thu 2018-08-30 08:28:53 CST, end at Thu 2018-08-30 08:47:53 CST. --
Aug 30 08:29:07 devops-101 systemd[1]: Starting Docker Application Container Engine...
Aug 30 08:29:08 devops-101 dockerd-current[1102]: unable to configure the Docker daemon with file /etc/docker/daemon.json: the following directives are specified both as a flag and in the configuration file: log-dri
Aug 30 08:29:08 devops-101 systemd[1]: docker.service: main process exited, code=exited, status=1/FAILURE
Aug 30 08:29:08 devops-101 systemd[1]: Failed to start Docker Application Container Engine.
```
这个含义应该是Docker启动的时候传入了命令行参数，同时也指定了配置文件，两个配置发生了冲突。那么就查看一下Docker服务启动文件。
```bash
[root@devops-101 ~]# vim /usr/lib/systemd/system/docker.service 
[Service]
Type=notify
NotifyAccess=all
EnvironmentFile=-/run/containers/registries.conf
EnvironmentFile=-/etc/sysconfig/docker
EnvironmentFile=-/etc/sysconfig/docker-storage
EnvironmentFile=-/etc/sysconfig/docker-network
Environment=GOTRACEBACK=crash
Environment=DOCKER_HTTP_HOST_COMPAT=1
Environment=PATH=/usr/libexec/docker:/usr/bin:/usr/sbin
ExecStart=/usr/bin/dockerd-current \
          --add-runtime docker-runc=/usr/libexec/docker/docker-runc-current \
          --default-runtime=docker-runc \
          --exec-opt native.cgroupdriver=systemd \
          --userland-proxy-path=/usr/libexec/docker/docker-proxy-current \
          --init-path=/usr/libexec/docker/docker-init-current \
          --seccomp-profile=/etc/docker/seccomp.json \
          $OPTIONS \
          $DOCKER_STORAGE_OPTIONS \
          $DOCKER_NETWORK_OPTIONS \
          $ADD_REGISTRY \
          $BLOCK_REGISTRY \
          $INSECURE_REGISTRY \
          $REGISTRIES
```
可以看到启动的时候会从```/etc/sysconfig/docker```中获取环境变量。
继续查看这个配置文件。
```bash
[root@devops-101 ~]# vim /etc/sysconfig/docker
# /etc/sysconfig/docker

# Modify these options if you want to change the way the docker daemon runs
OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false'
if [ -z "${DOCKER_CERT_PATH}" ]; then
    DOCKER_CERT_PATH=/etc/docker
fi
```
可以看到参数中默认了```--log-driver=journald```，把这一段删掉就可以了。重启之后，就可以在```/var/log/containers```下看到容器产生的日志文件了。
```bash
[root@devops-101 containers]# pwd
/var/log/containers
[root@devops-101 containers]# ls 
coredns-78fcdf6894-52gp9_kube-system_coredns-6eea955dfaad8027b9382a9911817057c917e3187dee41502dcd687d8de18729.log
coredns-78fcdf6894-mkvqn_kube-system_coredns-16ed56195ca55c613dec218cfd57c647f8bbe0341b38c446210db98d6ab78f3c.log
etcd-devops-101_kube-system_etcd-42717aa4eda15817f05f35b68f84306ab83fd3f397426c2c9b47d71ebf61942b.log
kube-apiserver-devops-101_kube-system_kube-apiserver-0df17d68a3b1742e9e34620b759b4617f1949bbe2d826b03216419b6994db42c.log
kube-controller-manager-devops-101_kube-system_kube-controller-manager-ee1743e4a4c0114da1cfb32bd78f4f7a576bfc021aacd943e92d2e09cb41116d.log
kube-flannel-ds-7sgps_kube-system_install-cni-92ce6121a1a5456752f502c07ae324676e0d1297261dad266b385a5e6522852c.log
kube-flannel-ds-7sgps_kube-system_kube-flannel-e6eb715062314f175d9fbaa52323f93eeffbb9fa16eed95797c9950884f68b73.log
kube-proxy-79gws_kube-system_kube-proxy-8ce96b3b403d0f7231ca6a004bfec162a093f85ea940867264f50530846d3e0a.log
kube-scheduler-devops-101_kube-system_kube-scheduler-c7ce974eedbc3119320e729310e1b905f65df84dbe46624918dfc33628d9a864.log
```












