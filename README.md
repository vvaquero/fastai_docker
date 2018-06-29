# Docker for fast.ai 2018 courses

NOTE: shamelessly based on [this!] https://github.com/MattKleinsmith/dockerfiles/tree/master/fastai

NOTE2: you could also check [this other nice post] https://nji-syd.github.io/2018/03/26/up-and-running-with-fast-ai-and-docker/ 

2018-06-30: The Docker image works with [the lesson1 notebook](https://github.com/fastai/fastai/blob/master/courses/dl1/lesson1.ipynb).


## Why Docker

Check out [perfect Docker tutorial for data science](https://towardsdatascience.com/how-docker-can-help-you-become-a-more-effective-data-scientist-7fc048ef91d5).

## Assumptions

- You have a machine with an NVIDIA GPU in it.
- Your machine is running Ubuntu.
- You've installed an [NVIDIA driver](http://www.nvidia.com/Download/index.aspx).
- You've installed [docker](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/).
- You've installed [nvidia docker](https://github.com/NVIDIA/nvidia-docker).

## How to use the Docker image

After following the setup:

1. Enter `fastai` into a terminal.
2. Enter `j8` into the container's terminal that popped up as a result.

A Jupyter server will now be running in a fastai environment with all of fastai's dependencies.

## Setup

1\. Define your code and data paths where you would like to have the persistent files:

```
CODE=$(pwd)/code
DATA=$(pwd)/data
```

2\. Clone the fast.ai repo onto your host machine by entering the following in a terminal:

```
git clone https://github.com/fastai/fastai.git $CODE/fastai
mkdir $DATA
wget http://files.fast.ai/data/dogscats.zip -O $DATA/dogscats.zip
unzip -q $DATA/dogscats.zip -d $DATA
```

3\. Spawn a docker container by entering the following in a terminal:

`docker run --runtime=nvidia --init -it -p 8888:8888 -v $CODE:/code -v $DATA:/data -w="/code/fastai" vvaquero/fastai`

4\. Start the Jupyter notebook server by entering the following bashrc created ALIAS into the container's terminal:

`jupyter_notebook_GPU_0_PORT_8888`

or

`j8`

there are also defined commands for `j9` , `jupyter_notebook_GPU_1_PORT_8889`  and `j0` , `jupyter_notebook_GPU_1_PORT_8890`.

5\. Authenticate your browser by opening the Jupyter notebook link that you see in the output (e.g. right click and press Open Link).  

6\. Open a Jupyter notebook in your browser and enjoy the fast.ai library.


To allow for *faster access next time*, enter the following into your host machine's terminal, only once:

`echo "alias fastai=\"docker run --runtime=nvidia --init -it -p 8888:8888 -v $CODE:/code -v $DATA:/data -w="/code/fastai" vvaquero/fastai\"" >> ~/.bashrc`

`source ~/.bashrc`

---



## Further Notes

- `docker run`: Runs a container from a Docker image. A container is an instance of a Docker image, like an object is an instance of a class. A Dockerfile defines a Docker image, like a class definition defines a class.

- `--runtime=nvidia`: Gives the container access to the host's NVIDIA driver.

- `-it`: Creates a terminal for the container that the user on the host can interact with.

- `-rm`: Removes the container once it exits. Otherwise inactive containers pile up.

- `-p 8888:8888`: Binds the host's 8888 port to the container's 8888 port, allowing the host to access the container's Jupyter server through the host's Internet browser.

- `-v $CODE:/code` and `-v $DATA:/data`: Gives the container access to the host's `$CODE` and `$DATA` directories and calls them "/code" and "/data" from the container's perspective. **Note: any changes to the files, either made by the host or the container, will change those files from both the host's perspective and the container's perspective.**

- `-w="/code/fastai"`: Sets the working directory of the container, from the container's perspective.

- `vvaquero/fastai`: Specifies the ID of the Docker image on [Docker Hub](https://hub.docker.com/). `docker run` will download the Docker image if it's not already on disk.

For more information, see [the Docker run reference](https://docs.docker.com/engine/reference/run/) and [the Docker run options list](https://docs.docker.com/engine/reference/commandline/run/).


`jupyter_notebook_GPU_0_PORT_8888` is defined in the container's ~/.bashrc, along with j9 and j0 (again to gpu0)

```
alias jupyter_notebook_GPU_0_PORT_8888=\
      "ln -sf /data /code/fastai/courses/dl1/ && \
       source activate fastai && \
       CUDA_VISIBLE_DEVICES=0 jupyter notebook --port=8888 --allow-root"
```

`docker ps -a -f status=running`: List running containers.  

`docker images`: List all images that you have saved locally.
