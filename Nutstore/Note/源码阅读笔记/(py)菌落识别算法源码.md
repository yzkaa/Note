# 源码展示
```python
from skimage import ( morphology as mpl,
                      exposure   as es,
                      feature    as ft,
                      color      as cl,
                      io         as skio, 
                    )
from matplotlib import pyplot as plt
from scipy import ndimage as ndi
from numba import jit

import numpy as np
import warnings
import time

warnings.filterwarnings('ignore')

###############################################################################
''' Parameters that must be fill in are as follows :
    image_loc       : location of input image
    label_loc       : location of la bel image
    gaussian_factor : Suppression of noise intensity
    R_threshold     : adjust according to brightness
    local_size      : adjustment according to image resolution
'''

image_loc   = 'image/1.jpg'
label_loc = image_loc.split('.')[0] + ' - 副本.jpg'
'''
gaussian_factor = 1
R_threshold = 0.02
local_size = 3
'''

local_size = 4
gaussian_factor = 50
R_threshold = 0.02

ts1 = time.time()
###############################################################################
o_image = skio.imread( image_loc )
lable   = skio.imread( label_loc )
print( f'> image size : {o_image.shape}')

''' Cut pictures to reduce irrelevant areas '''
r_loc = np.where( ( lable[...,0] > 200 ) *
                  ( lable[...,1] <  20 ) *
                  ( lable[...,2] <  20 )
                )
cut_x_min = r_loc[ 0].min(); cut_x_max = r_loc[ 0].max()
cut_y_min = r_loc[ 1].min(); cut_y_max = r_loc[ 1].max()
#
o_image = o_image[ cut_x_min:cut_x_max, cut_y_min:cut_y_max ]
lable   = lable  [ cut_x_min:cut_x_max, cut_y_min:cut_y_max ]
#
g_image   = cl.rgb2gray( o_image )
g_image_2 = np.copy( g_image )
distance  = np.copy( g_image )




if gaussian_factor != 0: 
    g_image = ndi.gaussian_filter( g_image, gaussian_factor )

###############################################################################
#''' increase pixel distance '''
##o_image = ndi.zoom( o_image, [2,2,1], mode = 'nearest')
##g_image = ndi.zoom( g_image, 2, mode = 'nearest')
##lable = ndi.zoom( lable, [2,2,1], mode = 'nearest')
    
###############################################################################
''' Fill in the relevant mean in the unrelated area '''
r_loc = np.where( ( lable[...,0] < 200 ) + 
                  ( lable[...,1] >  20 ) +
                  ( lable[...,2] >  20 )
                )
r_loc_2 = np.where( ( lable[...,0] > 200 ) *
                    ( lable[...,1] <  20 ) *
                    ( lable[...,2] <  20 )
                  )
g_image[ r_loc ] =  np.mean( g_image[ r_loc_2 ] )

skio.imsave( 'g_image.png', g_image)


''' Obtaining targert area by "reconstruction" method '''
target = np.copy( g_image)
target[ 1:-1, 1:-1 ] = np.min( g_image)
target = ( g_image - 
           mpl.reconstruction( target, g_image, method='dilation') 
         ) > R_threshold
target = mpl.remove_small_objects( target, 10)

''' Segmentation of overlapping regions by "watershed" merhod  '''
distance = g_image_2 * target 
distance = distance / np.max( distance)
distance = es.equalize_hist( distance, mask = target)
# 像素分割距离 = 像素灰度 * 像素距离
distance = distance * ndi.distance_transform_edt( target )

local_size = 2*local_size - 1
markers = ft.peak_local_max( image        = distance, 
                             indices      = False, 
                             footprint    = np.ones( [local_size, local_size]),
                             labels       = target
                           )
markers = ndi.label( markers )[0] > 0

@jit(nopython=True)
def corrosion( markers):
    for x in range( 1, markers.shape[0] -1 ):
        for y in range( 1, markers.shape[1] -1 ):
            if markers[ x, y ] == True:
                markers[ x -1, y -1] = markers[ x +1, y -1] = \
                markers[ x -1, y   ] = markers[ x +1, y   ] = \
                markers[ x -1, y +1] = markers[ x +1, y +1] = \
                markers[ x   , y -1] = markers[ x   , y +1] = False                
    
    return markers

markers = corrosion( markers)
markers = mpl.label( markers)

labels = mpl.watershed( -distance, markers, connectivity = 0, mask = target )

ts2 = time.time()
print( '菌落的数量是 %i 个, 耗时 %.2f s'% ( labels.max(), ( ts2 - ts1) ))


''' draw '''
g_image = ndi.zoom( np.resize( g_image, g_image.shape + (1,)), 
                    [1,1,3], 
                    mode = 'nearest'
                  )
g_image[...,0][ np.where( markers>0 )] = 1
g_image[...,1][ np.where( markers>0 )] = 0
g_image[...,2][ np.where( markers>0 )] = 0

skio.imsave( 'markers.png', g_image ) 
#skio.imsave( 'mark_result.png', 
#             cl.label2rgb( labels, o_image, alpha= 0.5, bg_label=0)
#           )

ts3 = time.time()
print( '> 绘图完毕，耗时 %.2f s, 程序总时长 %.2f s' % \
       ( ts3-ts2, ts3-ts1 ) 
     )


###############################################################################

```

