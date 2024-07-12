FROM ghcr.io/minostauros/pytorch-video-docker:2.2.2-cu121-py310

# some apt packages
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true apt-get install -y --no-install-recommends \
        unzip \
        netcat \
        # for pyaudio
        portaudio19-dev && \
        apt-get clean -y && \
# GaussianTalker
    DIR=/tmp/gaussian_talker && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone --recursive https://github.com/KU-CVLAB/GaussianTalker . && \
    pip install mmcv==1.6.0 pytorch_msssim open3d wandb face_alignment python_speech_features numba resampy pyaudio soundfile configargparse && \
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

CMD  ["/bin/bash"]
WORKDIR /workspace
