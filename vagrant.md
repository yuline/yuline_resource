##Vagrant
vagrant 是用来管理虚拟机的，通常情况下与virtual box结合使用，vagrant本身并不提供虚拟化的功能，只适用于管理、封装、分发虚拟化。

1. Vagrantfile  
配置一个vagrant 环境的第一步需要创建vagrant file.这个文件主要有两个作用：
 
	- 标识vagrant项目的根目录，大多vagrant 的配置都在此根目录下
	- 描述系统类型、软件等资源信息

	使用 ```vagrant init``` 对一个目录进行初始化，同时会在当前目录下生成一个Vagrantfile示例配置

2. Box  
 创建完Vagrantfile后，就需要指定Vagrant环境使用的box.  使用 ```vagrant box add```向已初始化的vagrant环境中添加box：  
 ``` $ vagrant box add  ```  
 从 [ HashiCorp's Atlas box catalog](https://atlas.hashicorp.com/boxes/search) 下载 ```hashicorp/precise32``` box到本地并且添加到当前环境中
 
 ``` $ vagrant box add hahaha ~/box/precise64.box```  
 hahaha 是我们给这个 box 命的名字，~/box/precise64.box 是 box 所在路径  
 
 
 >Added boxes can be re-used by multiple projects. Each project uses a box as an initial image to clone from, and never modifies the actual base image. This means that if you have two projects both using the hashicorp/precise32 box we just added, adding files in one guest machine will have no effect on the other machine.
   
从官方文档的论述可知，不同的vagrant project可以共用一个basic image ,之间互不影响，目前这点还不清楚如何实现。

3. Up and ssh
4. 


continue ......