# 所用到的包

```python
from skimage import ( morphology as mpl,
                      exposure   as es,
                      feature    as ft,
                      color      as cl,
                      io         as skio, 
                    )
from scipy import ndimage as ndi
from numba import jit

import numpy as np
import warnings
import time
```

skimg

其中 morphology 用于对图像进行形态学变换；exposure用于图像的亮度和对比度调整，fecture 特征，color存放了所有的颜色空间转换函数，io用于图片的输入和输出。

scipy

​		类似于Matlab的工具箱，它是Python科学计算程序的核心包，它用于有效地计算NumPy矩阵，与NumPy矩阵协同工作。 ndimage提供了有关数学形态学的方法。

numba

​		可以在运行时将Python代码编译为本地机器指令，达到加速的效果。

# 需要输入的参数

```python
''' Parameters that must be fill in are as follows :
    image_loc       : location of input image
    label_loc       : location of label image
    gaussian_factor : Suppression of noise intensity
    R_threshold     : adjust according to brightness
    local_size      : adjustment according to image resolution
'''
```

1. image_loc	待识别图像的位置
2. label_loc    标签图像的位置
3. gaussian_factor    噪声强度抑制
4. R_threshold    根据亮度调整
5. local_size    根据图像分辨率调整

# 程序流程

1. ## 输入的参数之后，使用imread读取image_loc，读取之后图像变成数组，赋值给o_image变量，同理将标签图片的位置赋值给label变量。输出图片的大小以及通道数。的

```python
o_image = skio.imread( image_loc )
lable   = skio.imread( label_loc )
print( f'> image size : {o_image.shape}')
```

2. ## 剪切图片以减少无关区域

   通过where函数，判断标签图像，如果标签图像的每个像素点：

   ​	r通道值大于200，返回True，否则返回False

   ​	g通道值小于20，返回True，否则返回False

   ​	b通道值小于20，返回True，否则返回False

   最后得到三个bool类型的数组，将这三个数组相乘，若同时满足条件则返回该点的坐标。

   **其实就是判断像素点是否为红色。**将结果赋值给r_loc中，结果为一个元组，里面存放了两个数组,分别是满足横坐标和纵坐标集合。

   ```python
   r_loc = np.where( ( lable[...,0] > 200 ) *
                     ( lable[...,1] <  20 ) *
                     ( lable[...,2] <  20 )
                   )
   ```

   将横坐标和纵坐标的最值分配到cut_x_min，cut_x_max，cut_y_min，cut_y_max中。

   ```python
   cut_x_min = r_loc[ 0].min(); cut_x_max = r_loc[ 0].max()
   cut_y_min = r_loc[ 1].min(); cut_y_max = r_loc[ 1].max()
   ```

   使用这些值对待识别图像和标签图像进行分割。

   ```python
   o_image = o_image[ cut_x_min:cut_x_max, cut_y_min:cut_y_max ]
   lable   = lable  [ cut_x_min:cu月t_x_max, cut_y_min:cut_y_max ]
   ```

   将分割后的待识别图像转换成灰度图像，并且复制两份用作之后的处理。

   ```python
   g_image   = cl.rgb2gray( o_image )
   g_image_2 = np.copy( g_image )
   distance  = np.copy( g_image )
   ```

   如果高斯因子不为零，对原图像进行高斯滤波

   高斯滤波是一种线性平滑滤波，适用于消除高斯噪声，广泛应用于图像处理的减噪过程。  

   高斯噪声是概率密度服从正态分布的噪声。   

   ```python
   if gaussian_factor != 0: 
       g_image = ndi.gaussian_filter( g_image, gaussian_factor )
   ```

   

3. ## 在不相关区域内填充相关区域的平均值

