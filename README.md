# rocm-ubuntu-setup

## Install basic dependencies
```
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
```

## ROCm install
```
sudo apt update
wget -q https://repo.radeon.com/amdgpu-install/7.2/ubuntu/noble/amdgpu-install_7.2.70200-1_all.deb -O /tmp/amdgpu-install_7.2.70200-1_all.deb
sudo apt install /tmp/amdgpu-install_7.2.70200-1_all.deb
sudo amdgpu-install -y --usecase=rocm --no-dkms
groups
sudo usermod -a -G render,video $LOGNAME
```

## Reboot host
```
sudo reboot now
```

## Install PyTorch via pip
1. Install pip
```
sudo apt install python3-pip python3-venv -y
```
2. Create Python env
```
python3 -m venv rocm-env
source rocm-env/bin/activate
```
3. Update pip and wheel
```
pip3 install --upgrade pip wheel
```
4. Install Torch, Torchvision, Triton, Torchaudio (ROCm wheels)

Important: AMD recommends using ROCm wheels available on https://repo.radeon.com/.
The ROCm wheels available at https://pytorch.org/ are not tested extensively by AMD as they change regularly with nightly builds.
When manually downloading wheels from repo.radeon, ensure you select compatible wheels for your Python version. See the compatibility matrices for support information.
Ubuntu 24.04:
```
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-7.2/torch-2.9.1%2Brocm7.2.0.lw.git7e1940d4-cp312-cp312-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-7.2/torchvision-0.24.0%2Brocm7.2.0.gitb919bd0c-cp312-cp312-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-7.2/triton-3.5.1%2Brocm7.2.0.gita272dfa8-cp312-cp312-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-7.2/torchaudio-2.9.0%2Brocm7.2.0.gite3c6ee2b-cp312-cp312-linux_x86_64.whl
pip3 uninstall -y torch torchvision triton torchaudio
pip3 install \
  torch-2.9.1+rocm7.2.0.lw.git7e1940d4-cp312-cp312-linux_x86_64.whl \
  torchvision-0.24.0+rocm7.2.0.gitb919bd0c-cp312-cp312-linux_x86_64.whl \
  torchaudio-2.9.0+rocm7.2.0.gite3c6ee2b-cp312-cp312-linux_x86_64.whl \
  triton-3.5.1+rocm7.2.0.gita272dfa8-cp312-cp312-linux_x86_64.whl
```

## 🧪 Verify your PyTorch installation

Confirm that PyTorch is correctly installed and can see the GPU.
1. Verify PyTorch import
```
python3 -c 'import torch' 2> /dev/null && echo 'Success' || echo 'Failure'
```
Expected result:
`Success`
2. Check if GPU is accessible from PyTorch

In PyTorch, torch.cuda is the generic way to access the GPU. This will use an AMD GPU if one is available.
```
python3 -c 'import torch; print(torch.cuda.is_available())'
```
Expected result:
`True`
3. Display the installed GPU device name
```
python3 -c "import torch; print(f'device name [0]:', torch.cuda.get_device_name(0))"
```
Expected result (example):
`device name [0]: <Supported AMD GPU>`
4. Collect environment information
```
python3 -m torch.utils.collect_env
```
Expected result (content includes):
- PyTorch version
- ROCm version used to build PyTorch
- OS
- Is CUDA available
- GPU model and configuration
- HIP runtime version
- MIOpen runtime version

The environment setup is complete, and the system is ready for use with PyTorch to work with machine learning models and algorithms.

## Workshop3 - desktop-robot prerequisites
### Ubuntu packages
```
sudo apt update
sudo apt install -y python3 python3-venv python3-pip curl espeak ffmpeg libsndfile1 portaudio19-dev
sudo apt-get install -y libcairo2-dev
sudo apt install -y libgirepository1.0-dev
sudo apt install -y \
    python3-gi \
    gir1.2-gst-plugins-base-1.0 \
    libgstreamer1.0-0 \
    gstreamer1.0-tools \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-libav
```
### Install Ollama
```
curl -fsSL https://ollama.com/install.sh | sh
ollama pull qwen3:0.6b
```
### Workshop Python Environent
```
cd ~
git clone https://github.com/alexhegit/ReachyMiniChat.git
cd ReachyMiniChat
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

pip install "reachy-mini[mujoco]"
```

