---
layout: post
title: "音频质量评估-2"
subtitle: '音频质量评估'
author: "叉叉敌"
header-style: text
tags:
  - 视频
  - webrtc
  - PEVR
  - 测试
---

[音频质量评估-1](https://chasays.github.io/2020/09/23/%E9%9F%B3%E8%A7%86%E9%A2%91%E8%B4%A8%E9%87%8F%E8%AF%84%E4%BC%B0-1/)：之前主要学习了音视频的编码和解码原理，和测试音频质量的方法。接下来继续学习下当前 短视频 领域的 视频质量测试方法。

## 算法了解

>可以参考python的 [scikit-image](https://scikit-image.org/docs/stable/index.html)这个库。里面有很很多算法。

- PSNR

用于表示信号的`最大可能功率与影响信号表示`的保真度的腐蚀噪声功率之间的比率。由于许多信号具有非常宽的动态范围，PSNR通常以对数分贝刻度表示。 是一个全参考算法



- SSIM Structural SIMilarity
因为视频就是很多帧图片合成的，然后通过编码压缩后的。因此测试视频质量 在测试图片的质量就很重要了。
`测量两个图像之间的相似性的方法`。SSIM指数可以看作是对被比较图像之一的`质量衡量标准`，前提是其他图像被视为质量完美。


```python
# Based on: https://github.com/mostafaGwely/Structural-Similarity-Index-SSIM-

# pip3 install scikit-image opencv-python imutils

from skimage.measure import compare_ssim
import imutils
import cv2


# 3. Load the two input images
imageA = cv2.imread("1.png")
imageB = cv2.imread("2.png")
# 4. Convert the images to grayscale
grayA = cv2.cvtColor(imageA, cv2.COLOR_BGR2GRAY)
grayB = cv2.cvtColor(imageB, cv2.COLOR_BGR2GRAY)

# 5. Compute the Structural Similarity Index (SSIM) between the two
#    images, ensuring that the difference image is returned
(score, diff) = compare_ssim(grayA, grayB, full=True)
diff = (diff * 255).astype("uint8")

# 6. You can print only the score if you want
print("SSIM: {}".format(score))

```

## 视频质量

基础测试指标

![gMLWMP](https://gitee.com/chasays/mdPic/raw/master/uPic/gMLWMP.png)

一是视频流畅度相关的参数，比如说fps, 视频帧抖动jitter，导入延时，端对端延时等，这方面主要影响因子是网络因数，上下行带宽，网络拥塞等，这些可以通过产品开发接口或者日志信息等获取相关的参数信息，

- FPS 帧数
- 抖动
- 延时
- e2e 延时
- 网络因子 --- `带宽， 网络拥塞`


除此之外呢，就是对视频画面也就是`视频帧观感的评估`， 业界有主观和客观的。
![VxdMn8](https://gitee.com/chasays/mdPic/raw/master/uPic/VxdMn8.png)

主观就是 每个人有每个人的看法。 采用平均意见分 MOS meanopinion score
- 观看距离
- 观测环境
- 测试序列的选择
- 序列的显示时间间隔等

客观就是根据每一帧的质量来量化， 和audio一样，分为全参考和无参考。概念都和差不多。

- 有参考评估，就是依赖原始视频和待评测视频进行对比，目前比较熟知的就是PSNR, SSIM VIF VMAF PEVQ等
- 无参考方法，在判断视频质量时不需要来自原始参考视频的任何信息，通过对`失真视频空域和频域`的处理分析来提取失真视频的特征，或者基于视频像素的质量模型等来得到视频质量。这种评估标准适合与线上无原始参考视频序列的无线和IP视频业务，或者输入和输出差异化的模型，比如说视频增强，视频合并等场景

## 测试框架

目前知晓的有2个，一个 [QoSTestFramework](https://github.com/open-webrtc-toolkit/QoSTestFramework)，一个是Netflix的 [vmaf](https://github.com/Netflix/vmaf)

### QoSTestFramework（这个里面集成了VMAF）

- 丰富的指标  ---用了 FR和NR 视频质量测试的指标 比如PSNR等
- 模块化 --- 易于扩展
- 可视化 -- 所有结果都可视化

框架图

![aH5AqV](https://gitee.com/chasays/mdPic/raw/master/uPic/aH5AqV.jpg)

- Qos server 处理来自web的请求
- High modular and scalable 预处理 
- Analysis module  分析
- Web Application  -- 触发测试任务和可视化结果显示
- Video transmission adapter module -- 用于不同实时视频系统的适配

###  VMAF  Video Multi-Method Assessment Fusion

![D8zGgO](https://gitee.com/chasays/mdPic/raw/master/uPic/D8zGgO.jpg)


>VMAF 是 Netflix 开发的感知视频质量评估算法。VMAF 开发工具包 （VDK） 是一个包含 VMAF 算法实现的软件包，以及一组允许用户训练和测试自定义 VMAF 模型的工具。

- VMAF python 库 - 提供完整的功能，包括运行基本的 VMAF 命令行、在一批视频文件上运行 VMAF、在视频数据集上训练和测试 VMAF 模型以及可视化工具等。
- vmafossexec - a C++ executable 

### 流媒体性能测试

https://github.com/winlinvip/srs-bench 服务器负载测试工具SB

## read more

[视频质量评估综述](https://testerhome.com/topics/19932)
https://ece.uwaterloo.ca/~z70wang/research/ssim/