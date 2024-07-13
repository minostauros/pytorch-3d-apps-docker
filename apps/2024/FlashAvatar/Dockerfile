FROM ghcr.io/minostauros/pytorch-video-docker:2.2.2-cu121-py310

# some apt packages
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true apt-get install -y --no-install-recommends \
        unzip \
        netcat && \
        apt-get clean -y && \
# FlashAvatar
    DIR=/tmp/flashavatar && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    git clone --recursive https://github.com/USTC3DV/FlashAvatar-code.git . && \
    MAKEFLAGS="-j$(nproc)" pip install submodules/simple-knn && \
    MAKEFLAGS="-j$(nproc)" pip install submodules/diff-gaussian-rasterization && \
    MAKEFLAGS="-j$(nproc)" FORCE_CUDA=1 pip install "git+https://github.com/facebookresearch/pytorch3d.git@stable" && \
    pip install lpips plyfile==0.8.1 scikit-image loguru chumpy && \
    # improve numpy compatibility of chumpy
    sed -i '11{/^from numpy/d}' /usr/local/lib/python3.10/dist-packages/chumpy/__init__.py && \
    rm -rf ${DIR} && \
    cd /tmp && \
# remove futile apt packages and pip cache
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /root/.cache/pip/*

CMD  ["/bin/bash"]
WORKDIR /workspace
