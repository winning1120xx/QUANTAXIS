
# QUANTAXIS DOCKER

## 安装docker 

ubuntu 一键脚本

```
wget https://raw.githubusercontent.com/QUANTAXIS/QUANTAXIS/master/config/install_docker.sh
sudo bash install_docker.sh
```
win/mac 安装

非常简单 去docker网站下载win/mac的docker_desktop

文件较大, 我在群文件也共享了



ps: quantaxis强烈推荐不要使用win10以下的系统...(好吧忽略我)


### 使用QA_SERVICE

第一次使用
```
wget https://raw.githubusercontent.com/QUANTAXIS/QUANTAXIS/master/docker/qaservice_docker.sh
sudo bash qaservice_docker.sh
```

后续使用

```
docker-compose up -d
```

端口:

- 27017 mongodb
- 8888 jupyter
- 8010 quantaxis_webserver
- 80 quantaxis_community 社区版界面
- 61208 系统监控
- 15672 qa-eventmq


然后就可以开始你的量化之路了骚年!



## 第一次部署：
1. 下载 [docker-compose.yaml](https://raw.githubusercontent.com/QUANTAXIS/QUANTAXIS/master/docker/qa-service/docker-compose.yaml)
2. 创建 docker volume  
  ```
  docker volume create qamg
  docker volume create qacode
  ```
3. 启动 QUANTAXIS 服务 （包括 QUANTAXIS-jupyter，自动更新服务，数据库，web，每次启动docker，所有服务都会自动启动）  
  ```
  docker-compose up -d
  ```

## 查看每天数据更新日志：
docker logs cron容器名  

日志只输出到容器前台，如果日志对你很重要，建议用专业的日志收集工具，从cron容器收集日志

## 查看服务状态
```
docker ps

docker stats

docker-compose top

docker-compose ps
```

## 停止/删除 QUANTAXIS 服务 （包括 QUANTAXIS，自动更新服务，数据库容器）：

停止：  
```
docker-compose stop
```
删除：  
```
docker-compose rm （只删除容器，不会删除数据）
```

## 更新：
1. 删除容器和镜像  
```
docker-compose down --rmi all
```  
2. 重新下载并启动容器  
```
docker-compose up -d
```

## 数据库备份(备份到宿主机当前目录，文件名：dbbackup.tar)：

1. 停止服务  
```
docker-compose stop
```

2. 备份到当前目录
```
docker run  --rm -v qamg:/data/db \
-v $(pwd):/backup alpine \
tar zcvf /backup/dbbackup.tar /data/db
```

## 数据库还原（宿主机当前目录下必要有以前备份过的文件，文件名：dbbackup.tar）：
1. 停止服务  
```
docker-compose stop
```

2. 还原当前目录下的dbbackup.tar到mongod数据库  
```
docker run  --rm -v qamg:/data/db \
-v $(pwd):/backup alpine \
sh -c "cd /data/db \
&& rm -rf diagnostic.data \
&& rm -rf journal \
&& rm -rf configdb \
&& cd / \
&& tar xvf /backup/dbbackup.tar"
```

3. 重新启动服务
```
docker-compose up -d
```
