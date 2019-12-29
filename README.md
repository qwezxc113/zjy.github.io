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
正确识别不同的拨号音。利用Python自带的wave库直接处理音频文件，通过matplotlib第三方包画出时域信号、频域信号、能量信号，实现对电话拨号音系统的简单的实验仿真。学习DTMF（双音多频）相关知识，知道双音多频的信号是用两个定的单音频率信号的组合来代表数字或功能。主要涉及到电话拨号音识别的基本原理和识别的主要方法。利用python软件中的scipy模块中的fft（快速傅里叶变换换）算法、ifft（傅里叶反变换）算法以及带通滤波进行抗干扰实现对电话通信系统中拨号音的识别。并利用pyqt5、QTDesigner制作GUI界面。

# 代码
1、	单个按键音识别

(1)  fttc
# path为文件路径
# 输出为x频率信号的自变量，yf2是频率数据，framrate为采样率，nframes为采样点数，yt为单声道的时域数据
def  fftc(path):
    # 打开wav文件 ，open返回一个的是一个Wave_read类的实例，通过调用它的方法读取WAV文件的格式和数据。
    file = wave.open(path, "rb")
    # 一次性返回所有的WAV文件的格式信息，它返回的是一个组元(tuple)：声道数, 量化位数（byte单位）, 采
    # 样频率, 采样点数, 压缩类型, 压缩类型的描述。
    information = file.getparams()
    nchannels, sampwidth, framerate, nframes = information[:4]
    # 读取声音数据，传递一个参数指定需要读取的长度（以取样点为单位）
    str_data = file.readframes(nframes)
    file.close()
    # 将波形数据转换成数组
    # 需要根据声道数和量化单位，将读取的二进制数据转换为一个可以计算的数组
    wave_data = np.fromstring(str_data, dtype=np.short)
    # 将wave_data数组改为2列，行数自动匹配。在修改shape的属性时，需使得数组的总长度不变。
    wave_data.shape = -1, 2
    wave_data = wave_data.T
yt = wave_data[0]
#单通道的时候不需要上述过程
    yf2 = yt
    x = np.arange(0, len(yf2) / 2) * framerate / nframes
    yf1 = abs(fft(yf2)) / len(yt)  # 归一化处理
    yf2 = yf1[range(int(len(yf2) / 2))]  # 由于对称性，只取一半区间
(2)  catch_number

