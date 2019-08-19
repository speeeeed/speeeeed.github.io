---
title: python读写wav文件
date: 2019-06-30
tags: [python, audio]
---


## 读取wav文件

Python读取wav文件，使用wave模块，使用时需要import该模块。
wave模块是python自带的模块：https://docs.python.org/3/library/wave.html

```python
import wave

f = wave.open(file_path, 'rb')   # f中包含了file_path文件的参数， 'rb'表示读，'wb'表示写
params = f.getparams()           # 读取wave文件的信息，params是一个tuple
nchannels, sampwidth, framerate, nframes = params[:4]  # tuple的前四个元素的含义
str_data = f.readframes(nframes) # 读取n个frame
f.close()                        # 关闭文件
```

如果需要使用numpy来处理wave文件，使用matplotlib来画出图形的话，则需要对读出来读数据做一下转换。

```python
import numpy as np
import matplotlib.pyplot as plt

wave_data = np.fromstring(str_data, dtype = np.int16)  # str_data为上面读取出来的wave数据
# 此时的数据是一维的，如果是双声道，还需要做一下转换
if nchannels == 2:
    wave_data.shape = -1, 2                      # 将wave_data这个一维数组，变为一个二维矩阵，2列，行数-1的含义是自动算出
    wave_data = wave_data.T                      # 得到wave_data的转置，即2行，第一行是左声道，第二行是右声道

time = np.arange(0, nframes) * (1.0 / framerate) # 根据frame个数和sample rate，算出每个frame对应的时间点，形成时间序列

# 画出波形图
if nchannels == 1:
    plt.plot(time, wave_data)                    # 单声道直接画出
else:
    plt.subplot(211)
    plt.plot(time, wave_data[0])                 # 左声道数据
    plt.subplot(212)
    plt.plot(time, wave_data[1], c = "g")        # 右声道数据

plt.show()
```

## 写入wav文件

Python写入wav文件同样适用wave模块，打开时使用'wb'方式打开。

```python
import wave
import numpy as np

out_f = wave.open(file_path, 'rb')

np.arange(0, 16000, 1)
out = 32767 * np.sin(0.1 * data)            # 要写入的数据
out = np.array(out, dtype = np.int16)
double_out = np.array([out, out])           # 双声道数据
double_out = double_out.T                   # 矩阵转置成2列，方便转成string data
out_data = double_out.tostring()            # 真正待写入的out data

nchannels = 2
samplewidth = 2
fs = 16000
framerate = int(fs)
nframes = len(out_data) / nchannels
comptype = "NONE"
compname = "not compressed"
out_f.setparams((nchannels, samplewidth, framerate, nframes, comptype, compname))

out_f.writeframes(outData)

out_f.close()
```
