# how to build docker image via dockerfile

## 1 pre-requisites

### 1.1 install docker

Fetch docker from offical site: https://docs.docker.com/get-docker/

> how to insall docker please read [Manage Docker as a non-root user](https://docs.docker.com/engine/install/linux-postinstall/)

### 1.2 install NVIDIA Container Toolkit

https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker


## 2. Build docker image

### 2.1 create docker image via dockerfile
~~~bash
    # step 1 open dockerfile directory
    $ cd /absolute/path/to/torch1.8.1-cuda11.1-ubuntu20.04
    # step 2 build dockerfile
    $ docker build -t pytorch:1.8.1-cuda11.1 .
~~~

> Tips: you can use `docker build -f /absolute/path/to/torch1.8.1-cuda11.1-ubuntu20.04 -t pytorch:1.8.1-cuda11.1 .` command to build docker image in any directory.

### 2.2 run a container
~~~bash
    # create container
    $ docker run -itd --init --gpus=all \
    --user=user --name=quant \
    --volume="$PWD:/app" --publish=8888:8888 \
    pytorch:1.8.1-cuda11.1 /bin/bash

    # enter container and test
    $ docker exec -it quant bash
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

> Learn more about `docker run` usage please check https://docs.docker.com/engine/reference/commandline/run/

## 3 run your objective library or project

### 3.1 make current account as root

* step 1: user root account to enter container
* step 2: add user to sudoer

#### step 1: user root account to enter container
~~~bash
    $ docker exec -it -u root quant bash
~~~

#### step 2: add user to sudoer
~~~bash
    # 1. add write permission to sudoers 
    $ chmod o+w /etc/sudoers

    # 2. editor sudoers file add user to sudo file and save
    $ vim /etc/sudoers
    
    # User privilege specification
    root    ALL=(ALL:ALL) ALL
    # --------- add ------------
    user    ALL=(ALL:ALL) ALL
    # --------- add ------------

    # 3. remove write permission to sudoers
    $ chmod o-w
~~~

### 3.2 setup python packages

* step 1: prepare your requirements
* step 2: install requirements in container

#### step 1: prepare your requirements
~~~bash
    $ cd /app
    $ echo -e "flask\npandas" > requirements.txt
~~~

> Tips: you can prepare requirements in host,then copy file to docker mapping directory.

#### step 2: install requirements in container
~~~bash
    $ pip install -r requirements.txt
~~~

### 3.3 setup jupyter lab

* step 1: launch jupyter lab in docker container
* step 2: visit jupyter lab via host browser

####  step 1: launch jupyter lab in docker container
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

#### step 2: visit jupyter lab via host browser
Open your host browser and enter `http://localhost:8888` or `http://x.x.x.x:8888` in browser address bar,then jupyter lab will check your visit token,where to find visit token?You can easily find token in container terminal output.For example:

~~~bash
    To access the server, open this file in a browser:
        file:///home/user/.local/share/jupyter/runtime/jpserver-129-open.html
    Or copy and paste one of these URLs:
        http://caa7b2515101:8001/lab?token=6ddcbc89fa1cb716721a739551e6a926e30f1adbe38470d3
~~~

copy token after equal symbolic and paste it in jupyter verfication box.

![jupyter_lab_verfication_box](../img/jupyterlab_token.png)

> Tips: launch jupyter lab in background,please check `/app/jupyterlab.log`.

**note:** `http://x.x.x.x` means your host ip address.

### 3.4 setup database
coming soon..

### 3.5 others
coming soon..

**Attention:** Chapter 3 precondition is based on you have already in container,if you exit container,please use `docker exec --it quant /bin/bash` to reenter container enviornment,then continue following the tutorial.

## 4 Contact Us
Please create an issue or send email to `yujiangallen@126.com`.
