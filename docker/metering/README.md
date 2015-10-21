Watcher Metering Chain Installation
===================================

Architecture
------------

Please have a look on the [Watcher metering architecture] before to deploy the complete chain.

You have to be familiar with Docker and its *docker-compose* orchestration tool.

 [Watcher metering architecture]: https://factory.b-com.com/www/watcher/watcher-metering


Install docker & docker compose
-------------------------------

To install Docker, you can refer to this page : [Supported installation]
and choose your distribution.

1.  On Ubuntu simply do:

        $ sudo apt-get install curl

        $ curl -sSL https://get.docker.com | sh

2.  To test your installation:

        $ sudo docker run hello-world

To install Docker compose, you can refer to this page: [Install Docker Compose]:

    $ curl -L https://github.com/docker/compose/releases/download/1.4.0/docker-compose-)\`uname -s\`-\`uname -m\` \> /usr/local/bin/docker-compose

    $ chmod +x /usr/local/bin/docker-compose




Watcher Metering containers
===========================

Watcher metering containers are:
 -   watcher-metering-agent
 -   watcher-metering-publisher
 -   riemann
 -   influxdb
 -   nanoconfig-server
 -   grafana


*Watcher-metering-agent* container is only used for development. Watcher Metering Agent must be deployed on compute nodes or on a DevStack platform (Please see instruction in project [Watcher Metering]). 


If you don't need to deploy a metering agent container, edit the orchestration file and remove/comment the metering-agent service:

       $ cd metering
       $ vi docker-compose.xml



Customize the orchestration template file
=========================================

If you install a metering agent on a compute node (not included into docker runtime environment), you have to customize the file *docker-compose.xml* file to allow this metering agent to send message to the metering publisher container.

1. In the service *watcher-metering-publisher*, export listening port 12345:

       $ vi metering/docker-compose.xml

   and uncomment *ports* part

       ports:
         - 12345:12345

2. In the service *nanoconfig-server* service, set the parameter *WATCHER_METERING_PUBLISHER_ENDPOINT* url
       
       WATCHER_METERING_PUBLISHER_ENDPOINT: tcp://<HOST_IP>:12345
   
   with

      HOST_IP: External IP address of the metering publisher container (i.e. IP address of the physical machine hosting the Docker Engine)


Run the Metering Chain
======================
Deploy the containers:

    $ cd metering
    $ docker-compose up -d


  [DevStack - an OpenStack Community Production]: http://docs.openstack.org/developer/devstack/
  [DevStack Configuration]: http://docs.openstack.org/developer/devstack/configuration.html
  [Supported installation]: https://docs.docker.com/installation/
  [Install Docker Compose]: https://docs.docker.com/compose/install/
  [Watcher Metering]:       https://github.com/b-com/watcher-metering
