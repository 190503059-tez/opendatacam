_We are working toward Opendatacam v2.0.0 release, if you have bugs / feedback please ping us on this issue: https://github.com/opendatacam/opendatacam/issues/70_

# Open data cam 2.0.0-beta.4

This project is offline lightweight DIY solution to monitor urban landscape. After installing this software on the specified hardware (Nvidia Jetson board + Logitech webcam), you will be able to count cars, pedestrians, motorbikes from your webcam live stream.

Behind the scenes, it feeds the webcam stream to a neural network (YOLO darknet) and make sense of the generated detections.

It is very alpha and we do not provide any guarantee that this will work for your use case, but we conceived it as a starting point from where you can build-on & improve.

[See Demo Video (60s)](https://www.youtube.com/watch?v=NwXrXHHGSgk)

[![Demo open data cam](https://img.youtube.com/vi/A-TvSjAU1pk/0.jpg)](https://www.youtube.com/watch?v=A-TvSjAU1pk)

## Table of content

- [Open data cam 2.0.0-beta.2](#open-data-cam-200-beta2)
  * [💻 Hardware pre-requisite](#-hardware-pre-requisite)
  * [🎬 Get Started, quick setup](#-get-started-quick-setup)
    + [1. Flash Jetson board to jetpack 4.2+ ⚡️ ️(if not installed)️:](#1-flash-jetson-board-to-jetpack-42-️-️if-not-installed️)
    + [2. Install and start Opendatacam (3 min 🚀):](#2-install-and-start-opendatacam-3-min-)
    + [3. Use Opendatacam 🖖](#3-use-opendatacam-)
    + [4. Configure your jetson Wifi hotspot 📲](#4-configure-your-jetson-wifi-hotspot-)
    + [5. Customize Opendatacam ️️⚙️](#5-customize-opendatacam-)
    + [6. Docker playbook ️📚](#6-docker-playbook-)
  * [🔌 API Documentation](#-api-documentation)
  * [⁉️ Troubleshooting](#-troubleshooting)
  * [🎛 Advanced uses](#-advanced-uses)
    + [How to use opendatacam without docker](#how-to-use-opendatacam-without-docker)
    + [How to create / update the docker image](#how-to-create--update-the-docker-image)
  * [🛠 Development notes](#-development-notes)

## 💻 Hardware pre-requisite

- Nvidia Jetson Nano / TX2 / Xavier or any GNU/Linux x86_64 machine with a CUDA compatible GPU compatible with [nvidia-docker v2.0](https://github.com/NVIDIA/nvidia-docker/wiki/Installation-(version-2.0)#prerequisites) (in the cloud or locally)
- Webcam Logitech C222 (or any usb webcam compatible with Ubuntu 18.04) / Rasberry Pi cam for Jetson nano / Video file / IP camera
- A smartphone / tablet / laptop that you will use to operate the system

## 🎬 Get Started, quick setup

### 1. 📦 Software pre-requisite :

#### Flash Jetson board to jetpack 4.2 ⚡️ ️(if not installed)️:

*If you are not running on a jetson, ignore this section.*

If your jetson does not have jetpack 4.2 *(CUDA 10, TensorRT 5, cuDNN 7.3, Ubuntu 18.04)*

[Follow this guide to flash your jetson](https://github.com/opendatacam/opendatacam/blob/master/documentation/jetson/FLASH_JETSON.md)

#### Install nvidia-docker v2.0 🔧:

*If you are running on a jetson, ignore this section, nvidia-docker isn't necessary with jetpack 4.2*

Nvidia-docker v2.0 is only compatible with GNU/Linux x86_64 with kernel version > 3.10. 

[Follow this guide to install nvidia-docker v2.0 on your machine](https://github.com/opendatacam/opendatacam/blob/master/documentation/jetson/FLASH_JETSON.md)

### 2. Install and start Opendatacam (3 min 🚀):

Open a terminal or ssh to you machine and run these command (make sure an usb webcam is connected)

```bash
# Download install script
wget -N https://raw.githubusercontent.com/opendatacam/opendatacam/v2.0.0-beta.4/docker/run-jetson/install-opendatacam.sh

# Give exec permission
chmod 777 install-opendatacam.sh

# Install command for jetson nano
sudo ./install-opendatacam.sh --platform nano

# Install command for jetson tx2
sudo ./install-opendatacam.sh --platform tx2

# Install command for jetson xavier
# TODO

# Install command for nvidia-docker
# TODO
```

This command will download and start a docker container on the machine. After it finishes the docker container starts a webserver on port 8080.

The docker container is started in auto-restart mode, so if you reboot your machine it will automaticaly start opendatacam on startup. ([Learn more about the specificities of docker on jetson](#6-docker-playbook-))

You can also [use opendatacam without docker](#how-to-run-opendatacam-without-docker)

[TODO UPDATE VIDEO TUTORIAL](https://www.youtube.com/watch?v=NwXrXHHGSgk)

### 2. bis (optional) Upgrade Opendatacam (from v2.x to another v2.x version):

- If you have modified the `config.json`, save it somewhere
- Run the install steps again (previous section), this will download a new default `config.json` file compatible with the opendatacam version you are installing and setup a new docker container
- Open the newly downloaded config.json script and modify with the things you had changed previously

_NB: we do not handle auto update of the config.json file_

### 3. Use Opendatacam 🖖

Open your browser at http://IPOFJETSON:8080 .

*If you are running with the jetson connected to a screen: http://localhost:8080*

### 4. Configure your Wifi hotspot 📲

In order to operate opendatacam from your phone / tablet / computer.

See [Make machine accessible via WIFI](https://github.com/opendatacam/opendatacam/blob/master/documentation/WIFI_HOTSPOT_SETUP.md)

### 5. Customize Opendatacam ️️⚙️

We offer several customization options:

- **Video input:** run from a file, change webcam resolution, change camera type (raspberry cam, usb cam...)

- **Neural network:** change YOLO weights files depending on your hardware capacity, desired FPS (tinyYOLO, full yolov3, yolov3-openimages ...)

- **Change display classes:** We default to mobility classes (car, bus, person...), but you can change this

[Learn how to customize Opendatacam](https://github.com/opendatacam/opendatacam/blob/master/documentation/CONFIG.md)

### 6. Docker playbook ️📚

**Docker specificities on jetson**

Docker doesn't support GPU usage on Jetson (see [issue #214 on docker-nvidia official repo](https://github.com/NVIDIA/nvidia-docker/issues/214))

We need to give to the docker container access to the host platform GPU. We do so by mounting several volumes with [this script](https://github.com/opendatacam/opendatacam/blob/master/docker/run-jetson/run-docker.sh).

That is why you need to use our install script to install a container.

**How to show logs Opendatacam**

```bash
# List containers
sudo docker container list

sudo docker logs <containerID>
```

**How to  stop / restart Opendatacam**

```bash
# List containers
sudo docker container list

# Stop container (get id from previous command)
sudo docker stop <containerID>

# Start container (get id from previous command)
sudo docker start <containerID>

# Restart container (after modifying the config.json file for example)
sudo docker restart <containerID>

# Install a newer version of opendatacam
# Follow the 1. Install and start Opendatacam

# See stats ( CPU , memory usage ...)
sudo docker stats <containerID>

# Clear all docker container, images ...
sudo docker system prune -a
```

## 🔌 API Documentation

In order to solve use cases that aren't taken care by our opendatacam base app, you might be able to build on top of our API instead of forking the project.

v2 API doc (TODO depoy on the github page of the project ?? ): [https://apidoc-dzchmakjlf.now.sh](https://apidoc-dzchmakjlf.now.sh)


## ⁉️ Troubleshooting

[Common errors with answers](https://github.com/opendatacam/opendatacam/blob/master/documentation/TROUBLESHOOTING.md)

## 🎛 Advanced uses

### How to use opendatacam without docker

Read [How to use opendatacam without docker](https://github.com/opendatacam/opendatacam/blob/master/documentation/USE_WITHOUT_DOCKER.md)

### How to create / update the docker image

*For jetson devices:*

See [How to create / update a docker image for a jetson device](https://github.com/opendatacam/opendatacam/blob/master/documentation/jetson/CREATE_DOCKER_IMAGE.md)

*For nvidia-docker machine:*

See [How to create / update a docker image for a nvidia-docker machine](https://github.com/opendatacam/opendatacam/blob/master/documentation/nvidia-docker/CREATE_NVIDIADOCKER_IMAGE.md)


## 🛠 Development notes

See [Development notes](https://github.com/opendatacam/opendatacam/blob/master/documentation/DEVELOPMENT_NOTES.md)

Technical architecture overview:

![Technical architecture](https://user-images.githubusercontent.com/533590/60489282-3f2d1700-9ca4-11e9-932c-19bf84e04f9a.png)



