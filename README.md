# Container Learning Week - Docker Basics

## Prerequisites:

### Datadog Sandbox Account

Ensure you have an active Datadog sandbox account
```
https://app.datadoghq.com/account/settings/agent/latest?platform=docker
```

### Docker (will begin here in workshop)

We'll be downloading Docker Desktop
```
[https://kubernetes.io/docs/tasks/tools/#kubectl](https://docs.docker.com/desktop/install/mac-install/)
```

### Other Essentials

- A working terminal with cURL installed


## Deploying the Docker Agent

### Step 1: Installing the Docker Agent

Reference: https://app.datadoghq.com/account/settings/agent/latest?platform=docker

I've generated a pre-built command below:
```
docker run -d --name dd-agent \
-e DD_API_KEY=c797c477e85d050512e243d46d3b5c1a \
-e DD_SITE="datadoghq.com" \
-e DD_LOGS_ENABLED=true \
-e DD_LOGS_CONFIG_CONTAINER_COLLECT_ALL=true \
-e DD_PROCESS_CONFIG_PROCESS_COLLECTION_ENABLED=true \
-v /etc/passwd:/etc/passwd:ro \
-v /var/run/docker.sock:/var/run/docker.sock:ro \
-v /proc/:/host/proc/:ro \
-v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro \
-v /var/lib/docker/containers:/var/lib/docker/containers:ro \
gcr.io/datadoghq/agent:7
```
Explaining this script: 

        a.  docker run \: Docker command to instruct docker to build a container with the steps below. More info https://docs.docker.com/engine/reference/run/  https://docs.datadoghq.com/containers/docker/?tab=standard 
        
        b. Setting the following environment to define the DD docker agent configuration:
           -e DD_API_KEY=<your_DD_api_key> \: API key. https://docs.datadoghq.com/containers/docker/?tab=standard#global-options 
           -e DD_SITE="datadoghq.com" \ Defining the site parameter. Common signs of this being incorrect are invalid API Key errors: https://docs.datadoghq.com/getting_started/site/
           -e DD_LOGS_ENABLED=true \: Enabling Log Collection for the container agent: https://docs.datadoghq.com/containers/docker/log/?tab=containerinstallation
           -e DD_LOGS_CONFIG_CONTAINER_COLLECT_ALL=true \: Asking the DD docker agent to collect logs from other containers on the Docker Desktop host. https://docs.datadoghq.com/containers/docker/?tab=standard#optional-collection-agents 
           -e DD_PROCESS_CONFIG_PROCESS_COLLECTION_ENABLED=true \: Enabling Live Process Monitoring on the container agent: https://docs.datadoghq.com/infrastructure/process/?tab=docker#installation
           
        c. Setting the volumes where the DD docker agent will be installed in the container
            -v /etc/passwd:/etc/passwd:ro \
            -v /var/run/docker.sock:/var/run/docker.sock:ro \
            -v /var/lib/docker/containers:/var/lib/docker/containers:ro \
            -v /proc/:/host/proc/:ro \
            -v /opt/datadog-agent/run:/opt/datadog-agent/run:rw \
            -v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro \
            
        d. --name datadog-agent \: Setting container name
        
        f. gcr.io/datadoghq/agent:latest: Defining image and version to use. Default reference: https://hub.docker.com/r/datadog/agent 

### Step 2: Verify Agent Is Running Successfully

Confirm Agent container is running:
```
docker ps
```

Agent status:

```
sudo docker exec -it <CONTAINER_ID_OR_NAME> agent status
```


## Helpful Troubleshooting Tips and Resources

Step 2 from above. Make sure Agent container is deployed and in running state.


