Watcher Installation
====================

To test Watcher, you can deploy it next to DevStack (or an OpenStack platform) and run Watcher in a
Docker environment. Watcher image containers integrate automatic
provisioning scripts to simplify installation and configuration. You can
install DevStack and Docker tools on the same machine.

You have to be familiar with Docker and its *docker-compose*
orchestration tool.

Note: this following procedure has been validated on Ubuntu 14.04.LTS OS, with docker v1.8.0. and docker-compose v1.4.0.

Install devstack
----------------

If you need to install DevStack, you can refer to this page : [DevStack - an OpenStack Community Production]. We recommend using a minimal install of Ubuntu or Fedora server in a new VM.

    $ git clone https://git.openstack.org/openstack-dev/devstack

    $ cd devstack

    $ git checkout stable/kilo 

    $ ./stack.sh

Note: If you deploy DevStack on a NATed virtual machine, don't forget to udpate the DevStack default configuration by setting, in the file *local.conf*, the HOST_IP parameter with the IP address on you physical machine. for more information, go to [DevStack Configuration].

Install docker and docker compose
---------------------------------

To install Docker, you can refer to this page : [Supported installation]
and choose your distribution.

1.  On Ubuntu simply do:

        $ sudo apt-get update

        $ sudo apt-get install curl

        $ curl -sSL https://get.docker.com | sh

2. If you would like to use Docker as a non-root user, you should now consider adding your user to the *docker* group with something like:

        $ sudo usermod -aG docker <your_non_root_user>
    Remember that you will have to log out and back in for this to take effect!

3.  Verify your docker version ...

        $ docker --version

3.  ... and test your installation:

        $ docker run hello-world

To install Docker compose, you can refer to this page: [Install Docker Compose]:

    $ sudo curl -L https://github.com/docker/compose/releases/download/1.4.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

    $ chmod +x /usr/local/bin/docker-compose

  [DevStack - an OpenStack Community Production]: http://docs.openstack.org/developer/devstack/
  [DevStack Configuration]: http://docs.openstack.org/developer/devstack/configuration.html
  [Supported installation]: https://docs.docker.com/installation/
  [Install Docker Compose]: https://docs.docker.com/compose/install/

Watcher containers
==================

Containers orchestration templating is composed of 3 groups of
containers, each group are independent, but it is advised to start the SERVICE_DISCOVERY first:

-   SERVICE\_DISCOVERY : containers providing an automatic service discovery tool, based on:
     -   [consul]
     -   [registrator]

-   WATCHER : containers providing a basic OpenStack identity Service and the Watcher modules (including Horizon plugin):
     -   **mariadb**: standalone SQL database used to store Watcher objects,
     -   **rabbitmq**: RabbitMQ Server providing communication bus for Watcher modules (API, Decision Engine and Applier).
     -   **keystone**: Identity Service.
     -   **console**: virtual console used to automatically create the Watcher service into Identity Service. It provides complete runtime environment to play with the Watcher CLI. 
     -   **watcher-api**: Watcher API service. This container also builds , at startup, the Watcher SQL tables.
     -   **watcher-decision-engine**: Watcher Decision Engine module.
     -   **watcher-applier**: Watcher Applier module.
     -   **horizon**: OpenStack dashboard including the Watcher UI plugin.

-  METERING: containers providing a complete metering chain for Watcher:
     -   **riemann**: Complex Event Processing module (CEP) used to trigger specific actions based on received metrics events.
     -   **influxdb**: Time Series database, used to store metrics coming from the Publisher module.
     -   **nanoconfig-server**: Nano Config server, used to configure automatically communication channel between Agent(s) and Publisher(s).
     -   **watcher-metering-publisher**: Metering Publisher module, used to collect metrics sent by Metering Agents, before they are pushed to Ceilometer or to the Time Series Database.
     -   **watcher-metering-agent**: Metering Agent module used to collect metrics from compute node.
     -   **grafana**: Metering dashboard, used to visualize metering data stored in the Time Series database.

  [consul]: https://github.com/hashicorp/consul
  [registrator]: https://github.com/gliderlabs/registrator

**Note:** Container images version is, by default, the latest one available on [Docker Hub web portal]. You can fix image version directly into docker-compose template files if you don't want to use the latest one.
  
[Docker Hub web portal]: https://hub.docker.com/u/watcher/

Run the Service Discovery tools
===============================

After cloning this project, deploy the containers:

    $ cd docker/service_discovery
    $ docker-compose up -d

You should be able to see docker fetching the needed Docker images and starting the containers necessary for the Service Discovery used by some Watcher components.
You can view the list of started containers very easily:

    $ docker ps

You can also connect to http://localhost:8500 to check Consul service works fine.

Run the Watcher Service
=======================
  Please see [README.md](https://github.com/b-com/watcher-tools/tree/master/docker/watcher/README.md) 

Run the Watcher Metering Chain
==============================
  Please see [README.md](https://github.com/b-com/watcher-tools/tree/master/docker/metering/README.md) 