<a id="top"></a>
<img src="http://imageshack.com/a/img921/9080/F5RKAh.png" alt="CyVerse logo">
<img src="http://imageshack.com/a/img923/2530/PG3oB4.png" alt="DE logo">
<img src="http://imageshack.com/a/img923/6969/JnPuWr.png" alt="Docker logo">

# CyVerse Focus Forum Webinar: Self-guided exercise 

<a href="#top" class="top" id="table-of-contents">Top</a>
## Table of Contents
- [Install Docker](#install)
- [Run Docker] (#run Docker)
- [Create a Dockerfile] (#createdockerfile)
- [Building images](#buildingimages)
- [Optional steps](#optional)
- [Summary point](#summary)
- [CyVerse support](#support)


<a id="install"></a>
### Install Docker

For Linux users, you need to install [Docker engine] (https://docs.docker.com/engine/installation/). For PC and Mac users you need to install [Docker toolbox for Mac and Windows](https://www.docker.com/products/docker-toolbox) and use [Docker Machine] (https://docs.docker.com/machine/get-started/) to create a virtual machine to run your Docker containers. Video series on setting up Docker on your machine: [Mac](https://www.youtube.com/watch?v=lNkVxDSRo7M), [Windows](https://youtu.be/S7NVloq0EBc) and [Linux](https://www.youtube.com/watch?v=V9AKvZZCWLc)

Now, configure the default user to use Docker:

`sudo usermod -aG docker <user>`

and log out and log back in.

<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="run Docker"></a>
### Run Docker

Once you are done installing Docker, test your Docker installation by running the following:

```
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
03f4658f8b78: Pull complete
a3ed95caeb02: Pull complete
Digest: sha256:8be990ef2aeb16dbcb9271ddfe2610fa6658d13f6dfb8bc72074cc1ca36966a7
Status: Downloaded newer image for hello-world:latest

Hello from Docker.
This message shows that your installation appears to be working correctly.
...
```
If you see the above output then your Docker installation is successful. You can procede to Building images with Docker now

<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="createdockerfile"></a>
### Create a Dockerfile

For the self-guided tutorial, let's build a Docker image for the MEGAHIT short-read assembler. This is all based on the `Assembling E. coli tutorial
<http://angus.readthedocs.org/en/2015/assembling-ecoli.html>`--.

You can use a Dockerfile to build an image. Let's encode the commands needed to build a `megahit` image in a Dockerfile::

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

Let's look at this Dockerfile before running it::

`cat Dockerfile`

Now see what each command in Docker file does::

The `FROM` command tells Docker what base image to load; the `RUN`
commands tell Docker what to execute (and then save the results from);
and the `ENTRYPOINT` specifies the script entry point - a command that is
run if no other command is given.

<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="buildingimages"></a>
### Building images

Let's build a Docker image from this::

docker build -t megahit .

(This will take a few minutes..)

This creates a new image named `megahit`. If you run:

`docker images`

you should see something like:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
megahit             latest              749fd74397ed        29 seconds ago      427.5 MB
ubuntu              14.04               91e54dfb1179        3 days ago          188.4 MB
```

Once it's built, you can now test the built `megahit` image by running it like so ::

`docker run megahit`

You should see all the command line arguments for `megahit` image

But this is not SO useful. You should test the same image using input arguments like so ::

First download the test data into a scratch directory like so::

```
mkdir ~/my-scratch-dir
cd ~/my-scratch-dir
wget http://public.ged.msu.edu.s3.amazonaws.com/ecoli_ref-5m-trim.se.fq.gz
wget http://public.ged.msu.edu.s3.amazonaws.com/ecoli_ref-5m-trim.pe.fq.gz
```

Now run it like so ::

```
docker run -v ~/my-scratch-dir:/working-dir -w /working-dir \
            megahit --12 ecoli_ref-5m-trim.pe.fq.gz \
            -r ecoli_ref-5m-trim.se.fq.gz -o ecoli -t 4
```
If the tool's container produced outputs in that host's scratch directory (`ecoli` in this case), then your tool is ready for installation in DE.


<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="optional"></a>
### Optional steps

You can tag the image like so::

`docker tag ubuntu/megahit:<tag> <user>/megahit:<tag>`

If you wanted to make this broadly available, the next steps
would be to log into the [Docker Hub] (https://hub.docker.com/) and push it; I did so with these commands: ``docker login`` and ``docker push <user>/megahit:<tag>``.


<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="summary"></a>
### Summary points

* Docker provides a nice way to bundle multiple packages of software
  together, for both yourself and for others to run.

* Docker gives you a good way to isolate what you're running from the
  data you're running it on.

* The Dockerfile enhances reproducibility by giving explicit instructions
  for what to install, rather than simply bundling it all in a binary.


<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="support"></a>
### CyVerse support

If you have any trouble dockerizing a tool post your question in CyVerse [Ask](http://ask.iplantcollaborative.org/questions/)
