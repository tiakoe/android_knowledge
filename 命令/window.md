<br/>

<!-- TOC -->

- [查看window端口占用，并杀死进程](#%e6%9f%a5%e7%9c%8bwindow%e7%ab%af%e5%8f%a3%e5%8d%a0%e7%94%a8%e5%b9%b6%e6%9d%80%e6%ad%bb%e8%bf%9b%e7%a8%8b)

<!-- /TOC -->

<br/>

## 查看window端口占用，并杀死进程

在windows命令行窗口下执行：

C:\> netstat -aon|findstr "80"

TCP  127.0.0.1:80    0.0.0.0:0       LISTENING   **2448**

<br/>

端口被进程号为**2448**的进程占用，继续执行下面命令：

C:\> tasklist|findstr "2448"

thread.exe          2016 Console        0  16,064 K

如果查不到，打开任务管理器，查找进程2448，然后杀之。



