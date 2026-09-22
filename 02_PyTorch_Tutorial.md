# PyTorch Tutorial 01：环境配置与安装

> 面向本科生的 PyTorch 入门教程  
> 本节目标：正确创建 Python 虚拟环境，并在 Windows、Linux、macOS 上安装 CPU / GPU 版本的 PyTorch。

---

## 0. 本课程推荐环境

截至 **2026-09**，本教程建议统一使用：

| 组件 | 课程推荐 | 说明 |
|---|---:|---|
| Python | **3.11** | 兼容性成熟，适合作为课程统一版本 |
| PyTorch | **2.14.0** | 2026-09-02 发布 |
| TorchVision | **0.29.0** | 与 PyTorch 2.14 配套 |
| 安装方式 | **pip** | PyTorch 2.6 起官方不再发布新的 Conda 包 |
| Windows / Linux NVIDIA GPU | CUDA 13.0 wheel 优先 | 需要较新的 NVIDIA Driver |
| 较旧 NVIDIA 环境 | CUDA 12.6 wheel | PyTorch 2.14 仍提供，兼容范围更广 |
| macOS Apple Silicon | MPS | macOS 不使用 CUDA |

> [!IMPORTANT]
> **推荐所有同学使用 Python 3.11。**
>
> PyTorch 2.14 是最后一个提供 Python 3.10 wheel 的版本。后续 PyTorch 2.15 起不再支持 Python 3.10。  
> 不建议为了“追新”在课程环境里随意切换 Python / PyTorch 大版本。

---

# 1. 为什么需要虚拟环境？

同一台电脑上，不同项目可能需要不同版本的：

- Python
- PyTorch
- NumPy
- CUDA 相关运行库
- Transformers / OpenCV / Jupyter 等第三方库

如果全部安装到系统 Python 中，很容易发生依赖冲突。

因此建议：

```text
一个项目 = 一个虚拟环境
```

例如：

```text
deep-learning-course/
│
├── .venv/              # 虚拟环境（如果使用 venv）
├── notebooks/
├── src/
├── data/
├── requirements.txt
└── README.md
```

常见的虚拟环境方案有：

1. `conda`
2. Python 自带的 `venv`

本课程两者都可以使用。

---

# 2. 安装前先认识几个版本

PyTorch 安装最容易出问题的地方不是命令本身，而是 **版本关系**。

需要区分：

```text
Python version
        ↓
PyTorch version
        ↓
PyTorch CUDA Runtime
        ↓
NVIDIA Driver
        ↓
GPU Hardware
```

## 2.1 Python 版本

检查：

```bash
python --version
```

Windows 也可以：

```powershell
py --version
```

本课程推荐：

```text
Python 3.11.x
```

---

## 2.2 PyTorch 版本

安装后检查：

```python
import torch

print(torch.__version__)
```

本教程示例：

```text
2.14.0
```

---

## 2.3 NVIDIA Driver

如果使用 NVIDIA GPU，先运行：

```bash
nvidia-smi
```

你可能看到：

```text
NVIDIA-SMI ...
Driver Version: ...
CUDA Version: ...
```

> [!WARNING]
> `nvidia-smi` 中显示的 **CUDA Version** 表示当前 NVIDIA Driver 能支持的最高 CUDA 版本之一，
> 它不等于你安装的 PyTorch CUDA Runtime，也不等于 `nvcc --version`。

PyTorch 自己使用的 CUDA Runtime 可以通过下面的 Python 代码查看：

```python
import torch

print(torch.version.cuda)
```

---

# 3. CPU 还是 GPU？

先判断自己的机器。

## 3.1 只使用 CPU

适合：

- 没有独立 NVIDIA GPU
- 只做课程前几次实验
- 网络较慢，希望减少安装体积
- 主要学习 Tensor、Autograd、基础网络

安装 CPU 版本即可。

---

## 3.2 NVIDIA GPU

Windows / Linux 上，如果电脑有 NVIDIA GPU，一般选择 CUDA 版本。

先运行：

```bash
nvidia-smi
```

如果能够正常显示 GPU 和 Driver 信息，说明 NVIDIA Driver 基本可用。

本课程推荐顺序：

