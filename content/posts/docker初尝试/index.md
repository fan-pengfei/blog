---
title: "docker初尝试"
date: 2023-03-19T02:27:15.000Z
draft: false
tags: ["docker"]
---
> docker初体验；

### docker常用命令

```plaintext
#docker常用命令:
1. 查看容器:
      docker ps
2. 查看镜像:
      docker images
3. 删除容器:
      docker rm 容器name
4. 删除镜像:
      docker rmi 镜像id
5. 创建容器:
      docker run --name dockermysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=654321 mysql:5.7.23
6. 启动容器:
      docker start 容器name #docker start dockermysql
7. 重启容器:
      docker restart dockermysql
8. 停止容器:
      docker stop dockermysql
9. 容器交互:
      docker exec -it dockermysql bash #或 docker attach dockermysql
10.退出交互:
      Ctrl+P,Ctrl+Q(Ctrl键一直保持按下)
11.设置开机自启:
      systemctl enable docker
12.容器设置自启,update命令:
      docker update --restart=always aeccnginx
13.启动docker
      systemctl start docker
14.重启docker
      systemctl restart docker
```
