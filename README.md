## rg2 正向推断过程整理

### 预处理数据
1. 使用SimpleITK resample image ->1536X1536X1
2. 剪裁：图像上下左右剪裁50个pixel
3. downsample 8x in each 2D dim
4. log transform 可以拉伸范围较窄的低灰度值，同时压缩范围较宽的高灰度值。可以用来扩展图像中的暗像素值，同时压缩亮像素值。简而言之是对图像中低灰度细节进行增强
5. pading 尺寸到192X192

### 输入数据到Net进行推断
1. 注意原始尺寸：默认下采样原始尺寸是180X180而不是192X192
2. 推断出的分割图像和热图写入H5文件，需要修该写入内存


### 利用热图估计标记点位置
1. est_lands_csv 读取热图估计位置
2. 生成高斯分布热力图
3. NCC(Normalized Cross Correlation)归一化互相关原理,计算位置信息

### 中间文件流程
 'ipcai_2020_ds_8x.h5'->'spec_301_resample.h5'->spec_resample_301_lands_930.csv->proj_301_resample_lands.fcsv
 
 ### 原始项目链接
 [DeepFluoroLabeling-IPCAI2020](https://github.com/rg2/DeepFluoroLabeling-IPCAI2020/commits/master)