```text
较新的 NVIDIA GPU + 较新的 Driver
        ↓
CUDA 13.0 wheel

旧一些的 GPU / Driver
        ↓
CUDA 12.6 wheel
```

### CUDA 13.0

CUDA 13.x 的 NVIDIA Driver 应至少属于 **580** 系列或更高。

### CUDA 12.6

PyTorch 2.14 仍提供 CUDA 12.6 wheel。它也是 PyTorch 2.14 中保留的旧架构兼容方案之一。

> [!TIP]
> 对本科课程而言，不建议一开始单独安装完整 CUDA Toolkit。
>
> PyTorch 官方提供的 CUDA wheel 已携带运行 PyTorch 所需的 CUDA 用户态运行库。
> 通常只需要：
>
> 1. NVIDIA GPU
> 2. 合适的 NVIDIA Driver
> 3. 正确的 PyTorch CUDA wheel
>
> 只有在编译自定义 CUDA 扩展等情况下，才经常需要额外安装 CUDA Toolkit / `nvcc`。

---

## 3.3 macOS GPU

macOS 不使用 NVIDIA CUDA。

Apple Silicon Mac：

```text
M1 / M2 / M3 / M4 / ...
```

PyTorch 可以通过 **MPS (Metal Performance Shaders)** 使用 Apple GPU。

检查：

```python
import torch

print(torch.backends.mps.is_available())
```

如果输出：

```text
True
```

即可使用：

```python
device = torch.device("mps")
```

---

# 4. Windows：Anaconda / Miniconda 方法

## 4.1 安装 Anaconda 或 Miniconda

二者都可以。

如果只是为了课程环境，推荐 **Miniconda**，更轻量。

安装完成后打开：

```text
Anaconda Prompt
```

或者初始化 Conda 后，在 Windows Terminal / PowerShell 中使用。

检查：

```bash
conda --version
```

---

## 4.2 创建课程环境

```bash
conda create -n pytorch python=3.11 -y
```

激活：

```bash
conda activate pytorch
```

检查：

```bash
python --version
```

应该看到类似：

```text
Python 3.11.x
```

升级 pip：

```bash
python -m pip install --upgrade pip
```

---

## 4.3 Windows + CPU

```bash
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cpu
```

安装完成后：

```bash
python
```

然后：

```python
import torch

print(torch.__version__)
print(torch.cuda.is_available())
```

CPU 环境中：

```text
torch.cuda.is_available()
```

通常为：

```text
False
```

这是正常现象。

---

## 4.4 Windows + NVIDIA GPU：CUDA 13.0

先检查：

```bash
nvidia-smi
```

然后安装：

```bash
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cu130
```

验证：

```python
import torch

print("PyTorch:", torch.__version__)
print("PyTorch CUDA:", torch.version.cuda)
print("CUDA available:", torch.cuda.is_available())

if torch.cuda.is_available():
    print("GPU:", torch.cuda.get_device_name(0))
```

---

## 4.5 Windows + NVIDIA GPU：CUDA 12.6

如果课程机器或旧 GPU 更适合 CUDA 12.6：

```bash
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cu126
```

验证方法与 CUDA 13.0 相同。

---

## 4.6 一个重要变化：Conda 只负责环境

旧教程中经常出现：

```bash
conda install pytorch torchvision ...
```

对于现代 PyTorch，不建议再照搬这类命令。

从 **PyTorch 2.6** 开始，PyTorch 官方不再发布新的 Conda 包。

因此推荐：

```text
conda
  ↓
创建 Python 虚拟环境

pip
  ↓
安装 PyTorch
```

也就是：

```bash
conda create -n pytorch python=3.11 -y
conda activate pytorch

python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cu130
```

---

# 5. Windows：不用 Anaconda，直接使用终端

如果不想安装 Conda，可以直接使用 Python 自带的 `venv`。

## 5.1 检查 Python

PowerShell / CMD：

```powershell
py -3.11 --version
```

---

## 5.2 创建虚拟环境

进入项目目录：

```powershell
mkdir deep-learning-course
cd deep-learning-course
```

创建：

```powershell
py -3.11 -m venv .venv
```

---

## 5.3 激活虚拟环境

### PowerShell

```powershell
.\.venv\Scripts\Activate.ps1
```

### CMD

```cmd
.venv\Scripts\activate.bat
```

激活后终端通常会出现：

