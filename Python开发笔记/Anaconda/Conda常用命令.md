---
title: Conda常用命令
tags: Conda
grammar_cjkPython: true
date:  2018-6-13
---

### 一、环境管理

1. 新建虚拟环境

	conda create --name your_env_name
	
2. 新建指定Python版本虚拟环境

	conda create --name your_env_name  python=2.7  #等号两边不能有空格
	conda create --name your_env_name  python=3.6 #等号两边不能有空格

3. 创建包含某些包的环境

	conda create --name your_env_name numpy scipy
	
4. 创建指定python版本下包含某些包的环境

	conda create --name your_env_name python=3.6 numpy scipy
	
5. 列举当前所有环境

	conda info --envs
	conda env list
	
6. 进入某个环境

	activate your_env_name
	
7. 退出当前环境
	
	deactivate 

8. 复制某个环境

	conda create --name new_env_name --clone old_env_name 
	
9. 删除某个环境

	conda remove --name your_env_name --all
	
### 二、分享环境

如果你想把你当前的环境配置与别人分享，这样ta可以快速建立一个与你一模一样的环境（同一个版本的python及各种包）来共同开发/进行新的实验。一个分享环境的快速方法就是给ta一个你的环境的.yml文件

首先通过activate target_env要分享的环境target_env，然后输入下面的命令会在当前工作目录下生成一个environment.yml文件

   conda env export > environment.yml

小伙伴拿到environment.yml文件后，将该文件放在工作目录下，可以通过以下命令从该文件创建环境

   conda env create -f environment.yml

###  三、包管理

列举当前活跃环境下的所有包

   conda list
   
列举一个非当前活跃环境下的所有包

   conda list -n your_env_name
   
为指定环境安装某个包
    
   conda install -n env_name package_name
	
如果不能通过conda install来安装，文档中提到可以从Anaconda.org安装，但我觉得会更习惯用pip直接安装。pip在Anaconda中已安装好，不需要单独为每个环境安装pip。如需要用pip管理包，activate环境后直接使用即可。



参考博文：
	https://blog.csdn.net/menc15/article/details/71477949