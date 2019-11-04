# Faster_Rcnn_Run_problem
这是关于运行github里面faster rcnn出现的各种问题，最终跑通（最先是 ubuntu 18 + cuda 9.0 + torch 1.3 + torchvison 0.3以上 + numpy 1.16 + python 3.7 ！！） py3.7有很大的坑 建议 py3.6

1. 首先想跑的是 https://github.com/jwyang/faster-rcnn.pytorch/tree/master 上的代码

首先该仓库有 master 和 pytorch 1.0的分支。

坑之一： -----git clone 时两个分支的路径一摸一样  
  脱坑：
  
    git clone -b 分支名 仓库地址
    git clone -b 2.1.6 https://github.com/aspnet/AspNetCore.git
  
  
  
坑之二： -----error: invalid command 'develop'
        我和师兄公用电脑 cuda = 9.0  ubuntu = 18 torch =1.3 想跑之中的 pytorch 1.0 分支 出现一大堆问题，各种百度，例如 如下问题：
        运行 python setup.py develop  出现 error: invalid command 'develop'
        
   脱坑：
   
     https://github.com/django-extensions/django-extensions/issues/92 按照上面说的 
     将下面这句由
     from distutils.core import setup
     替换成
     from setuptools import setup
  
  
  
坑之三： ------ImportError: No module named pycocotools.coco

  方法： https://blog.csdn.net/u013591306/article/details/79458220     解决方法是安装ipython： git clone https://github.com/pdollar/coco
  
        然后 进入PythonAPI目录：   cd coco/PythonAPI
        编译安装ipython:          make -j8
        安装成功后,进入ipython解释器,即终端输入: 输入 python 
        from pycocotools.coco import COCO
        from pycocotools import mask 没报错就安装好了。
        重新输入import COCO命令即可若进入PythonAPI目录可以，而运行程序仍然报错则修改环境变量即可：
        vi  ~/.bashrc   在末尾加上export  PATHONPATH=/home/john/coco/PythonAPI:$PATH     #当前PythonAPI目录的绝对路径
        
   
   win10 64位配置mask-rcnn环境的坑(主要是安装imgaug和pycocotools，解决“No module named ‘pycocotools._mask”
   https://blog.csdn.net/RicardoSuzaku/article/details/86496203    
   
   方法：pycocotools的安装
   
     巨坑，直接pip install pycocotools会报错，下载https://github.com/philferriere/cocoapi文件，虚拟环境下cd 到PythonAPI目录下，执行
     python setup.py build_ext install
     会编译并将pycocotools安装到虚拟环境下的Lib/site-packages目录中，编译需要安装visual studio2015。（有的博主推荐的https://github.com     /cocodataset/cocoapi地址，我在这个文件中执行python setup.py build_ext instal会报bug）
     安装完成之后python命令进入python环境，能运行from pycocotools.coco import COCO和import pycocotools._mask as _mask即大功告成



坑之四： ------- ImportError: cannot import name 'imread' from 'scipy.misc'

   是由于 `imread` is deprecated in SciPy 1.0.0, and will be removed in 1.2.0.
   Use ``imageio.imread`` instead.
     
    处理方法： 首先pip3 install imageio;
    然后 import imageio
    代码里imageio.imread()处理
   
  
  
 坑之五： -------最后运行，报错，说是显卡驱动太低， 或者更换 pytorch版本  （ubuntu 18 + cuda 9.0 + torch 1.3 + torchvison 0.3以上）
 
    方法： 
           我换了pytorch=0.4.1 同时 torchvison = 0.2.2 
           pip install torch == 0.4.1   pip install torchvison == 0.2.2  （这两个搭配） 
           同时换了这个版本的代码：https://github.com/Lite-Java/faster-rcnn.pytorch-0.4.1- （其里面标注pytorch0.4.1 python3.6 cuda9.0 cuDNNv7 ）
           
          （如果，torchivison更高，那么会自动将torch更新1.0以上
           看到有的文章说，其实 pytorch分支的版 还是会有错，换成 0.4.1 版本） 然后在 代码下/data（不知要不要进入data） 
    重新:
    cd lib 
    sh make.sh
    那个进入 PythonAPI 中也重新 再弄下：
    python setup.py build
    #build之后会发现lib/下多了个build文件夹
    python setup.py install
  
  
  
 坑之六： ------- RuntimeError : PyTorch was compiled without NumPy support
     接着出现如上报错。 百度没有相应的解答。于是谷歌， 
     
     https://github.com/pytorch/pytorch/issues/6747
     
     https://github.com/HazyResearch/metal/issues/101
     
     https://github.com/HazyResearch/fonduer-tutorials/issues/34 
     
     里面说到， pytorch 0.4.1也不行， 需要换成 pytorch 0.4.1.post2 并且同时 numpy版本要降成 1.15.0 ，最终发现， 只有 只换pytorch 0.4.1.post2才有用！！！
   
   
 最终结果跑出来了： 最终环境为:
 
 [ubuntu 18 + torch 0.4.1.post2 + torchvison 0.2.2 + cuda 9.0 + python 3.7] + 代码: https://github.com/Lite-Java/faster-rcnn.pytorch-0.4.1-
 
 为了这个网站上的 https://github.com/jwyang/faster-rcnn.pytorch/tree/master  这个faster rcnn试了很多 花了 很多天！！最终换了环境换了其他人的代码！！
 
 
 还有：
 1.关于 sudo执行脚本失败command not found问题 加不加 sudo 两个环境不一样 https://blog.csdn.net/u012578322/article/details/79548599
 
 2.关于 用sudo ./install.sh 安装软件 提示命令找不到   https://forum.ubuntu.org.cn/viewtopic.php?t=463331 
   可能原因之一
   
   你還沒賦予可執行屬性
   
   sudo chmod +x ./install.sh  
   
 3. AttributeError: 'dict' object has no attribute 'iteritems'    https://blog.csdn.net/qq_30638831/article/details/79928463 
    
    Python3.5中：iteritems变为items
    
    
 4. make: *** No targets specified and no makefile found. Stop.   https://blog.csdn.net/weiyangdong/article/details/79203712
   
   错误解决办法：
   
    wget http://ftp.gnu.org/pub/gnu/ncurses/ncurses-5.6.tar.gz
    
    tar zxvf ncurses-5.6.tar.gz
    
    cd ncurses-5.6
    
    ./configure -prefix=/usr/local -with-shared -without-debug
    
    make
    
    make install
  

此处报错我解决的办法很低级，之所以会遇到这个错误提示，是因为我在安装 nginx 时，没有执行 ./configure 就去 make 了，才导致的报错，结果都尝试了一遍之后，才惊醒。写在此处以提醒各位看官不要犯同样的错误。

 
 参考文章：
 http://www.pianshen.com/article/6481145026/
 
 https://blog.csdn.net/hai008007/article/details/85482303
 
 https://blog.csdn.net/qq_41644339/article/details/97641164#commentBox
 
 https://blog.csdn.net/m0_38052384/article/details/91359131

     
