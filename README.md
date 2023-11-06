# DIPProcessor
image process class homework

2023年哈尔滨工业大学（威海）数字图像处理课程作业
C++不使用非标准库实现基本的数字图像处理的原理
程序提供10个标准功能，以及控制台标准输入的交互界面

Dev环境：
WSL2.0:Ubuntu-20.04  GCC9.4.0 x86_64-linux-gnu Cmake

语言：C++

### 目录结构：
##### src
###### image_process (作业1 图像变换)
###### image_histogram (作业2 图像直方图)
###### spatial_domain_filtering（作业3 空间滤波）
###### image_transformation（作业4 图像变换）
###### threshold_segmentation（作业5 阈值分割）
###### region_segmentation（作业6 区域分割）
###### edge_election（作业7 边缘检测）
###### hough_transformation（作业8 hough变换）
###### zone_marker（作业9 区域标记）
###### contour_extraction（作业10 轮廓提取）

### 运行
mkdir build  
cd build  
cmake ..  
cmake --build .  
./image-server 要处理的图像的路径  

###### 2.1文件处理
	转为灰度图像：1.读取bmp文件，在内存中修改相应的文件头信息并重新写回磁盘2.数据部分对每个unit（24位为三位R、G、B），用公式(299 * r + 587 * g + 114 * b)/ 1000求出对应的灰度值写回内存一个新的data数组里面，最后写到磁盘file。
	颜色反转：1.读取bmp文件2.对每个位图数据的数组中每个元素取255 – 当前值并重写写回磁盘
	通道分离：1.读取bmp文件2.对每个位图数据的数组中每个Unit拆出R、G、B三个不同的值分别写到一个单独的data数组中，将这三个新的data数组写到新的磁盘file中
###### 2.2直方图处理
	得到直方图：1.读取bmp文件2.内存中开辟一个256大小的数组，对每个位图数据统计个数3.统一y坐标4.生成一个新的bmp文件头，刷到磁盘file，并将内存中的统计数组转化到bmp位图数据格式，刷到磁盘file
	直方图均衡化：1.得到像素点累加数组，都除以面积得到灰度频度数组2.对每个像素进行灰度值的映射，写到一个新的data数组3.内存数据刷盘
###### 2.3空间域滤波
	均值滤波/中值滤波：1.读取bmp文件2.扩充边界（复制原来的图像边界）3.每个位图数组的数据单元进行卷积核计算4.卷积计算结果写入内存新的位图数组5.刷盘file
###### 2.4图像变换
	图像平移、旋转、镜像、缩放：1.读取bmp文件2.开辟一块相同大小的位图数据数组，对每一个元素向前通过给定坐标转换公式找到在原图中的位置3.对于不正好落在单元格的点，通过双线性插值法得到它的值，赋给新的位图数据数组    4.刷盘file
###### 2.5阈值分割
	给定阈值：1.读取bmp文件2. 获取阈值（给定）3. 对位图数据中的每个数据根据阈值给2色划分，写入新的位图数组4. 刷盘file
	迭代阈值法：1.读取bmp文件2. 获取阈值（迭代法）3. 对位图数据中的每个数据根据阈值给2色划分，写入新的位图数组4. 刷盘file
	Ostu阈值法：1.读取bmp文件2.获取阈值（遍历T，两个部分方差最大）3. 对位图数据中的每个数据根据阈值给2色划分，写入新的位图数组4. 刷盘file
###### 2.6基于区域的分割
	区域生长：从一开始给定的几个点集开始，每次如果与周围四个方向的距离小于2则继续扩散遍历（dfs递归调用），用一个二维数组标记（leetcode 200岛屿问题设计思路）
	区域分裂：定义一个函数判断四个角构成的矩形区域是否满足相邻点的间距阈值，不满足则继续递归（以纵坐标起始中点和横坐标起始中点分割区域，在四个新区域中继续递归），每次递归都对中间画线
###### 2.7边缘检测
	Sobel边缘检测/prewitt边缘检测/LOG边缘检测：1.读取bmp文件2.扩充边界（复制原来的图像边界）3.每个位图数组的数据单元进行卷积核计算(分X方向和Y方向，取绝对值合在一起)4.卷积计算结果写入内存新的位图数组5.刷盘 
  file
###### 2.8霍夫变换
	Hough变换：1.读取bmp文件2.转化为24位存于内存中一个新的bmp结构体3. 转化坐标，用极坐标表示法表示直线4.遍历所有位图像素点，给所有其所在直线对应的转化后的theta和t的关系的值+1 5. 遍历刚才得到的计数板，对每一 
  个阈值之上的theta和t关系用随机的颜色勾画出直线到24位bmp结构体中6. 刷到磁盘file
###### 2.9区域标记
	读取bmp并转化为24色图片
	识别2色图区域的算法如下：定义一个vis二位数组，0代表未遍历过，1代表遍历到过未染色，2代表染过色。每轮遍历都去递归查找那些标记为0的数组，找到他相邻的所有相同颜色的区域并标记为1.每轮结束后将这部分区域染上随即 
   一种颜色并标记为2。
###### 2.10轮廓提取
	读取bmp文件的位图数据，遍历每个位图数组中的元素，判断它是否是内部点（四周都是背景色，四周包括八个方向），是的话置白色。将新的位图数据刷到内存，并最后写到磁盘文件中去。
