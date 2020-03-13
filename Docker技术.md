# Docker技术
你今天的优势会被未来的趋势所取代，所以请持续学习。
## 简介

+ 是什么  
Docker是基于Go实现的云开源项目</br>
Docker的主要目标是“Build, Ship and Run Any App,Anywhere”，也就是通过对用用组件的封装、分发、部署、运行等生命周期的管理，使用户的APP（可以是一个WEB项目或者数据库应用等等）及其运行环境能够做到“一次封装，到处运行”。将应用运行在Docker容器上面，而Docker容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。<font color="red">只需要一次配置文件，换到别的机器上就可以一键部署，大大简化了操作。</font></br>
Docker解决了运行环境和配置问题软件容器，方便做持续继承并有助于整体发布的容器虚拟化技术。

+ 发展
    - 虚拟机
    缺点：1.占用资源多 2.冗余步骤多 3.启动慢
    - 容器虚拟化技术
    Linux容器（Linux Containers，简称LXC），不是模拟一个完整的操作系统，而是对进程进行隔离。有了容器就可以将软件运行所需要的所有资源打包到一个隔离的容器中。容器与虚拟机不同，不需要捆绑一整套完整的操作系统，只需要软件工作所欲的库资源和设置。系统因此而变的高效轻量并保证部署在任何的环境中的软件始终如一的运行。
    - Docker与传统的虚拟化方式的不同
        * 传统虚拟技术是虚拟出一套硬件后，在其上运行一个完整的操作系统，在该操作系统上在运行所需应用进程
        * Docker容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟，因此容器与传统虚拟器更为轻便。
        * 每个容器之间相互间隔，每个容器有自己的文件系统，容器之间进程不会相互影响，能区分计算资源。
+ 优势
    - DevOps开发自运维，一次构建、随处运行（提供全部的资源以及环境，避免冲突）
        * 更快速的应用交付和部署
        * 更便捷的升级和扩缩容
        * 更简单的系统运维
        * 更高效的计算资源利用
+ 仓库，Docker Hub官网： https://hub.docker.com

## 安装
+ 前提
    - CentOS仅发行版本中的内核支持Docker
    - 运行在CentOS7上，要求系统为64位，系统内核版本在3.10以上。
    - 运行在CENTOS6.5或者更高版本的CentOS上，要求系统为64位、系统内核版本为2.6.32-431或者更高版本。
    - 查看系统的内核
        ```linux
        uname -r
        ```
+ Docker架构图
![docker架构.png](https://i.loli.net/2019/11/29/Oxyms6T5DVv3XJH.jpg)
+ 基本组成（三要素）
    - 镜像（image)
        一个只读的模板。可以用来创建Docker容器，一个镜像可以创建很多容器。
    - 容器（container)
        是用镜像创建的运行实例，Docker利用容器独立运行一个或一组应用。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。可以把容器看做是一个简易版的Linux环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的程序。容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。
    - 仓库（repository)
        仓库是集中存放镜像文件的场所。仓库（Repository)和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。仓库分为公开仓库（Public）和私有仓库（Private)两种形式。最大的公开仓库是Docker Hub（https://hub.docker/com/），存放了大量的镜像供用户下载，国内的公开仓库包括阿里云、网易云等。

+ 总结，正确理解仓库/镜像/容器这几个概念
    Docker本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就是一个image镜像文件。只有通过这个镜像文件才能生成Docker容器。image文件可以看作是容器的模板。Docker根据image文件生成容器的实例。同一个image文件，可以生成多个同时运行的容器实例。

+ 安装，见官方文档：https://docs.docker.com/install/linux/docker-ce/centos/

