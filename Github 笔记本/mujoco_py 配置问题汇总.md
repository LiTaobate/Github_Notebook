## 问题1
```
Creating window glfw
ERROR: GLEW initalization error: Missing GL version
```
**参考GitHub issues：** https://github.com/openai/mujoco-py/issues/432

### 方法1：在.bashrc中添加一下内容
```py
sudo gedit ~/.bashrc
export PATH=/usr/local/cuda-11.6/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.6/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libGLEW.so:/usr/lib/nvidia-390/libGL.so
# 特别注意：如果使用vim，或者gedit打开，保存后，要不关闭窗口，要不使用source一下，不然有时候不行，这是linux知识，我也不知道为啥，反正就是让生效
source ~/.bashrc
```
以上第三行我的没有libGL.so，所以改成：

```py
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libGLEW.so
```

我首先需要安装GLew
命令：
先命令查看glew：
``` apt-cache search glew```

显示结果：

```py
(rl_ultrasound) litao@litao-X11DAi-N:~/Documents/robotic-ultrasound-imaging-master-1$ apt-cache search glew
fonts-glewlwyd - OAuth2 authentication server providing Json Web Tokens - font files
glew-utils - OpenGL Extension Wrangler - utilities
glewlwyd - OAuth2 authentication server providing Json Web Tokens
glewlwyd-common - OAuth2 authentication server providing Json Web Tokens - common files
libglew-dev - OpenGL Extension Wrangler - development environment
libglew2.1 - OpenGL Extension Wrangler - runtime environment
libglewmx-dev - OpenGL Extension Wrangler MX - development environment
libglewmx1.13 - OpenGL Extension Wrangler (Multiple Rendering Contexts)
```

报错：
```py
(rl_ultrasound) litao@litao-X11DAi-N:~/Documents/robotic-ultrasound-imaging-master-1$ sudo apt-get install glew-utils glewlwyd glewlwyd-common libglew-dev libglew2.1 libglewmx-dev libglewmx1.13
[sudo] password for litao: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
libglew-dev is already the newest version (2.1.0-4).
libglew2.1 is already the newest version (2.1.0-4).
libglew2.1 set to manually installed.
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 libglewmx-dev : Conflicts: libglew-dev but 2.1.0-4 is to be installed
E: Unable to correct problems, you have held broken packages.
```
安装过程提示：

```py
  The glewlwyd package can be configured to use one of several database types. 
Below, you will be presented with the available choices.                                                                                                                                                                                    
  If other database types are supported by glewlwyd but not shown here, the reason for their omission is that the corresponding dbconfig-<database type> packages are not   
    │ installed. If you know that you want the package to use another supported database type, your best option is to back out of the dbconfig-common questions and opt out of   
    │ dbconfig-common assistance for this package for now. Install your preferred dbconfig-<database type> option from the list in the package dependencies, and then        
    │ "dpkg-reconfigure glewlwyd" to select it.                                                                                                                                                                                                                                                                                                           
    │ Database type to be used by glewlwyd:              
```                                                                                                                        
### 问题2 

```py
ValueError: numpy.ndarray size changed, may indicate binary incompatibility. Expected 96 from C header, got 88 from PyObject
```

原因分析：
numpy安装版本和llvmlite版本文件冲突。（以上报错只反映numpy版本过低）
解决方案：升级版本，但是遇到了新的报错，如下问题3



### 问题3
```py
ERROR:Cannot uninstall ‘llvmlite‘. It is a distutils installed project and thus we cannot accurately deter
```
查看了多种方法，尝试用conda或者pip uninstall的方式但是并不管用，还是会有相同报错。网上最直接的方式是直接将llvmlite的包强制删除，具体路径在```/home/litao/anaconda3/envs/rl_ultrasound/lib/python3.8/site-packages```我在该路径下直接找到所有的llvmlite文件包，直接删除，问题解决，然后再安装自己需要的版本。但是直接使用pip的方式安装后又遇到问题4,解决方法如下：

 ### 问题4
 
