## 版本切换  
update-alternatives --list python  
update-alternatives --config python  

## 文件查找
find -name xxx			//查找所有子目录下文件名为xxx的文件  
grep -nr xxx			//查找所有子目录下文件内容包含xxx的文件  
grep -A/-B/-C 2			//显示前/后/前后 2行  
grep --include "\*.cpp"	//只匹配cpp文件

## 压缩相关
tar -zcvf /home/abc.tar.gz /home/abc        打包，并用gzip压缩
tar -zxvf /home/abc.tar.gz /home/abc        解压缩

## Linux管道
将一个命令的标准输出作为另一个命令的标准输入。也就是把几个命令组合起来使用，后一个命令除以前一个命令的结果。  
例：grep -r "close" /home/* | more       //在home目录下所有文件中查找，包括close的文件，并分页输出。  

## Linux软件包相关
sudo dpkg -i tree_1.5.3-1_i386.deb			//安装.deb软件  
sudo dpkg -r tree							//卸载软件  

sudo apt-get install tree					//安装，适用于有网络的环境  
sudo apt-get remove tree					//卸载  
sudo apt-get update  

## 文件权限
三种基本权限  
R           读         数值表示为4  
W          写         数值表示为2  
X           可执行  数值表示为1  
如图所示，jdk-7u21-linux-i586.tar.gz文件的权限为-rw-rw-r--  
-rw-rw-r--一共十个字符，分成四段。  
第一个字符“-”表示普通文件；这个位置还可能会出现“l”链接；“d”表示目录  
第二三四个字符“rw-”表示当前所属用户的权限。   所以用数值表示为4+2=6  
第五六七个字符“rw-”表示当前所属组的权限。      所以用数值表示为4+2=6  
第八九十个字符“r--”表示其他用户权限。              所以用数值表示为2  
所以操作此文件的权限用数值表示为662  

更改权限  

sudo chmod [u所属用户  g所属组  o其他用户  a所有用户]  [+增加权限  -减少权限]  [r  w  x]   目录名   
例如：有一个文件filename，权限为“-rw-r----x” ,将权限值改为"-rwxrw-r-x"，用数值表示为765  
sudo chmod u+x g+w o+r  filename  

## 网络
sudo lshw -C network			//查看MAC地址  
sudo ifconfig -a				//查看IP/MAC地址  

## Anaconda环境管理
conda env list  //查看所有环境
conda update --all  //更新包信息
conda create -n your_env_name python=3.5  //创建新环境
conda activate your_env_name //切换环境
conda deactivate  //关闭环境