```text
(.venv)
```

---

## 5.4 PowerShell 无法激活怎么办？

如果看到与执行策略相关的报错，可以先查看：

```powershell
Get-ExecutionPolicy
```

可在当前用户范围修改：

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

然后重新打开 PowerShell，再激活环境。

---

## 5.5 安装 PyTorch

升级 pip：

```powershell
python -m pip install --upgrade pip
```

CPU：

```powershell
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cpu
```

NVIDIA CUDA 13.0：

```powershell
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cu130
```

NVIDIA CUDA 12.6：

```powershell
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cu126
```

---

# 6. Linux

下面以 Ubuntu / Debian 系 Linux 为主要示例。

## 6.1 检查 Python

```bash
python3 --version
```

如果系统没有 `venv`：

```bash
sudo apt update
sudo apt install python3-venv python3-pip
```

> [!NOTE]
> 如果系统默认 Python 不是 3.11，可以使用 Conda / Miniconda 建立 Python 3.11 环境，
> 不建议为了课程实验强行替换 Linux 系统自带 Python。

---

## 6.2 使用 venv

创建项目：

```bash
mkdir -p ~/deep-learning-course
cd ~/deep-learning-course
```

创建环境：

```bash
python3 -m venv .venv
```

激活：

```bash
source .venv/bin/activate
```

升级 pip：

```bash
python -m pip install --upgrade pip
```

---

## 6.3 Linux + CPU

```bash
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cpu
```

---

## 6.4 Linux + NVIDIA GPU

检查：

```bash
nvidia-smi
```

CUDA 13.0：

```bash
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cu130
```

CUDA 12.6：

```bash
python -m pip install torch==2.14.0 torchvision==0.29.0 --index-url https://download.pytorch.org/whl/cu126
```

---

## 6.5 Linux + AMD GPU

AMD GPU 不使用 CUDA。

PyTorch 在 Linux 上提供 ROCm 构建。PyTorch 2.14 发布周期提供 ROCm 7.x 对应 wheel。

ROCm 对 GPU 型号、Linux 发行版和驱动版本要求较严格，因此不要直接把 NVIDIA 的 CUDA 命令改几个字符后使用。

建议从 PyTorch 官方安装页面根据当前硬件选择：

```text
OS: Linux
Package: Pip
Compute Platform: ROCm
```

---

# 7. macOS

## 7.1 推荐环境

对于 Apple Silicon：

```text
Python 3.11
PyTorch 2.14
MPS
```

macOS 不需要 CUDA。

---

## 7.2 使用 venv

检查：

```bash
python3 --version
```

创建目录：

```bash
mkdir -p ~/deep-learning-course
cd ~/deep-learning-course
```

创建环境：

```bash
python3 -m venv .venv
```

激活：

```bash
source .venv/bin/activate
```

升级 pip：

```bash
python -m pip install --upgrade pip
```

---

## 7.3 安装 PyTorch

macOS 使用标准 wheel：

```bash
python -m pip install torch==2.14.0 torchvision==0.29.0
```

---

## 7.4 检查 MPS

```python
import torch

print("PyTorch:", torch.__version__)
print("MPS built:", torch.backends.mps.is_built())
print("MPS available:", torch.backends.mps.is_available())
```

使用：

```python
device = torch.device("mps")
x = torch.randn(3, 3, device=device)

print(x)
```

---

# 8. 一段跨平台设备选择代码

后续实验推荐统一使用下面的逻辑：

```python
import torch

if torch.cuda.is_available():
    device = torch.device("cuda")
elif torch.backends.mps.is_available():
    device = torch.device("mps")
else:
    device = torch.device("cpu")

print(f"Using device: {device}")
```

模型：

```python
model = model.to(device)
```

数据：

```python
x = x.to(device)
y = y.to(device)
```

---

# 9. 国内网络：镜像配置

国内网络环境中，常见问题是：

```text
pip 下载慢
conda 下载慢
下载大型 PyTorch wheel 失败
```

可以使用国内镜像改善普通 Python 包和 Conda 包的下载。

---

## 9.1 PyPI：清华 TUNA

临时使用：

```bash
python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple numpy
```

设置为默认：

