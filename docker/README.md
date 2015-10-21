Watcher Installation
====================

To test Watcher, you can deploy it next to DevStack and run Watcher in a
Docker environment. Watcher image containers integrate automatic
provisioning scripts to simplify installation and configuration. You can
install DevStack and Docker tools on the same machine.

You have to be familiar with Docker and its *docker-compose*
orchestration tool.

Install devstack
----------------

If you need to install DevStack, you can refer to this page : [DevStack - an OpenStack Community Production] (We recommend using a minimal install of Ubuntu or Fedora server in a new VM)

    $ git clone https://git.openstack.org/openstack-dev/devstack

    $ cd devstack

    $ git checkout stable/kilo 

    $ ./stack.sh

Note: If you deploy DevStack on a NATed virtual machine, don't forget to udpate the DevStack default configuration by setting, in the file *local.conf*, the HOST_IP parameter with the IP address on you physical machine. for more information, go to [DevStack Configuration].


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

  [DevStack - an OpenStack Community Production]: http://docs.openstack.org/developer/devstack/
  [DevStack Configuration]: http://docs.openstack.org/developer/devstack/configuration.html
  [Supported installation]: https://docs.docker.com/installation/
  [Install Docker Compose]: https://docs.docker.com/compose/install/

Watcher containers
==================

Containers orchestration templating is composed of 3 groups of
containers, each group are independent:

-   SERVICE\_DISCOVERY : containers providing an automatic service discovery tool, based on:
     -   [consul]
     -   [registrator]

-   WATCHER : containers providing a basic OpenStack identity Service and the Watcher modules (including Horizon plugin):
     -   **mariadb**: standalone SQL database used to store Watcher objects,
     -   **rabbitmq**: RabbitMQ Server providing communication bus for Watcher modules (API, DE and APPLIER).
     -   **keystone**: Identity Service.
     -   **console**: virtual console used to automaticaly create the Watcher service into Identity Service. It provide complete runtime environment to play with the Watcher CLI. 
     -   **watcher-api**: Watcher API service. This container build also, at startup, the Watcher SQL tables.
     -   **watcher-decision-engine**: Watcher Decision Engine module.
     -   **watcher-applier**: Watcher Applier module.
     -   **horizon**: OpenStack dashboard including the Watcher UI plugin.

-  METERING: containers providing a complete metering chain for Watcher:
     -   **riemann**: Complex Event Processing module (CEP) used to trigger specific actions based on received metrics events.
     -   **influxdb**: Time Serie Db, used to store metrics coming from the Publisher module.
     -   **nanoconfig-server**: Nano Config server, used to configure automaticaly communication channel between Agent and Publisher.
     -   **watcher-metering-publisher**: Metering Publisher module, used to collect metrics sending by Agents, before to push them into Time Serie Db.
     -   **watcher-metering-agent**: Metering Agent module used to collect metrics from host.
     -   **grafana**: Metering dashboard, used to visualize metering data stored in the Time Serie Db.

  [consul]: https://github.com/hashicorp/consul
  [registrator]: https://github.com/gliderlabs/registrator

Run the Service Discovery tool
==============================

Deploy the containers:

    $ cd service-discovery
    $ docker-compose up -d


Run the Watcher Service
=======================
  Please see [README.md](watcher/README.md) 

Run the Wathcer Metering Chain
==============================
  Please see [README.md](metering/README.md) 
