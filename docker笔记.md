# Docker使用笔记

# 常见命令使用

- `docker pull [images]`
  - 获取镜像
- `docker run -it ubuntu /bin/bash`
  - 以命令行模式进入容器
  - `-i`**interact交互式操作**
  - `-t`**terminal终端**
  - `-d`**后台运行**
  - `-P`容器内部使用的网络端口随机映射到我们使用的主机
  - `-p`连接指定的端口
  - `-network`后面加网络名连接上指定网络
  - `--name`命名容器
  - `ubuntu`images 镜像
  - `/bin/bash`命令行
- `exit`
  - 退出
- `docker ps -a `
  - 查看所有的容器
- `docker attach[exec]`
  - 进入容器，exec进入容器退出以后容器不会关闭
- `docker expoer [images id] > local file`
  - 导出容器

- `docker rm -f images id`
  - 删除容器

- `docker stop images name`
- `docker statr images name`

- `docker top images name`
  - 查看容器内的进程
- `docker logs -f [ID or names]`
  - 像使用tail -f 一样来输出容器内部的标准输出
- `docker images`
  - 列取本地主机的镜像
- `docker search`
  - 搜索镜像
- `docker rmi name`
  - 删除镜像
- `docker commit -m "comment" -a "author" id name`
  - 从已有的容器中更新镜像，并且提交这个镜像
  - `-m`描述信息
  - `-a`作者
  - `id`容器ID
  - `name`创建的目标镜像名
- `docker build`
  - 从一个**Dockerfile**中构建一个镜像
- `docker tag id tag`
  - 为镜像添加一个新的标签
  - `id`镜像id
  - `tag`镜像的标签
- `docker network create -d bridge name`
  - `-d`指定Docker网络类型,bridge / overlay

