# Aidfinity


This project is built with `Python` and `uni-app`


**Features**

High concurrency support, using Gunicorn + Redis to implement multithreading; supports building images with DockerCompose; frontend UI optimization.

Set the server address on the frontend; optimized UI, adapted for H5 end.

Interface to query the number of online robots~~; added local caching to avoid having to manually enter the account password every time you use it.

Supports logging in with ChatGPT account and password, which can be deployed to a server for multi-user access.




**Usage is as follows:**

## Server side:

Pull this repository to the server (requires internet access, preferably an overseas server, and docker and docker-compose installed)

**For those who haven't installed docker and docker-compose, refer to the following commands**

**Installing docker:**

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

```shell
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
```

```shell
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```shell
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```shell
sudo chmod a+r /etc/apt/keyrings/docker.gpg
sudo apt-get update
```

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

```shell
sudo systemctl enable docker
sudo systemctl start docker
```

Installing docker-compose:

As of the development of this project, the latest version of docker-compose is 2.16.0, which can be viewed here. You can replace the version number below with the latest version.
```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.16.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```shell
sudo chmod +x /usr/local/bin/docker-compose
```



**Pull the source code**

```
git clone https://github.com/Fangnan700/AI-aides.git
```

```shell
cd ./AI-aides/ai_aides(Server)
```



**根据需要修改Dockerfile、docker-compose.yml和gunicorn.conf**

```dockerfile
# Base image
FROM python:3.11

# Working directory
WORKDIR /app

# Install dependencies based on the dependency list
COPY requirements.txt /app/
RUN pip install --upgrade --no-cache-dir -r requirements.txt

# Copy all files into the image
COPY . /app

# Expose port 5000 to the host machine
EXPOSE 5000

# Project start command, using gunicorn
CMD ["gunicorn", "-c", "gunicorn.conf", "app:app"]
```

```yaml
version: "1"
services:
  web:
    build: .
    command: gunicorn -c gunicorn.conf app:app
    volumes:
      - .:/app
    ports:
      - "80:5000"
    depends_on:
      - redis
  redis:
    image: redis:latest
    ports:
      - "6379:6379"

```

Modify ports of web in docker-compose.yml as needed to run the service on the specified port of the host machine.

```conf
# gunicorn.conf

# Number of parallel worker processes
workers = 4
# Number of threads per worker process
threads = 4
# Listen on internal port 5000
bind = '0.0.0.0:5000'
# Set the daemon to false, process is managed by supervisor
daemon = 'false'
# Work mode coroutine
worker_class = 'gevent'
# Set maximum concurrency
worker_connections = 2000
# Set
```

**注意！！！**

如果运行时报错：访问Redis服务被拒绝，请按以下步骤修改`app.py`

1. 在服务器上运行`ifconfig docker0 | grep "inet " | awk '{ print $2 }`查看`docker0`的IP
2. 将`app.py`中的redis连接配置修改为docker0对应的IP

```python
redis_pool = redis.ConnectionPool(host='<your docker0's IP>', port='6379', decode_responses=False)
redis_p = redis.Redis(host='<your docker0's IP>', port=6379, decode_responses=False)
```





**构建镜像**

```shell
sudo docker-compose build
```



**开始运行**

```shell
sudo docker-compose up
```



## App端：

- 拉取本仓库到本地（需要Nodejs环境，HBuilder X编辑器）

- 在服务器设置中配置后端服务器地址。
- 通过 HBuilder X 云打包apk，安装到手机即可。

**注意：**

H5页面由Uniapp直接导出，代码经过混淆，比较难读懂，但只需要修改以下两个地方即可直接部署：

1、`pages-main-login-login.70f92203.js`

```js
// 这里的接口根据自己的服务器来修改
url: "<your server's host and port>/login",
```

2、`pages-main-form-index.5990799a.js`

```js
// 这里的接口根据自己的服务器来修改
url: "<your server's host and port>/chat",
```



## 测试截图：

### App端

![4.jpgCF84A65723D1F60705A304301C188138](https://yvling-typora-image-1257337367.cos.ap-nanjing.myqcloud.com/typora/4.jpgCF84A65723D1F60705A304301C188138.jpg)

![4.jpg4CE37E195003AC37ED73776E8AD19BB6](https://yvling-typora-image-1257337367.cos.ap-nanjing.myqcloud.com/typora/4.jpg4CE37E195003AC37ED73776E8AD19BB6.jpg)

![4.jpgDD388427B495A6C012978E54BDFB718C](https://yvling-typora-image-1257337367.cos.ap-nanjing.myqcloud.com/typora/4.jpgDD388427B495A6C012978E54BDFB718C.jpg)

![4.jpgB65EB95506D04A0B0E0A95AD74DDDBFD](https://yvling-typora-image-1257337367.cos.ap-nanjing.myqcloud.com/typora/4.jpgB65EB95506D04A0B0E0A95AD74DDDBFD.jpg)



### H5端

![image-20230227165724151](https://yvling-typora-image-1257337367.cos.ap-nanjing.myqcloud.com/typora/image-20230227165724151.png)

![image-20230227165736094](https://yvling-typora-image-1257337367.cos.ap-nanjing.myqcloud.com/typora/image-20230227165736094.png)

![image-20230228103155627](https://yvling-typora-image-1257337367.cos.ap-nanjing.myqcloud.com/typora/image-20230228103155627.png)