## 使用
+ 安装完成后使用`sudo systemctl start docker`启动docker服务，一定要先启动docker服务，尤其是在重启宿主机后，docker服务没有启动。
+ 推荐使用阿里云镜像加速：注册阿里云，搜索容器镜像服务，按照提示进行申请
+ 配置完阿里云镜像后，使用`docker info`命令查看`Registry Mirrors`项是否是阿里云镜像的地址
+ 通过运行hello-world镜像来验证是否正确安装了Docker Engine-Community 。运行`docker run hello-world`命令。
    - docker run 镜像，运行相应的镜像，运行时首先会在本地寻找此镜像，若没有，会自动从设置的仓库中进行拉取，然后在容器中运行
    - 第一次运行时，会得到提示：`Unable to find image 'hello-world:latest' locally`，意思是本地没有该镜像，然后从仓库拉取，并在容器中运行
    - `hello-world:latest`后面的`:latest`为版本号，不写，则默认为latest
    - run的过程
        1. 在Docker本机中寻找该镜像，看本机是否有该镜像，若有，则以该镜像为模板生产容器实例运行
        2. 若本地没有则去Docker Hub上查找该镜像（若设置了阿里云镜像，则在阿里云镜像进行查找），能找到则下载该镜像到本地，以该镜像为模板生产容器实例运行
        3. 若未能找到，则返回失败，找不到该镜像