```bash
python -m pip install --upgrade pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

查看配置：

```bash
pip config list
```

恢复官方 PyPI：

```bash
pip config unset global.index-url
```

> [!IMPORTANT]
> **PyTorch 的 CPU / CUDA wheel 建议仍然明确使用 PyTorch 官方 wheel index。**
>
> 例如：
>
> ```bash
> python -m pip install torch==2.14.0 torchvision==0.29.0 \
>   --index-url https://download.pytorch.org/whl/cu130
> ```
>
> 这样可以明确知道安装的是 CPU、CUDA 12.6 还是 CUDA 13.0，避免环境不一致。

---

## 9.2 Conda：清华 TUNA

如果 `conda create` 下载很慢，可以配置 Conda 镜像。

Windows 用户可以先运行：

```bash
conda config --set show_channel_urls yes
```

`.condarc` 常见位置：

```text
Windows:
C:\Users\<用户名>\.condarc

Linux:
~/.condarc

macOS:
~/.condarc
```

一个简化配置示例：

```yaml
channels:
  - defaults

show_channel_urls: true

default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2

custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

修改后：

```bash
conda clean -i
```

> [!NOTE]
> 镜像主要用于 Conda 环境和普通依赖。
>
> 对 PyTorch 2.14 本身，本教程仍采用官方 pip wheel，因为 PyTorch 2.6 起已经停止发布新的官方 Conda 包。

---

# 10. 安装完成后的统一检查

建议所有同学执行：

```python
import platform
import torch

print("=" * 50)
print("Python platform :", platform.platform())
print("PyTorch version :", torch.__version__)
print("CUDA runtime    :", torch.version.cuda)
print("CUDA available  :", torch.cuda.is_available())

if torch.cuda.is_available():
    print("GPU             :", torch.cuda.get_device_name(0))
    print("GPU count       :", torch.cuda.device_count())

print("MPS built       :", torch.backends.mps.is_built())
print("MPS available   :", torch.backends.mps.is_available())
print("=" * 50)
```

### Windows / Linux + NVIDIA GPU

预期类似：

```text
PyTorch version : 2.14.0+cu130
CUDA runtime    : 13.0
CUDA available  : True
GPU             : NVIDIA ...
```

### CPU 环境

预期：

```text
CUDA available  : False
```

### Apple Silicon Mac

预期：

```text
MPS available   : True
```

---

# 11. 最常见的安装错误

## 11.1 `torch.cuda.is_available()` 是 `False`

先检查：

```bash
nvidia-smi
```

如果 `nvidia-smi` 本身失败：

```text
优先检查 NVIDIA Driver
```

如果 `nvidia-smi` 正常，再检查：

```python
import torch

print(torch.__version__)
print(torch.version.cuda)
```

如果：

```python
torch.version.cuda
```

输出：

```text
None
```

很可能装成了 CPU 版本。

---

## 11.2 `pip` 和 `python` 不是同一个环境

Windows：

```powershell
where python
where pip
```

Linux / macOS：

```bash
which python
which pip
```

更稳妥的习惯是永远使用：

```bash
python -m pip install ...
```

而不是：

```bash
pip install ...
```

因为：

```text
python -m pip
```

明确使用“当前 Python 环境对应的 pip”。

---

## 11.3 装完以后 Jupyter 找不到环境

在虚拟环境中安装：

```bash
python -m pip install ipykernel
```

注册：

```bash
python -m ipykernel install --user --name pytorch --display-name "Python (PyTorch)"
```

之后在 Jupyter / VS Code 中选择：

```text
Python (PyTorch)
```

---

## 11.4 不要混装很多套 PyTorch

避免在同一个环境中反复执行：

```text
CPU
→ CUDA 12.6
→ CUDA 13.0
→ Nightly
→ Conda 旧包
```

如果环境已经非常混乱，最快的处理通常是：

```bash
conda remove -n pytorch --all
```

然后重新创建：

```bash
conda create -n pytorch python=3.11 -y
```

对于 `venv`，直接删除 `.venv` 后重建即可。

---

# 12. 推荐的课程标准安装方案

## 方案 A：Windows + Anaconda + NVIDIA GPU

```bash
conda create -n pytorch python=3.11 -y
conda activate pytorch

python -m pip install --upgrade pip

python -m pip install torch==2.14.0 torchvision==0.29.0 \
  --index-url https://download.pytorch.org/whl/cu130
```

