# 快速开始1 - 基于MetaBCI编写离线实验范式

MetaBCI是天津大学神经工程团队开发的一款基于Python的脑机接口实验设计框架。通过使用框架中的三个模块：Brainda、Brainstim以及Brainflow，研究人员可以快速地搭建起离线分析、刺激呈现以及在线分析模块。MetaBCI的还提供了多个demo，用于演示各功能模块的使用（[MetaBCI/demos](https://github.com/TBC-TJU/MetaBCI/tree/master/demos)）。但是，由于缺少文字引导，初次接触MetaBCI的研究人员需要从代码中推测实验的开发流程，加大了学习的成本。同时，笔者在使用MetaBCI的过程中遇到了许许多多的坑。为了方便后人快速上手，避免重复蹚雷，本文应运而生。本文基于一个简单的运动想象和SSVEP的混合BCI，演示如何使用MetaBCI快速编写一个实验范式。

- 前驱知识：在Python中编写实验范式使用的是**psychopy**框架。另外，需要读者懂得**面向对象编程**的基础知识。如果不懂问题也不大，跟随笔者的脚步，哪里不会搜哪里。

## 文件结构

MetaBCI中实现一个实验范式需要三部分的内容：

1. 范式类，用于配置范式中用到的各个元素。
2. 范式函数，实验范式的实现逻辑。控制范式类中的元素以怎样的逻辑呈现在屏幕上，是真正意义上的实验范式。
3. 范式对象，初始化范式类的对象。根据实验要求设置各元素的参数，设置范式函数的参数。

MetaBCI在文件 `metabci/brainstim/paradigm.py `提供了几个常用的实验范式（MI、SSVEP、P300等）的范式类和范式函数。在文件`demo/brainstim_demos/stim_demo.py`中演示了如何初始化范式的参数，并让范式跑起来。

因为MetaBCI的paradigm.py中的范式不满足我们的使用要求，所以我们仿照它编写自己的paradigm.py。我们的文件组织如下：

```
textures
	left_hand.png
	right_hand.png
stim_demo.py
paradigm.py
```

其中textures是文件夹，里面存放左右手的图片。

## 实验窗口和开始界面

我们使用Psychopy框架设计实验。设计实验的第一步是实例化一个屏幕。

```python
# stim_demo.py

from psychopy import monitors

if __name__ == '__main__':
    mon = monitors.Monitor(
        name='primary',
        width=59.6, distance=60,  # width 显示器尺寸cm; distance 受试者与显示器间的距离
        verbose=False
    )
    mon.setSizePix([1920, 1080])  # 显示器的分辨率
```

上面的代码实例化了一个屏幕，后续的实验都将在这个屏幕对象中显示。

- 值得注意的是，`name='primary' `是屏幕的名字，在某些电脑上， 可能因为编码问题报错。这个时候只需要将名字改成数字即可，即`name='1'`。



## 范式类中的元素

设计一款实验范式，我们首先需要思考实现这个范式需要用到哪些元素（即实验过程中电脑屏幕中显示的内容）。我们希望设计一个基于运动想象和SSVEP的混合BCI实验范式，被试在每个trail中，进行左手或者右手的运动想象，同时盯着屏幕中闪烁的图标，激发SSVEP信号。要实现这个实验，需要用到哪些元素呢？对于运动想象而言，我们只需要**两张图片，分别代表左手和右手**，如下所示。

<center>  
<figure class="half">     
    <img src="https://cdn.jsdelivr.net/gh/smart-huang/bolgImage@main/img/left_hand.png"> 
    <img src="https://cdn.jsdelivr.net/gh/smart-huang/bolgImage@main/img/right_hand.png"> 
</figure>
</center>


然后，需要一个**指示物**，用于提示被试本次trail需要进行哪个手的运动想象。本实验使用的思路是，使用不同颜色的图片作为指示。如下图所示，使用纯黑色背景，白色的图片指示本次trail的目标。

<center>   
    <img src="https://cdn.jsdelivr.net/gh/smart-huang/bolgImage@main/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20230403104030.png"> 
</center>

对于SSVEP，在实验阶段，左右手的图片需要以**不同的频率闪烁**。

综上，要实现该实验，至少需要3种不同的元素：

1. 灰色的左右手图片
2. 白的的左右手图片
3. 左右手的闪烁

## 自定义范式类

基于上面的3种元素，我们可以提取出自定义的范式类的框架：

```python
class MyStim(object):
    
    # 构造函数
    def __init__(self):
        pass
    
    # 配置左右手图片，包括灰色和白色的
    def config_stim(self):
        self.left_index     # 白色左手
        self.right_index    # 白色右手
        self.left_stimuli   # 灰色左手
        self.right_stimuli  # 灰色右手
    
    # 配置SSVEP刺激
	def config_flash(self):
        self.flash_stimuli = []  # 闪烁刺激
```

### 构造函数

构造函数用于从类创建实例对象。当我们执行下列语句时，构造函数会自动调用。

```python
mystim = MyStim()
```

通过构造函数不仅可以创建类的对象，而且可以通过输入参数的形式，初始化对象的成员变量的值。

```python
import os

def __init(self, win, colorSpace='rgb', allowGUI=True):
	# 设置屏幕的色域
    self.win = win
    win.colorSpace = colorSpace
    win.allowGUI = allowGUI

    self.tex_left = os.path.join(os.path.abspath(os.path.dirname(os.path.abspath(__file__))),
                                     'textures\\left_hand.png')
    self.tex_right = os.path.join(os.path.abspath(os.path.dirname(os.path.abspath(__file__))),
                                      'textures\\right_hand.png')
```

win是`psychopy.visual.Window`对象。`colorSpace`是win的色域，rgb是三原色，即红、绿、蓝。值得注意的是，同样是rgb模式，不同的软件框架使用的数值范围也不同。最常见的三种是 [0, 255]、[0, 1]、[-1, 1]。我们最长使用的是[0, 255]，即[0, 0, 0]代表黑色，[255, 255, 255]代表白色。MetaBCI默认使用的是 [-1, 1]，有点反人类。但是为了与MetBCI保持同步，这里也采用这个方案。

构造函数初始化了两个成员变量，分别是左右手的图片的源文件。

### 配置左右手图片属性

```python
import numpy as np

def config_stim(self, left_pos=None, right_pos=None, stim_length=288, stim_width=288, stim_color=None, index_color=None):
	# 设置刺激的颜色和位置
    if index_color is None:
    	index_color = [[1, 1, 1]]
    if stim_color is None:
    	stim_color = [[-0.8, -0.8, -0.8]]
    if right_pos is None:
    	right_pos = [480, 0]
    if left_pos is None:
    	left_pos = [-480, 0]
       
    # 记录图片大小，用于保证生成的SSVEP刺激与图片大小相同
    self.stim_sizes = np.array([stim_length, stim_width])
    # 记录图片位置，用于保证生成的SSVEP刺激与图片在相同位置
    self.stim_pos = np.array([left_pos, right_pos])
```

就像上面说的，MetaBCI使用的色域是数值范围[-1, 1]的RGB模式，[1, 1, 1]是白色，[-0.8, -0.8, -0.8]是灰色。 

要理解图片位置需要以下知识点：

- 屏幕是由许多像素点组成的，像素点的数量就是屏幕的分辨率。最常见的分辨率是 1920*1080，即横向1920个像素点，纵向1080个像素点。这些像素点构成的二维矩阵即我们的屏幕。
- 每个像素点在二维矩阵中应该有一个坐标，坐标的值取决于坐标原点如何选取。有的框架中坐标原点位于左上角，坐标值向右向下递增，即左上角坐标[0, 0]，右下角坐标[1920, 1080]。而在Psychopy中，坐标原点位于屏幕中心点，坐标的值向右向上增加，向左向下减小。坐标值可以是负值。
- 图片的位置`right_pos`和`left_pos`是图片的中心点的坐标。

通过上上面的代码，我们设置：

- 正常的图片是灰色，用于提示的图片是白色
- 图片纵向居中，左手图片位置屏幕左侧，右手图片位于图片右侧

### 实例化图片

`psychopy.visual`提供了多种可视化刺激类，详见[psychopy.visual](https://psychopy.org/api/visual/index.html)。`ImageStim`用于在屏幕中显示图片类型的刺激。但是MetaBCI中使用的是`ElementArrayStim`，这个类用于显示多个相同类型的刺激。参数`nElements`是该类型刺激的数量。

```python
#  实例化图片
n_Elements = 1
self.left_index = visual.ElementArrayStim(self.win, units='pix', elementTex=self.tex_left,
                                          elementMask=None, texRes=2, nElements=n_Elements,
                                          sizes=[[stim_length, stim_width]], xys=np.array([left_pos]),
                                          oris=[0], colors=np.array(index_color), opacities=[1],
                                          contrs=[-1])

self.right_index = visual.ElementArrayStim(self.win, units='pix', elementTex=self.tex_right,
                                           elementMask=None, texRes=2, nElements=n_Elements,
                                           sizes=[[stim_length, stim_width]], xys=np.array([right_pos]),
                                           oris=[0], colors=np.array(index_color), opacities=[1],
                                           contrs=[-1])

self.left_stimuli = visual.ElementArrayStim(self.win, units='pix', elementTex=self.tex_left,
                                            elementMask=None, texRes=2, nElements=n_Elements,
                                            sizes=[[stim_length, stim_width]], xys=np.array([left_pos]),
                                            oris=[0], colors=np.array(stim_color), opacities=[1],
                                            contrs=[-1])
self.right_stimuli = visual.ElementArrayStim(self.win, units='pix', elementTex=self.tex_right,
                                             elementMask=None, texRes=2, nElements=n_Elements,
                                             sizes=[[stim_length, stim_width]], xys=np.array([right_pos]),
                                             oris=[0], colors=np.array(stim_color), opacities=[1],
                                             contrs=[-1])
```

### 配置SSVEP刺激

```
def config_flash(self, refresh_rate, stim_time, flash_color, freqs, phases, stimtype='sinusoid', stim_opacities=1):
    if refresh_rate == 0:
    	self.refresh_rate = np.floor(self.win.getActualFrameRate(nIdentical=20, nWarmUpFrames=20))
    # 刺激的帧数 = 刺激时长 * 屏幕刷新率
    stim_frames = int(stim_time * refresh_rate)

    n_elements = 2
    stim_oris = np.zeros((n_elements,))  # orientation
    stim_sfs = np.zeros((n_elements,))  # spatial frequency
    stim_contrs = np.ones((n_elements,))  # contrast
```

### 生成正弦控制信号

```python
# preFunctions
def sinusoidal_sample(freqs, phases, srate, frames, stim_color):
    time = np.linspace(0, (frames - 1) / srate, frames)
    color = np.zeros((frames, len(freqs), 3))
    for ne, (freq, phase) in enumerate(zip(freqs, phases)):
        sinw = np.sin(2 * pi * freq * time + pi * phase) + 1
        color[:, ne, :] = np.vstack(
            (sinw * stim_color[0], sinw * stim_color[1], sinw * stim_color[2])
        ).T
        if stim_color == [-1, -1, -1]:
            pass
        else:
            if stim_color[0] == -1:
                color[:, ne, 0] = -1
            if stim_color[1] == -1:
                color[:, ne, 1] = -1
            if stim_color[2] == -1:
                color[:, ne, 2] = -1

    return color
```

```python
# 生成正弦控制信号
if stimtype == 'sinusoid':
    self.stim_colors = sinusoidal_sample(freqs=freqs, phases=phases, srate=refresh_rate,
                                         frames=stim_frames, stim_color=flash_color)
if self.stim_colors[0].shape[0] != n_elements:
    raise Exception('Please input correct num of stims!')

# 检查stim_colors的尺寸是否合法
incorrect_frame = (self.stim_colors.shape[0] != stim_frames)
incorrect_number = (self.stim_colors.shape[1] != n_elements)
if incorrect_frame or incorrect_number:
    raise Exception('Incorrect color matrix or flash frames!')
```



```
self.flash_stimuli = []
for sf in range(stim_frames):
	self.flash_stimuli.append(visual.ElementArrayStim(win=self.win, units='pix', nElements=n_elements,
                                                      sizes=self.stim_sizes, xys=self.stim_pos,
                                                      colors=self.stim_colors[sf, ...],
                                                      opacities=stim_opacities,
                                                      oris=stim_oris, sfs=stim_sfs,
                                                      contrs=stim_contrs,
                                                      elementTex=np.ones((64, 64)), elementMask=None,
                                                      texRes=48))
```



## 自定义范式函数

设计自己的实验范式似于拼积木。范式类中各个元素等同于不同的积木块，范式函数便是如何将积木块拼装在一块。

- 视频是由一张张图片构成的，当这些图片连续地快速呈现在屏幕上，在人的眼中便是图片。每一张图片称为一帧。
- 电脑屏幕的刷新率就是屏幕在1秒钟的时间内最多呈现多少帧。比如60Hz的屏幕1秒钟可以呈现60帧。
- 不管是Psychopy还是Matlab中的Pschtoolbox，都将屏幕抽象为前后两个屏幕。在后面的屏幕绘制图像，绘制好以后翻转两个屏幕的位置，将后屏幕置于前面，呈现刚刚绘制的图像；前屏幕置于后面，等待下次绘制。
- 在后屏幕绘制好图像，然后翻转屏幕，这便是一帧。

我们的实验其实就是一段可交互的视频。我的任务便是设计每一帧的绘制的内容。

![微信图片_20230406101206](E:\Desktop\微信图片_20230406101206.png)

​          

```python
from metabci.brainstim.utils import _check_array_like

def paradigm(VSObject, win, bg_color, display_time=1.0, index_time=1.0,
        	 rest_time=0.5, port_addr=9045, nrep=1, pdim="ssvep", online=None,
        	 device_type='NeuroScan'):
    if not _check_array_like(bg_color, 3):
        raise ValueError("bg_color should be 3 elements array-like object.")
    win.color = bg_color
    fps = VSObject.refresh_rate
```

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       



















