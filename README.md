<div align="center">
  <img src="img/fmm_social.jpg">
</div>

Fast Map Matching：基于HMM实现的快速地图匹配算法。

Fork自[原仓库](https://github.com/cyang-kth/fmm)，这里基于ubuntu18.04，python3.6，c++11构建fmm。

---

# 0 介绍
[原仓库](https://github.com/cyang-kth/fmm)只支持python2；
为构建支持python3的fmm，这里修改CMakeLists。

两种安装方式：
- cmake 编译安装
- docker
	- 拉取dockerhub上的fmm
	- 使用Dockerfile构建image

# 1 cmake insatll
1/ Environment
```bash
ubuntu 18.04
c++ 11 # 编译器版本g++ 7,支持c++11以及c++14规范 
```
---
2/ clone this fork repo

```bash
git clone https://github.com/Aoidayo/fmm-py3.git
```
---
3/ Install dependency


3.1// 可以按照我的步骤来：
```bash
apt-get update
apt-get install --fix-missing
apt-get install -y build-essential software-properties-common libboost-dev libboost-serialization-dev libssl-dev cmake vim wget make libbz2-dev libexpat1-dev swig python3-dev
```

注：编译python3版本，故python-dev改为python3-dev。


3.2// 对[官网上的步骤](https://fmm-wiki.github.io/docs/installation/ubuntu.html)稍作修改，亦可以安装依赖：
```bash
# Update the ppa in order to install required version (2.2) of GDAL.
apt update
apt install -y software-properties-common
apt update
add-apt-repository ppa:ubuntugis/ppa
apt-get -q update

# Install all the requirements with
apt-get install libboost-dev libboost-serialization-dev \
gdal-bin libgdal-dev make cmake libbz2-dev libexpat1-dev swig python3-dev
```

---
4/ Install C++ program and Python bindings
```bash
# Under the project folder
mkdir build
cd build
cmake ..
make -j4
sudo make install
```

---

5/ verification
- 键入 `fmm`，输出如下help即install成功。
- 在 `python3` 中 `import fmm`，没有报错即为成功。
```bash
fmm argument lists:
--ubodt (required) <string>: Ubodt file name
--network (required) <string>: Network file name
--gps (required) <string>: GPS file name
--output (required) <string>: Output file name
--network_id (optional) <string>: Network id name (id)
--source (optional) <string>: Network source name (source)
--target (optional) <string>: Network target name (target)
...
```


# 2 docker
1/ 使用修改后的 `docker/Dockerfile` 来build镜像

```dockerfile
FROM ubuntu:18.04
RUN apt-get update && apt-get install --fix-missing && apt-get install -y \
    build-essential software-properties-common\
    libboost-dev libboost-serialization-dev libssl-dev \
    cmake vim\
    wget \
    make libbz2-dev libexpat1-dev swig python3-dev
RUN add-apt-repository -y ppa:ubuntugis/ppa && apt-get -q update
RUN apt-get -y install gdal-bin libgdal-dev
RUN mkdir -p /fmm
COPY . /fmm
WORKDIR /fmm
RUN rm -rf build
RUN mkdir -p build && \
    cd build && \
    cmake .. && \
    make install
EXPOSE 8080
CMD
```

将此 `Dockerfile` 移至fmm根目录使用。

2/ 拉取在 `dockerhub` 上传的镜像
```bash
docker pull aoidayo/fmm-py3-map-matching:latest
```
