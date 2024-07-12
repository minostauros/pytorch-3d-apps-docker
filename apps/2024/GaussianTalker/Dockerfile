FROM ghcr.io/minostauros/pytorch-video-docker:2.2.2-cu121-py310

# some apt packages
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true apt-get install -y --no-install-recommends \
        unzip \
        netcat  && \
        apt-get clean -y && \
# GaussianTalker
    DIR=/tmp/gaussian_talker && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone https://github.com/joungbinlee/GaussianTalker.git . && \
    pip install -r requirements.txt && \
    pip install -e submodules/custom-bg-depth-diff-gaussian-rasterization && \
    pip install -e submodules/simple-knn && \
    pip install "git+https://github.com/facebookresearch/pytorch3d.git" && \
    pip install tensorflow-gpu==2.8.0 && \
    pip install --upgrade "protobuf<=3.20.1" && \
# remove futile apt packages and pip cache
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /root/.cache/pip/*

CMD  ["/bin/bash"]
WORKDIR /workspace
