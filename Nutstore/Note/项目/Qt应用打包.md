​		Qt虽然是跨平台IDE，编写的代码可以在任何安装有Qt环境下的系统运行，但是不同的系统在进行应用打包时还是要回到相应的系统进行打包发布的，这是一个蛋疼的问题。

# Windows Qt打包

记得配置环境变量！！！！！！！

先在Qt中release一下，找到生成的release文件夹，将文件夹里的bin目录下的.exe文件拷贝到一个空的文件夹中，比如我的文件叫faims_.exe，我选择的空文件夹目录是H:\faims。

使用命令行管理器cmd（**注意：该命令行并不是Windows自带的命令行，而是Qt提供的，可能有多个命令行可以选择，根据自己所使用的编译器进行选择**）

进入faims目录

```powershell
cd /d H:\faims
```

使用Qt自带的windeployqt工具进行库导入

```powershell
windeployqt faims_.exe
```

在这一步，如果cmd没有找到windeployqt命令，说明没有配置环境变量。

等待完成后，使用enigma virtual box软件将库压缩到exe文件中即可。



## 可能出现的问题

1.无法定位程序接入点

原因可能是：（1）环境变量中除了Qt的mingw环境变量之外还有其他的mingw的环境变量，影响了windeployqt库的导入。

​								解决方法：将Qt的环境变量移动到其他换将变量的之前。

​					（2）没有使用Qt提供的cmd而是使用的Windows自带的cmd

​								解决方法：更换即可





# Linux Qt打包

