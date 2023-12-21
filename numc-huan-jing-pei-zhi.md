# numc环境配置

## numc环境配置

## 基本环境

version: su20

os: macOS 14.0 23A344 arm64 → Ubuntu 20.04.6 LTS x86\_64 (github codespace)

```bash
(.venv) @scatyf3 ➜ /workspaces/numc/Python-3.6.15 (master) $ neofetch
            .-/+oossssoo+/-.               codespace@codespaces-51e639 
        `:+ssssssssssssssssss+:`           --------------------------- 
      -+ssssssssssssssssssyyssss+-         OS: Ubuntu 20.04.6 LTS x86_64 
    .ossssssssssssssssssdMMMNysssso.       Host: Virtual Machine 7.0 
   /ssssssssssshdmmNNmmyNMMMMhssssss/      Kernel: 6.2.0-1016-azure 
  +ssssssssshmydMMMMMMMNddddyssssssss+     Uptime: 4 hours, 2 mins 
 /sssssssshNMMMyhhyyyyhmNMMMNhssssssss/    Packages: 659 (dpkg) 
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Shell: bash 5.0.17 
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   Terminal: vscode 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   CPU: AMD EPYC 7763 (2) @ 3.243GHz 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   Memory: 2286MiB / 7929MiB 
+sssshhhyNMMNyssssssssssssyNMMMysssssss+
.ssssssssdMMMNhsssssssssshNMMMdssssssss.                           
 /sssssssshNMMMyhhyyyyhdNMMMNhssssssss/                            
  +sssssssssdmydMMMMMMMMddddyssssssss+
   /ssssssssssshdmNNNNmyNMMMMhssssss/
    .ossssssssssssssssssdMMMNysssso.
      -+sssssssssssssssssyyyssss+-
        `:+ssssssssssssssssss+:`
            .-/+oossssoo+/-.
```

配置时间: 2023.12

## 创建python3.6虚拟环境报错

运行下述指令时遇到错误，无法找到python3.6

```bash
➜  ~ python3.6 -m venv .venv
zsh: command not found: python3.6
```

尝试在本地macos使用anaconda创建虚拟环境，但是报错

```bash
homebrew git:(stable) conda create -n myenv python=3.6
Retrieving notices: ...working... done
Collecting package metadata (current_repodata.json): done
Solving environment: unsuccessful attempt using repodata from current_repodata.json, retrying with next repodata source.
Collecting package metadata (repodata.json): done
Solving environment: failed

PackagesNotFoundError: The following packages are not available from current channels:

  - python=3.6

Current channels:

  - https://repo.anaconda.com/pkgs/main/osx-arm64
  - https://repo.anaconda.com/pkgs/main/noarch
  - https://repo.anaconda.com/pkgs/r/osx-arm64
  - https://repo.anaconda.com/pkgs/r/noarch

To search for alternate channels that may provide the conda package you're
looking for, navigate to

    https://anaconda.org

and use the search bar at the top of the page.
```

