# CyVerse Focus Forum Webinar: Fastqc_plus demo 

This document aims to be the one-stop shop for getting your hands dirty with Docker. It'll provide you hands-on experience with building your tools in CyVerse's DE. For this demo, i'm going to show how you dockerize[Fastqc_plus](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.10.1.zip) as well importing them to CyVerse DE. Even if you have no prior experience with docker, this demo should be all you need to get started. All the code used in the demo is available in the [Github repo](https://github.com/upendrak/docker-webinar-1).

### What do you need?

1. [Docker](https://github.com/upendrak/docker-webinar-1#install-docker) 
2. Dockerfile for Fastqc_plus

<a href="#top" class="top" id="table-of-contents">Top</a>
## Table of Contents
- [Create a Dockerfile] (#createdockerfile)
- [Building images](#buildingimages)
- [Optional steps](#optional)
- [CyVerse support](#support)


<a href="#top" class="top" id="table-of-contents">Top</a>
<a id="createdockerfile"></a>
### Create a Dockerfile.

```
mkdir ~/make_fastqc_plus
cd ~/make_fastqc_plus
cat <<EOF > Dockerfile
FROM ubuntu 
MAINTAINER Roger Barthelson <rogerab@email.arizona.edu>
LABEL Description="This image is used for running FastQC and creating an output html file"
RUN apt-get -y update
RUN apt-get -y install wget unzip make tzdata-java openjdk-7-jre-headless openjdk-7-jre default-jre
ADD http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.10.1.zip \
    && unzip fastqc_v0.10.1.zip \ 
    && chmod a+x FastQC/fastqc \
    && cp -r FastQC/* /usr/bin \
ADD -O - http://cpanmin.us | perl - App::cpanminus && cpanm File::Slurp
ADD https://github.com/azroger/iPlant-basic-scripts/archive/master.zip && unzip master.zip
RUN chmod a+x iPlant-basic-scripts-master/embed_images.pl \
    && chmod a+x iPlant-basic-scripts-master/fastqc_plus.sh
RUN cp /iPlant-basic-scripts-master/fastqc_plus.sh /usr/bin \
    && cp /iPlant-basic-scripts-master/embed_images.pl /usr/bin
ENTRYPOINT [ "fastqc_plus.sh" ]
```
