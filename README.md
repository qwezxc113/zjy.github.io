##欢迎来到我们的网站

我们的课题是根据音频拨号音识别出当前所拨的号码
小组成员：应玲玲
         张佳媛
         江玲

### 课题概述

1、目的
（1）通过人为的按键输入形成录音m4a文件，再经Au处理形成wav文件，最终通过GUI界面输出结果。
（2）通过对连续语音信号的端点检测，了解语音截取的算法
2、原理
（1）每个按键音都是通过两个定的单音频率信号的组合来代表数字或功能。分为高频群和低频群，每个群里有四个不同的频率的信号，一共可以代表16个按键
（2）通过fft（快速傅里叶变换换）算法，把每个按键音时域信号转换成频域信号，再通过DTMF（双音多频）表进行分辨
3、实现 
正确识别不同的拨号音。利用Python自带的wave库直接处理音频文件，通过matplotlib第三方包画出时域信号、频域信号、能量信号，实现对电话拨号音系统的简单的实验仿真。学习DTMF（双音多频）相关知识，知道双音多频的信号是用两个定的单音频率信号的组合来代表数字或功能。
	主要涉及到电话拨号音识别的基本原理和识别的主要方法。利用python软件中的scipy模块中的fft（快速傅里叶变换换）算法、ifft（傅里叶反变换）算法以及带通滤波进行抗干扰实现对电话通信系统中拨号音的识别。并利用pyqt5、QTDesigner制作GUI界面。

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/qwezxc113/zjy.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
