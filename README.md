 Docker Images Pusher

使用Github Action将国外的Docker镜像转存到腾讯CNB，供国内服务器使用，免费易用<br>
- 支持DockerHub, gcr.io, k8s.io, ghcr.io等任意仓库<br>
- 支持最大40GB的大型镜像<br>
- 使用腾讯云的官方线路，速度快<br>


modify from 作者：**[技术爬爬虾](https://github.com/tech-shrimp/me)**<br>

## 使用方式


### 配置腾讯云
登录腾讯云容器镜像服务<br>
https://cnb.cool/<br>
启用个人实例，创建一个命名空间（**CNB_NAME_SPACE**）
![](/doc/命名空间.png)

访问凭证–>获取环境变量<br>
用户名（**CNB_REGISTRY_USER**)<br>
密码（**CNB_REGISTRY_PASSWORD**)<br>
仓库地址（**CNB_REGISTRY**）"org/proj" <br>

![](/doc/用户名密码.png)


### Fork本项目
Fork本项目<br>
#### 启动Action
进入您自己的项目，点击Action，启用Github Action功能<br>
#### 配置环境变量
进入Settings->Secret and variables->Actions->New Repository secret
![](doc/配置环境变量.png)
将上一步的**四个值**<br>
CNB_NAME_SPACE,CNB_REGISTRY_USER，CNB_REGISTRY_PASSWORD，CNB_REGISTRY, BARK_KEY<br>
配置成环境变量

### 添加镜像
打开images.txt文件，添加你想要的镜像 
可以加tag，也可以不用(默认latest)<br>
可添加 --platform=xxxxx 的参数指定镜像架构<br>
可使用 k8s.gcr.io/kube-state-metrics/kube-state-metrics 格式指定私库<br>
可使用 #开头作为注释<br>
![](doc/images.png)
文件提交后，自动进入Github Action构建

### 使用镜像
回到腾讯云，镜像仓库，点击任意镜像，可查看镜像状态。(可以改成公开，拉取镜像免登录)
![](doc/开始使用.png)

在国内服务器pull镜像, 例如：<br>
```
docker pull docker.cnb.cool/motorao/hub/home-assistant
```
docker.cnb.cool 即 CNB_REGISTRY(腾讯云仓库地址)<br>
motorao/hub 即 CNB_NAME_SPACE(腾讯云命名空间)<br>
home-assistant 即 腾讯云中显示的镜像名<br>

### 多架构
需要在images.txt中用 --platform=xxxxx手动指定镜像架构
指定后的架构会以前缀的形式放在镜像名字前面
![](doc/多架构.png)

### 镜像重名
程序自动判断是否存在名称相同, 但是属于不同命名空间的情况。
如果存在，会把命名空间作为前缀加在镜像名称前。
例如:
```
xhofe/alist
xiaoyaliu/alist
```
![](doc/镜像重名.png)

### 定时执行
修改/.github/workflows/docker.yaml文件
添加 schedule即可定时执行(此处cron使用UTC时区)
![](doc/定时执行.png)

### 增加bark提醒
在镜像有更新的时候给bark发个提醒，如果上游没更新就不提醒了。
