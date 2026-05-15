# 源代码
## 逻辑架构
### 图像输出层
#### load_images
load_images(path,reduce) 实现了数据的读取和图像缩小数据实现
files = dir(\[path '/\*.jpg']); --> files(1).name = 'A.jpg'
sz是第一个图像的长宽高通道数
I成为了一个lin[缩放后高度 × 缩放后宽度 × 3(RGB通道) × 图像数量]

