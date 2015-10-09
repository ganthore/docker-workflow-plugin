<<<<<<< HEAD
Docker image for Docker workflow demo
=====================================
This image contains a "Docker Workflow" Job that demonstrates Jenkins Workflow integration
with Docker via [CloudBees Docker Workflow](https://wiki.jenkins-ci.org/display/JENKINS/CloudBees+Docker+Workflow+Plugin) plugin.

```
docker run --rm -p 8080:8080 -p 8081:8081 -p 8022:22 --add-host=docker.example.com:127.0.0.1 -ti --privileged jenkinsci/docker-workflow-demo
```

The "Docker Workflow" Job simply does the following:

1. Gets the Spring Pet Clinic demonstration application code from GitHub.
1. Builds the Pet Clinic application in a Docker container.
1. Builds a runnable Pet Clinic application Docker image.
1. Runs a Pet Clinic app container (from the Pet Clinic application Docker image) + a second maven3 container that runs automated tests against the Pet Clinic app container.
  * The 2 containers are linked, allowing the test container to fire requests at the Pet Clinic app container.

The "Docker Workflow" Job demonstrates how to use the `docker` DSL:

1. Use `docker.image` to define a DSL `Image` object (not to be confused with `build`) that can then be used to perform operations on a Docker image:
  * use `Image.inside` to run a Docker container and execute commands in it. The build workspace is mounted as the working directory in the container.
  * use `Image.run` to run a Docker container in detached mode, returning a DSL `Container` object that can be later used to stop the container (via `Container.stop`).
1. Use `docker.build` to build a Docker image from a `Dockerfile`, returning a DSL `Image` object that can then be used to perform operations on that image (as above). 
  
The `docker` DSL supports some additional capabilities not shown in the "Docker Workflow" Job:
  
1. Use the `docker.withRegistry` and `docker.withServer` to register endpoints for the Docker registry and host to be used when executing docker commands.
  * `docker.withRegistry(<registryUrl>, <registryCredentialsId>)`
  * `docker.withServer(<serverUri>, <serverCredentialsId>)` 
1. Use the `Image.pull` to pull Docker image layers into the Docker host cache.
1. Use the `Image.push` to push a Docker image to the associated Docker Registry. See `docker.withRegistry` above. 
=======
Docker image for workflow demo
==============================

This container includes Jenkins with workflow plugin and Jetty to demonstrate a continuous delivery pipeline of Java web application.
It highlights key parts of the workflow plugin:

Run it like:

    docker run -p 8080:8080 -p 8081:8081 -p 9418:9418 -ti jenkinsci/workflow-demo

Jenkins runs on port 8080, and Jetty runs on port 8081.

__Note__: If using [boot2docker](https://github.com/boot2docker/boot2docker), you will need to connect using the boot2docker
VM's IP (instead of `localhost`).  You can get this by running `boot2docker ip` on the command line.

The continuous delivery pipeline consists of the following sequence.

* Loads the workflow script from [Jenkinsfile](https://github.com/jenkinsci/workflow-plugin/blob/master/demo/repo/Jenkinsfile) in a local Git repository.
  You may clone from, edit, and push to `git://localhost/repo`.
  Each branch automatically creates a matching subproject that builds that branch.
* Checks out source code from the same repository and commit as `Jenkinsfile`.
* Builds sources via Maven with unit testing.
* Run two parallel integration tests that involve deploying the app to a PaaS-like ephemeral server instances, which get
  thrown away when tests are done (this is done by using auto-deployment of Jetty)
* Once integration tests are successful, the webapp gets to the staging server at http://localhost:8081/staging/
* Human will manually inspect the staging instance, and when ready, approves the deployment to the production server at http://localhost:8081/production/
* Workflow completes

To log in to the container, get `nsenter` and [you can use docker-enter](http://jpetazzo.github.io/2014/06/23/docker-ssh-considered-evil/),
or just use `docker exec -ti $container /bin/bash`.
This is useful to kill Jetty to simulate a failure in the production deployment (via `pkill -9 -f jetty`) or restart it (via `jetty &`)

[Binary image page](https://registry.hub.docker.com/u/jenkinsci/workflow-demo/)

Sample demo scenario
--------------------

* Explain the setup of the continuous delivery pipeline in a picture
* Go to [job configuration](http://localhost:8080/job/cd/configure) and walk through the workflow script
  and how that implements the pipeline explained above
    * Discuss use of abstractions like functions to organize complex workflow
    * Highlight and explain some of the primitives, such as `stage`, `input`, and `node`
* Get one build going, and watch [Jetty top page](http://localhost:8081/) to see ephemeral test instances
  deployed and deleted
* When it gets to the pause, go to the pause UI in the [build top page](http://localhost:8080/job/cd/1/) (left on the action list) and terminate the workflow
* Get another build going, but this time restart the Jenkins instance while the workflow is in progress
  via [restart UI](http://localhost:8080/restart). Doing this while the integration test is running,
  as steps like Git checkout will get disrupted by restart.

CloudBees Jenkins Enterprise variant
--------------------------

If you would like to see CloudBees Jenkins Enterprise features (such as checkpoints and the stage pipeline view),
see the [extended demo page](https://registry.hub.docker.com/u/cloudbees/workflow-demo/).
>>>>>>> upstream/master
