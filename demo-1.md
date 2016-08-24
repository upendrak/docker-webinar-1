# Basic introduction and building images using Docker 

<a href="#top" class="top" id="table-of-contents">Top</a>

The goal of this tutorial is to show you the basics of building images using Docker.

<a href="#top" class="top" id="steps">Top</a>
### Steps
1. [Install Docker](#installdocker)
2. [Create Dockerfile](#createdockerfile)
3. [Build and test the image](#buildtest)
4. [Optional steps](#optional)
5. [Summary](#summary)

<a href="#top" class="top" id="steps">Top</a>
<a id="installdocker"></a>
### Install Docker for your platform (one time only)

For Linux users, you need to install [Docker engine] (https://docs.docker.com/engine/installation/). For PC and Mac users you need to install [Docker toolbox for Mac and Windows](https://www.docker.com/products/docker-toolbox) and use [Docker Machine] (https://docs.docker.com/machine/get-started/) to create a virtual machine to run your Docker containers. Video series on setting up Docker on your machine: [Mac](https://www.youtube.com/watch?v=lNkVxDSRo7M), [Windows](https://youtu.be/S7NVloq0EBc) and [Linux](https://www.youtube.com/watch?v=V9AKvZZCWLc). Recently Mac and Windows users can install Docker directly onto your computer without having to use virtual machines. Download Docker for [Mac](https://download.docker.com/mac/stable/Docker.dmg) and Docker for [Windows](https://download.docker.com/win/stable/InstallDocker.msi).


<a href="#top" class="top" id="steps">Top</a>
<a id="createdockerfile"></a>
### Create a Dockerfile
Dockerfile is a file that constains set of instructions/commands that are used to build the Docker image. Create a file called `Dockerfile` before building an image

```
mkdir ~/make_megahit
cd ~/make_megahit
cat <<EOF > Dockerfile
FROM ubuntu:14.04
RUN apt-get update
RUN apt-get install -y g++ make git zlib1g-dev python
RUN git clone https://github.com/voutcn/megahit.git /home/megahit \
    && cd /home/megahit \
    && git checkout 01b6692a4286973343da133f63d9e3c1252a5e8e \
    && make
ENTRYPOINT ["/home/megahit/megahit"]
EOF
```

Let's look at this Dockerfile before running it:

`cat Dockerfile`

Now see what each command in Docker file does::

The `FROM` command tells Docker what base image to load; the `RUN` commands tell Docker what to execute (and then save the results from);
and the `ENTRYPOINT` specifies the script entry point - a command that is run if no other command is given.

<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="buildingimages"></a>
### Building images

Let's build a Docker image from the above Dockerfile:

docker build -t megahit .

(This will take a few minutes..)

This creates a new image named `megahit`. If you run:

`docker images`

you should see something like:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
megahit             latest              749fd74397ed        29 seconds ago      427.5 MB
ubuntu              14.04               91e54dfb1179        3 days ago          188.4 MB
-------
-------
```

Once it's built, you can now test the built `megahit` image by running it like so ::

`docker run megahit`

You should see all the command line arguments for `megahit` image

But this is not so useful. You should test the same image using input arguments:

<a href="#top" class="top" id="steps">Top</a>
<a id="buildtest"></a>

First download the test data into a scratch directory like so:

```
mkdir ~/my-scratch-dir
cd ~/my-scratch-dir
wget http://public.ged.msu.edu.s3.amazonaws.com/ecoli_ref-5m-trim.se.fq.gz
wget http://public.ged.msu.edu.s3.amazonaws.com/ecoli_ref-5m-trim.pe.fq.gz
```

And run the built image like so:

```
docker run -v ~/my-scratch-dir:/working-dir -w /working-dir \
            megahit --12 ecoli_ref-5m-trim.pe.fq.gz \
            -r ecoli_ref-5m-trim.se.fq.gz -o ecoli -t 4
```
If the tool's container produced outputs in that host's scratch directory (`ecoli` in this case), then your tool is working.


<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="optional"></a>
### Optional steps

You can tag the image like so:

`docker tag ubuntu/megahit:<tag> <user>/megahit:<tag>`

If you wanted to make this broadly available, the next steps
would be to log into the [Docker Hub] (https://hub.docker.com/) and push it; I did so with these commands: ``docker login`` and ``docker push <user>/megahit:<tag>``.


<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="summary"></a>
### Summary

* Docker provides a nice way to bundle multiple packages of software
  together, for both yourself and for others to run.

* Docker gives you a good way to isolate what you're running from the
  data you're running it on.

* The Dockerfile enhances reproducibility by giving explicit instructions
  for what to install, rather than simply bundling it all in a binary.

Let's create a Dockerfile and image for [SPAdes-3.5.0](http://angus.readthedocs.io/en/2016/assembling-ecoli.html) assembler

<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="support"></a>
