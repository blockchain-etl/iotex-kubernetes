# IoTeX Node in Kubernetes

[IoTeX](https://iotex.io/) is a decentralized cryptosystem, a new generation of blockchain platform for the 
development of the Internet of things (IoT). The project team is sure that the users do not have such an 
application that would motivate to implement the technology of the Internet of things in life. 
And while this will not be created, people will not have the desire to spend money and time on IoT. 
The developers of IoTeX decided to implement not the application itself, but the platform for creation. 
It is through the platform that innovative steps in the space of the Internet of things will be encouraged.

## Introduction

This chart bootstraps a single IoTeX node deployment on a [Kubernetes](http://kubernetes.io) cluster using 
the [Helm](https://helm.sh) package manager.
Docker image was taken from [IoTeX on Docker hub](https://hub.docker.com/r/iotex/iotex-core)

## Prerequisites

- Kubernetes 1.8+
- Helm 2.16
- PV provisioner support in the underlying infrastructure
- [gcloud](https://cloud.google.com/sdk/install)

## Creating GKE Kubernetes Cluster

```bash
CLUSTER_INDEX=0
CLUSTER_NAME=iotex-node-${CLUSTER_INDEX} && echo "Cluster name is ${CLUSTER_NAME}"
MASTER_ZONE=us-central1-a

gcloud container clusters create $CLUSTER_NAME \
    --num-nodes 1 \
    --enable-autoscaling --max-nodes=1 --min-nodes=1 \
    --machine-type=n1-standard-2 \
    --cluster-version latest \
    --enable-autorepair \
    --enable-ip-alias \
    --zone=$MASTER_ZONE

gcloud container clusters get-credentials $CLUSTER_NAME --zone=$MASTER_ZONE

kubectl create -f storageclass-ssd.yaml 
helm init
bash patch-tiller.sh
```

## Installing the Chart

To install the chart with the release name `iotex`:

```bash
$ helm install --name iotex charts/iotex
```

The command deploys IoTeX on the Kubernetes cluster in the default configuration.
The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `iotex` deployment:

```bash
$ helm delete iotex --purge
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the iotex chart and their default values.

Parameter                       | Description                                       | Default
------------------------------- | ------------------------------------------------- | ----------------------------------------------------------
`image.repository`              | Image source repository name                      | `iotex/iotex-core`
`image.tag`                     | `iotex` release tag.                              | `v1.0.0`
`image.pullPolicy`              | Image pull policy                                 | `IfNotPresent`
`service.rpcPort`               | RPC port                                          | `14014`
`service.p2pPort`               | P2P port                                          | `4689`
`persistence.enabled`           | Create a volume to store data                     | `true`
`persistence.accessMode`        | ReadWriteOnce or ReadOnly                         | `ReadWriteOnce`
`persistence.size`              | Size of persistent volume claim                   | `100Gi`
`resources`                     | CPU/Memory resource requests/limits               | `{}`
`terminationGracePeriodSeconds` | Wait time before forcefully terminating container | `30`


Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name iote -f values.yaml charts/iotex
```

> **Tip**: You can use the default [values.yaml](iotex/values.yaml)

Check a [separate file](ops.md) for more details about additional troubleshooting.