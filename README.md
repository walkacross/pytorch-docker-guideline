# pytorch-docker-guideline

# 1 pre-requisites

## 1.1 install docker

Fetch docker from offical site: https://docs.docker.com/get-docker/

> how to insall docker please read [Manage Docker as a non-root user](https://docs.docker.com/engine/install/linux-postinstall/)

## 1.2 install NVIDIA Container Toolkit

https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker


# 2 run a docker image with pytorch and cuda

## 2.1 create or docker pull a image with pytorch and cuda with your custom version
to get more info, see https://github.com/anibali/docker-pytorch
~~~bash
    $ docker pull anibani/pytorch:1.8.1-cuda11.1
~~~

## 2.2 run a container
~~~bash
    # create container
    $ docker run -it --init --gpus=all \
    --user="$(id -u):$(id -g)" --name=torch_docker \
    --volume="$PWD:/app" --publish=8888:8888 \
    anibali/pytorch:1.8.1-cuda11.1 /bin/bash

    # check gpu status
    $ nvidia-smi

    Thu Oct 21 09:49:06 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 470.74       Driver Version: 470.74       CUDA Version: 11.4     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Quadro RTX 6000     Off  | 00000000:3B:00.0 Off |                    0 |
    | N/A   46C    P0    59W / 250W |   1432MiB / 22698MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    |   1  Quadro RTX 6000     Off  | 00000000:AF:00.0 Off |                    0 |
    | N/A   29C    P8    13W / 250W |      7MiB / 22698MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+

    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    +-----------------------------------------------------------------------------+
~~~

> the default user in contrainer is *user*,if you have trouble in wakeup container by using `--user` parameter please drop it and retry.Learn more about `docker run` usage please check https://docs.docker.com/engine/reference/commandline/run/


# 3 install system software

## 3.1 install system software
~~~bash
    $ sudo apt-get update

    # to install gcc
    $ sudo apt-get install build-essential

    $ sudo apt-get install vim
~~~


## 3.2 install optional software
~~~bash
    # install github cli to checkout pr
    $ conda install gh --channel conda-forge

    # to install cmake
    $ pip install cmake
~~~

## 3.3 install python library
~~~bash 
    $ pip install rqalpha jqdatasdk huggingface_hub dill pytorch_lightning joblib lightgbm jupyterlab
~~~

# 4 run your objective library or project

## 4.1 setup jupyter lab

* step1: launch jupyter lab in docker container
* step2: visit jupyter lab via host browser

###  step1: launch jupyter lab in docker container
~~~bash
    # open mapping directory
    $ cd /app

    # foreground run jupyter lab
    $ jupyter lab --no-browser --ip=0.0.0.0 --port=8888 --allow-root
~~~

> Optional: launch juypter lab in background

~~~bash
    # background run jupyter lab
    $ jupyter lab --no-browser --ip=0.0.0.0 --port=8888 --allow-root > jupyterlab.log 2>&1 &

    # shutdown background jupyter lab process(optional)
    $ ps -aux | grep jupyter-lab | grep -v grep | awk '{print $2}'
~~~

**Attention:** if you exit container,please use `docker exec --it torch_docker /bin/bash` to enter container enviornment,then continue following the tutorial.

### step2: visit jupyter lab via host browser
Open your host browser and enter `http://localhost:8888` or `http://x.x.x.x:8888` in browser address bar,then jupyter lab will check your visit token,where to find visit token?You can easily find token in container terminal output.For example:

~~~bash
    To access the server, open this file in a browser:
        file:///home/user/.local/share/jupyter/runtime/jpserver-129-open.html
    Or copy and paste one of these URLs:
        http://caa7b2515101:8001/lab?token=6ddcbc89fa1cb716721a739551e6a926e30f1adbe38470d3
~~~

copy token after equal symbolic and paste it in jupyter verfication box.

![jupyter_lab_verfication_box](./img/jupyterlab_token.png)

> Tips: launch jupyter lab in background,please check `/app/jupyterlab.log`.

**note:** `http://x.x.x.x` means your host ip address.

## 4.2 setup database
coming soon..

## 4.3 others
coming soon..

# 5 Contact Us
Please create an issue or send email to `yujiangallen@126.com`.
