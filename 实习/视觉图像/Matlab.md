# 源代码
## 逻辑架构
### 图像输出层
#### load_images
load_images(path,reduce) 实现了数据的读取和图像缩小数据实现
files = dir(\[path '/\*.jpg']); --> files(1).name = 'A.jpg'
sz是第一个图像的长宽高通道数
floor是向下取整
I成为了一个零数组\[缩放后高度 × 缩放后宽度 × 3(RGB通道) × 图像数量]
im是一个高宽通道数的数组归一化
size(im,1)一维的大小
im = imresize(im,\[r c],'bicubic'); 按照双三次插值法进行缩小
通道保持不变
#### display_pyramid
用于拼接显示拉普拉斯金字塔或高斯金字塔
主要是长不变，宽二倍，然后一层一层累宽值
R（：）转换为列向量
判断最小值是否大于0，若小于零，则延伸到0-1

### 数据结构层
#### downsample(I, filter)
实现滤波器的使用，形成了图像尺寸缩小一半
symmetric是镜像图像扩充的意思 不会因为简单的重复或零填充而产生人工边缘效应
首先行向量然后列向量，

### 金字塔构建层
gaussian_pyramid(I,nlev) 
nlev是金字塔层数，所以需要除以 log(2) 来换算为以2为底的对数算出层数
cell创建一个空单元格组

















