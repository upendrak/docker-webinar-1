<a id="top"></a>
<img src="http://imageshack.com/a/img921/9080/F5RKAh.png" alt="CyVerse logo">
<img src="http://imageshack.com/a/img923/2530/PG3oB4.png" alt="DE logo">
<img src="http://imageshack.com/a/img923/6969/JnPuWr.png" alt="Docker logo">

# CyVerse Focus Forum Webinar: *Fastqc_plus* app demo 

<a id="top"></a>
<img src="http://imageshack.com/a/img924/4108/f9PfBn.png" alt="fq-1 logo">
<img src="http://imageshack.com/a/img923/6054/z3D1pg.png" alt="fq-2 logo">

This document aims to provide you hands-on experience with building your tools in CyVerse's DE. For this, i'm going to demo how you can dockerize *Fastqc_plus* as well importing them to CyVerse DE as app. Even if you have no prior experience with docker, this demo should be all you need to get started. All the code used in the demo is available in the [Github repo](https://github.com/upendrak/docker-webinar-1).

<a href="#top" class="top" id="steps">Top</a>
### Steps
1. [Install Docker](#installdocker)
2. [Create Dockerfile](#createdockerfile)
3. [Build and test the image](#buildtest) 
4. [Request installation of the Dockerized tool](#request)
5. [Create and save the new app interface in the DE](#newUI)
6. [Test your app in the DE](#testapp)

<a href="#top" class="top" id="steps">Top</a>
<a id="installdocker"></a>
### Install Docker (one time only)

For Linux users, you need to install [Docker engine] (https://docs.docker.com/engine/installation/). For PC and Mac users you need to install [Docker toolbox for Mac and Windows](https://www.docker.com/products/docker-toolbox) and use [Docker Machine] (https://docs.docker.com/machine/get-started/) to create a virtual machine to run your Docker containers. Video series on setting up Docker on your machine: [Mac](https://www.youtube.com/watch?v=lNkVxDSRo7M), [Windows](https://youtu.be/S7NVloq0EBc) and [Linux](https://www.youtube.com/watch?v=V9AKvZZCWLc)


<a href="#top" class="top" id="steps">Top</a>
<a id="createdockerfile"></a>
### Create a Dockerfile
Dockerfile is a file that constains set of instructions/commands that are used to build the Docker image. Create a file called `Dockerfile` before building an image

```
mkdir ~/make_fastqc_plus
cd ~/make_fastqc_plus
```
```
FROM ubuntu:14.04.3
MAINTAINER Roger Barthelson <rogerab@email.arizona.edu>
LABEL Description="This image is used for running FastQC and creating an output html file"
RUN apt-get -y update
RUN apt-get -y install wget unzip make tzdata-java openjdk-7-jre-headless openjdk-7-jre default-jre
RUN wget http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.10.1.zip \
    && unzip fastqc_v0.10.1.zip \
    && chmod a+x FastQC/fastqc \
    && cp -r FastQC/* /usr/bin
RUN wget -O- http://cpanmin.us | perl - App::cpanminus && cpanm File::Slurp
RUN wget https://github.com/azroger/iPlant-basic-scripts/archive/master.zip && unzip master.zip
RUN chmod a+x /iPlant-basic-scripts-master/embed_images.pl \
    && chmod a+x /iPlant-basic-scripts-master/fastqc_plus.sh
RUN cp /iPlant-basic-scripts-master/fastqc_plus.sh /usr/bin \
    && cp /iPlant-basic-scripts-master/embed_images.pl /usr/bin
ENTRYPOINT [ "fastqc_plus.sh" ]
```

<a href="#top" class="top" id="steps">Top</a>
<a id="buildtest"></a>
### Build and test the image

Let's build a Docker image from this::

`docker build -t fastqc_plus_test .`

(This will take a several minutes..)

This creates a new image named `fastqc_plus_test`. If you run:

`docker images`

you should see `fastqc_plus` image

Once it's built, you can now test the built `fastqc_plus_test` image by running it like so ::

`docker run fastqc_plus_test -h`

You should see all the command line arguments for `fastqc_plus_test` image

`Usage : fastqc_plus fastq1/fq1 fastq2/fq2 ....`

But this is not so useful. You should test the same image using input arguments ::

First download the test data into a scratch directory like so::

```
mkdir ~/my-scratch-dir
cd ~/my-scratch-dir
iget -PVT /iplant/home/shared/iplantcollaborative/example_data/fastqc_plus/inputs/ATreads.fq
iget -PVT /iplant/home/shared/iplantcollaborative/example_data/fastqc_plus/inputs/ATreads2.fq
```

Now run it like so ::

```
docker run -v ~/my-scratch-dir:/working-dir -w /working-dir fastqc_plus_test ATreads.fq ATreads2.fq
```
The `-v` option mounts the scratch directory on the host machine into that `/working-dir` directory inside the container, and the `-w` option sets the working directory inside the container to that same `/working-dir` directory.

If the tool's container produced outputs in that host's scratch directory (`individual_reports` in this case), then your tool is ready for installation in DE.

<a href="#top" class="top" id="steps">Top</a>
<a id="request"></a>
### Request installation of the Dockerized tool
Read the main steps in the DE user manual for [submitting your request for installation](https://wiki.cyverse.org/wiki/display/DEmanual/Requesting+Installation+of+a+New+Tool) of the new tool (executable) in the DE. Once the tool is installed, you will receive an email notification.


<a href="#top" class="top" id="steps">Top</a>
<a id="newUI"></a>
### Create and save the new app interface in the DE
Once the Dockerized tool is installed, you can [design a new interface](https://pods.iplantCollaborative.org/wiki/display/DEmanual/Designing+the+Interface) within the DE.


<a href="#top" class="top" id="steps">Top</a>
<a id="testapp"></a>
### Test your app using test data
After creating the new app according to your design, test your app in the DE to make sure it works properly.

## References
- [What is docker](https://www.docker.com/what-docker)
- [Docker in DE](https://pods.cyverse.org/wiki/display/DEmanual/Dockerizing+Your+Tools+for+the+CyVerse+Discovery+Environment#DockerizingYourToolsfortheCyVerseDiscoveryEnvironment-Steps)
- [Self-guided exercise](https://github.com/upendrak/docker-webinar-1/blob/master/exercise.md#optional)
