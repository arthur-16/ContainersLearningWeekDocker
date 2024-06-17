# Container Learning Week - Docker Basics

## What is Docker?
Docker is an open application development framework that’s designed to benefit DevOps and developers. Using Docker, developers can easily build, pack, ship, and run applications as lightweight, portable, self-sufficient containers, which can run virtually anywhere.



## Prerequisites:

### Datadog Sandbox Account

Ensure you have an active Datadog sandbox account
```
https://app.datadoghq.com/account/settings/agent/latest?platform=docker
```

### Docker (will begin here in workshop)

We'll be downloading Docker Desktop
```
https://docs.docker.com/desktop/install/mac-install/
```

### Other Essentials

- A working terminal with cURL installed


## Deploying the Docker Agent

### Step 1: Installing the Docker Agent

Reference: https://app.datadoghq.com/account/settings/agent/latest?platform=docker

#### I've generated a pre-built command below:
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
#### Script breakdown: 

a.  **docker run \:** Docker command to instruct docker to build a container with the steps below: 
https://docs.docker.com/engine/reference/run/  
https://docs.datadoghq.com/containers/docker/?tab=standard 

        
b. Setting the following environment to define the DD docker agent configuration:

-e **DD_API_KEY=<your_DD_api_key> \:** API key. https://docs.datadoghq.com/containers/docker/?tab=standard#global-options 
           
-e **DD_SITE="datadoghq.com" \:** Defining the site parameter. Common signs of this being incorrect are invalid API Key errors: https://docs.datadoghq.com/getting_started/site/
           
-e **DD_LOGS_ENABLED=true \:** Enabling Log Collection for the container agent: https://docs.datadoghq.com/containers/docker/log/?tab=containerinstallation
           
-e **DD_LOGS_CONFIG_CONTAINER_COLLECT_ALL=true \:** Asking the DD docker agent to collect logs from other containers on the Docker Desktop host. https://docs.datadoghq.com/containers/docker/?tab=standard#optional-collection-agents 
           
-e **DD_PROCESS_CONFIG_PROCESS_COLLECTION_ENABLED=true \:** Enabling Live Process Monitoring on the container agent: https://docs.datadoghq.com/infrastructure/process/?tab=docker#installation

           
c. Setting the volumes where the DD docker agent will be installed in the container
```
            -v /etc/passwd:/etc/passwd:ro \
            -v /var/run/docker.sock:/var/run/docker.sock:ro \
            -v /var/lib/docker/containers:/var/lib/docker/containers:ro \
            -v /proc/:/host/proc/:ro \
            -v /opt/datadog-agent/run:/opt/datadog-agent/run:rw \
            -v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro \
```
            
d. **--name datadog-agent \:** Setting container name

        
f. **gcr.io/datadoghq/agent:7:** Defining image and version to use. Default reference: https://hub.docker.com/r/datadog/agent 

### Step 2: Verify Agent Is Running Successfully

Confirm Agent container is running with the following command:
```
docker ps
```
Command should show an output similar to below, indicating Agent container is actively running:
```
CONTAINER ID   IMAGE                      COMMAND                CREATED          STATUS                    PORTS                NAMES
629a8eda38f8   gcr.io/datadoghq/agent:7   "/bin/entrypoint.sh"   27 minutes ago   Up 27 minutes (healthy)   8125/udp, 8126/tcp   dd-agent
```

Agent status:
```
sudo docker exec -it <CONTAINER_ID_OR_NAME> agent status
```
Returns status output of Agent

Reference: https://docs.datadoghq.com/agent/configuration/agent-commands/?tab=agentv6v7

### Quick Autodiscovery Example:

We'll be using redis for this example: https://hub.docker.com/_/redis

Pull redis image:
```
docker pull redis
```

Verify image with below:
```
docker images
```
You should see an image name referencing redis in the output

Start the instance:
```
docker run --name some-redis -d redis
```
Then run docker ps to ensure the container is up and running.

From there we should be able to observe that the redis container is being automatically discovered by the Docker Agent host. Additionally, you'll see where the redis integration is automatically enabled, as Redis is one of our Autodiscovered integrations: https://docs.datadoghq.com/containers/guide/auto_conf/?tab=datadogoperator#pagetitle



### Next Steps For Further Enablement:

Docker Compose and The Agent: https://docs.datadoghq.com/containers/guide/compose-and-the-datadog-agent/

Docker Compose Deep Dive: https://drive.google.com/file/d/1PjldKLii-9Y-81FE3za2TBHYVQbpxTbg/view

Docker with APM: https://docs.datadoghq.com/containers/docker/apm/?tab=linux

Docker Swarm: https://github.com/john-reiner/datadog-swarm

Docker with DogStatsD: https://datadoghq.atlassian.net/wiki/x/L4LOt

Docker Glossary: https://docs.docker.com/glossary/


### Quick Troubleshooting Resources

Front-facing doc: https://docs.datadoghq.com/containers/troubleshooting/

#### Docker inspect:
```
docker inspect <container_id>
```
Docker inspect provides detailed information on constructs controlled by Docker. By default, docker inspect will render results in a JSON array.

#### Gather Agent Flare
```
docker exec -it dd-agent agent flare
```

#### If you encounter a Docker Agent ticket over chat (these would likely be first steps for tickets you inherit as well):
For Docker Agent configs, collect the docker run ... , docker-compose file, DockerFile, or details for whatever other tool used to start the Agent container. A docker inspect <Agent Container> would also be helpful.

For Agent diagnostics, we'll need an Agent Flare from the instance in question. In Docker, ECS/EC2, and Kubernetes - It's the responsibility of the Agent pod/container on the same node as the monitored container, task, pod to discover that entity and monitor it. So you may ask “Can you please send us a flare from the Agent container on the same host as the (EXAMPLE) container you want monitored”. In general gathering this information with debug level logging is going to be the most helpful. This will depend of course on the problem presented, but if possible: re-deploying the Agent with debug logging enabled, letting the Agent encounter the problem, and then sending the flare, will be the most helpful. This may require taking it offline if it takes ~20+ minutes for the problem to occur.

Full reference here: https://datadoghq.atlassian.net/wiki/spaces/TS/pages/328439533/Zendesk+Chat+Information+Collecting+Questions+to+ask#Containers








