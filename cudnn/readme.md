1.https://developer.nvidia.com/rdp/cudnn-download下载cudnn v5 for cuda7.5，解压安装

cd cuda/include

sudo cp *.h /usr/local/cuda/include/

cd ../lib64

sudo cp lib* /usr/local/cuda/lib64/

2.更新软链接 cd /usr/local/cuda/lib64/

sudo ln -sf libcudnn.so.5.0.5 libcudnn.so.5

sudo ln -sf libcudnn.so.5 libcudnn.so

3.配置环境变量

gedit /etc/profile

在打开的文件尾部加上

PATH=/usr/local/cuda/bin:$PATH export PATH

保存后执行以下命令使之生效

source /etc/profile

同时创建以下文件

sudo vim /etc/ld.so.conf.d/cuda.conf

内容是

/usr/local/cuda/lib64

保存后，使之生效

sudo ldconfig
