# 开始

## 介绍

> Docker只是方便了应用的分发与管理, 简化运维人员负担. 但于开发者而言, 除了方便搭建依赖组件(MySQL,Redis), 于开发无益, 徒增烦恼. 
>
> 目前okteto项目支持将代码修改同步到容器中, 利用热加载来看到修改的效果. 但体验仍不是很好.

* 基于**进程隔离**技术, 将程序以及其运行环境一起打包, 称之为**镜像**. 镜像可安全的运行在Docker存在的环境中, 而不会出现进程冲突, 此时运行的镜像称为**容器**. ( 类似程序和进程 )

* 与虚拟机本质上的区别是, 容器不带有内核, 容器功能是基于内核提供的进程隔离功能的; 而虚拟机虚拟了物理环境, 需要在该环境上运行操作系统. 

  > 因此有些容器装载了整个Linux发行版, 如Ubuntu, 但却无内容的. 有些容器可仅装可执行文件. 

* 关于性能, 并不会受太大影响, 但是内存可能会消耗更多, 因为运行了多份相同环境.

* Docker provides the ability to package and run an application in a loosely isolated environment called a container. 

* 好处
  * Docker将程序已经环境打包, 让你能够快速分发应用.
  * 使得上线和线下环境一致

* Container

  * Windows Container

    * Windows Server Container

      原生的Windows容器, 于Linux类似, 基于进程隔离 , 运行WIndows的软件.

    * Hyper-V Container

      通过Hyper-V启动**很小的**Linux虚拟机, 然后运行的Linux容器

  * Linux Container

    Linux上的容器

  > 参考
  >
  > * [Windows Container 和 Docker：你需要知道的5件事](https://www.cnblogs.com/ups216/p/6385663.html)
  >* [Windows系统下的Windows Container和Linux Contaner](https://blog.csdn.net/littleworm0/article/details/102626516)

## Docker Engine

![Docker Engine Components Flow](.Docker/engine-components-flow.png)

Docker Engine是一个提供容器服务的客户端, 主要由三部分组成:

1. 运行容器的守护进程`dockerd`
2. 提供与守护进程交互的Rest API
3. 提供与守护进程交互的命令行客户端.

## 安装

### Docker Desktop

* 介绍

  Docker Desktop提供了WIndows容器和Linux容器. 

  * Windows容器使用系统本身的进程隔离技术;
  * Linux容器使用Windows原生虚拟机方案Hyper-V创建虚拟机, 进而使用Linux系统的进程隔离技术.

* 安装

  ...

* 卸载

  右键卸载即可, 有些电脑却卡死, 这里给出脚本卸载方式.
  
  新建脚本, 如`remote-docker.ps1`, 内容:
  
  ```powershell
  $ErrorActionPreference = "SilentlyContinue"
  
  kill -force -processname 'Docker for Windows', com.docker.db, vpnkit, com.docker.proxy, com.docker.9pdb, moby-diag-dl, dockerd
  
  try {
  ./MobyLinux.ps1 -Destroy
  } Catch {}
  
  $service = Get-WmiObject -Class Win32_Service -Filter "Name='com.docker.service'"
  if ($service) { $service.StopService() }
  if ($service) { $service.Delete() }
  Start-Sleep -s 5
  Remove-Item -Recurse -Force "~/AppData/Local/Docker"
  Remove-Item -Recurse -Force "~/AppData/Roaming/Docker"
  if (Test-Path "C:\ProgramData\Docker") { takeown.exe /F "C:\ProgramData\Docker" /R /A /D Y }
  if (Test-Path "C:\ProgramData\Docker") { icacls "C:\ProgramData\Docker\" /T /C /grant Administrators:F }
  Remove-Item -Recurse -Force "C:\ProgramData\Docker"
  Remove-Item -Recurse -Force "C:\Program Files\Docker"
  Remove-Item -Recurse -Force "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Docker"
  Remove-Item -Force "C:\Users\Public\Desktop\Docker for Windows.lnk"
  Get-ChildItem HKLM:\software\microsoft\windows\currentversion\uninstall | % {Get-ItemProperty $_.PSPath}  | ? { $_.DisplayName -eq "Docker" } | Remove-Item -Recurse -Force
  Get-ChildItem HKLM:\software\classes\installer\products | % {Get-ItemProperty $_.pspath} | ? { $_.ProductName -eq "Docker" } | Remove-Item -Recurse -Force
  Get-Item 'HKLM:\software\Docker Inc.' | Remove-Item -Recurse -Force
  Get-ItemProperty HKCU:\software\microsoft\windows\currentversion\Run -name "Docker for Windows" | Remove-Item -Recurse -Force
  #Get-ItemProperty HKCU:\software\microsoft\windows\currentversion\UFH\SHC | ForEach-Object {Get-ItemProperty $_.PSPath} | Where-Object { $_.ToString().Contains("Docker for Windows.exe") } | Remove-Item -Recurse -Force $_.PSPath
  #Get-ItemProperty HKCU:\software\microsoft\windows\currentversion\UFH\SHC | Where-Object { $(Get-ItemPropertyValue $_) -Contains "Docker" }
  ```
  
  以管理员权限打开Powershell, 在里面运行该脚本即可.
  
  > 参考: [How to completely remove Docker in Windows 10](https://success.docker.com/article/how-to-completely-remove-docker-in-windows-10)

### Docker Toolbox

* 介绍

  对于不满足安装Docker Desktop的比较老的Mac和Windows系统, Docker Toolbox提供了传统方案. Toolbox使用VirtualBox而非Hyper-V来创建虚拟机.

* 安装条件

  * 64位
  * Windows7+
  * 使能虚拟化

  > 一般都满足

* 开始安装

  * 下载[Toolbox Releases](https://github.com/docker/toolbox/releases), 并安装

    > 默认就好

  * 安装好后, 双击Docker Quickstart Terminal, 首次打开会进行初始化.

    > 可能会碰到一些问题, 如文件下载失败, 手动下载即可; 还会遇到其他问题, 重试即可; 总之随机应变

  * Ok, 安装完毕. 但是每次重启, 都需要通过Docker Quickstart Terminal来启动Docker. 之后可以在该Terminal中执行Docker命令, 或者在其他终端中执行. ( 命令已添加到PATH下)

> 参考[Docker Toolbox overview](https://docs.docker.com/toolbox/overview/)

### Linux

```shell
apt install docker
```

# 使用



# 参考

* [Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
* [Docker 微服务教程](http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html)
* [【 全干货 】5 分钟带你看懂 Docker ！](https://zhuanlan.zhihu.com/p/30713987)
* [Docker Documentation](https://docs.docker.com/) 官方文档
* [Docker Quick Start](https://hub.docker.com/?overlay=onboarding) 官方入门
* [Docker最全教程——从理论到实战(一)](https://www.cnblogs.com/codelove/p/10030439.html) 

