# Linux conda 环境搭建

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

## 更换镜像源  [镜像链接](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
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

```
