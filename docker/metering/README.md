Watcher Metering Chain Installation
===================================

Architecture
------------

Please have a look at the [Watcher metering architecture] before you deploy the complete chain.

You have to be familiar with Docker and its *docker-compose* orchestration tool.

 [Watcher metering architecture]: https://factory.b-com.com/www/watcher/doc/watcher-metering


Install docker and docker compose
---------------------------------

We suppose you have already installed docker and docker-compose tools. If not, please read this [README] part.

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

*watcher-metering-agent* container is only used for development. Watcher Metering Agent container must be deployed on compute nodes or on a DevStack platform (Please see instruction in project [Watcher Metering]). 

For non-dev infrastructure (QA, Production, ...), you'd rather deploy the metering agent via the pip utility because it does not oblige you to install docker and docker compose to all the  compute nodes of your Openstack infrastructure. If you don't need to deploy a metering agent container, edit the *docker-compose.yml* file and remove/comment the metering-agent service:

       $ cd docker/metering
       $ vi docker-compose.yml



Customize the Docker Compose template file
==========================================

If you install a metering agent on a distant compute node (i.e. not included into docker runtime environment), you have to customize the *docker-compose.yml* file to allow this Watcher metering agent to send messages to the Watcher metering publisher container.

1. In the *watcher-metering-publisher* service, export listening port 12345:

       $ vi docker/metering/docker-compose.yml

   and uncomment *ports* part

       ports:
         - 12345:12345

2. In the *nanoconfig-server* service, set the parameter *WATCHER_METERING_PUBLISHER_ENDPOINT* url
       
       WATCHER_METERING_PUBLISHER_ENDPOINT: tcp://<HOST_IP>:12345
   
   with:

      HOST_IP: External IP address of the metering publisher container (i.e. IP address of the physical machine hosting the Docker Engine)

You may also want to customize the name of your Datacenter so that this name appears in some metrics tag (each metric is stored in the InfluxDB database with one or several tags such as the associated data-center, the associated compute node name, ...).

3. In the *watcher-metering-publisher* service, set the parameter *METRICS_DATACENTER* to the value you want, for instance "Datacenter-West-Area-1".


Run the Metering Chain
======================
Deploy the containers:

    $ cd docker/metering
    $ docker-compose up -d

You should be able to access the InfluxDB web interface : http://localhost:8083

And you should also be able to view the evolution of some metrics using the Grafana Web interface : http://localhost:3000

Note that you will only be able to see metrics coming the compute nodes where a Watcher metering agent has been installed and configured to send those metrics to the host where you have deployed the Watcher metering containers.

  [DevStack - an OpenStack Community Production]: http://docs.openstack.org/developer/devstack/
  [Watcher Metering]:       https://github.com/b-com/watcher-metering