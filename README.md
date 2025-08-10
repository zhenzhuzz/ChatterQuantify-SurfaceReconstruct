# ChatterQuantify-SurfaceReconstruct

## 项目简介
本项目提出了一种新的振动能量指标，用于量化铣削过程中的颤振（Chatter）和受迫振动（Forced Vibration）对表面质量的影响，并结合加速度信号重建铣削表面拓扑。通过一维卷积神经网络（1D CNN）处理加速度数据，预测表面振幅、空间频率和叠加角度，进而重建表面。1160 张白光干涉图像用于训练和测试模型，重建的表面与真实表面高度匹配，Ra 预测误差小于 0.5 µm。该振动能量指标与表面粗糙度 Ra² 强相关（r = 0.98），为精准的表面质量评估提供了新的方法，并可集成于数字孪生系统，实现实时在线质量监控。

### 流程介绍与结果
*图：表面重建与指标计算流程图*  
<img src="Media/Fig. 1. The flow diagram of surface reconstruction and indicator computation.jpg" alt="Media/Fig. 1" width="80%" />

*图：真实表面（通过WLI扫描）与重建表面的直观比较*  
<img src="Media/Fig. 13. Intuitive comparison of real (scanned by WLI) and reconstructed surfaces.jpg" alt="Media/Fig. 13" width="80%" />

*图：振动能量 Ef 和 Ec 在表面粗糙度平方 Ra² 上的分布*  
<img src="Media/Fig. 9. Distribution of vibration energy Ef and Ec across surface roughness squared Ra2.jpg" alt="Media/Fig. 9" width="80%" />

### 实验设备
*图：实验设备与数据分析示意图*  
<img src="Media/Fig. 6. Experimental equipment and data analysis illustration.png" alt="Media/Fig. 6" width="80%" /> 

*图：Rtec白光干涉仪（WLI）示意图*  
<img src="Media/Fig. 7. Rtec white-light interferometer (WLI) illustration.jpg" alt="Media/Fig. 7" width="80%" />

### 1D CNN结构
*图：1D CNN结构图*  
<img src="Media/Fig. 5. Structure of 1D CNN.jpg" alt="Media/Fig. 5" width="80%" />


## 项目结构
```

ChatterQuantify-SurfaceReconstruct/  
│  
├── data/  
│ ├── EXP20211020Data_path.mat # 核心数据索引表格（含58组加工参数与白光表面数据索引）  
│ ├── EXP20211020Data_path.xlsx # 上述索引的Excel可读版本  
│ ├── raw_data/ # EXP20211020 所有加速度及白光原始数据文件  
│ ├── backups/ # 其他 mat 备份文件（可忽略）  
│  
├── src/  
│ ├── Surface/ # 白光表面数据处理程序  
│ │ ├── func_smoothed_visualization.m # 表面平滑与可视化（可直接运行）  
│ │ └── surface_preprocessing.m # 白光表面数据预处理程序  
│ ├── Acc/ # 加速度数据单独处理的程序  
│ │ └── Acc_analysis.m # 加速度信号分析  
│ ├── Acc2Surf/ # 从加速度信号重建仿真白光的完整程序  
│ │ ├── core_functions/ # 重建核心算法  
│ │ ├── Figures/ # 论文相关图片（受迫/颤振分解结果等）  
│ │ └── utils/ # 支撑工具函数  
│ ├── cnn_model/ # 1D CNN 训练与预测程序  
│ │ ├── CNN_8_FFT.ipynb # 最终使用的CNN训练程序（RTX3090）  
│ │ └── cnn_inference.py # 训练后批量预测脚本  
│ ├── utils/ # 公共工具函数  
│ │ ├── fft_analysis.m # FFT分析  
│ │ └── data_processing.m # 数据处理  
│  
├── Figures/ # 图像和仿真结果  
│ ├── Acc2Surf/Figures/ # 重建表面及能量分析图像  
│ ├── CNN_Figures/ # CNN训练与预测结果  
│ └── Paper_Figures/ # 论文插图  
│  
├── tests/ # 单元测试  
│ ├── test_surface_processing.m # 表面处理测试  
│ ├── test_acceleration_processing.m # 加速度数据处理测试  
│ └── test_cnn_model.m # CNN模型测试  
│  
├── README.md # 项目说明文档  
└── requirements.txt # Python依赖包

```

## 数据说明

### 核心索引表
- **EXP20211020Data_path.mat / .xlsx**：包含 58 组加工参数，每组参数对应 1 道槽铣，每道槽有 20 张白光干涉图像，总计 1160 张。
- 其他 `.mat` 文件为备份，可忽略。
- `实验数据库.xlsx` 为最原始数据索引，已被整合为上述 `.mat`，可忽略。

