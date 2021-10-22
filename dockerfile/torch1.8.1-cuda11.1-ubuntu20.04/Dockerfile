# This dockerfile uses the nvidia/cuda based on ubuntu image
# VERSION 1 - EDITION 1
# Author: zzy
# Command: docker build -f /absolute/path/to/Dockerfile -t repository_name:tag_description .
# Description: 
# 1. image using tsinghua mirrors to replace default mirrors.
# 2. avaiable software: git, vim, curl, gcc, conda, etc
# 3. avaiable python packages: pytorch=1.8.1, torchvision=0.9.1, jupyterlab, etc
FROM nvidia/cuda:11.1.1-base-ubuntu20.04

# Ubuntu Release name
ARG RELEASE_NAME=focal
# Get rid of the warning: "debconf: unable to initialize frontend: Dialog"
ARG DEBIAN_FRONTEND=noninteractive

# 1. Install preparation
#
# step 1.1: backup default mirrors list
#
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak \
#
# step 1.2: write tsinghua mirrors to source list
#
&& echo "# default comment source mirror image to imporve 'apt update' speed, if needed uncomment them\n\
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block main restricted universe multiverse\n# \
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block main restricted universe multiverse\n\
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block-updates main restricted universe multiverse\n# \
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block-updates main restricted universe multiverse\n\
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block-backports main restricted universe multiverse\n# \
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block-backports main restricted universe multiverse\n\
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block-security main restricted universe multiverse\n# \
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ name-block-security main restricted universe multiverse\n" > /etc/apt/sources.list \
&& sed -i 's/name-block/'$RELEASE_NAME'/g' /etc/apt/sources.list \
#
# step 1.3: install some basic utilities
#
&& apt-get update \
# Get rid of the warning: "debconf: delaying package configu ration, since apt-utils is not installed"
&& apt-get -y install apt-utils 2>&1 | grep -v "debconf: delaying package configuration, since apt-utils is not installed" \
&& apt-get install -yq --no-install-recommends\
  curl \
  ca-certificates \
  sudo \
  git \
  vim \
  bzip2 \
  libx11-6 \
  build-essential \
#
# step 1.4: clean up
#
&& rm -rf /var/lib/apt/lists/* \
&& apt-get clean

# 2. Create a working directory
RUN mkdir /app
WORKDIR /app

# 3. Create a non-root user and switch to it
RUN adduser --disabled-password --gecos '' --shell /bin/bash user \
&& chown -R user:user /app \
&& echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-user
USER user

# 4. All users can use /home/user as their home directory
ENV HOME=/home/user
RUN chmod 777 /home/user

# 5. Install Miniconda and Python 3.8
ENV CONDA_AUTO_UPDATE_CONDA=false
ENV PATH=/home/user/miniconda/bin:$PATH
RUN curl -sLo ~/miniconda.sh https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py38_4.9.2-Linux-x86_64.sh \
&& chmod +x ~/miniconda.sh \
&& ~/miniconda.sh -b -p ~/miniconda \
&& rm ~/miniconda.sh \
&& echo "channels:\n\
  - defaults\n\
show_channel_urls: true\n\
default_channels:\n\
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main\n\
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r\n\
custom_channels:\n\
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud\n\
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud" > /home/user/.condarc \
&& conda install -y python==3.8.8 \
#
# step 5.1: CUDA 11.1-specific steps
#
# && conda install -y -c conda-forge cudatoolkit=11.1.1 \
&& conda install -y -c pytorch -c conda-forge \
  pytorch==1.8.1 torchvision==0.9.1 cudatoolkit=11.1.1 \
#
# step 5.2: Quantitative trading environment setup steps
#
&& conda install -y -c conda-forge gh \
&& conda install -y dill joblib lightgbm jupyterlab \
&& conda clean -ya \
#
# step 5.3: Install optional software
# 
&& pip install cmake --no-cache-dir

# Set the default command to bash
CMD ["/bin/bash"]
