# appserv-generic-interface


This documents describes the generic system level interface that all application services need to be deployed onto complient systems.

Inspiration was taken from [The Twelve-Factor App](http://12factor.net/) to provide a clean contract between the system
and application services.  This document assumes that services are twelve-factor apps and comply with the tweleve-factor design constraints.

## Definitions

* Deployer: The system that is using this interface to deploy the application
* Service: The HTTP based service that exposes this interface to enable a deployer to deploy the service
* Build stage: The stage that enables the deployer to create a deployable build from the code repo
* Container: A cleanroom environment free from artifacts from previous builds (examples: Docker container, new EC2 instance)
* Service Cluster: A cluster of container nodes runing the service

## Deployment Pipeline

The high level deployment pipeline is as follows

1. developer commits to the mainline branch
2. deployer instantiates a new build container
3. code is deployed to container
4. `make install` ran in container
5. deployer creates deployable image of container
6. container is deployed to a staging cluster
7. `make server` is started in each container with $CONFIG_URL set to production and $PORT set to the exposed port
8. deployer runs `make acceptance` on the staging cluster $URL configured
9. deployer puts staging cluster into production

## Contract

### make install

Services will have a `make install` command that deployers use during the build stage to configure the container for deployment.  The "make install" command will pull down all dependancies neccessary to run the service.  It is at this stage that environment agnostic tests are executed (unit tests, component tests, etc).

### make server

Services will have a `make server` command that starts an HTTP server on `127.0.0.1:$PORT`.  

The services expect that the `make server` process will be supervised and restarted if the process crashes.

The services expect that `make server` process environment contains a `CONFIG_URL` value that allows the service to configure itself at runtime.

### make acceptance

Once the serivce is running on a production-like environment, `make acceptance` is ran to prove that the service and deployment is acceptable for public traffic.

The services expect that `make acceptance` process environment contains a `URL` value that allows the acceptance tests to locate the service for testing.  The `URL` environment variable allows the deployer to point the acceptance tests at any layer of the HTTP infrastructure, from origin to the edge.


