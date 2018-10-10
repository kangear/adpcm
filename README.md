
ADPCM移植指南
---

ADPCM是一种音频压缩算法，4位的压缩率，即原文件大小为16KiB的话，压缩完是4KiB。这个算法一般是C语言版本在网上公开的。因为需要将其用在`js`语言中，故对其做了移植，这里记录移植过程和思路，也属于工作总结。

# 选择一段大众的音频win7.wav
我一般喜欢使用win7.wav，其为Windows 7开机音乐，比较不失一般性。

# 体验ADPCM压缩
一般地，了解一个新东西一般是在做任何编程之前先体验，有了直观的认识之后编程时就会思路会比较清晰。
Audacity含有ADPCM压缩功能，我们体验一下。使用Audacity打开win7.wav，可以看到win7.wav是`44100`采样率、`16bit`，为了测试压缩效果，我们以相同参数导出压缩后的数据，导出步骤如下`File -> Export Audio`，格式选择`Custom FFmpeg Export`然后点击`Open custom FFmpeg format options`在`Format`选择`wav`，`Codes`选择`adpcm_ima_wav`。右侧`Bit Rate`选择`16`，`Sample Rate`选择`44100`。如下图所示：

![在这里插入图片描述](https://img-blog.csdn.net/20181010164421405?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2thbmdlYXI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
大小对比如下：
```
-rw-rw-r-- 1 tony tony  570478 10月 10 15:40 win7.adpcm_ima
-rw-rw-r-- 1 tony tony 2265820 9月  18 17:42 win7.wav
```
```
2265820/570478=3.97
```
我们计算一下，大小差不多差4倍，关于`wav`的头部信息更多解释见这里：http://soundfile.sapp.org/doc/WaveFormat/

使用Audacity打开压缩过的win7.adpcm_ima后还是可以听到和原来音质差不的音乐。
这是压缩了，不过这里只能展示`pcm`到`adpcm`的转换，不能`adpcm`到`pcm`的转换。

# 使用C程序adpcm

## 编译C程序用于压缩解压缩
使用网络上公开的adpcm算法https://github.com/ctuning/ctuning-programs/blob/master/program/cbench-telecom-adpcm-d/adpcm.c
得到`adpcm`。

## 将win7.wav转换成win7_16bit_8k_mono.pcm
即去掉wav头，将其转换成16bit，采样率为8k，声音为`mono`单声道。
注：为保证提取正确，可以在提取后，使用Audacity打开试听，试听正常则提取正常，否则失败。

## 将win7_16bit_8k_mono.pcm压缩成win7_16bit_8k_mono.adpcm
使用`adpcm`将`win7_16bit_8k_mono.pcm`压缩成`win7_16bit_8k_mono.adpcm`。
注：生成的adpcm没有办法单独验证正确与否。需要解压后试听；

## 将win7_16bit_8k_mono.adpcm解压成win7_16bit_8k_mono.pcm2
使用`adpcm`将`win7_16bit_8k_mono.adpcm`解压成`win7_16bit_8k_mono.pcm2`。
注：使用Audacity导入`Raw Data`方式试听`win7_16bit_8k_mono.pcm2`，如果试听正常，则压缩解压正常，否则失败。

# 开始移植到js语言中
有了`win7_16bit_8k_mono.pcm`、`win7_16bit_8k_mono.adpcm`和`win7_16bit_8k_mono.pcm2`这些死数据，对移植能起到很多的作用。
adpcm算法就是对数据操作一些作用，作一些加减乘除操作，将其对应得转换成`js`中的运行符即可。
压缩完，使用二进制对比工具和死数据对比；
解压完，使用二进制对比工具和死数据对比；
每一步的对比数据必须完全一致，才是移植成功。
就这样就完成了移植。移植到其他语言中也是一样的步骤，首先两种新语言和C语言的差异要了解就会方便移植一些。





