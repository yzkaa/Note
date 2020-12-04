# 安装：

前提已经安装好python环境，或者直接安装anaconda。

1.使用conda env list查看环境：

```shell
conda env list
```

2.创建新的虚拟环境

```shell
conda create -n XXX名字 python==版本号
```

3.激活该虚拟环境

```shell
source activate XXX
```

4.在虚拟环境中下载TensorFlow

```shell
conda install -n tensorflow-gpu==2.0 #因为我是3.8的python，所以安装2.2或2.3的tensorflow，gpu版本
```

等待安装完成