### 最基本的OpenCV程序
```python
import cv2 as cv

src = cv.imread("C:/Users/ZQ/Desktop/verifycode.jpg")
cv.namedWindow("input image", cv.WINDOW_AUTOSIZE)
cv.imshow("input image", src)
cv.waitKey(0)
cv.destroyAllWindows()
```

## OpenCV函数
```python
imread(path)  #读入一张图片

imshow(nameSpace, src)  #展示一张图片

namedWindow(nameSpace, command)  #command = cv.WINDOW.NORMAL可以调节展示图片的大小

VideoCapture(path)  #读入视频流; 如果接入摄像头path = 0, 如果要读取视频path为视频路径

ret, frame = capture.read()  #ret 为视频是否到尾部, frame为视频的每一帧, 一般使用方法如下
    while(True):
        ret, frame = capture.read()
        frame = cv.flip(frame, 1)
        cv.imshow("tset", frame)
        c = cv.waitKey(40)
        if(ret == False):
            break
        if c == 27:
            break
    
flip(frame, commond)  #是否左右翻转视频, commond默认为0不翻转, 翻转时commond为1

image.shape  #返回一个image长宽以及通道数的数组

image.size  #返回图片大小

image.dtype  #返回读取图片的位深度

bitwise_not(src)  #对整个图像取非
bitwise_nor(src1, src2) #对两幅图像异或
bitwise_or(src1, src2)  #对两幅图像或
bitwise_and(src1, src2)  #对两幅图像去与
    
getTickCount()  #取当前CPU运行计数
getTickFrequency()  #取当前CPU运行频率
一般如下使用：
t1 = cv.getTickCount()
.......
t2 = cv.getTickCount()
Time = (t2 - t1)/cv.getTickFrequency() * 1000
        
convertFp16(ndarray , np.数据类型)  #转换ndarray的数据类型

cvtColor(image, cv.颜色转换关系)  #不同颜色系统之间转换; 
        cv.COLOR_BGR2GRAY  #RGB转灰色
        cv.COLOR_BGR2HSV  #RGB转HSV颜色
        cv.COLOR_BGR2YUV  #RGB转YUV颜色
        
inRange(src, lowerb[], upperb[])  #对颜色进行选择

imshow("mask", mask)  #利用遮罩显示图片

split(frame)  #分离三个通道
merge([b,g,r])  #合并三个通道
    
add(src1, src2)  #对两幅图片加法运算
substract(src1, src2)  #对两幅图片减法运算
multiply(src1, src2)  #对两幅图片乘法运算
divide(src1, src2)  #对两幅图片除法运算
    
mean(src)  #对每一个通道求出一个均值生成一个列表
M1, M2 = meanStdDev()  #M1得到每个通道的均值， M2得到每个通道的方差
    
addWeighted(src1, weight1, src2, weight2, bright)  #混合两张图片，每一个像素点值为
```
$$
\frac{src_1 * weight_1 + src_2 * weight_2}{weight_1 + weight_2} + light
$$

```python
mask = np.zeros([src.shape[0] + 2, src.shape[1] + 2], src.dtype)  #创建一个遮罩,需要的部分为1，不需要的部分为0
floodFill(src, mask, (开始点位置[二维]), (填充颜色), (比此颜色小的最大值), (比此颜色大的最大值), cv.FLOODFILL_FIXED_RANGE)  #利用所给点范围内颜色差来填充
floodFill(src, mask, (开始点的位置), (color), cv.FLOODFILL_MASK_ONLY )  #利用遮罩来填充, 遮罩形状可以修改上述mask实现 
```
