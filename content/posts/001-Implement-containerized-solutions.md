---
title: "Implement containerized solutions"
date: 2023-07-19T22:59:43+02:00
draft: false
---
# Implement containerized solutions

## Create and manage container images for solutions

ACR = Azure Container Registry

- private Docker registry service
- based on Docker Registry
- to store Docker images

Azure Container Registry tasks - to build containers in Az
    - build on demand
    - triggers (e.g. code commits, base image updates)

### Use cases

- Orchestrators: e.g. Kubernetes, Docker swarm
- Az services: e.g. AKS, App Service, Batch, Service Fabric, etc.
- CI/CD pipelines e.g. Azure pipelines, Jenkins

### ACR service tiers

- Basic - for learning.
- Standard - more storage, image throughput, for most prod scenarios.
- Premium - geo-replication, private link - restricted access, for high volume scenarios.

### Storage capabilities

- Encryption-at-rest - decrypted on the fly when pulled.
- Regional storage - stored in one region by default.
- Zone redundancy - only in Premium - uses availability zones to copy registry to min. 3 zones in each enabled region.
- Scalable storage - different limits in each tier.

### Build & manage containers with tasks

ACR task scenarios:
- Quick task: build & push container image to registry on demand. Like `docker build`, `docker run` in the cloud.

    - `docker build` -- `az acr build`

- Auto triggered tasks

    - source code update,
    - base image update,
    - on schedule

    `az acr task create` - specify Git repo, branch & Dockerfile

- Multi-step task - build-and-push for multi containers.
    - defined in YAML

task has a context - files or git repo

### Dockerfile

- instructions to build Docker image
    - base image
    - update base OS & install software
    - build artifacts
    - services to expose
    - run commands

```
az acr create --resource-group az204-acr-rg \
    --name <myContainerRegistry> --sku Basic
```

```
az acr repository list --name <myContainerRegistry> --output table
```

```
az acr run --registry <myContainerRegistry> \
    --cmd '$Registry/sample/hello-world:v1' /dev/null
```

## Publish an image to Azure Container Registry



## Run containers by using Azure Container Instance

ACI
- isolated containers
    - simple apps
    - task automation
    - build jobs

Benefits:
    - fast startup
    - expose FQDN or IP
    - hypervisor-level security - isolation like in VM
    - custom size of CPU & memory
    - persistent storage - use Azure Files

### Container groups

Containers scheduled on the same host machine

Share:
- lifecycle
- resources
- local network
- storage volumes. 

Like a pod in Kubernetes.

Multi container groups - only for Linux

### Deployement

- ARM template - recommended if need to export additional Az resources (e.g. Az Files)
- YAML - recommended if only ACI

### Resource allocation

In resource group, each instance requests resource (like CPU).

### Networking

Container groups share IP address & port namespace.

### Storage

Mount volume within container group, then map to specific path in each container.

Supported volumes:
- Azure file share
- Secret
- Empty directory
- Cloned git repo

### Restart policies

- Always - default
- Never - run at most once
- OnFailure - process <> 0

ACI starts the container, and then stops it when its application, or script, exits.
When Azure Container Instances stops a container whose restart policy is `Never` or `OnFailure`, the container's status is set to `Terminated`.

### Environment variables

In YML:

```yml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### Mount Azure File Share

https://learn.microsoft.com/en-us/training/modules/create-run-container-images-azure-container-instances/6-mount-azure-file-share-azure-container-instances

## Create solutions by using Azure Container Apps

on top of AKS, for:
- api endpoints
- background processing
- event-driven processing
- microservices

can dynamically scale based on:

- HTTP traffic
- event-driven processing
- CPU/memory load
- Any KEDA supported scaler

you can:

- run container revisions
- autoscale
- HTTPs ingress
- split traffic across versions
- internal ingress & service discovery
- microservices with Dapr
- use existing VNET

### Azure Container Apps environments

Environment:
 - same log analytics workspace
 - same vnet
 - boundary around group of container apps

 Reasons to deploy to single environment:
 - manage related services
 - different apps use same vnet
 - use Dapr
 - app share Dapr config
 - share log analytics workspace

 Reasons to have different environments
 - not to share same compute resources
 - Dapr apps cannot comunicate via Dapr service invocation API

### Microservices with Azure Container Apps

- scaling, versioning, upgrades
- service discovery
- Dapr integration

### Dapr integration

failures, retries, timeouts
Dapr has observability, pub/sub, service-to-service invocation, retries, etc.

## Explore containers in Azure Container Apps

Environment -> Container App -> Revision -> Replica -> Container(s)

Any Linux-based container image

### Config

ARM template

### Multiple containers

in one Container app
sidecar pattern

### Container registries

Use private container registry

## Implement authentication and authorization in Azure Container Apps

- only use it with https
- `allowInsecure` should be disabled in container app's ingress config

`Restrict Access` setting -> `Require authentication` or `Allow unauthenticated`

### Identity providers

MS, fb, GitHub, Google, Twitter, any OpenID Connect provider

middleware runs as a sidecar container on each replica of app.
each incoming HTTP request passes through the layer before reaching app.

## Manage revisions & secrets in container apps

- revisions are used for app versioning
- revision name, revision suffix
- `az containerapp create`, `az containerapp update`
- revision-scope changes: https://learn.microsoft.com/en-us/azure/container-apps/revisions#revision-scope-changes
- `az containerapp revision list`

Secrets 

- app scope (ouside of revision)
- changing secrets does not generate new revisions

when you delete a secret:
- deploy a new revision that does not use this secret or
- restart existing revision

Container app does not support Azure Key Vault but
you can enable managed identity to use Key Vault SDK.

```
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING"
```

```
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --env-vars "QueueName=myqueue" "ConnectionString=secretref:queue-connection-string"
```

## Dapr

Distributed Application Runtime
- open source
- Cloud Native Computing Foundation

- container app wraps and simplifies Dapr usage

Dapr APIs

- Service to service integration
- State mgt
- pub/sub
- bindings
- actors
- observability
- secrets

- Dapr sidecar
- HTTP or gRPC

- CLI
- Bicep or ARM
- Azure portal

