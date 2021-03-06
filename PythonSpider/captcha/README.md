# 运用机器学习进行验证码识别

前段时间学习了python的网络爬虫，在登录时，碰到了需要输入
验证码的情况，在经过了简单的查询之后，准备运用libsvm的库
进行机器学习，从而能够识别出简单的验证码。

---

## 一、所需环境

本代码实现环境如下：
>1. Centos6.4 64位系统
>2. Python3以及PIL库
>3. [libSVM](http://www.csie.ntu.edu.tw/~cjlin/libsvm/)<br />

这里稍微提一下linux64位系统下libSVM的安装方法：
```
cd libsvm-3.22
make
cd python
make
cp svm.py svmutil.py /usr/local/python3/lib/python3.6
cp ../libsvm.so.2 /usr/local/python3/lib/
```

## 二、识别思路总结

>对于验证码进行降噪处理，对于规律的图片进行字符分割。

>对分割后的图片进行人工分类，这是重要的一步，训练初期的数据就来源于你的归类信息

>分割后的字符图片即已经实现了降低维度的处理，随后将图片信息向量化，生成svm的特征值

>爬取验证码图片，生成svm的训练数据，然后运用libsvm生成训练的模型

>准备测试数据，使用svm进行预测，查看预测的准确度

>根据预测的准确度，来进行训练数据的增加，以此来提高机器学习的准确度

### 1.图片预处理
图片降噪，针对噪点，干扰线等简单的干扰，二值化后对黑点的周围进行一个个分析即可。

原图初始如下：

![index](https://github.com/SuperHighMan/celery/raw/master/PythonSpider/captcha/image/captcha_1.png)

处理后图片如下：

![index](https://github.com/SuperHighMan/celery/raw/master/PythonSpider/captcha/image/captcha_2.png)

图片分割使用软件放大像素后，找出规律即可，如下例子：
![index](https://github.com/SuperHighMan/celery/raw/master/PythonSpider/captcha/image/captcha_3.png)

此处我们分割出每个字符的间隔为3个像素。

在完成了这一步之后，我们就有了一些验证码字符的样本，接下来需要将信息特征化。

### 2.图片字符分类
针对分割好的图片，我们首先应对其进行分类，此处都选取了数字的验证码进行实验。

最后我们会得到10个分完类的文件夹。0-9


### 3.特征化图片信息：
libSVM的训练数据采用向量机的形式：
格式信息如下：
```
+1 1:0.708333 2:1 3:1 4:-0.320755 5:-0.105023 6:-1 7:1 8:-0.419847 9:-1 10:-0.225806 12:1 13:-1
-1 1:0.583333 2:-1 3:0.333333 4:-0.603774 5:1 6:-1 7:1 8:0.358779 9:-1 10:-0.483871 12:-1 13:1
+1 1:0.166667 2:1 3:-0.333333 4:-0.433962 5:-0.383562 6:-1 7:-1 8:0.0687023 9:-1 10:-0.903226 11:-1 12:-1 13:1
-1 1:0.458333 2:1 3:1 4:-0.358491 5:-0.374429 6:-1 7:-1 8:-0.480916 9:1 10:-0.935484 12:-0.333333 13:1

```
数据格式为 label \<index>:\<value> \<index>:\<value>
>思考：我们的图片信息如何转化成特征值

通过观察不难发现，图片中的黑点数可作为特征，最高的一种维度方式就是将height*width
数量的维度全部作为向量进行输入训练。但其实可以降低维度，统计height+width数量的
维度即可，我们只要统计出每一行的黑点数量即可。

label即为字符数字， index表示维度，value表示黑色点数

最后我们可以从分完类的文件夹中生成训练数据。

### 4.svm模型训练及测试
使用libSVM库，我们可以很方便的对训练数据生成模型，随后准备测试数据，对模型进行
检验，根据输出的准确率，丰富训练数据，优化模型，一步步的再进行测试。
通过不断的周而复始的训练，让机器的识别率提高。

我训练了大概1200张字符图，平均每个字符120张图片
最后破解率非常高，几乎每张图片都能识别出验证码
如下所示：

![index](https://github.com/SuperHighMan/celery/raw/master/PythonSpider/captcha/image/captcha_4.png)

## 三、后续的想法

1. 结合自动识别验证码，来对某些网站进行登录测试
2. [CNN卷积神经网络学习运用](https://github.com/SuperHighMan/MachineLearning/blob/master/README.md)
>目前正在使用keras库进行验证码的识别
3. TensorFlow的学习

## 四、感谢

### 参考资料
1. LIBSVM -- A Library for Support Vector Machines[http://www.csie.ntu.edu.tw/~cjlin/libsvm/](http://www.csie.ntu.edu.tw/~cjlin/libsvm/)
2. 字符型图片验证码识别完整过程及Python实现 [http://www.cnblogs.com/beer/p/5672678.html](http://www.cnblogs.com/beer/p/5672678.html)

以上:smile: