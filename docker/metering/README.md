Watcher Metering Chain Installation
===================================

Architecture
------------

Please have a look at the [Watcher metering architecture] before you deploy the complete chain.

You have to be familiar with Docker and its *docker-compose* orchestration tool.

 [Watcher metering architecture]: https://factory.b-com.com/www/watcher/doc/watcher-metering


Install docker and docker compose
---------------------------------

We suppose you have already installed docker and docker-compose tools. if not, please read this [README] part.

 [README]: https://github.com/b-com/watcher-tools/tree/master/docker#install-docker-and-docker-compose 

Watcher Metering containers
===========================

This installation procedure will help you deploy the following Docker containers using a Docker compose description file:
 -   watcher-metering-agent
 -   watcher-metering-publisher
 -   riemann
 -   influxdb
 -   nanoconfig-server
 -   grafana

*Watcher-metering-agent* container is only used for development. Watcher Metering Agent container must be deployed on compute nodes or on a DevStack platform (Please see instruction in project [Watcher Metering]). 

For non-dev infrastructure (QA, Production, ...), you'd rather deploy the metering agent via the pip utility because it does not oblige you to install docker and docker compose to all the  compute nodes of your Openstack infrastructure. If you don't need to deploy a metering agent container, edit the orchestration file and remove/comment the metering-agent service:

       $ cd docker/metering
       $ vi docker-compose.yml



Customize the orchestration template file
=========================================

If you install a metering agent on a compute node (not included into docker runtime environment), you have to customize the file *docker-compose.xml* file to allow this metering agent to send message to the metering publisher container.

1. In the service *watcher-metering-publisher*, export listening port 12345:

       $ vi docker/metering/docker-compose.xml

   and uncomment *ports* part

       ports:
         - 12345:12345

2. In the service *nanoconfig-server* service, set the parameter *WATCHER_METERING_PUBLISHER_ENDPOINT* url
       
       WATCHER_METERING_PUBLISHER_ENDPOINT: tcp://<HOST_IP>:12345
   
   with:

      HOST_IP: External IP address of the metering publisher container (i.e. IP address of the physical machine hosting the Docker Engine)


Run the Metering Chain
======================
Deploy the containers:

    $ cd docker/metering
    $ docker-compose up -d


  [DevStack - an OpenStack Community Production]: http://docs.openstack.org/developer/devstack/
  [Watcher Metering]:       https://github.com/b-com/watcher-metering
