FROM ghcr.io/minostauros/pytorch-video-docker:2.2.2-cu121-py310

# some apt packages
RUN apt-get update && \
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
# OpenFace \
    DIR=/tmp/OpenFace && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone https://github.com/TadasBaltrusaitis/OpenFace.git . && \
    sed -i '1s|cd lib/local/LandmarkDetector/model/patch_experts|cd build/bin/model/patch_experts|' download_models.sh && \
    mkdir -p build/bin/model/patch_experts && \
    chmod +x download_models.sh && \
    ./download_models.sh && \
    cd build && \
    cmake -D CMAKE_CXX_COMPILER=g++-8 -D CMAKE_C_COMPILER=gcc-8 -D CMAKE_BUILD_TYPE=RELEASE .. && \
    make -j$(nproc) && \
    cp -r bin/* /usr/local/bin/ && \
    rm -rf ${DIR} && \
    cd /tmp && \
# GaussianTalker
    DIR=/tmp/gaussian_talker && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone --recursive https://github.com/KU-CVLAB/GaussianTalker . && \
    pip install mmcv==2.2.0 -f https://download.openmmlab.com/mmcv/dist/cu121/torch2.2/index.html && \
    pip install pytorch_msssim open3d wandb face_alignment python_speech_features numba resampy pyaudio soundfile configargparse && \
    pip install -e submodules/custom-bg-depth-diff-gaussian-rasterization && \
    pip install -e submodules/simple-knn && \
    pip install "git+https://github.com/facebookresearch/pytorch3d.git" && \
    pip install tensorflow-gpu==2.8.0 && \
    pip install --upgrade "protobuf<=3.20.1" && \
    rm -rf ${DIR} && \
    cd /tmp && \
# remove futile apt packages and pip cache
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /root/.cache/pip/*

ENV QT_QPA_PLATFORM="offscreen"

CMD  ["/bin/bash"]
WORKDIR /workspace
