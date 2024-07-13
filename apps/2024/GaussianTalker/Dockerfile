FROM ghcr.io/minostauros/pytorch-video-docker:2.2.2-cu121-py310

# some apt packages
RUN apt-get update && \
    # g++8 is for OpenFace
    echo 'deb http://old-releases.ubuntu.com/ubuntu/ impish main restricted universe multiverse' | tee /etc/apt/sources.list.d/gcc-8.list && \
    apt update && \
    DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true apt-get install -y --no-install-recommends \
        unzip \
        netcat \
        # for pyaudio
        portaudio19-dev \
        # for OpenFace
        libopenblas-dev \
        g++-8 \
        # for GaussianTalker
        python3-tk && \
        apt-get clean -y && \
# dlib
    DIR=/tmp/dlib && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone https://github.com/davisking/dlib . && \
    mkdir -p build && \
    cd build && \
    cmake .. && \
    cmake --build . --config Release --parallel $(nproc) && \
    make install && \
    ldconfig && \
    rm -rf ${DIR} && \
    cd /tmp && \
# OpenFace
    DIR=/tmp/OpenFace && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone https://github.com/TadasBaltrusaitis/OpenFace.git . && \
    mkdir -p ${DIR}/build/bin/model/patch_experts && \
    chmod +x download_models.sh && \
    ./download_models.sh && \
    cp -r ${DIR}/lib/local/LandmarkDetector/model/patch_experts/* ${DIR}/build/bin/model/patch_experts/ && \
    cd ${DIR}/build && \
    cmake -D CMAKE_CXX_COMPILER=g++-8 -D CMAKE_C_COMPILER=gcc-8 -D CMAKE_BUILD_TYPE=RELEASE .. && \
    make -j$(nproc) && \
    make install && \
    rm -rf ${DIR} && \
    cd /tmp && \
# GaussianTalker
    DIR=/tmp/gaussian_talker && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone --recursive https://github.com/KU-CVLAB/GaussianTalker . && \
    pip install mmcv==1.6.0 plyfile pytorch_msssim open3d wandb face_alignment python_speech_features numba resampy pyaudio soundfile configargparse && \
    MAKEFLAGS="-j$(nproc)" pip install submodules/custom-bg-depth-diff-gaussian-rasterization && \
    MAKEFLAGS="-j$(nproc)" pip install submodules/simple-knn && \
    MAKEFLAGS="-j$(nproc)" FORCE_CUDA=1 pip install "git+https://github.com/facebookresearch/pytorch3d.git" && \
    pip install tensorflow==2.12.0 tensorflow-tensorboard && \
    pip install --upgrade "protobuf<=3.20.1" && \
    rm -rf ${DIR} && \
    cd /tmp && \
# remove futile apt packages and pip cache to save space
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /root/.cache/pip/* && \
# download GaussianTalker required checkpoints
    mkdir -p /root/.cache/torch/hub/checkpoints/ && \
    wget https://download.pytorch.org/models/resnet18-5c106cde.pth -O /root/.cache/torch/hub/checkpoints/resnet18-5c106cde.pth && \
    wget https://download.pytorch.org/models/vgg16-397923af.pth -O /root/.cache/torch/hub/checkpoints/vgg16-397923af.pth && \
    mkdir -p /root/.tensorflow/models/ && \
    wget https://github.com/osmr/deepspeech_features/releases/download/v0.0.1/deepspeech-0_1_0-b90017e8.pb.zip -O /root/.tensorflow/models/deepspeech-0_1_0-b90017e8.pb.zip

ENV QT_QPA_PLATFORM="offscreen"

CMD  ["/bin/bash"]
WORKDIR /workspace
