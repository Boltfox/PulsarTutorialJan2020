## Hello World

```
docker pull hello-world
docker run hello-world
```

## Docker pull

Pull an existing container from dockerhub
```
docker pull <image name:tag>
```
*(tag is optional, otherwise pulls latest)*

Most of the software we use can be found at the MPIfR dockerhub page:

[https://hub.docker.com/u/mpifrpsr](https://hub.docker.com/u/mpifrpsr)

## Dockerfile

Build your own container from a Dockerfile.

cd into Dockerfile directory and run
```
docker build -t <name:tag> .
```
*(tag is optional)*

## Launching image

### Basic usage

Opening a terminal in image
```
docker run --rm -ti <image name> bash
```
Exit by pressing **[ctrl]+[d]** or writing **exit** in the terminal.

### Mounting volumes

You can mount directories with the **-v** tag, e.g.
```
docker run --rm -ti -v /home/batman/work/non_detections/:<path within image> ...
```
The **path within image** can be whatever you like. However, it is often
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
and cd into the **docker** directory. We will build a simple Docker container
from the Dockerfile there, which includes Python and some packages.
``` 
docker build -t simple_py .
```
*(you can name the image whatever you like)*

In the **code** directory is a tiny python script which reads in a two column data file
and saves a plot of the data. This is a very familiar setup, where we have our **code**,
**data**, and **results**. Start by making a directory called **results**. 

Now we use all of the above information run a Docker image which runs a script which reads
in data from somewhere else, and spits out results in another directory.
```
docker run --rm -u 1000:1000 -v /home/batman/work/PulsarTutorialJan2020/code/:/code
    -v /home/batman/work/PulsarTutorialJan2020/data/:/data
    -v /home/batman/work/PulsarTutorialJan2020/results/:/results
    simple_py python /code/example_program.py --data /data/data.txt --outdir /results
```
