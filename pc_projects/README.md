## 因如下测试结果，OcrLiteOnnx项目将会继续更新，OcrLiteOnnxToNcnn项目停止更新

## 以下内容仅代表benjaminwan个人观点 仅供参考

#### PC平台onnxruntime vs ncnn对比测试介绍
1. OcrLiteOnnx项目使用onnxruntime框架，OcrLiteOnnxToNcnn项目使用ncnn框架，用于对比的项目均使用C++编写。
2. 除了调用推理框架的代码，其它代码尽量保持一致。
3. 推理框架均不使用GPU或NPU加速，仅使用CPU进行计算。
4. 模型使用chineseocr-lite的onnx分支的模型，ncnn框架是用专用的模型格式，故需要从onnx格式转为ncnn专用格式，理论上是一样的模型，但转换可能造成精度损失。
5. onnxruntime官方提供了pc各平台的动态库，ncnn则需要自己编译。
6. 对比测试的项目有：推理速度、编译后文件大小

#### 推理速度对比
1. 在同一台真机上的mac和win双系统中分别测试。
2. onnxruntime版本v1.5.2，ncnn版本tag 20200916
3. 测试条件：不计算模型加载、框架初始化、加载图片的时间，线程数均设置为当前CPU逻辑核心数量，以同一张图片，跑500次计算平均耗时。

| 平台    | 系统版本  | onnx耗时 | ncnn耗时 | 耗时倍率 |
| ------- | ------- | ----: | ----: | ----: |
| macOS   | 10.15.7 | 128ms | 429ms | 0.298 |
| Windows | 10 x64  | 168ms | 394ms | 0.426 |
| Linux   | 未测试   | 未测试 | 未测试 | 未测试 |

#### 推理速度对比总结：
1. 在mac平台上onnxruntime是ncnn速度的3倍，在win平台则差距没有那么大，但onnxruntime还是快很多。
2. 从win平台的任务管理器里可以看出，两者CPU占用率都达到100%。
3. 以mac和win两个平台之间纵向对比，onnxruntime在mac平台更快，而ncnn则在win平台更快。

#### 文件大小对比
1. onnxruntime为动态库，所以onnx是可执行文件大小加上动态库的大小，ncnn为静态库。
2. onnxruntime的大小还能通过自己编译进一步优化，但这里仅使用官方提供的包来对比大小。

| 平台     | onnx大小    | ncnn大小 | Size倍率 |
| ------- | ----------- | ----: | -----: |
| macOS   | 176K+15036K | 1973K | 7.71倍 |
| Windows | 448K+5778K  | 1098K | 5.67倍 |
| Linux   | 179K+11826K | 2281K | 5.26倍 |

#### 文件大小对比总结：
1. ncnn在PC平台文件大小项目完胜onnx。
2. 没有对比onnx静态编译的情况，所以此对比不够严谨。

#### 总体总结：
1. ncnn为移动端优化，模型大小和文件大小都更小，适合移动端部署。
2. onnxruntime的pc端推理速度更快，更适合pc端部署。