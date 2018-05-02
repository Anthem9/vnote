# 进入docker容器

进入``Docker``容器比较常见的几种做法如下：

* 使用``docker attach``
* 使用``docker exec``
## docker run
```bash
- -d, --detach=false Run container in background and print container ID
```
如果要``attach``交互使用，考虑

```bash
-i, --interactive=false Keep STDIN open even if not attached
-t, --tty=false Allocate a pseudo-TTY
```

## 使用docker attach
* ``Docker``提供了``attach``命令来进入``Docker``容器。
```bash
$ docker attach --help

Usage:  docker attach [OPTIONS] CONTAINER

Attach local standard input, output, and error streams to a running container

Options:
      --detach-keys string   Override the key sequence for detaching a
                             container
      --no-stdin             Do not attach STDIN
      --sig-proxy            Proxy all received signals to the process
                             (default true)
```

* 先创建一个守护态（后台运行）的``Docker``容器，就是加``-itd``参数
```bash
$ docker run -p 8080:8080 -itd webgoat/webgoat-7.1
a951769562560aa3c9e214347a96c5dc2676bf3ed06773fc5b8e3d617042519e
```

* 然后使用``docker ps ``查看到该容器信息，复制``CONTAINER ID``
```bash
$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                    NAMES
ca41f316c698        webgoat/webgoat-7.1   "java -Djava.securit…"   10 hours ago        Up 10 hours         0.0.0.0:8080->8080/tcp   cranky_almeida
```
* 最后使用``docker attach``进入该容器
```bash
$ docker attach ca41f316c698

```

* 然后你就会发现卡住了...
### 为什么执行 docker attach 卡住了?
首先要明确的是 ``docker attach ``不是一个用来进入容器的命令, 或者说他不是用来在容器内运行一个 ``bash(shell) ``的命令, 它是用来连接到容器中运行中的进程, 也就是容器的 ``CMD``, 容器内 ``PID=1`` 的那个进程, 如果这个进程没有 ``stdout/stderr`` 那么你将看不到任何输出, 如果它没有接收`` stdin ``你也无法发送指令给它. 这也就是为什么你运行一个`` bash`` 的容器, 就可以 ``attach`` 进去执行命令, 而你运行一个`` mysql server`` 的容器就无法操作的原因

## 使用docker exec
```bash
$ docker exec --help

Usage:  docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

Run a command in a running container

Options:
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a
                             container
  -e, --env list             Set environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format:
                             <name|uid>[:<group|gid>])
  -w, --workdir string       Working directory inside the container
```

* 查看容器
```bash
$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                    NAMES
ca41f316c698        webgoat/webgoat-7.1   "java -Djava.securit…"   10 hours ago        Up 10 hours         0.0.0.0:8080->8080/tcp   cranky_almeida
```
* 使用``docker exec``进入该容器
```bash
docker exec -it ca41f316c698 /bin/bash
root@ca41f316c698:/#
```