### 重要数据文件夹
- **Surface/**：白光表面数据处理程序  
- **Acc/**：加速度信号独立分析程序  
- **Acc2Surf/**：从加速度信号重建仿真白光表面的完整程序  
- **EXP20211020/**：所有加速度信号与白光原始数据  

### 数据表结构
`EXP20211020Data_path` 共 23 列，包含加工参数、加速度信号路径、白光数据索引、状态标注等信息。  
其中 `realSurf`、`simSurf` 分别为真实白光数据与仿真重建数据的多层结构 table，详细列注释已在项目说明中保留。

## 核心程序

### 表面处理
- **Surface/func_smoothed_visualization.m**：一键运行，完成白光表面数据的平滑与可视化。

### 加速度数据处理
- **Acc/**：包含加速度信号分析的独立工具程序。

### 从加速度重建表面
- **Acc2Surf/**：实现从加速度信号 → 频域特征 → 表面参数预测 → 仿真白光表面生成的完整流程。

### 1D CNN 模型训练与预测

#### 模型简介
本项目使用的 1D CNN 模型通过融合 **加速度信号的频域特征（幅值和相位）** 与 **切削工艺参数**，预测切削表面参数 (Amplitude, Frequency, Orientation_angle)。  
核心思想是先对加速度信号进行 FFT 转换提取频域信息，再结合切削参数送入多输入神经网络实现回归预测。

#### 训练环境（AutoDL 平台）
- **操作系统**: Ubuntu 20.04  
- **Python**: 3.8  
- **TensorFlow**: 2.9.0  
- **GPU**: NVIDIA RTX 3090 (24 GB)  
- **CUDA**: 11.2  
- **CPU**: 14-core Intel® Xeon® Platinum 8362 @ 2.80GHz  
- **RAM**: 45 GB  

#### 数据处理流程
1. 从 `AccSeg` 读取加速度信号，进行 FFT 分解，提取幅值和相位特征。  
2. 从文件名解析切削参数（转速、切深、进给、采样起止时间）。  
3. 读取 `Label` 目录下对应标签数据（真实表面特征）。  
4. 对加速度频域特征、切削参数、标签进行标准化。  

#### 网络结构
- **加速度特征分支**：Conv1D → MaxPooling → Dropout → Flatten  
- **切削参数分支**：Dense → Dropout  
- **融合层**：Concatenate → Dense → Dropout → Dense 输出预测矩阵  

#### 训练细节
- 优化器：Adam (lr = 1e-6)  
- 损失函数：MSE  
- 回调：EarlyStopping、LearningRateScheduler  
- 模型保存：`surface_reconstruction_model.h5` + 各归一化器 (`acc_scaler.pkl`、`cutting_scaler.pkl`、`label_scaler.pkl`)  

#### 预测流程
1. 加载模型与归一化器。  
2. 对新加速度数据进行 FFT 特征提取和归一化处理。  
3. 输出预测结果并反归一化，保存至 `Predicted` 目录。  
4. 可调用可视化与评估脚本，生成真实值与预测值的对比曲线，并计算 MSE 与 Pearson 相关系数。  

模型性能：
- **MSE** ≈ 1.45 × 10⁻¹³  
- **Pearson 相关系数** ≈ 0.994  

## 运行说明

### 1. 表面数据处理
```bash
cd src/Surface
matlab -r "func_smoothed_visualization"
```

### 2. 加速度数据处理

```bash
cd src/Acc
matlab -r "Acc_analysis"
```

### 3. 从加速度重建表面

```bash
cd src/Acc2Surf
# 按需运行核心算法脚本
```

### 4. 1D CNN 模型训练

```bash
cd src/cnn_model
jupyter notebook CNN_8_FFT.ipynb
```

### 5. 批量预测

```bash
cd src/cnn_model
python cnn_inference.py
```

依赖
--

```bash
pip install -r requirements.txt
```

主要依赖：

* tensorflow==2.9.0
    
* numpy
    
* scipy
    
* matplotlib
    
* pandas
    
* scikit-learn
    
* joblib
    

单元测试
----

```bash
cd tests
matlab -r "test_surface_processing"
matlab -r "test_acceleration_processing"
matlab -r "test_cnn_model"
```

贡献
--

欢迎提出问题、提交 Issue 或 Pull Request：

* [Issues](https://github.com/zhenzhuzz/ChatterQuantify-SurfaceReconstruct/issues)
    
* [Pull Requests](https://github.com/zhenzhuzz/ChatterQuantify-SurfaceReconstruct/pulls)
    
