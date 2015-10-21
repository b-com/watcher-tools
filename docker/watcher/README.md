Install the Watcher module
==========================

This installation procedure will help you deploy the following Docker containers using a Docker compose description file:
-   mariadb,
-   rabbitmq,
-   keystone,
-   console,
-   watcher-api,
-   watcher-decision-engine,
-   watcher-applier,
-   horizon (including Watcher plugin)


The file *docker/watcher/docker-compose.xml* is a global orchestration template file. You can customize it in order to deploy and configure all containers or a part of them, according to your needs.

Some Watcher components need to connect to the following Openstack components:
 - mariadb,
 - rabbitmq,
 - keystone

For each of these Openstack components you have two possible options:
 - either you leave the *docker-compose.yml* file unchanged so that Watcher components connect to the containers deployed by Docker compose on the same host,
 - or you configure the *docker-compose.yml* file so that Watcher components connect to an already existing Openstack controller.

If you want to connect to an existing OpenStack Identity Service Keystone
-------------------------------------------------------------------------

1. Edit the template file *docker-compose.yml*:

       $ cd docker/watcher
       $ vi docker-compose.yml

   and remove/comment the *keystone* service.

2. in this file, set the following environment variables (you have to set them in several services): 
   -   OpenStack Controller modules settings:
       -   **KEYSTONE\_NODE**: Keystone IP address
       -   **KEYSTONE\_ADMIN\_TOKEN**: Keystone admin token (default is *ADMIN\_TOKEN*)
       -   **KEYSTONE\_SERVICE\_TENANT\_NAME**: Keystone service project name (default is *service*)

   -  Watcher console:

      - **WATCHER_API_NODE**: Watcher API node (default is *watcher.service.watcher.b-com.com*)
      - **EXIT_AFTER_INIT**: Set to true if you want to stop the console container after provisionning of watcher service (default is *false*)

   -  *admin* user's credentials :

      - **OS_AUTH_URL**: DevStack Keystone Identity Auth URL (default is *http://keystone.service.watcher.b-com.com:5000/v3*) 
       -   **OS\_IDENTITY\_API\_VERSION**: Keystone Identity API Version (2.0|3, default is *3*)
       -   **OS\_PROJECT\_DOMAIN\_ID**: Project Domain ID (default is *default*)
       -   **OS\_USER\_DOMAIN\_ID**: User Domain ID (default is *default*)
       -   **OS\_USERNAME**: User name (default is *admin*)
       -   **OS\_PASSWORD**: User password (default is *1234*)
       -   **OS\_PROJECT\_NAME**: Project name (default is *admin*)



If you want to connect to an existing OpenStack MariaDB SQL database
--------------------------------------------------------------------

1. Edit the template file *docker-compose.yml*:

       $ cd docker/watcher
       $ vi docker-compose.yml

   and remove/comment the *mariadb* service.


2. In this file, set the following environment variable (you have to set it in several services): 
      -   **MARIADB\_NODE**: MySQL server IP address.

3. into your SQL database, create the Watcher database, with these commands:

        $ mysql -u<mysql_user> -p<mysql_pwd> -e 'DROP DATABASE IF EXISTS watcher;'
        $ mysql -u<mysql_user> -p<mysql_pwd> -e 'CREATE DATABASE watcher;'
        $ mysql -u<mysql_user> -p<mysql_pwd> -e 'GRANT ALL PRIVILEGES ON watcher.* TO "watcher"@"localhost" IDENTIFIED BY "watcher";'
        $ mysql -u<mysql_user> -p<mysql pwd> -e 'GRANT ALL PRIVILEGES ON watcher.* TO "watcher"@"%" IDENTIFIED BY "watcher";'



If you want to connect to an existing OpenStack RabbitMQ server
---------------------------------------------------------------

1. Edit the template file *docker-compose.yml*:

       $ cd docker/watcher
       $ vi docker-compose.yml

   and remove/comment the *rabbitmq* service.


2. In this file, set the following environment variables (you have to set it in several services): 
    - **WATCHER_MESSAGING_USER**: RabbitMQ user name (default is *guest*).
    - **WATCHER_MESSAGING_PASSWORD**: RabbitMQ user password (default is *guest*).
    - **WATCHER_MESSAGING_HOST**: RabbitMQ host IP address.
    - **WATCHER_MESSAGING_PORT**: RabbitMQ listening port (default is *5672*).



If you want to enable debugging
-------------------------------

You can enable/disable debug and verbose mode, by setting these parameters:

1. Edit the template file *docker-compose.yml*:

       $ cd docker/watcher
       $ vi docker-compose.yml

2. In this file, set the following environment variables (you can set them in several services): 
      -   **DEBUG\_MODE**: Debug mode (true|false, default is *false*)
      -   **VERBOSE\_MODE**: Verbose mode (true|false, default is *false*)



Run Watcher
===========
To start Watcher containers, you can simply run this command: 

  $ cd docker/watcher
  $ docker-compose up -d


Use Watcher
===========

You can use the *console* container to play with Watcher CLI.

1. Connect to the Watcher console container:

        $ docker ps

2. Get the *console* container ID and attach to it :

        $ docker exec -it < watcher_console_container_ID > bash
   and use watcher CLI:

        root@console:/# cd /root

        root@console:/# source creds

        root@console:~# watcher audit-list
        +------+------+---------------------+-------+
        | UUID | Type | Audit Template Name | State |
        +------+------+---------------------+-------+
        +------+------+---------------------+-------+

Important notes
---------------

Please check your Compute’s hypervisor configuration to handle correctly
instance live migration:  [OpenStack - Configure Migrations]

  [OpenStack - Configure Migrations]: http://docs.openstack.org/admin-guide-cloud/compute-configuring-migrations.html