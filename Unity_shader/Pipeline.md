# 渲染流水线

## 三个阶段

- 应用阶段
- 几何阶段
- 光栅化阶段

### 应用阶段

- 开发者控制的，应用主导的
- 由CPU负责实现
- 开发者的三个主要任务
  1. 准备场景数据，如摄像机位置，模型、光源
  2. 为了提高渲染性能，做一个粗粒度剔除(culling)工作，将不可见的物体剔除出去，不再交给几何阶段处理
  3. 设置每个模型的渲染状态，包括但不限于材质（漫反射颜色、高光反射颜色）、使用的纹理、使用的shader等。输出渲染所需的几何信息，即渲染图元(rendering primitives)

### 几何阶段

- 处理绘制几何相关的事情。决定要绘制的图元的是什么，怎样绘制，哪里绘制。GPU上进行
- 进行逐顶点，逐多边形的操作
- 将顶点坐标变换到屏幕空间中，交给光栅器进行处理
- 多步处理渲染图元，输出屏幕空间的二维顶点坐标、每个顶点的深度值、着色等信息给下一阶段

阶段：

1. 输入顶点数据
2. 顶点着色器
3. 曲面细分着色器
4. 几何着色器
5. 裁剪
6. 屏幕映射

### 光栅化阶段

- GPU上进行

阶段：

1. 三角形设置
2. 三角形遍历
3. 片元着色器
4. 注片元操作
5. 输出屏幕图像

## CPU和GPU的通信

应用阶段分为三个阶段

- 把数据加载到显存中
- 设置渲染状态
- 调用Draw Call

### 把数据加载到显存中

- 将渲染所需数据加载到系统内存
- 网格和纹理等数据加载到显存（显卡对于显存的访问速度更快，而且大多数显卡对于RAM没有直接的访问权限
  - 加载到显存的数据：顶点位置信息，法线方向，顶点颜色，纹理坐标等
- RAM数据移除，保留某些数据（如CPU需要访问网格数据进行碰撞检测）

### 设置渲染状态

- 渲染状态定义了场景中的网格如何被渲染
- 如，使用哪个顶点着色器/片元着色器，光源属性，材质等。

### 调用Draw Call

- 绘制