+ 底层原理
    - Docker是怎么工作的
        Docker是一个Client-Server结构的系统，Docker守护进程运行在主机上，然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器，容器，是一个运行环境，就是所谓的集装箱。

    - Docker为什么快
        * docker有着比虚拟机更少的抽象层。不需要Hypervisor实现硬件资源虚拟化，运行在docker上的程序直接使用的都是实际物理机硬件资源。因此在CPU、内存利用率上都有着更为明显的优势
        ![docker.png](https://i.loli.net/2019/11/29/K9tEiTJuX5lnRdO.jpg)

        * docker利用的是宿主机的内核，而不是Guest OS。docker直接利用宿主机的操作系统，新建一个docker容器只需要几秒钟。
    - Docker和虚拟机的区别

    ![docker与虚拟机的区别.jpg](https://i.loli.net/2019/11/29/LsfpxybV6eXDHO5.jpg)

### Docker常用命令
+ docker info，对Docker信息的概括
+ docker version，查看Docker的版本
+ docker --help，Docker命令的帮助
+ docker镜像命令
    - docker images：列出<font color='red'>本地</font>主机上所有的镜像，通过在最后指定镜像的名字，可以查看相应镜像的名字
        ```linux
        REPOSITORY       TAG           IMAGE ID         CREATED           SIZE
        hello-world      latest        fce289e99eb9     11 months ago     1.84kB
        ```
        各项的说明：
        * REPOSITORY：镜像的仓库源
        * TAG：镜像的标签
        * IMAGE ID：镜像ID
        * CREATED：镜像创建的时间
        * SIZE：镜像大小
        
        同一个仓库源下可以有多个TAG，代表这个仓库源的不同版本，我们可以使用`REPOSITORY:TAG`来定义不同的镜像。如果不指定一个镜像的版本标签，例如你只使用ubuntu，docker将默认使用ubuntu:latest镜像。</br>

        OPTIONS说明：
        * -a：列出本地所有的镜像（含中间映像层）
        * -q：只显示镜像ID
        * --digests：显示镜像的摘要信息
        * --no-trunc：显示完整的镜像信息
        * -qa：组合命令，显示所有的本地镜像的ID
    
    - docker search 镜像名： 从[Docker Hub](https://hub.docker.com)中查找某个镜像（尽管之前配置了阿里云镜像，但是这里查找镜像还是从Docker Hub上查找，拉取的时候从阿里云上拉）</br>

        OPTIONS说明：
        * -s：指定显示STARTS数不小于某个值的镜像，如docker search -s 30 tomcat指定STARTS超过30的Tomcat镜像
        * --no-trunc：显示完整的信息，不要省略，docker search -s 30 --no-trunc tomcat
        * --automated：只列出automated build（自动构建）类型的镜像，即列出AUTOMATED为ok的镜像

    - docker pull 镜像名：下载镜像，可以在镜像名后添加 `:版本号` 来下载指定版本的镜像，默认不添加的时候下载latest的版本

    - docker rmi 镜像名[镜像ID]：删除镜像，多个镜像时，默认不添加时版本是删除latest版本的。docker rmi -f 镜像名强制删除，如果有某个镜像被多个镜像调用，则必须使用-f强制删除所有相关的镜像。

        * 删除多个：docker rmi 镜像名1[镜像ID] 镜像名2[镜像ID]
        * 删除全部：docker rmi -f $(docker images -qa)，docker images -qa为组合命令，显示所有的镜像的ID

+ docker容器命令（以centos镜像为例）
    - 新建并启动容器：docker run [OPTIONS] IMAGES [COMMAND]

        OPTIONS说明：
        * --name='容器新名字'：为容器指定一个新的名字
        * -d：后台运行容器，并返回容器ID，也即启动守护式容器
        * -i：以交互模式运行容器，通常与-t同时使用；
        * -t：为容器重新分配一个伪输入终端，通常与-i同时使用； docker run -it 镜像 实际是得到一个容器的终端进行交互
        * -P：随机端口映射
        * -p：指定端口映射，有以下四种格式
            ```
            ip:hostPort:containerPort
            ip::containerPort
            hostPort:containerPort
            containerPort
            ```

            **注意，`run 镜像：版本号`，如果未添加版本，则默认是latest版本的，所以若下载的不是latest版本，你不添加对应的版本号，则会去下载latest版本并运行。**

        COMMAND说明：</br>
        * 该项目是可选项，可以指定运行容器时附加运行的指令，使用`docker run -it 镜像`创建实例的时候，即使不指定COMMAND为`/bin/bash`，默认不写该项时也是使用的这个。不使用-it参数，或者创建时没有指定一个前台运行的进程，创建的容器都会在创建之后由于没有前台进程而被自动kill掉。
        * **每个镜像都设置了其容器默认执行的相应的COMMAND，使得容器在被创建的时候，如果没有手动执行COMMAND，则会自动调用相应的默认的COMMAND指令，如tomcat镜像设置了其容器的默认COMMAND为"catalina.sh run"，这样tomcat容器一创建完成就会调用这个命令，开始执行tomcat服务器。**

        **使用docker run [OPTIONS] IMAGES [COMMAND]时的 [OPTIONS] 很重要，像对于centos这种，由于其没有默认的前台进程，如果使用-d作为守护式进程启动的话，由于docker本身的问题，这个容器一被创建就会由于没有前台进程而会被自动kill，且使用docker start也无法启动该容器，所以除非有特殊需要，以后台运行时要指定一个前台进程，或者就使用-it以交互式的方式运行**
        
        疑问：为什么使用`docker run -d centos`创建的容器，没有添加COMMAD时，会被kill，但是使用docker ps查看时显示COMMADN为`/bin/bash`，自带的？，那为什么又会被kill呢？</br>*这是docker本身的问题，Docker容器需要运行一个使其保持活动状态的进程。只要命令继续运行，容器将继续运行。而centos自带的/bin/bash会一执行玩就结束了，所以没有前台进程，该容器就被终止了，所以需要能使容器长时间挂起的方法，即所以可以：*
        1. 执行一个死循环
        2. 使用`docker run -dit centos`即使用-d启动守护式容器的同时，使用-it为其分配一个交互式伪终端即可

    - 查看docker内运行的进程：`docker ps` 默认显示当前正在运行的容器

        OPTIONS说明：
        * -a：列出当前所有正在运行的容器+历史上运行过的
        * -l：显示最近创建的容器
        * -n：显示最近n个创建的容器
        * -q：静默模式，只显示容器编号
        * --no-trunc：不截断输出

    - 退出容器：
        * exit：容器停止退出
        * ctrl+P+Q：容器不停止退出

    - 启动容器：`docker start 容器ID或者容器名`（若没有使用docker run --name 设置别名，则使用的的是默认的容器名）
        * 启动成功后会返回对应的容器ID，启动的容器执行的COMMAND为使用docker run命令创建容器时执行的COMMAND。
        * <font color='red'>可以通过`docker exec -it 容器ID bashShell`命令以命令行形式进入容器进行交互，如/bin/bash就会打开一个终端。</font>

    - 重启容器：`docker restart 容器ID或者容器名`，重启成功后会返回对应的容器ID

    - 停止容器：`docker stop 容器ID或者容器名`，关闭成功后会返回对应的容器ID
    
    - 强制停止：`docker kill 容器ID或者容器名`
    
    - 删除已停止的容器：`docker rm 容器ID或者容器名`，一定要是停止的，否则会报冲突
    
    - 删除没有停止的容器：`docker rm -f 容器ID或者容器名`，先关闭容器然后再删除
    
    - 一次性删除多个容器
        * docker rm -f $(docker ps -a -q)
        * docker ps -a -q | xargs docker rm

    - 灵活运用组合命令，如启动最近刚创建的容器，使用`docker start $(docker ps -lq)`
        * 解释：docker start需要容器ID，docker ps -lq添加了-q，使其只返回容器ID.

+ 重要
    - 启动守护式容器：`docker run -d 镜像名`
        * 注意该命令没有指定创建容器要执行的命令COMMAND（且该镜像创建的容器默认的COMMAND不是一个长久的前台进程，如centos镜像的默认COMMAND为`"/bin/bash"`会在一执行完成就结束了，但是tomcat的COMMADN为`"catalina.sh run"`这个前台进程会一直执行并等待）</br>
        此时使用docker ps发现没有正在运行的容器，因为Docker容器后台运行<font color='red'>必须</font>有一个前台进程，容器如果运行的不是那些一直挂起的命令（如运行top，tail），就会自动退出。<font color='red'>所以最佳的解决方案是，将你要运行的程序以前台的形式运行。</font>

    - 查看容器日志：`docker logs -f -t --tail 容器ID`
        * -t：加入时间戳
        * -f：跟随最新的日志打印
        * --tail 数字：显示最后的多少条

        所谓日志，就是docker容器终端的操作以及输出，直接使用`docker run 镜像`或者`docker run -d 镜像的`方式创建的容器会直接停止（即使用docker ps是无法查看的，即没有再运行），原因是因为docker在后台启动后，没有前台交互，无事可做，所以自动kill了，但是在创建时添加参数如`docker run -d centos /bin/sh -c "while true;do echo hello skylor;sleep 2;done"`这条命令的作用是创建centos的容器并且在容器中不断输出hello skylor，这样docker容器就一直有一个运行的前台进程，就不会自动kill了。</br>
        或者使用-d启动的容器，会后台打印日志，如tomcat容器，这种容器使用-d以后台启动的方式启动后就不会直接被kill掉。</br>
        另外，在使用`docker start 容器ID`方式启动后容器会继续运行这个命令。

    - 查看容器内运行的进程：`docker top 容器ID`
        * docker容器本质是就是运行的一个精简版的linux系统，所以大部分的linux命令和docker是通用的，有一些命令不行，另外由于是精简版，比如centos就没有vim，只有自带的vi，想使用vim，需要手动下载。

    - 查看容器内部的细节：`docker inspect 容器ID`
        * 能够查看所有存在的容器的内部信息，不仅是正在运行的容器。以键值对的形式展示内部细节。

    - 进入<font color="red">正在运行的</font>容器并以命令行交互，注意是正在运行的容器，所以容器若是关闭的，需先使用`docker start 容器ID`命令进行启动容器。
        * docker exec -it 容器ID bashShell， bashShell可以直接是操作命令，这样执行后，会直接返回命令的执行结果，而如果是/bon/bash，则会新开一个容器的终端并进入。
        * docker attach 容器ID，此时重新进入容器执行的默认的COMMAND。
        * 以上两种方式的区别： attach直接进入容器启动命令的终端，不会启动新的进程，exec是在容器中打开新的终端，并且可以启动新的进程。</br>
        **推荐使用docker exec -it 容器ID bashShell的方式，因为这样会再新打开一个shell进行交互，使用exit退出时仅仅退出了新开启的shell，而不会由于退出原始的shell而导致容器被关闭**

    - 将容器内的文件拷贝到主机上，`docker cp 容器ID：容器的内路径 目的主机路径`

### Docker镜像

+ 是什么，镜像时一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的内容，包括代码、运行时、库、环境变量和配置文件。
    - UnionFS联合文件系统：Union文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。Union文件系统实Docker镜像的基础，镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
        * 特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各种文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。
    - Docker镜像加载原理

    - 分层的镜像，一个镜像是由好几层各种的其他的镜像叠加而成，当多个镜像都有相同的镜像层时只需保存一个。

    - 为什么Docker镜像要采用这种分层结构呢：共享资源，比如说，有多个镜像都从相同的base镜像构建而来，那么宿主机只需在磁盘上保存一个base镜像，同时内存中也只需要加载一份base镜像，就可以为所有的容器服务了。而且镜像的每一层都可以被共享。

+ 特点：镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部，这一层通常被称作“容器层”，“容器层”之下都叫“镜像层”。

+ Docker镜像commit操作补充
    - docker commit 提交<font color='red'>容器</font>副本使之成为一个新的镜像

    - docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名：[标签名]

### Docker容器数据卷

+ 创建的容器对象只是关闭，没有删除的情况下，容器内的内容是不会丢失的，但是一旦容器被删除，则保存的内容就会丢失（当然也可以是使用docker commit生成一个新的镜像，使得数据成为镜像的一一部分保存下来），此时就需要使用Docker容器数据卷。

+ 能干什么：容器间继承和共享数据，做数据的持久化，类似redis中的rdb和aof文件

+ 是什么：卷就是目录或者文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供一些用于持续存储或共享数据的特性，卷的设计目的就是数据的持久化，完全能独立于容器的生存周期，因此Docker不会在容器删除时，删除其挂载的数据卷（类似于外置的活动存储器）
    特点： 
    1. 数据卷可在容器之间共享或重用数据----使用同一个本地目录，实现了两个容器之间的数据共享
    2. 数据卷中的更改可以直接生效
    3. 数据卷中的更改不会包含在镜像的更新中
    4. 数据卷的生命周期一直持续到没有容器使用它为止
    
+ 数据卷：容器内添加
    - 直接命令添加
        * 命令：`docker run -it -v /宿主机（即本地）绝对路径目录:/容器内目录  镜像名`，/宿主机（即本地）绝对路径目录和容器内目录都可以先不存在，而是由docker创建，如果 -v 之后只有一个路径，则此路径为容器内目录，而宿主机目录则由docker默认创建，目录为``/var/lib/docker/volumes/`下。
        * 查看数据卷是否挂载成功：使用`docker inspect 容器ID`查看`Volumes`和`VolumesRW`可以看到卷路径
        * 容器和宿主机之间数据共享：在容器或主机的任意一方的共享目录中的所有操作都是共享的，文件属性也是一致的，任意一方的修改，另外一方都能看到并使用。
        * 容器停止退出后，主机修改后数据是否同步：任然同步，内容是一致的
        * （带权限的）命令：`docker run -it -v /宿主机（即本地）绝对路径目录:/容器内目录:ro  镜像名`，其中的`:ro`的意思为readonly只读，使用此命令后，使用`docker inspect 容器ID`查看`VolumesRW`值为false，意思不是可读可写，而是变成了只读，即容器内的目录只能读，而不能写。（类比于外置活动硬盘进行了写保护）

    - DockerFile添加
        * 是什么，是Docker镜像的编译文件
        * 使用过程：
            - 1. 根目录下创建docker文件夹并进入
            - 2. 可在Dockerfile中使用VOLUME指令来给镜像添加一个或者多个数据卷
                    ```
                    VOLUME["/dataVolumeContainer","/dataVolumeContainer2","/dataVolumeContainer3"]
                    ```
                    说明，出于可移植和分享考虑，用`-v 主机目录：容器目录`这种方法不能够直接在Dockerfile中实现。由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有的宿主机上都存在这样特定的目录，所以VOLUME设置的都是<font color='red'>在容器上的目录</font>。
            - 3. File构建
                    创建一个Dockerfile脚本
                    ```
                    FROM centos 
                    VOLUME ["/dataVolumeContainer","/dataVolumeContainer2"]
                    CMD echo "finished,-------success"
                    CMD /bin/bash
                    ```
                    类似相当于下面的命令（但是要注意的是，出于可移植性考虑，Dokcerfile并不会在本地创建文件夹）
                    ```
                    docker run -it -v /host1:/dataVolumeContainer1 -v /host2:/dataVolumeContainer2 centos /bin/bash
                    ```
            - 4. build后生成镜像，将Dockerfile文件build为一个新的镜像，命令格式为 `docker build [OPTIONS] <上下文路径/URL/->`
                    ```
                    docker build -f /root/mydocker/Dockerfile -t skylor/centos .
                    ```
                    理解，这里的路径（上下文的问题），要注意，在执行该条命令的时候，有两种情况：
                    1. 是在含有要创建的镜像的Dockerfile文件所在的目录下，此时不使用-f指定Dockerfile文件的所在路径，docker会自动识别找到Dockerfile文件（当然前提是Dockerfile文件的命名必须是Dockerfile，而不能是其他的名字，如果是其他的名字，则就需要使用-f指定Dockerfile的具体路径，包括Dockerfile文件名）
                    2. 是在任意的目录下，必须使用-f参数指定要创建的镜像的Dockerfile文件的所在路径（最好写全，也可以只要大致目录，docker会自动找到，同理，对于直接命名为Dockerfile的文件可以直接找到，但是对于命名为其他的Dockerfile文件，则需要写具体的路径，包括Dockerfile文件名）
                    3. 上下文参数为根据当前执行`docker build`命令的目录为准，这个目录就是所谓的上下文，而在Dockerfile文件中执行COPY等命令时，如`COPY ./package.json /app/`这里的`.`指的是上下文路径，这是个相对路径，以上下文为基础。</br>
                    **一般都是新建一个文件夹，然后在其中添加Dockerfile文件，然后只要使用 `docker build -t 镜像名：版本号 .` 就完成了镜像的创建。**
            - 5. run容器
            - 6. 对于设置了卷的镜像，在使用run创建容器后，会有一个默认的主机的目录地址用于与容器内的目录进行共享
            - 7. 主机对应默认地址：使用`docker inspect 容器ID`进行查看
            
    - 备注：在使用`docker run -it -v /host1:/dataVolumeContainer1 -v /host2:/dataVolumeContainer2 centos`的方式给容器挂载主机目录，Docker访问出现`cannot open directory.: Permission denied`时，解决办法为：添加--privileged=true参数，即  
        ```
        docker run -it -v /host1:/dataVolumeContainer1 -v /host2:/dataVolumeContainer2 --privileged=true centos
        ```
+ 数据卷容器：命名的容器挂载数据卷，其他容器通过挂载这个（父容器）实现数据共享，这个用于挂载数据卷的容器，称之为数据卷容器
    - 命令：在创建容器时添加`--volumes-from`参数即可
        * 首先需要先创建一个含有数据卷的容器作为初始的数据卷容器
        * 之后使用`docker run -it --volumes-from 数据卷容器ID 镜像`来创建新的容器，则该新建的容器会在其内部创建与数据卷容器含有相同的文件目录用于数据共享。
        * 所有的共享的容器不需要是同一种镜像创建
        * 新创建的容器也可以作为数据卷容器去被其他的新容器使用
        * 任何的容器之间都可以进行数据共享
        * 首先创建的的数据卷容器的本地目的可以同时对所有的容器进行共享

    容器之间配置信息的传递，数据卷生命周期一致持续到没有容器使用它为止。

### Dockerfile解析
+ 是什么：Dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。

+ 构建三步骤：
    * 编写Dockerfile文件
    * docker build 
    * docker run 

+ 文件结构（以centos镜像为例）
    - Dokcerfile内容基础知识
        * 每条保留字指令都必须是大写字母且后面要跟随至少一个参数
        * 指令按照从上到下，顺序执行
        * #表示注释
        * 每条指令都会创建一个新的镜像层，并对镜像层进行提交
        * 最终暴露出来最终的镜像文件

    - Docker执行Dockerfile文件的大致流程
        * docker从基础镜像运行一个容器--对应于Dockerfile文件中的`FROM 镜像`
        * 执行一条指令并对容器作出修改
        * 执行类似docker commit的操作提交一个新的镜像层
        * docker再基于刚提交的镜像运行一个新容器
        * 执行Dockerfile中的下一条指令知道所有指令都执行完成
    
+ Dockerfile体系结构（保留字指令）
    - FROM：基础镜像，当前新镜像是基于哪个镜像的
    
    - MAINTAINER：镜像维护者的姓名和邮箱地址

    - RUN：容器构建时要运行的命令

    - EXPOSE：当前容器对外暴露出的端口

    - WORKDIR：指定在创建容器后，终端默认登录进来的工作目录，如果不设置的话，默认应该是根目录 /

    - ENV：用来在构建镜像的过程中设置环境变量
        * 类似键值对的形式，如`ENV MY_PATH /usr/mytest`，是创建了一个名为MY_PATH，对应的值为/usr/mytest的环境变量，这个环境变量可以在后续的任何RUN指令中使用，也可以在其他指令中直接使用这些环境变量，如：`WORKDIR $MY_PATH`

    - ADD：拷贝加解压缩。将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包

    - COPY：类似ADD，拷贝文件和目录到镜像中。将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>位置
        * COPY src dest  <--- shell格式
        * COPY ["src", "dest"]    <--- json格式

    - VOLUME：容器数据卷，用于数据保存和持久化工作，如`VOLUME ["/dataVolumeContainer", "/dataVolumeContainer2"]`

    - CMD：指定一个容器启动时要运行的命令
        * Dockerfile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换

    - ENTRYPOINT：指定一个容器启动时要运行的命令
        * ENTRYPOINT的目的和CMD一样，都是在指定容器启动程序及参数
        * 和CMD的区别在，CMD会被docker run之后的参数替换，但是使用ENTRYPOINT时，docker run之后添加的命令不会覆盖掉ENTRYPOINT，而是变为追加模式，即将原有的命令和docker run新添加的命令组成一个新的命令去执行。

    - ONBUILD：当构建一个被继承的Dockerfile时运行命令，父镜像在被子镜像继承后父镜像的onbuild被触发，是一个触发器。

    - .dockerignore：类似于git中的.gitignore，用于设置需要忽略的上下文中的构建镜像时不需要用到的文件，使其在使用docker build时不需要都上传至docker引擎，而造成的速度缓慢。

+ Dockerfile实际操作案例：镜像就是[一堆分层的文件](###Docker镜像)

    - Base镜像（scratch）:Docker Hub中99%的镜像都是通过在base镜像中安装和配置需要的软件构建而来的

    - 自定义镜像 mycentos：对官方版的centos镜像重构为新的镜像mycentos，使其能够
        1. 登录后的默认路径不在根目录
        2. 添加vim编辑器
        3. 支持使用ifconfig查看网络配置 

        Dokcerfile内容：
        ```
        FROM centos
        MAINTAINER skylor<tang1996mei@126.com>

        ENV MYPATH /usr/local
        WORKDIR $MYPATH

        RUN yum -y install vim
        RUN yum -y install net-tools

        EXPOSE 80

        CMD echo $MYPATH
        CMD echo "success-------------------ok"
        CMD ["/bin/bash"]
        ``` 
        docker引擎将Dockerfile文件分解为10个步骤，每执行一层会得到一个中间容器，但是在执行完该步骤之后会卸载这个生成的中间容器层，除此以外还有中间层镜像（很多镜像依赖这些中间层，所以中间层镜像是不能随意删除的）</br>
        使用`docker history 镜像名`查看镜像变更历史

    - CMD/ENTRYPOINT 镜像案例
        * 都是指定一个容器启动时要运行的命令
        * CMD: Dockerfile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换
        * ENTRYPOINT: 相比CMD，使用ENTRYPOINT时，docker run之后添加的命令不会覆盖掉ENTRYPOINT，而是变为追加模式，即将原有的命令和docker run新添加的命令组成一个新的命令去执行。

    
    - 自定义镜像 Tomcat9
        * 建立本地Dockerfile文件夹，`mkdir -p /root/mydockerfile/tomcat9`
        * 在上述目录下touch c.txt
        * 将jdk和tomcat安装的压缩包拷贝进上一步的目录中，`apache-tomcat-9.0.8.tar.gz`,`jdk-8u171-linux-x64.tar.gz`
        * 在`/root/mydockerfile/tomcat9`目录下新建Dockerfile文件
            ```
            FROM  centos
            MAINTAINER skylor<tang1996mei@126,com>

            # 把宿主机当前上下文的copy.txt文件拷贝到容器/usr/local/路径下
            COPY copy.txt /usr/local/cincontainer.txt

            # 把java和tomcat添加到容器中
            ADD apache-tomcat-9.0.8.tar.gz /usr/local/
            ADD jdk-8u171-linux-x64.tar.gz /usr/local/

            # 安装vim
            RUN yum -y install vim

            # 设置工作访问时候的WORKDIR路径，登录落脚点
            ENV MYPATH /usr/local
            WORKDIR $MYPATH

            # 配置java和tomcat环境变量
            ENV JAVA_HOME /usr/local/jdk1.8.0_171
            ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
            ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.8
            ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.8
            ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
            
            # 容器运行时监听端口
            EXPOSE 8080
            
            # 启动时运行tomcat
            # ENTRYPOINT ["/usr/local/apache-tomcat-9.0.8/bin/startup.sh"]
            # CMD ["/usr/local/apache-tomcat-9.0.8/bin/catalina.sh", "run"]
            CMD /usr/local/apache-tomcat-9.0.8/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.8/bin/logs/catalina.out

            ```
        * 构建： 在当前目录下`docker build -t centos_tomcat9 .`
        * run： `docker run -d -p 9080:8080 -v /hostdatavolume/centos_tomcat9/test:/usr/local/atache-tomcat-9.0.8/webapps/test -v /hostdatavolume/centos_tomcat9/tomcatlogs/:/usr/local/apache-tomcat-9.0.8/logs --privileged=true --name ct9 centos_tomcat9`

### 几个基本镜像的使用
+ mysql镜像:
    - run命令： `docker run -p 12345:3306 --name mysql -v /hostdatavolume/mysql5.6/conf:/etc/mysql/conf.d -v /hostdatavolume/mysql5.6/logs:/logs -v /hostdatavolume/mysql5.6/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6`</br>
    -e MYSQL_ROOT_PASSWORD=123456是对mysql的民吗进行设置

+ redis镜像：
    - run命令： `docker run -p 6379:6379 -v /hostdatavolume/redis3.2/data:/data -v /hostdatavolume/redis3.2/conf/redis.conf:/usr/local/etc/redis/redis.conf -d redis:3.2 redis-server /usr/local/etc/redis/redis.conf --appendonly yes`</br>
    `redis-server /usr/local/etc/redis/redis.conf --appendonly yes--appendonly yes`是redis用于配置数据持久化的

+ 说明： 需要使用端口的镜像，创建容器的时候都需要做端口映射，`-p 宿主机端口：容器使用端口`会将端口映射到本地的端口，使用`- P`则会随机分配一个端口。


### 本地镜像发布到阿里云
+ 使用docker commit命令提交一个本地的修改容器为新的镜像

    - docker commit 提交<font color='red'>容器</font>副本使之成为一个新的镜像

    - docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名：[标签名]
    
+ 阿里云提供了完善的提交方案