```python
r_loc = np.where( ( lable[...,0] < 200 ) + 
                  ( lable[...,1] >  20 ) +
                  ( lable[...,2] >  20 )
                )
r_loc_2 = np.where( ( lable[...,0] > 200 ) *
                    ( lable[...,1] <  20 ) *
                    ( lable[...,2] <  20 )
                  )
g_image[ r_loc ] =  np.mean( g_image[ r_loc_2 ] )
skio.imsave( 'g_image.png', g_image)
```

4. ## 通过“重构”方法获得的目标区域

   target变量作为目标区域，对target进行形态学重构。首先将g_image的最小值赋值给target除了边上一圈以外的点，然后使用reconstruction函数进行重构，并且用g_image减掉重构后的值，将这个值与R_threshold进行比较，最后target就是一个bool型数组，也是一个0-1数组，然后使用remove_small_objects删除孤立小区域。

   ```python
   target = np.copy(g_image)
   target[ 1:-1, 1:-1 ] = np.min( g_image)
   target = ( g_image - 
              mpl.reconstruction( target, g_image, method='dilation') 
            ) > R_threshold
   target = mpl.remove_small_objects( target, 10)
   ```


5. ## 用“分水岭”方法分割重叠区域

   先进行图像的预处理

   equalize_hist直方图均衡化函数，用于提高图片质量。

   然后进行距离变换，像素分割距离 = 像素灰度 * 像素距离，之后使用peak_local_max函数寻找局部最大值，local_size为寻找的局部范围。然后使用label()寻找连通区域，该函数会将连通区域打上数字标记，会覆盖掉之前的数值。因为连通区的标记是从1开始，即必为正数，所以判断点是否大于0即可得到一个二值化的数组（0和1）。

label函数举例：

```python
x=np.array([[1,0,0,0,0],[0,1,8,8,0],[0,0,1,1,8],[0,0,0,0,1]])
x
'''Out[109]: 
array([[1, 0, 0, 0, 0],
       [0, 1, 8, 8, 0],
       [0, 0, 1, 1, 8],
       [0, 0, 0, 0, 1]])'''
label(x,connectivity = 1, return_num=True)
'''Out[110]: 
(array([[1, 0, 0, 0, 0],
        [0, 2, 3, 3, 0],
        [0, 0, 4, 4, 5],
        [0, 0, 0, 0, 6]]), 6)'''
label(x,connectivity = 2, return_num=True)
'''Out[111]: 
(array([[1, 0, 0, 0, 0],
        [0, 1, 2, 2, 0],
        [0, 0, 1, 1, 2],
        [0, 0, 0, 0, 1]]), 2)'''
label(x,return_num=True)
'''Out[112]: 
(array([[1, 0, 0, 0, 0],
        [0, 1, 2, 2, 0],
        [0, 0, 1, 1, 2],
        [0, 0, 0, 0, 1]]), 2)'''
```

之后编写一个腐蚀函数，将像素值为1的区域向内收缩1个像素点，然后再进行一次标记，并将标记结果和标记个数一起保存。

最后使用基于距离变换的分水岭算法进行图像分割，将被分割的区域进行标记。（具体参数不是很理解），获得一个标记过的数组，然后取这个数组的最大值即为菌落的个数。

```python
distance = g_image_2 * target
distance = distance / np.max( distance)
distance = es.equalize_hist( distance, mask = target)
skio.imsave('distance2.png',distance)
##
# 像素分割距离 = 像素灰度 * 像素距离
distance = distance * ndi.distance_transform_edt( target )
skio.imsave('distance3.png',distance)
local_size = 2*local_size - 1
markers = ft.peak_local_max( image        = distance, 
                             indices      = False, 
                             footprint    = np.ones( [local_size, local_size]),
                             labels       = target
                           )

markers = ndi.label( markers )[0] > 0

@jit(nopython=True)
def corrosion( markers):
    for x in range( 1, markers.shape[0] -1 ):
        for y in range( 1, markers.shape[1] -1 ):
            if markers[ x, y ] == True:
                markers[ x -1, y -1] = markers[ x +1, y -1] = \
                markers[ x -1, y   ] = markers[ x +1, y   ] = \
                markers[ x -1, y +1] = markers[ x +1, y +1] = \
                markers[ x   , y -1] = markers[ x   , y +1] = False                
    
    return markers


markers = corrosion( markers)
markers = mpl.label( markers)

labels = mpl.watershed( -distance, markers, connectivity = 0, mask = target )

ts2 = time.time()
print( '菌落的数量是 %i 个, 耗时 %.2f s'% ( labels.max(), ( ts2 - ts1) ))
```

