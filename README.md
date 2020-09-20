# This is Jenkins cluster with master, two slaves and communication via JNLP.

To be able to initialise all the nodes (the master and two slaves) and have the whole cluster running local on you computer this project uses **docker-compose** 

<img src="readme-imgs/2020-09-21 20-51-02.png"/>


## Docker Base Image
The first thing you need to do is to create a base image with the stack required to build your projects. 
The base image on this project is a dockerized Ubuntu with (java 8, and node 8 as well as maven and git)
So after you clone this project, the first thing is to enter the ```base-image``` folder. 
```
 cd ./base-image
``` 
Inside of base-image/softwares you may find **apache-maven-3.2.5.tar.gz** and **node-v8.16.2-linux-x64.tar.gz**, you must download the 
[jdk1.8.0_261.tar.gz](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html#license-lightbox) and save it on the base-image/softwares folder (I didn't upload the jdk because of its size) to then run **buildBaseImage.sh** 

After the script finishes you should have the base image for your jenkins slaves locally installed, to test it type:
```
docker images
```
<img src="readme-imgs/2020-09-20%2020-37-20.png"/>

## Master Node
The first thing to do after you have the **engcpp/base-image** is to initialise and configure the master Jenkins.
So run:
```
docker-compose up -d jenkins-master
```
Jenkins will initialise and print a hash on the terminal/logs, you will need the hash to login on jenkin to do initial configurations, so to see the logs type:
```
docker-compose logs -f
```
You will then do all the basic initialisation/configuration for your Jenkins on ```http://localhost:8080```, adding a admin user and selecting the plugins that you want to use plus all the initial configurations for your jenkins.. (which is not the focus here.. )


### Configuring the Slaves
To be able to add nodes (slaves) to our master jenkins (we wanna add two), click in **Manage Jenkins** -> **Manage Nodes and Clouds** -> **New Node**
and for the name of the node you should use **slave1** (image bellow is adding slave3 and you should ignore it ;)) and select 

- [x] Permanent Agent
<img src="readme-imgs/2020-09-20%2018-00-05.png"/>
(Permanent agents won't be bootstrapped or destroyed by the master)


Copy the values on the picture to your slave1 (configurations form), please mind the tags **slave** **java** **node** jenkins will use this tags to decide where to send your build to
<img src="readme-imgs/2020-09-20%2018-00-18.png"/>
<img src="readme-imgs/2020-09-20 18-00-29.png"/>
After you save the details you should copy the **secret** of the slave, it will be used to authenticate to the master later on, paste the **secret** on
.env file (This is were we keep environmental variables for our docker-compose project)

```
SLAVE1_SECRET=74bcd10bcab3ce0c43627ed28c0232e5a8a76b07007cbc0e551fc92f6a3c2e3c
SLAVE2_SECRET=986374f3ef1b2adcd2a918c0510f6c21788ada98d08d354b60ac4df9fda29038
```

Repeat the process for slave2 *(if you need more slaves you may have to add then to the docker-compose.yaml)*


### Starting the Slaves
You can now use docker compose to start the slaves, type:
```
docker-compose up -d jenkins-slave1 jenkins-slave2
```

You should be able to se the connections hapenning for the two slaves 
<img src="readme-imgs/2020-09-20 18-00-40.png"/>
<img src="readme-imgs/2020-09-20 18-00-53.png"/>

Configure your pipeline, and trigger the build you should be able to see jenkins delegating the build to the slaves:

> *to be able to delegate the services programatically on your pipeline you can use ``` agent { label 'slave && java && node' } ``` for specific tags "nodes" or  ```agent any``` if you don't want to pick the slave.

<img src="readme-imgs/2020-09-20 17-50-20.png"/>
<img src="readme-imgs/2020-09-20%2017-44-42.png"/>