此函数是识别单个数字的按键音，输入为频域信号，采样频率，采样点数。输出为按键音所代表的数字。此函数在连续识别时候被调用。
def catch_number(y, framerate, nframes):
    frh = []
    frl = []
    math_list = [] # 存放频率
    # DTMF编码
    fruquent = [697, 770, 852, 941, 1209, 1336, 1477, 1633]
    math = {(697, 1209): '1', (697, 1336): '2', (697, 1477): '3', (697, 1633): 'A',
            (770, 1209): '4', (770, 1336): '5', (770, 1477): '6', (770, 1633): 'B',
            (852, 1209): '7', (852, 1336): '8', (852, 1477): '9', (852, 1633): 'C',
            (941, 1209): '*', (941, 1336): '0', (941, 1477): '#', (941, 1633): 'D'}
    for fr in fruquent:
        sum1 = sum(y[fr* nframes //framerate-(20* nframes //framerate):fr* nframes //framerate])
        sum2 = sum(y[fr* nframes //framerate:fr * nframes //framerate+(20* nframes //framerate)])
        sumx = sum1 + sum2
        if fr < 1000:
            frl.append([fr, sumx])
        else:
            frh.append([fr, sumx])
    frl = sorted(frl, key=lambda x: x[1])
    frh = sorted(frh, key=lambda x: x[1])
    frl = frl.pop()
    frh = frh.pop()
    if frh[1] < 10:
        return None
    if frl[1] < 10:
        return None
    math_list.append(frl[0])
    math_list.append(frh[0])
    math_list = tuple(math_list)
    for key, value in math.items():
        if key == math_list:
            return value

2、	连续按键音识别

（1）	calZero和calEnergy	

这两个函数是为了计算出能量信号和短时过零率给函数Endpoint_detection提供数据。
#计算短时过零率
def calZero(waveData):
    frameSize = 256
    overLap = 0
    wlen = len(waveData)
    step = frameSize - overLap
    frameNum = math.ceil(wlen/step)
    zcr = np.zeros((frameNum,1))
    for i in range(frameNum):
        curFrame = waveData[np.arange(i*step, min(i*step+frameSize,wlen))]
        curFrame = curFrame - np.mean(curFrame) # zero-justified
        zcr[i] = sum(curFrame[0:-1]*curFrame[1::] <= 0)
return zcr
#计算短时能量
def calEnergy(wave_data) :
    energy = []
    sum = 0
    for i in range(len(wave_data)) :
        sum = sum + (int(wave_data[i]) ** ui.horizontalSlider_2.value() )
        if (i + 1) % 256 == 0 :
            energy.append(sum)
            sum = 0
        elif i == len(wave_data) - 1 :
            energy.append(sum)
    return energy
（2）	Endpoint_detection
此两个函数是为了计算出检测出端点，输出为检测到的端点（因为以256为一帧，所以要*256）。较高的短时能量作为阈值MH，利用这个阈值，我们就可以先分出语音中的浊音部分。取一个较低的能量阈值ML，利用这个阈值，我们可以从向两端进行搜索，将较低能量段的语音部分也加入到语音段，进一步扩大语音段范围。再通过短时过零率分辨。在最后加上语音持续时间分别，把小于10帧的非语音干扰信号删除。
def Endpoint_detection(wave_data, energy, Zero) :
    sum = 0
    energyAverage = 0
    for en in energy :
        sum = sum + en
    energyAverage = sum / len(energy)
    sum = 0
    for en in energy[:10] :
        sum = sum + en
    ML = sum / 10
    MH = energyAverage /4             #较高的能量阈值
    ML = (ML + MH) / 2    #较低的能量阈值
    sum = 0
    for zcr in Zero[:100] :
        sum = float(sum) + zcr
    Zs = sum / 100                    #过零率阈值
   	A = []
    B = []
    C = []
    # 首先利用较大能量阈值 MH 进行初步检测
    flag = 0
    for i in range(len(energy)):
        if len(A) == 0 and flag == 0 and energy[i] > MH :
            A.append(i)
            flag = 1
        elif flag == 0 and energy[i] > MH and i - 21 > A[len(A) - 1]:
            A.append(i)
            flag = 1
        elif flag == 0 and energy[i] > MH and i - 21 <= A[len(A) - 1]:
            A = A[:len(A) - 1]
            flag = 1
		if flag == 1 and energy[i] < MH :
            A.append(i)
            flag = 0

 # 利用较小能量阈值 ML 进行第二步能量检测
    for j in range(len(A)) :
        i = A[j]
        if j % 2 == 1 :
            while i < len(energy) and energy[i] > ML :
                i = i + 1
            B.append(i)
        else :
            while i > 0 and energy[i] > ML :
                i = i - 1
            B.append(i)
    # 利用过零率进行最后一步检测
    for j in range(len(B)) :
        i = B[j]
        if j % 2 == 1 :
            while i < len(Zero) and Zero[i] >= 3 * Zs :
                i = i + 1
            C.append(i)
        else :
            while i > 0 and Zero[i] >= 3 * Zs :
                i = i - 1
            C.append(i)
    # 除去帧数小于10的语音段
    for i, data in enumerate(C):
        if i % 2 == 1:
            x = C[i] - C[i-1]
            if x < 10:
                del C[i]
                del C[i-1]

    print("过零率阈值，最终语音分段C:" + str(C))
    count = []
    for data in C:
        count.append(data * 256)
return count
## gui界面
pyqt5：Qt是一套跨平台的C ++库，它们实现了高级API，可以访问现代桌面和移动系统的许多方面。其中包括定位和定位服务，多媒体，NFC和蓝牙连接，基于Chromium的Web浏览器以及传统的UI开发。
PyQt5是Qt v5的一套全面的Python绑定。它实现为超过35个扩展模块，并使Python能够在所有支持的平台（包括iOS和Android）上用作C ++的替代应用程序开发语言。
PyQt5也可以嵌入到基于C ++的应用程序中，以允许这些应用程序的用户配置或增强这些应用程序的功能。
QTDesigner：Qt Designer是Qt工具，用于使用Qt Widgets设计和构建图形用户界面（GUI）。可按照所见即所得（WYSIWYG）的方式编写和自定义窗口或对话框，并使用不同的样式和分辨率对其进行测试。
使用Qt信号和插槽机制，使用Qt Designer创建的小部件和表单与编程代码无缝集成，因此可以轻松地将行为分配给图形元素。Qt Designer中设置的所有属性都可以在代码中动态更改。此外，小部件升级和自定义插件等功能允许在Qt Designer中使用自己的组件。

### 结论
音频识别正确率较高。在识别连续按键音时，若音频文件中各按键音的间隔时间过短，则识别错误率普遍较高。在音频文件较为理想的情况下，识别正确率高。一些杂音的存在易导致识别错误。在打开GUI页面调用函数时，经常出现页面卡死现象，且识别速度时快时慢。

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
