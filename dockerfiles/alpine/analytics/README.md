# Dockerfile for WSO2 Enterprise Integrator Analytics #

This section defines the step-by-step instructions to build [Alpine](https://hub.docker.com/_/alpine/) Linux based Docker images for multiple profiles
provided by WSO2 Enterprise Integrator 6.4.0, namely:<br>

1. Dashboard
2. Worker

## Prerequisites

* [Docker](https://www.docker.com/get-docker) v17.09.0 or above

## How to build an image and run
##### 1. Checkout this repository into your local machine using the following Git command.

```
git clone https://github.com/wso2/docker-ei.git
```

>The local copy of the `dockerfile/alpine/analytics` directory will be referred to as `ANALYTICS_DOCKERFILE_HOME` from this point onwards.
    
##### 2. Build Docker images specific to each profile.

- For dashboard, navigate to `<ANALYTICS_DOCKERFILE_HOME>/dashboard` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2ei-analytics-dashboard:6.4.0-alpine .`
- For worker, navigate to `<ANALYTICS_DOCKERFILE_HOME>/worker` directory. <br>
  Execute `docker build` command as shown below.
    + `docker build -t wso2ei-analytics-worker:6.4.0-alpine .`

- If you want to use your own distribution, you may build the image by executing the following command,
    + eg:- Hosted locally:` docker build --build-arg WSO2_SERVER_DIST_URL=http://172.17.0.1:8000/wso2ei-6.4.0.zip -t wso2ei-<PROFILE>:6.4.0-alpine .`

##### 3. Running Docker images specific to each profile.

- For dashboard,
    + `docker run -p 9713:9713 -p 9643:9643 ...all-port-mappings-here... wso2ei-analytics-dashboard:6.4.0-alpine`
- For worker,
    + `docker run -p 9444:9444 ...all-port-mappings-here... wso2ei-analytics-worker:6.4.0-alpine`
    
##### 4. Accessing the Dashboard portal.

- For Analytics Dashboard,
    + Business Rules:<br>
    `https://<DOCKER_HOST>:9643/business-rules`
    + Portal:<br>
    `https://<DOCKER_HOST>:9643/portal`
    + Monitoring:<br>
    `https://<DOCKER_HOST>:9643/monitoring`
    
>In here, <DOCKER_HOST> refers to hostname or IP of the host machine on top of which containers are spawned.

## How to update configurations
Configurations would lie on the Docker host machine and they can be volume mounted to the container. <br>
As an example, steps required to change the port offset using `deployment.yaml` is as follows.

##### 1. Stop the Enterprise Integrator Analytics container if it's already running.
In Analytics Worker profile product distribution, `deployment.yaml` configuration file <br>
can be found at `<DISTRIBUTION_HOME>/wso2/analytics/conf/worker`. Copy the file to some suitable location of the host machine, <br>
referred to as `<SOURCE_CONFIGS>/deployment.yaml` and increase the offset value under ports by 1.

##### 2. Grant read permission to `other` users for `<SOURCE_CONFIGS>/deployment.yaml`
```
chmod o+r <SOURCE_CONFIGS>/deployment.yaml
```

##### 3. Run the image by mounting the file to container as follows.
```
docker run 
-p 9445:9445
--volume <SOURCE_CONFIGS>/deployment.yaml:<TARGET_CONFIGS>/deployment.yaml
wso2ei-analytics-worker:6.4.0-alpine
```

>In here, <TARGET_CONFIGS> refers to /home/wso2carbon/wso2ei-6.4.0/wso2/analytics/conf/worker folder of the container.


## Docker command usage references

* [Docker build command reference](https://docs.docker.com/engine/reference/commandline/build/)
* [Docker run command reference](https://docs.docker.com/engine/reference/run/)
* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)