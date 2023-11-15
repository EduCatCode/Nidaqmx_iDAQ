# Nidaqmx 0.8.0 Python接口使用指南

本指南提供了如何使用`nidaqmx` Python包與National Instruments (NI) 數據擷取卡進行交互的說明。

## 系統要求

- National Instruments數據擷取硬件
- 與您的硬件相容的NI-DAQmx驅動程式
- Python 3.7或更新版本
- `nidaqmx` Python套件

## 安裝步驟

### 步驟1: 安裝NI-DAQmx驅動程式

在使用`nidaqmx`包之前，確保已安裝並且與您的NI硬件相容的NI-DAQmx驅動程式。可以從[National Instruments網站](https://www.ni.com/en-us/support/downloads/drivers/download.ni-daqmx.html)下載驅動程式。

### 步驟2: 安裝`nidaqmx` Python套件

安裝NI-DAQmx驅動程式後，使用pip安裝`nidaqmx` Python包：

```bash
python -m pip install nidaqmx
```

## 基本使用範例
讀取模擬輸入
以下示例演示了如何讀取模擬電壓輸入：


```python=
import nidaqmx

# 讀取單一樣本
with nidaqmx.Task() as task:
    task.ai_channels.add_ai_voltage_chan("Dev1/ai0")
    print(task.read())

# 讀取多個樣本
with nidaqmx.Task() as task:
    task.ai_channels.add_ai_voltage_chan("Dev1/ai0")
    print(task.read(number_of_samples_per_channel=2))
```

### 讀取數字輸入
使用以下方式讀取數字信號：

```python=
from nidaqmx.constants import LineGrouping

with nidaqmx.Task() as task:
    task.di_channels.add_di_chan(
        "cDAQ2Mod4/port0/line0:1", line_grouping=LineGrouping.CHAN_PER_LINE)
    print(task.read(number_of_samples_per_channel=2))
```

### 在NI cDAQ-9171上的進階應用
以下是一個更進階的例子，從NI cDAQ-9171數據擷取系統收集模擬輸入數據，並將其存儲在CSV檔案中：

```python=
import nidaqmx
import numpy as np
import pandas as pd

# 配置參數
sampling_rate = 1000  # 採樣頻率（赫茲）
display_time = 1      # 顯示數據時間（秒）
sample_points = sampling_rate * display_time  # 每次更新的樣本點數
total_time = 60       # 總運行時間（秒）
total_samples = sampling_rate * total_time    # 總樣本點數

# 初始化數據緩存
data_buffer = np.zeros((total_samples,))

# 創建任務
with nidaqmx.Task() as task:
    task.ai_channels.add_ai_voltage_chan("cDAQ1Mod1/ai0")  # 根據您的設備調整通道
    task.timing.cfg_samp_clk_timing(sampling_rate)        # 設置
```
