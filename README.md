<div align="center">
   <h1>Linux Conda 环境搭建</h1>
</div>

conda配置环境变量(https://blog.csdn.net/yinjun3215/article/details/123705879)

## pip批量更新所有包
```bash
pip freeze > requirements.txt

 # 查看可更新包：
 pip list  --outdated --format=columns
 # 批量下载并更新：
 pip install pip-review
 pip-review --local --interactive
```

## 查看CPU and GPU
```bash
lscpu

nvidia-smi
```

## 安装miniconda并创建环境
```bash
bash Miniconda3-py39_24.3.0-0-Linux-x86_64.sh

conda --version

conda create --name Wenhao python=3.10
# conda remove --name Wenhao --all

```

## 更换镜像源---[镜像链接](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
```bash
conda config --show channels
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/

conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2/
conda config --set show_channel_urls yes

pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

## 安装jupyterlab(base环境)
```bash
conda install nb_conda_kernels
conda install -c conda-forge jupyterlab

conda install ipykernel #Wenhao环境中也要有
```

## 启动jupyterlab(base环境)
```bash
screen -mS d2l # 创建并连接到一个新的 tmux 会话，会话名称为 "d2l"
jupyter lab --ip=0.0.0.0 --port=8888 --no-browser

# screen -ls
# screen -r d2l 进入会话
# 解挂会话 ctr+a+d
# exit  结束会话
```

## 安装pytorch and tensorflow(Wenhao 环境)
```bash
pip install tensorflow
# kill 1889755 取消pid为改数字的进程防止显存一直占用
tensorboard --logdir logs/fit --bind_all  #  开启tensorboard 并运行所有ip

pip3 install torch torchvision torchaudio
```

## 移植conda环境并删除原环境
```bash
# 复制现有环境到新的环境名
conda create --name Wenhao_tensorflow --clone Wenhao
conda install ipykernel
conda remove --name Wenhao --all
```

## 分别测试tensorflow和pytorch的GPU是否可用
```python
import torch

# 检查CUDA是否可用
if torch.cuda.is_available():
    print("CUDA可用，可以使用GPU加速。")
else:
    print("CUDA不可用，将使用CPU。")

# 获取当前CUDA设备数量
device_count = torch.cuda.device_count()
print(f"发现 {device_count} 个CUDA设备。")

# 获取当前CUDA设备的名称
for i in range(device_count):
    print(f"CUDA 设备 {i}: {torch.cuda.get_device_name(i)}")


import tensorflow as tf

# 检查是否有可用的 GPU
if tf.config.list_physical_devices('GPU'):
    print('GPU 可用')
else:
    print('GPU 不可用')

# 获取 GPU 设备列表
gpu_devices = tf.config.list_physical_devices('GPU')
if gpu_devices:
    # 显示 GPU 设备信息
    for gpu in gpu_devices:
        print(f"设备名称: {gpu.name}, 类型: {gpu.device_type}")
else:
    print("未找到 GPU 设备")

# 可以通过以下方式查看当前 TensorFlow 是否使用 GPU 加速
print("TensorFlow 默认设备:", tf.test.gpu_device_name())
```
