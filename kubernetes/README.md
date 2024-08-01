# Kubernetes Configuration Overview

This document provides an overview of the Kubernetes configuration for the SynergyChat application, including ConfigMaps, Deployments, Services, PersistentVolumeClaims (PVC), Horizontal Pod Autoscalers (HPA), and Ingress.

## Directory Structure

```
├── configmaps
│   ├── api-configmap.yaml           <--|
│   ├── crawler-configmap.yaml       <--|
│   ├── testram-configmap.yaml       <--| Used by Deployments for environment variables/configurations
│   └── web-configmap.yaml           <--|
├── deployments
│   ├── api-deployment.yaml          --> Manages Pods for API, uses api-configmap, connected to api-service
│   ├── crawler-deployment.yaml      --> Manages Pods for Crawler, uses crawler-configmap, connected to crawler-service
│   ├── testcpu-deployment.yaml      --> Manages Pods for TestCPU, potentially connected to an HPA
│   ├── testram-deployment.yaml      --> Manages Pods for TestRAM, uses testram-configmap
│   └── web-deployment.yaml          --> Manages Pods for Web, uses web-configmap, connected to web-service
├── hpa
│   ├── testcpu-hpa.yaml             --> Scales testcpu-deployment based on CPU usage
│   └── web-hpa.yaml                 --> Scales web-deployment based on CPU usage
├── ingress
│   └── app-ingress.yaml             --> Manages external access to api-service, crawler-service, and web-service
├── pvc
│   └── api-pvc.yaml                 --> Provides persistent storage for api-deployment
└── services
    ├── api-service.yaml             --> Provides a stable endpoint for API Pods
    ├── crawler-service.yaml         --> Provides a stable endpoint for Crawler Pods
    └── web-service.yaml             --> Provides a stable endpoint for Web Pods
```

## Diagram

### Visual Diagram (Text-Based)

```
              +-------------------+
              |      Clients      |
              +-------------------+
                         |
              +-------------------+                      
              |  Load Balancer    |                      
              +-------------------+                      
                         |                                         
+------------------------------------------------------+
|                                                      |
|                   Kubernetes Cluster                 |
|                                                      |
|           +-------------------+                      |
|           |     Ingress       |                      |
|           +-------------------+                      |
|                   |                                  |
|       +-----------+------------------+               |
|       |           |                  |               |
| +-----+-----+ +---+-----------+ +---+------------+   |
| | web-service | | api-service | | crawler-service |  |
| +-----+-----+ +------+--------+ +------+-----+       |
|       |             |             |                  |
|       |             |             |                  |
| +-----+-----+ +-----+-----+ +------+-----+           |
| |web-deployment| |api-deployment| |crawler-deployment|
| +------+-----+ +------+-----+ +------+-----+         |
|       |             |             |                  |
|       |             |             |                  |
| +-----+-----+ +-----+-----+ +-----+-----+            |
| |web-configmap| |api-configmap| |crawler-configmap|  |
| +-----+-----+ +------+-----+ +------+-----+          |
|                   |                                  |
|             +-----+-------+                          |
|             | api-pvc     |                          |
|             +-------------+                          |
|                                                      |
+------------------------------------------------------+

```

## Steps to Deploy

Steps to Apply These Configurations

### 1. Apply ConfigMaps
```kubectl apply -f kubernetes/configmaps/api-configmap.yaml
kubectl apply -f kubernetes/configmaps/crawler-configmap.yaml
kubectl apply -f kubernetes/configmaps/testram-configmap.yaml
kubectl apply -f kubernetes/configmaps/web-configmap.yaml
```

### 2. Apply PersistentVolumeClaims (PVC)

```
kubectl apply -f kubernetes/pvc/api-pvc.yaml
```

### 3. Apply Deployments
```
kubectl apply -f kubernetes/deployments/api-deployment.yaml
kubectl apply -f kubernetes/deployments/crawler-deployment.yaml
kubectl apply -f kubernetes/deployments/testcpu-deployment.yaml
kubectl apply -f kubernetes/deployments/testram-deployment.yaml
kubectl apply -f kubernetes/deployments/web-deployment.yaml
```

### 4. Apply Services
```
kubectl apply -f kubernetes/services/api-service.yaml
kubectl apply -f kubernetes/services/crawler-service.yaml
kubectl apply -f kubernetes/services/web-service.yaml
```

### 5. Apply Horizontal Pod Autoscalers (HPA)
```
kubectl apply -f kubernetes/hpa/testcpu-hpa.yaml
kubectl apply -f kubernetes/hpa/web-hpa.yaml
```

### 6. Apply Ingress

```
kubectl apply -f kubernetes/ingress/app-ingress.yaml
```

## Summary
By following this structure and applying the configurations in the specified order, you ensure that all components are correctly set up and interconnected. This helps maintain clarity and manageability within your Kubernetes environment.
