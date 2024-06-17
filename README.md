# ContainerLearningWeekDocker

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


### Deploying the Docker Agent

## Step 1: Installing the Docker Agent

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

## Step 2: Verify Agent Is Running Successfully

Confirm Agent container is running:
```
docker ps
```

Agent status:

```
sudo docker exec -it <CONTAINER_ID_OR_NAME> agent status
```