```py
AttributeError: module 'numba' has no attribute 'jit'
```
全网找到比较靠谱的处理办法为以下：
参考：https://github.com/dereneaton/ipyrad/issues/180

>博主说可能由于是conda本身导致的，miniconda没有这样的问题，我尝试先一个个按要求安装numba、numpy、llvmlite但是没有解决问题。也许较大堆栈中的某些东西会导致问题。
另外看到有大佬说numba 被称为 finnicky，这就是为什么通常不建议尝试使用 pip 或 setup.py 安装它的原因。不过，通常它会被 conda 很好地处理。

**注意：llvm 或 llvmpy 安装可能会阻止 numba 安装它需要的 llvmlite。所以使用conda来做如下：**

```py
#先移除旧的packages
conda remove numba
conda remove llvm
conda remove llvmpy

##使用conda安装 numba anew
conda install numba
```

然后关闭会话并完全重新启动它。
### 问题5
在渲染时候遇到的具体问题：无法渲染包含可变形对象的模型
```py
Creating window glfw
Segmentation fault (core dumped)
```

这个是Mujoco_py的官方bug，使用这个包做软体仿真的时候可能会遇到这个问题（官方现在没有给出解决方案），但是在mujoco_py的github网站上的issues里边找到一些方式，每个人最后解决的方式不太一样。
**参考GitHub issues：**https://github.com/openai/mujoco-py/issues/373**