在conda的[github issue](https://github.com/conda/conda/issues/12925)里发现python3.6没有arm64的版本😭

所以怎么挣扎也没用，遂迁移到github codaspace的linux-x86环境开发

但是apt-get安装指定python版本的时候仍然报错，遂安装三方python环境管理

```bash
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get install python3.6
sudo apt install python3.6-venv
```

然后再运行它要求的那个创建虚拟环境的命令就没有问题了

## make test踩坑

存在两个头文件无法识别

```bash
mat_test.c:1:10: fatal error: CUnit/CUnit.h: No such file or directory
    1 | #include "CUnit/CUnit.h"
      |          ^~~~~~~~~~~~~~~
compilation terminated.

In file included from matrix.c:1:
matrix.h:1:10: fatal error: Python.h: No such file or directory
    1 | #include <Python.h>
      |          ^~~~~~~~~~
compilation terminated.

无法找到这个python头文件，这是一个c-python接口，我应该怎么做以寻找它
```

## cunit头文件无法识别

这个比较简单，直接问ai即可

是的，您可以使用apt包管理器在Ubuntu或Debian系统上安装CUnit。

请在终端中执行以下命令：

```
sudo apt update
sudo apt install libcunit1 libcunit1-dev

```

第一条命令将更新您的软件包列表，第二条命令将安装CUnit库和开发文件。

安装完成后，您可以在代码中使用以下方式包含CUnit头文件：

```c
#include <CUnit/CUnit.h>

```

然后使用gcc编译器编译您的程序，并链接CUnit库：

```bash
gcc -o your_program mat_test.c -lcunit

```

这样，CUnit库将与您的程序一起编译和链接。

请注意，这些命令适用于Ubuntu或Debian系统。如果您使用的是其他Linux发行版，请参考该发行版的包管理器文档，以确定正确的安装命令。

## python头文件无法识别

定位问题

```bash
(.venv) @scatyf3 ➜ /workspaces/numc/Python-3.6.15 (master) $ sudo find / -name Python.h
/opt/conda/include/python3.10/Python.h
/usr/local/python3.6/include/python3.6m/Python.h
/usr/local/python/3.10.13/include/python3.10/Python.h
/usr/local/python/3.9.18/include/python3.9/Python.h
/usr/include/python3.8/Python.h
/workspaces/numc/Python-3.6.15/Include/Python.h
```

注意到这里的Python.h没有3.6版本的，又观察到我们的cmake里写着

```bash
PYTHON = -I/usr/include/python3.6 -lpython3.6m
```

所以我们问问ai如何安装python3.6的头文件

如果您需要安装Python 3.6的头文件，但系统中没有可用的安装包或头文件，您可以尝试手动安装Python 3.6的开发包。

以下是一种可能的方法：

1.  首先，确保您具备构建源码的工具和依赖项。在Ubuntu或Debian系统上，可以运行以下命令安装这些工具：

    ```bash
    sudo apt update
    sudo apt install build-essential

    ```
2.  接下来，下载并解压Python 3.6的源代码。您可以从Python官方网站下载源代码包，或者使用以下命令下载：

    ```bash
    wget <https://www.python.org/ftp/python/3.6.15/Python-3.6.15.tgz>
    tar -xvf Python-3.6.15.tgz
    ```
3.  进入解压后的Python 3.6源代码目录：

    ```bash
    cd Python-3.6.15
    ```
4.  配置和编译Python 3.6。运行以下命令进行配置,这将将Python 3.6安装到`/usr/local/python3.6`目录下。：

    ```bash
    ./configure --prefix=/usr/local/python3.6
    ```
5.  编译和安装Python 3.6：

    ```bash
    make
    sudo make install

    ```
6.  安装完成后，您应该能够在`/usr/local/python3.6/include/python3.6`路径下找到Python 3.6的头文件。确保这个路径包含了`Python.h`文件。

    ```bash
    ls /usr/local/python3.6/include/python3.6
    ```
7.  最后，根据前面提到的方法在您的代码中使用正确的头文件路径进行编译：

    ```bash
    gcc -o your_program matrix.c -I/usr/local/python3.6/include/python3.6 -lpython3.6m

    ```

请注意，这些步骤是手动安装Python 3.6的开发包，并从源代码构建。这可能需要一些时间和配置，因此请确保您具备必要的工具和技能。另外，确保仔细阅读并遵循Python 3.6的安装和编译说明，以确保正确安装和配置Python 3.6。

一通操作之后，我们在我们的环境里增加了python3.6的头文件

```bash
(.venv) @scatyf3 ➜ /workspaces/numc/Python-3.6.15 (master) $ sudo find / -name Python.h
/opt/conda/include/python3.10/Python.h
/usr/local/python3.6/include/python3.6m/Python.h
/usr/local/python/3.10.13/include/python3.10/Python.h
/usr/local/python/3.9.18/include/python3.9/Python.h
/usr/include/python3.8/Python.h
/workspaces/numc/Python-3.6.15/Include/Python.h
```

这样一通操作之后，我们可以跑通make test了😄
