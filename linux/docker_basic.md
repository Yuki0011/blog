##docker 容器

一、docker 安装

```
官方文档地址:	https://docs.docker.com/engine/install/centos/   
1.卸载旧的版本
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
2.需要的安装包
$ sudo yum install -y yum-utils
3.设置镜像仓库（阿里云 速度快）
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
4.更新软件包索引
yum makecache fast
5.安装docker  ce 社区版
sudo yum install docker-ce docker-ce-cli containerd.io
6.启动docker
sudo systemctl start docker

docker version  测试是否安装成功
docker images 查看下载的镜像
7.卸载docker
sudo yum remove docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker

8.配置镜像加速（阿里云镜像加速）
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://qwiql8b0.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
二、基本命令

```
docker version
docker info
docker 命令  --help 
镜像
docker images   查看本地所有镜像
	-a  查看所有镜像
	-q  显示所有id
docker search  搜索	
	-filter=STARS=3000 搜索stars 不小于三千
docker pull  下载镜像
	docker pull mysql:5.7   指定版本下载
docke  -rmi -f  容器id    删除指定容器/所有
	docker -rmi -f $(docker images -aq) 删除所有容器
容器
docker run [可选参数] image 启动容器
	--name 制定容器名称
	-d 后台方式运行
	-it 交互方式运行，进入容器查看内容
	-P 指定容器的端口
		—P ip:主机地址：容器端口号
		-P 主机端口号：容器端口（常用）
		-P 容器端口
	-p 指定随机端口
	
docker run it centos /bin/bash   

docker ps
   -a  列出历史运行容器和正在运行的
   -n=? 显示最近创建的容器
   -q   只显示编号
退出容器  
exit  直接停止退出
ctrl+p+q   不停止退出
删除容器 
docker rm 容器id     删除指定id容器，不能删除正在运行的容器
docker rm -f $(docker ps -aq)  删除所有容器
docker ps -a -qlxargs docker rm 删除所有容器

启动和停止容器
docker start 容器id 
docker restart 容器id
docker stop 容器id
	docker kill 容器id

常用其他命令
docker run -d 镜像名
需要一个前台进程否则就会自动关闭
查看日志
docker logs
	-tf  显示日志
	--tail number  显示指定条数的日志
查看容器内部进程信息
docker top 容器id
docker inspect 容器id  容器元数据

进入到正在运行的容器
docker exec -it 容器id /bin/bash   进入容器后开启一个新的终端
docker attach 容器id               进入容器正在执行的终端，不启动新的进程

从容器中拷贝文件到主机上
docker cp 容器id:文件路径  主机路径
```

**三.例子**

部署nginx

```
1.官网或者docker research 查询镜像
2.下载镜像  docker pull nginx
3.启动容器 docker run  -d --name nginx -p 3344:80  nginx
	开启宿主机 3344端口
	1.firewall-cmd --get-active-zones
		如果提示防火墙未开启的话，先开启防火墙
		systemctl start firewalld
	2.firewall-cmd --zone=public --add-port=3344/tcp --permanent
	3.firewall-cmd --reload 重启防火墙
	4.firewall-cmd --query-port=3344/tcp 查看是否开启成功  yes 代表成功
	这一步之后,宿主机的3344端口已经开启，但是执行启动容器的命令会报错
	 (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 	3305 -j DNAT --to-destination 172.18.0.2:80 ! -i docker0: iptables: No 	chain/target/match by that name.
	此时重启一下docker 服务即可解决问题
	sudo systemctl restart docker
4.curl localhost:3344
```
部署tomcat

```
docker run -it --rm tomcat:9.0  用完即删  一般测试用
启动容器报错
The container name "/xxx" is already in use by container ......
查看 所有的容器
docker ps  -a
删除容器 
docker rm 容器id
再启动
docker run -d -p 3304:8080 --name tomcat01 tomcat

cp webapps.dist/* -r webapps/  把 webapps.dist 下的内容复制到 webapps 下
```

部署elasticsearch

```
docker stats 查看cpu 使用状况

设置elasticsearch的内存区间
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xms64m -Xmx512m" -e "discovery.type=single-node" elasticsearch:tag

docker可视化工具portainer
docker volume create portainer_data

docker run -d -p 9000:9000 -p 8000:8000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer

联合文件系统，层级概念

```