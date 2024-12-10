# Jenkins StatefulSet Deployment on Kubernetes

This repository contains the configuration files to deploy a Jenkins master as a Kubernetes StatefulSet with an integrated Horizontal Pod Autoscaler (HPA).

## Overview

This configuration deploys a Jenkins master node using the following Kubernetes resources:

- **StatefulSet**: Ensures consistent pod identity and persistent storage for Jenkins data.
- **Horizontal Pod Autoscaler (HPA)**: Monitors CPU utilization and adjusts the number of replicas accordingly.

## Prerequisites

- A Kubernetes cluster with `kubectl` configured.
- A PersistentVolumeClaim (PVC) named `jenkins-home` to store Jenkins data.
- A service account named `jenkins-service-account` with the necessary permissions.

## Deployment Details

### StatefulSet Configuration

- **Name**: `jenkins-standalone-statefulset`
- **Service Name**: `jenkins-service`
- **Image**: `jenkins/jenkins:lts`
- **Replicas**: 1
- **Volume**: Persistent storage mounted at `/var/jenkins_home`.

#### Probes

- **Liveness Probe**: Ensures the Jenkins master pod is running.
- **Readiness Probe**: Ensures the pod is ready to serve traffic.

#### Resource Requests and Limits

- **Requests**: 512Mi memory, 500m CPU
- **Limits**: 1Gi memory, 1 CPU

#### Security Context

- **fsGroup**: 1000
- **runAsUser**: 1000

### Horizontal Pod Autoscaler Configuration

- **Name**: `jenkins-hpa`
- **Target Resource**: The Jenkins StatefulSet.

#### Scaling Parameters

- **Minimum replicas**: 1
- **Maximum replicas**: 1 (can be increased based on workload requirements).
- **Target CPU Utilization**: 80%

## Deployment Steps

### Apply the PersistentVolumeClaim (PVC) for Jenkins data

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jenkins-home
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi