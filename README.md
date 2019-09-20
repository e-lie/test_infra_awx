# Web app infrastructure example 

This is a simple example of a complete web architecture configuration using Ansible to configure a set of VMs either on local infrastructure using VirtualBox and Vagrant (using the included Vagrantfile), or on a cloud hosting provider (in this case, DigitalOcean).

The architecture for the example web application will be:

                       --------------------------
                      |  haproxy.test (haproxy)  |
                      |  10.164.210.111          |
                       --------------------------
                          /                   \
         ----------------------          ----------------------
        |  app1.test (Apache)  |        |  app2.test (Apache)  |
        |  10.164.210.121      |        |  10.164.210.122      |
         ----------------------          ----------------------
                          \                   /
                      ------------------------------
                     |  memcached.test (Memcached)  |
                     |  10.164.210.141              |
                      ------------------------------
                          /                   \
     -----------------------------       ----------------------------
    |  db1.test (MySQL - Master)  |     |  db2.test (MySQL - Slave)  |
    |  10.164.210.131             |     |  10.164.210.132            |
     -----------------------------       ----------------------------

*IP addresses and hostnames in this diagram are modeled after local LXD containers configuration.*

This architecture offers multiple levels of caching and high availability/redundancy on almost all levels, though to keep it simple, there are single points of failure. All persistent data stored in the database is stored in a slave server, and one of the slowest and most constrained parts of the stack (the web servers, in this case running a PHP application through Apache) is easy to scale horizontally, behind haproxy, which is acting as a caching (reverse proxy) layer and load balancer.