---

## 方案 B：Windows + Anaconda + CPU

```bash
conda create -n pytorch python=3.11 -y
conda activate pytorch

python -m pip install --upgrade pip

python -m pip install torch==2.14.0 torchvision==0.29.0 \
  --index-url https://download.pytorch.org/whl/cpu
```

---

## 方案 C：Windows Terminal + venv + NVIDIA GPU

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip

python -m pip install torch==2.14.0 torchvision==0.29.0 `
  --index-url https://download.pytorch.org/whl/cu130
```

> PowerShell 的换行符是反引号 `` ` ``。  
> 如果不确定，直接把安装命令写成一行。

---

## 方案 D：Linux + NVIDIA GPU

```bash
python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip

python -m pip install torch==2.14.0 torchvision==0.29.0 \
  --index-url https://download.pytorch.org/whl/cu130
```

---

## 方案 E：macOS + Apple Silicon

```bash
python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
python -m pip install torch==2.14.0 torchvision==0.29.0
```

GPU 使用 MPS：

```python
device = torch.device("mps")
```

---

# 13. 推荐安装的常用工具

完成 PyTorch 后，可以安装课程中常用的软件包：

```bash
python -m pip install numpy matplotlib pandas scikit-learn jupyter ipykernel
```

计算机视觉课程常用：

```bash
python -m pip install pillow opencv-python
```

深度学习 / Transformer 相关课程可能会用到：

```bash
python -m pip install transformers datasets accelerate
```

查看当前环境：

```bash
python -m pip list
```

保存环境：

```bash
python -m pip freeze > requirements.txt
```

重新安装：

```bash
python -m pip install -r requirements.txt
```

---

# 14. TorchAudio：可选安装

如果课程不涉及音频任务，可以暂时不安装 `torchaudio`。

当前 TorchAudio 已进入维护阶段；从 TorchAudio 2.11 起使用稳定 ABI，可与 PyTorch 2.11 及之后的 PyTorch 配合。

如果后续确实需要音频内容，再单独安装即可。

---

# 15. 常用资源

## PyTorch

- 官方主页：<https://pytorch.org/>
- 安装页面：<https://pytorch.org/get-started/locally/>
- 官方文档：<https://docs.pytorch.org/docs/stable/>
- 官方 Tutorials：<https://docs.pytorch.org/tutorials/>
- 历史版本安装：<https://pytorch.org/get-started/previous-versions/>

## Python / Conda

- Python：<https://www.python.org/>
- Miniconda：<https://docs.conda.io/projects/miniconda/>
- Anaconda：<https://www.anaconda.com/>

## NVIDIA

- NVIDIA Driver：<https://www.nvidia.com/Download/index.aspx>
- CUDA Compatibility：<https://docs.nvidia.com/deploy/cuda-compatibility/>

## 国内镜像

- 清华 TUNA：<https://mirrors.tuna.tsinghua.edu.cn/>
- PyPI 镜像帮助：<https://mirrors.tuna.tsinghua.edu.cn/help/pypi/>
- Anaconda 镜像帮助：<https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/>

---

# 16. 本节小结

安装 PyTorch 时，真正需要记住的不是某一条命令，而是下面的关系：

```text
操作系统
   ↓
虚拟环境
   ↓
Python 版本
   ↓
PyTorch 版本
   ↓
CPU / CUDA / MPS / ROCm
   ↓
驱动与硬件
```

推荐的课程统一环境：

```text
Python 3.11
PyTorch 2.14.0
TorchVision 0.29.0
```

Windows / Linux：

```text
CPU
或
NVIDIA CUDA
```

macOS Apple Silicon：

```text
MPS
```

最后一定运行：

```python
import torch

print(torch.__version__)
print(torch.cuda.is_available())
print(torch.backends.mps.is_available())
```

确保环境正确，再进入下一部分。

---

# Next

下一节可以继续：

```text
PyTorch Tutorial 02
Tensor：创建、形状、索引、广播、设备与数据类型
```

---

## 版本说明

本文档按 **2026-09-22** 的 PyTorch 官方发布与安装信息编写。

教学材料中建议固定版本，避免一学期内因为上游版本变化导致不同学生的安装命令和实验结果不一致。
