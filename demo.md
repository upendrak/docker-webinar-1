Demo for Dockerizing a tool in the DE 
======================================

This document aims to be the one-stop shop for getting your hands dirty with Docker. It'll provide you hands-on experience with building your tools in CyVerse's DE. For this demo, i'm going to show how you can dockerize couple of tools - [Kallisto](https://github.com/pachterlab/kallisto) and [Fastqc_plus](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.10.1.zip) as well importing them to CyVerse DE. Even if you have no prior experience with deployments, this demo should be all you need to get started. All the code used in the demo is available in the [Github repo](https://github.com/upendrak/docker-webinar-1).

### Prerequisites

1. Install [Docker](https://github.com/upendrak/docker-webinar-1#install-docker) and any other dependencies
2. Ensure the tool you want to Dockerize is available from a reliable source (examples of reliable source include - github, bitbucket etc., examples of non reliable source include dropbox links, google drive links, personal computers etc)

### Steps

Step 1: Check if the tool and correct version are already installed in the DE.
Before requesting installation of a new tool or new version of an existing tool, check the [list of all tools in the DE] (https://pods.cyverse.org/wiki/display/DEmanual/Finding+the+List+of+Available+Tools)

Step 2: Create a Dockerfile.
## Kallisto with Docker

kallisto is a program for quantifying abundances of transcripts from RNA-Seq data, or more generally of target sequences using high-throughput sequencing reads. It is based on the novel idea of pseudoalignment for rapidly determining the compatibility of reads with targets, without the need for alignment.

Here is the Dockerfile for Kallisto

```
FROM ubuntu:14.04.3
MAINTAINER Kapeel Chougule <kapeel@cyverse.org>
LABEL Description="This image is used for running Kallisto RNA seq quantification tool"
RUN apt-get update 
RUN apt-get install -y build-essential cmake zlib1g-dev libhdf5-dev git
RUN git clone https://github.com/pachterlab/kallisto.git \
    && cd kallisto \
    && git checkout 5c5ee8a45d6afce65adf4ab18048b40d527fcf5c \
    && mkdir build \
    && cd build \
    && cmake .. \
    && make \
    && make install
ENTRYPOINT ["kallisto"]
```

Build the Kallisto image now ::

`Docker build -t"=ubuntu/kallisto" .`


Test the built Kallisto image with input arguments ::

```
docker run --rm -v=~/my-scratch-dir:/kallisto_data -w \
                kallisto_data ubuntu/kallisto kallisto \
                index -i transcripts.idx transcripts.fasta.gz
```

Tag the built Kallisto image::

`docker tag ubuntu/kallisto:latest kapeel/kallisto:latest`

Push the Kallisto image to Dockerhub (optional) ::

```
docker login
docker push kapeel/kallisto:latest
```

## Fastqc with Docker



