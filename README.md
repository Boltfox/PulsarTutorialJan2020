# Docker/Singularity Tutorial

[**Singularity installation guide:**](https://sylabs.io/guides/3.4/user-guide/installation.html)

[**Docker installation guide:**](https://docs.docker.com/install/)

[**How to run Docker without 'sudo'**](https://docs.docker.com/install/linux/linux-postinstall/)

## Hello World

```
docker pull hello-world
docker run hello-world
```

## Docker pull

Pull an existing image from dockerhub
```
docker pull <image name:tag>
```
*(tag is optional, otherwise pulls latest)*

Most of the software we use can be found at the [MPIfR dockerhub page](https://hub.docker.com/u/mpifrpsr)

## Dockerfile

Build your own image from a Dockerfile.

cd into Dockerfile directory and run
```
docker build -t <name:tag> .
```
*(tag is optional)*

## Launching container

### Basic usage

Opening a terminal in container
```
docker run --rm -ti <image name> bash
```
Exit by pressing **[ctrl]+[d]** or writing **exit** in the terminal.

### Mounting volumes

You can mount directories with the **-v** tag, e.g.
```
docker run --rm -ti -v /home/batman/work/non_detections/:<path within container> ...
```
The **path within container** can be whatever you like. However, it is often
convenient to use the directory structure as it is in the file system. 
That way your code will not freak out if it depends on the file system's 
directory structure. This can also help you realizing where you are working.
Long path strings can be annoying though, so it's also a good idea to just
use simple, concise catchwords, e.g. **/work**, **/data**,...

Example
```
docker run --rm -ti -v /home/batman/work/non_detections/:/home/batman/work/non_detections ...
docker run --rm -ti -v /home/batman/work/non_detections/:/data ...
```
*NOTE: You can mount as many directories as you need*

### User privileges

In Docker you are root by default *(dangerous)*. This can also cause problems
accessing files created within the Docker image. The **-u** tag solves this, as it
forwards your user and group IDs.

```
docker run --rm -ti -u <UID>:<GID> ...
```

### Putting it all together

Using the example code in this repository.

First clone this repo to your computer
```
git clone git@github.com:ghenning/PulsarTutorialJan2020.git
```
and cd into the **docker** directory. We will build a simple Docker image
from the Dockerfile there, which includes Python and some packages.
``` 
docker build -t simple_py .
```
*(you can name the image whatever you like)*

In the **code** directory is a tiny python script which reads in a two column data file
and saves a plot of the data. This is a very familiar setup, where we have our **code**,
**data**, and **results**. Start by making a directory called **results**. 

Now we use all of the above information run a Docker container which runs a script which reads
in data from somewhere else, and spits out results in another directory.
```
docker run --rm -u 1000:1000 -v /home/batman/work/PulsarTutorialJan2020/code/:/code
    -v /home/batman/work/PulsarTutorialJan2020/data/:/data
    -v /home/batman/work/PulsarTutorialJan2020/results/:/results
    simple_py python /code/example_program.py --data /data/data.txt --outdir /results
```
This should create a figure in your results directory.

## Moving to Singularity

Singularity is available on many of our computers, so we need to convert our Docker
image to Singularity. 
One such way is to use [**docker2singularity**](https://github.com/singularityhub/docker2singularity)
```
docker run -v /var/run/docker.sock:/var/run/docker.sock 
    -v <your output dir>:/output 
    --privileged -t --rm 
    singularityware/docker2singularity 
    <image to convert>
```
where **your output dir** is where you want to store your Singularity image,
and **image to convert** is the Docker image which you want to convert.

*NOTE: Singularity images are stored as files on your computer, so you can NOT
run something like 'singularity images' to list your images. Store your Singularity
images in one place so you won't lose them. You only need to build your Singularity
image once, and then transfer it to the machine where you need to use it.*

### Using Singularity

The usage of singularity is more or less identical to Docker, with only some differences
in syntax. 

To run a terminal on a container (like *docker run ... bash*) 
```
singularity shell <path to image>
```
where **path to image** is where you store your image (just type in the image name if you're
within the same directory as the image).

Singularity keeps your privileges, so a tag like **-u** from Docker is not required.

To mount directories use **-B**.

To run something from within Singularity without using a terminal, use **exec**.

If we run the same reading/plotting data code from before using Singularity, it will
look like this
```
singularity exec -B /home/batman/work/PulsarTutorialJan2020/code/:/code
    -B /home/batman/work/PulsarTutorialJan2020/data/:/data
    -B /home/batman/work/PulsarTutorialJan2020/results/:/results
    /home/batman/work/singularity_images/simple_py.simg
    /code/example_program.py --data /data/data.txt --outdir /results
```
