# Docker入门
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/Docker/_v_images/_1524657822_18293.png)
## 起因&&闲聊
学习``Docker``的起因是这样的，星辰``DevOps``组的学长转发了一个广告，那人说要解决一个问题，是安全方向的，然后我面试的时候说过自己对安全有兴趣，结果学长就让我去试试。

去了一问，结果是一个国外留学生的信息安全作业，其中``Part2``正好是``Web``方向，是要用``WebGoat``学习``Web``漏洞，我寻思正好啊，既能学东西又有钱赚，就接了。

好嘛，直接打开``WebGoat``的``Github``，``README``里第一个介绍的就是使用``Docker``进行环境安装。

我对是``Docker``仰慕已久了，但一直没敢动手去学，心道干脆借这个机会学了算了，就这样开始了我的``Docker``学习之路。

## 初识Docker
### 环境配置的难题
软件开发的时候最大的麻烦事就是环境配置。我用``Python``写``Flask``的时候就被这个烦的不行，``Linux``自带的``Python``环境是``2.7``的，也是我的错，我没用虚拟环境直接安装了``Python3.4``，结果导致``Flask``找库的时候各种找不到，每次运行服务总有各种各样的玄学问题，实在是把我折磨疯了。

除了版本问题，还有环境变量，一般来说，我们在开发过程中需要使用邮箱密码，数据库密码等敏感信息来使用这些服务，有些程序员直接把这些信息写在代码里，但这样做是不安全的（``Github``上很多这样的泄露，造成很大隐患），比较安全的做法是把这些数据保存在环境变量里，然后在代码中引用环境变量，但这又会带来迁移的麻烦。但``Docker``解决了这个问题，环境变量也一起打包好了。

简单来说，``Docker``就是为了防止开发人员在一台又一台的机器上重复恼人的环境配置而开发的。

### 虚拟机
肯定有人会提到虚拟机，虚拟机也是带环境安装的一种解决方案。它可以在一种操作系统里面运行另一种操作系统。而运行在虚拟机里的应用程序对此是无感知的，因为虚拟机看上去跟真实系统一模一样。而对于底层操作系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。

虽然用户可以通过虚拟机还原软件的原始环境，但是，这个方案有几个缺点：

* 资源占用多
* 冗余步骤多
如操作系统的用户登录等
* 启动慢

### Linux容器
由于虚拟机存在这些缺点，``Linux`` 发展出了另一种虚拟化技术：``Linux`` 容器（``Linux Containers``，缩写为`` LXC``）。

``Linux`` 容器不是模拟一个完整的操作系统，而是对进程进行隔离。或者说，在正常进程的外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。

由于容器是进程级别的，相比虚拟机有很多优势。

* 启动快
容器里面的应用，直接就是底层系统的一个进程，而不是虚拟机内部的进程。所以，启动容器相当于启动本机的一个进程，而不是启动一个操作系统，速度就快很多。

* 资源占用少
容器只占用需要的资源，不占用那些没有用到的资源；虚拟机由于是完整的操作系统，不可避免要占用所有资源。另外，多个容器可以共享资源，虚拟机都是独享资源。

* 体积小
容器只要包含用到的组件即可，而虚拟机是整个操作系统的打包，所以容器文件比虚拟机文件要小很多。

总之，容器有点像轻量级的虚拟机，能够提供虚拟化的环境，但是成本开销小得多。

### Docker是什么？
**Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。**它是目前最流行的 ``Linux`` 容器解决方案。

``Docker`` 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 ``Docker``，就不用担心环境问题。

总体来说，``Docker ``的接口相当简单，用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。

### Docker的用途
``Docke``r 的主要用途，目前有三大类。

* **提供一次性的环境。**比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。

* **提供弹性的云服务。**因为 ``Docker ``容器可以随开随关，很适合动态扩容和缩容。

* **组建微服务架构。**通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。

这里我搭建``WebGoat``就是其第一个用途。

### Docker的安装
我的系统是``windows10 家庭版`` 不是``Pro`` 没有``Hyper-V``的安装，所以在家庭版中安装``Docker``必须使用``Docker Toolbox``来安装。
``Docker Toolbox``[下载地址](https://www.docker.com/products/docker-toolbox)，选择``Windows``版本，点击下载即可。
下载完成后，双击``DockerToolbox.exe``进行安装，一路``next``就可以，

看到安装了``Vbox``和``Git Bash``想来是要模拟``Linux``环境了，``Windows``果然麻烦的一匹。

完成后会有下图三个图标

![](https://github.com/Anthem9/vnote/raw/master/vnotebook/Docker/_v_images/_1524659633_18828.png)

双击``Docker Quickstart Terminal``，会提示错误，如图：

![](https://github.com/Anthem9/vnote/raw/master/vnotebook/Docker/_v_images/_1524659666_12661.png)

这时，点击浏览，找到``git``安装目录下的``bin``目录，选择``bin``目录下的``bash.exe``文件，点击确定，``Docker``开始初始化。

初始化过程中如果下载``boot2docker.iso``没反应，可以直接从``GitHub``[下载](https://github.com/boot2docker/boot2docker/releases/download/v18.04.0-ce/boot2docker.iso)，直接复制到那个文件里

成功后界面如下图：
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/Docker/_v_images/_1524659700_7710.png)
到此成功在``Windows 10``中安装``Docker``，但是每次启动都需要手动选择``bash.exe``文件。

其他操作系统的安装请参考[官方文档](https://docs.docker.com/)

## 一把梭
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/Docker/_v_images/_1524660237_24106.png)
```bash
docker pull webgoat/webgoat-7.1
docker run -p 8080:8080 -t webgoat/webgoat-7.1
```
``pull``完就``run``很美妙对不对？
可是它为啥卡在这不动...
醒醒吧，你在天朝啊！天朝无所不有，你怎么能用外国的源呢？
```bash
docker pull registry.docker-cn.com/webgoat/webgoat-7.1
docker run -p 8080:8080 -t webgoat/webgoat-7.1
```

* ``Docker`` 中国官方镜像加速可通过 ``registry.docker-cn.com`` 访问。该镜像库只包含流行的公有镜像。私有镜像仍需要从美国镜像库中拉取。
* 实测挂了全局的``ss``代理好像还是不能直接用国外的源，惆怅。
* 永久配置请参考[官方文档](https://www.docker-cn.com/registry-mirror)

### Docker容器的IP
当你运行``Docker``的时候，注意这个地方
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/Docker/_v_images/_1524660762_29280.png)
这就是这个容器的``IP``

当你运行``WebGoat``的时候，不要被它骗了
![](https://github.com/Anthem9/vnote/raw/master/vnotebook/Docker/_v_images/_1524660894_17757.png)
不管它怎么在``localhost``欢迎你去``hacking``，你要记得去访问上面哪个``IP``
```url
192.168.99.100:8080/WebGoat
```
因为它的``localhost``是虚拟容器的``localhost``不是你的``localhost``
## 结束
期间等待过程中还``Standalone``安装了一下，虽然步骤简单，但打开服务发现有少量乱码，很难受。也不知道怎么解决。

但使用``Docker``安装好就没有这个问题，很妙。

至此，第一次使用``Docker``的坑记录完毕！