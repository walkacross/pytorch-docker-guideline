# pytorch-docker-guideline

# 1 pre-requisites

## 1.1 install docker

https://docs.docker.com/get-docker/

> ![Manage Docker as a non-root user](https://docs.docker.com/engine/install/linux-postinstall/)

## 1.2 install NVIDIA Container Toolkit

https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker


# 2 run a docker image with pytorch and cuda

## 2.1 create or docker pull a image with pytorch and cuda with your custom version
to get more info, see https://github.com/anibali/docker-pytorch
~~~
docker pull anibani/pytorch:1.8.1-cuda11.1
~~~

## 2.2 run a container
~~~
docker run --rm -it --init --gpus=all --user="$(id -u):$(id -g)" --volume="$PWD:/app" --volume=/home/yujiang/.log/constexpr:/home/user/.log/constexpr anibali/pytorch:1.8.1-cuda11.1 bash
~~~

> the default user in contrainer is *user*


# 3 install system software

## 3.1 install system software
~~~
sudo apt-get update

# to install gcc
sudo apt-get install build-essential

sudo apt-get install vim
~~~


## 3.2 install optional software
~~~
# install github cli to checkout pr
conda install gh --channel conda-forge

# to install cmake
pip install cmake
~~~

# 3.3 install python library
~~~
pip install rqalpha
pip install jqdatasdk
pip install huggingface_hub
pip install dill
pip install pytorch_lightning
pip install joblib
pip install lightgbm
~~~

# 4 run your objective library or project