方法一：
在issues的问题下有个大佬的文章发布了个解决方案，有人用这个解决了问题，我之前一次配置是用这个解决的，但是这第二次配置，我的最后好像也没用：
大佬文章链接：https://github.com/wutter/mujoco-py-to-c-viewer
使用方法：
>1. Modify your model (copy), so it does not use skin.
>2. Use your python code with the model without skin to generate the sim.data.qpos (and than export it to csv by file.writerow(sim.data.qpos).
>3. Use the csv with qpos + model with skin as inputs for the viewer (.cpp).

方法二：
重装并编译mujoco-py

```py
pip3 uninstall  -y mujoco_py

git clone git@github.com:fantasyRqg/mujoco-py.git

cd mujoco-py

python3 setup.py build

python3 setup.py install
```

方法三：
参考：

https://github.com/ARISE-Initiative/robosuite/issues/217
渲染具有皮肤属性的可变形对象会引发分段错误 #217
大佬表示就算升级了最新版本的mujoco-py 2.0.2.13，也会遇到分段错误，所以他的解决办法是使用 Herman大神的mujoco代码可以解决问题。[https://github.com/hermanjakobsen/mujoco-py](https://github.com/hermanjakobsen/mujoco-py)
安装方式：
```py
pip3 install --user --force-reinstall --no-compile git+https://github.com/hermanjakobsen/mujoco-py
```
这里的包使用的不再是官方的包，而是一个大佬自己的包，尝试后有用。
具体如下：

```py
(rl_ultrasound) litao@litao-X11DAi-N:~/Documents/robotic-ultrasound-imaging-master-1$ pip3 install --user --force-reinstall --no-compile git+https://github.com/hermanjakobsen/mujoco-py
Collecting git+https://github.com/hermanjakobsen/mujoco-py
  Cloning https://github.com/hermanjakobsen/mujoco-py to /tmp/pip-req-build-r1iu3zew
  Running command git clone -q https://github.com/hermanjakobsen/mujoco-py /tmp/pip-req-build-r1iu3zew
  Resolved https://github.com/hermanjakobsen/mujoco-py to commit cb8d3fba4b5aae7e50bf733ca5253f89ab96cbf4
  Installing build dependencies ... done
  WARNING: Missing build requirements in pyproject.toml for git+https://github.com/hermanjakobsen/mujoco-py.
  WARNING: The project does not specify a build backend, and pip cannot fall back to setuptools without 'wheel'.
  Getting requirements to build wheel ... done
  Installing backend dependencies ... done
    Preparing wheel metadata ... done
Collecting fasteners~=0.15
  Using cached fasteners-0.17.3-py3-none-any.whl (18 kB)
Collecting glfw>=1.4.0
  Using cached glfw-2.5.1-py2.py27.py3.py30.py31.py32.py33.py34.py35.py36.py37.py38-none-manylinux2014_x86_64.whl (205 kB)
Collecting imageio>=2.1.2
  Using cached imageio-2.16.1-py3-none-any.whl (3.3 MB)
Collecting cffi>=1.10
  Using cached cffi-1.15.0-cp38-cp38-manylinux_2_12_x86_64.manylinux2010_x86_64.whl (446 kB)
Collecting numpy>=1.11
  Using cached numpy-1.22.3-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (16.8 MB)
Collecting Cython>=0.27.2
  Using cached Cython-0.29.28-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.manylinux_2_24_x86_64.whl (1.9 MB)
Collecting pycparser
  Using cached pycparser-2.21-py2.py3-none-any.whl (118 kB)
Collecting pillow>=8.3.2
  Using cached Pillow-9.0.1-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (4.3 MB)
Building wheels for collected packages: mujoco-py
  Building wheel for mujoco-py (PEP 517) ... done
  Created wheel for mujoco-py: filename=mujoco_py-2.0.2.13-py3-none-any.whl size=5174777 sha256=79dda1d5305b95b50f785584d714f01588efa6ecf7368393c2508392c8fcbb81
  Stored in directory: /tmp/pip-ephem-wheel-cache-fdljxtm8/wheels/eb/6d/a5/e058ecbfa49884139621692160f2f42b2ec4760123f5b28301
Successfully built mujoco-py
Installing collected packages: pycparser, pillow, numpy, imageio, glfw, fasteners, Cython, cffi, mujoco-py
  WARNING: The scripts f2py, f2py3 and f2py3.8 are installed in '/home/litao/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
  Attempting uninstall: imageio
    Found existing installation: imageio 2.16.1
    Uninstalling imageio-2.16.1:
      Successfully uninstalled imageio-2.16.1
  WARNING: The scripts imageio_download_bin and imageio_remove_bin are installed in '/home/litao/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
  Attempting uninstall: glfw
    Found existing installation: glfw 2.5.1
    Uninstalling glfw-2.5.1:
      Successfully uninstalled glfw-2.5.1
  Attempting uninstall: fasteners
    Found existing installation: fasteners 0.17.3
    Uninstalling fasteners-0.17.3:
      Successfully uninstalled fasteners-0.17.3
  WARNING: The scripts cygdb, cython and cythonize are installed in '/home/litao/.local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
  Attempting uninstall: cffi
    Found existing installation: cffi 1.15.0
    Uninstalling cffi-1.15.0:
      Successfully uninstalled cffi-1.15.0
  Attempting uninstall: mujoco-py
    Found existing installation: mujoco-py 2.0.2.13
    Uninstalling mujoco-py-2.0.2.13:
      Successfully uninstalled mujoco-py-2.0.2.13
Successfully installed Cython-0.29.28 cffi-1.15.0 fasteners-0.17.3 glfw-2.5.1 imageio-2.16.1 mujoco-py-2.0.2.13 numpy-1.22.3 pillow-9.0.1 pycparser-2.21
```

在这一步过程后，没有分段错误，但是有包冲突（应该是之前安装改变的），所以使用命令再次安装了如下的强化学习的包：
>git+https://github.com/DLR-RM/stable-baselines3
git+https://github.com/hermanjakobsen/robosuite
安装方法还是一样的：
```py
pip3 install --user --force-reinstall --no-compile git+https://github.com/DLR-RM/stable-baselines3
pip3 install --user --force-reinstall --no-compile git+https://github.com/hermanjakobsen/robosuite
```


报错
```py
Missing path to your environment variable. 
Current values LD_LIBRARY_PATH=
Please add following line to .bashrc:
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/litao/.mujoco/mujoco200/bin
```
