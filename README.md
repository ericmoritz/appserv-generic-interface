appserv-generic-interface
=========================

This documents describes the generic system level interface that all application services need to be deployed onto complient systems.

Inspiration was taken from [The Twelve-Factor App](http://12factor.net/) to provide a clean contract between the system
and application services.

Definitions
-------------

* Deployer: The system that is using this interface to deploy the application
* Service: The HTTP based service that exposes this interface to enable a deployer to deploy the service
* Build stage: The stage that enables the deployer to create a deployable build from the code repo
* Container: A cleanroom environment free from artifacts from previous builds (examples: Docker container, new EC2 instance)

Contract
-----------

 * Services will have a `make install` command that serves is used during the build stage to configure the container for deployment.  The "make install" command will pull down all dependancies neccessary to run the service.  It is at this stage that environment agnostic tests are executed (unit tests, component tests, etc).
 * Services will have a `make server` comman that starts an HTTP server on `127.0.0.1:$PORT`
 * The services expect that the `make server` process will be supervised and restarted by the underlying system
 * The services expect that `make server` process environment contains a `CONFIG_URL` value that allows the service to configure itself at runtime
