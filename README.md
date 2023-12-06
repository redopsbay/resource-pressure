# Resource Pressure #

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)


Tool to create a CPU and Memory pressure to test the scaling capabilities of your cluster.

| Prerequisite                                   |
| ---------------------------------------------- |
| 1. AWS Autoscaling Group                       |
| 2. AWS EKS Cluster or other kubernetes cluster |



Build 🏗️
=========

```
docker build -t <target-repo> .
```


Running on Docker 🏃
====================
```
docker run -d --rm --name resource-pressure --hostname resource-pressure \
        --env NUM_THREADS="500" <docker-image>:<docker-tag>
```


Running on Kubernetes 🚢
======================
```
---
# Horizontal Pod Autoscaling
kind: HorizontalPodAutoscaler
apiVersion: autoscaling/v2beta2
metadata:
  name: resource-pressure-hpa
  namespace: default
spec:
  minReplicas: 1
  maxReplicas: 200
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: resource-pressure
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80%
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80%
---
# Sample Deployment #
apiVersion: apps/v1
kind: Deployment
metadata:
  name: resource-pressure
spec:
  replicas: 2
  selector:
    matchLabels:
      app: resource-pressure
      version: v1
  template:
    metadata:
      labels:
        app: resourcepressure
        version: v1
    spec:
      containers:
      - image: redopsbay/resource-pressure:v1
        imagePullPolicy: IfNotPresent
        name: resource-pressure
        env:
        - name: NUM_THREADS
          value: "150"
```
