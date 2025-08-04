# ChatterQuantify-SurfaceReconstruct

## 项目简介
本项目提出了一种新的振动能量指标，用于量化铣削过程中的颤振（Chatter）和受迫振动（Forced Vibration）对表面质量的影响，并结合加速度信号重建铣削表面拓扑。通过一维卷积神经网络（1D CNN）处理加速度数据，预测表面振幅、空间频率和叠加角度，进而重建表面。1160 张白光干涉图像用于训练和测试模型，重建的表面与真实表面高度匹配，Ra 预测误差小于 0.5 µm。该振动能量指标与表面粗糙度 Ra2 强相关（r = 0.98），为精准的表面质量评估提供了新的方法，并可集成于数字孪生系统，实现实时在线质量监控。

### 流程介绍与结果
*图：表面重建与指标计算流程图*
<img src="Media/Fig. 1. The flow diagram of surface reconstruction and indicator computation.jpg" alt="Media/Fig. 1" width="80%" />

*图：真实表面（通过WLI扫描）与重建表面的直观比较*
<img src="Media/Fig. 13. Intuitive comparison of real (scanned by WLI) and reconstructed surfaces.jpg" alt="Media/Fig. 13" width="80%" />

*图：振动能量 Ef 和 Ec 在表面粗糙度平方 Ra2 上的分布*
<img src="Media/Fig. 9. Distribution of vibration energy Ef and Ec across surface roughness squared Ra2.jpg" alt="Media/Fig. 9" width="80%" />


### 实验设备
*图：实验设备与数据分析示意图*
<img src="Media/Fig. 6. Experimental equipment and data analysis illustration.png" alt="Media/Fig. 6" width="45%" /> 

*图：Rtec白光干涉仪（WLI）示意图*
<img src="Media/Fig. 7. Rtec white-light interferometer (WLI) illustration.jpg" alt="Media/Fig. 7" width="45%" />


### 1D CNN结构
*图：1D CNN结构图*
<img src="Media/Fig. 5. Structure of 1D CNN.jpg" alt="Media/Fig. 5" width="80%" />







## 项目结构

```

ChatterQuantify-SurfaceReconstruct/  
│  
├── data/  
│ ├── EXP20211020Data_path.mat # 数据文件  
│ ├── EXP20211020Data_path.xlsx # 数据文件（Excel格式，方便查看）  
│ ├── EXP20211020Data_path_backups/ # 备份文件（如果有）  
│ └── raw_data/ # 其他原始数据  
│  
├── src/  
│ ├── surface_processing/ # 表面处理相关代码  
│ │ ├── func_smoothed_visualization.m # 平滑和可视化  
│ │ └── surface_preprocessing.m # 预处理程序  
│ ├── acceleration_processing/ # 加速度数据处理相关代码  
│ │ ├── Acc2Surf.m # 加速度到表面重建  
│ │ └── Acc_analysis.m # 加速度分析  
│ ├── cnn_model/ # CNN模型代码  
│ │ ├── cnn_train.py # CNN训练代码  
│ │ └── cnn_inference.py # CNN预测代码  
│ ├── utils/ # 工具函数  
│ │ ├── fft_analysis.m # FFT分析工具  
│ │ └── data_processing.m # 数据处理工具  
│  
├── Figures/ # 图像和仿真结果  
│ ├── Acc2Surf/Figures/ # 加速度到表面仿真图像  
│ ├── CNN_Figures/ # CNN训练结果图像  
│ └── Paper_Figures/ # 论文中的插图  
│  
├── tests/ # 单元测试  
│ ├── test_surface_processing.m # 表面处理单元测试  
│ ├── test_acceleration_processing.m # 加速度数据处理单元测试  
│ └── test_cnn_model.m # CNN模型单元测试  
│  
├── README.md # 项目说明文档  
└── requirements.txt # Python依赖包列表

```

## 数据说明

项目使用的主要数据文件有：

- **EXP20211020Data_path.mat**：包含58组加工参数，每组参数对应1道槽铣，数据结构包括加速度数据和白光表面数据。
- **realSurf**：白光干涉测量的真实表面数据。
- **simSurf**：通过加速度信号和CNN模型重建的仿真表面数据。

数据表格列解释：
- `EXP`：实验槽铣板的日期。
- `s_r_min`：转速（单位rpm）。
- `d_0_1mm`：切深（单位0.1mm）。
- `f_mm_min`：进给（单位mm/min）。
- `f_t_mm_tooth`：每齿进给量（单位mm/tooth）。
- `Path2Acc`：槽对应的加速度数据路径。
- `Path2FFTWhole`：槽对应的加速度数据FFT路径。
- `AccFile`：槽对应的加速度数据文件名。
- `realSurf`：白光拍摄的表面数据。
- `simSurf`：通过加速度重建的仿真表面数据。

## 核心程序

### 表面处理

- **func_smoothed_visualization.m**：一键运行的表面数据平滑和可视化程序，能够对白光表面数据进行处理并生成图像。
- **surface_preprocessing.m**：用于对白光数据进行预处理。

### 加速度数据处理

- **Acc2Surf.m**：将加速度信号转换为白光表面数据。
- **Acc_analysis.m**：用于加速度数据的分析。

### CNN模型训练与预测

- **cnn_train.py**：用于训练CNN模型，输入加速度特征，输出重建的表面数据。
- **cnn_inference.py**：使用训练好的CNN模型进行预测，生成仿真表面数据。

## 运行说明

### 1. 表面数据处理

运行 `src/surface_processing/func_smoothed_visualization.m` 进行白光表面数据的平滑处理和可视化。

### 2. 加速度数据处理

使用 `src/acceleration_processing/Acc2Surf.m` 将加速度信号转换为表面数据，进行后续分析。

### 3. CNN模型训练

运行 `src/cnn_model/cnn_train.py` 对CNN模型进行训练，训练结果会保存为模型文件。

### 4. CNN预测

使用 `src/cnn_model/cnn_inference.py` 对新的加速度数据进行预测，生成仿真表面数据。

## 依赖

本项目依赖以下Python包：

- `tensorflow==2.x`
- `numpy==1.x`
- `scipy==1.x`
- `matplotlib==3.x`
- `pandas==1.x`
- `scikit-learn==0.x`

请在项目根目录下运行以下命令来安装所需的依赖：

```bash
pip install -r requirements.txt
```

单元测试
----

项目中包含了单元测试代码，用于验证各模块的功能和正确性。运行 `tests/` 文件夹中的测试文件来确保所有功能正常。

贡献
--

欢迎提出问题、提交问题报告或贡献代码。您可以通过以下方式与我们联系：

* 提交问题报告：[Issues](https://github.com/zhenzhuzz/ChatterQuantify-SurfaceReconstruct/issues)
    
* 提交代码贡献：[Pull Requests](https://github.com/zhenzhuzz/ChatterQuantify-SurfaceReconstruct/pulls)
    



