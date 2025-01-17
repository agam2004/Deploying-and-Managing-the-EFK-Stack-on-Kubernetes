# 📜 Deploying Elasticsearch on Kubernetes
Welcome to the second lab of our hands-on course, "Learn by Doing - EFK Stack"! 🎉 In this lab, we'll guide you through the process of deploying Elasticsearch within a Kubernetes cluster. We'll cover setting up a StatefulSet for Elasticsearch, configuring Persistent Volumes for data storage, and using Services for network access. Each section builds upon the last, ensuring a comprehensive understanding of deploying Elasticsearch in a Kubernetes environment. 📚

# 🤔 Understanding Kubernetes and Elasticsearch
Before diving into the deployment process, let's quickly recap the basics of Kubernetes and Elasticsearch.

## Kubernetes Overview
Kubernetes is an open-source platform designed to automate deploying, scaling, and operating application containers. It groups containers that make up an application into logical units for easy management and discovery.

## Elasticsearch Overview
Elasticsearch is a powerful open-source search and analytics engine designed for horizontal scalability, reliability, and real-time search. It's commonly used for log or event data analysis and full-text search.
Using Elasticsearch in conjunction with Kubernetes, allows it to reap benefits from Kubernetes' self-healing and scalability features.

# 🏰 Setting Up a StatefulSet for Elasticsearch
StatefulSets are ideal for stateful applications like Elasticsearch. They provide stable, unique network identifiers and persistent storage for each pod.

## Why Use StatefulSet?
Stable Network Identity: Each pod gets a unique and stable hostname.
Ordered, Graceful Deployment and Scaling: Pods are created and deleted in a predictable order.
Persistent Storage: Each pod can be associated with its storage, which persists across pod rescheduling.
## Creating a StatefulSet
Below is a basic example of a StatefulSet definition for Elasticsearch. Note that you should adjust values such as volumeClaimTemplates according to your environment and needs.
``` yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: elasticsearch
spec:
  serviceName: "elasticsearch"
  replicas: 3
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: docker.elastic.co/elasticsearch/elasticsearch:7.9.3
        ports:
        - containerPort: 9200
          name: es-http
        volumeMounts:
        - name: es-data
          mountPath: /usr/share/elasticsearch/data
  volumeClaimTemplates:
  - metadata:
      name: es-data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 10Gi
```
This YAML file defines a StatefulSet named elasticsearch with three replicas. It specifies the use of the Elasticsearch 7.9.3 Docker image and a persistent volume claim for data storage.

# 💾 Configuring Persistent Volumes for Data Storage
Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) are critical in managing storage in Kubernetes.

## Understanding PVs and PVCs
Persistent Volume (PV): Represents a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.
Persistent Volume Claim (PVC): A request for storage by a user. It specifies size, and access modes, and can be linked to specific PVs.
### Example: Persistent Volume Claim
The volumeClaimTemplates section in the StatefulSet YAML file automatically creates a PVC for each pod in the StatefulSet. Here's what happens behind the scenes:
``` yaml
volumeClaimTemplates:
- metadata:
    name: es-data
  spec:
    accessModes: [ "ReadWriteOnce" ]
    resources:
      requests:
        storage: 10Gi
```
This configuration requests a 10Gi volume with ReadWriteOnce access mode for each Elasticsearch pod. The Kubernetes cluster automatically provisions this storage if dynamic provisioning is set up or binds the PVC to a manually created PV.

# 🌐 Using Services for Network Access
Services in Kubernetes provide a way to expose an application running on a set of Pods as a network service.

## Why Services?
Stable IP Addresses: Services provide stable IP addresses for pods.
Load Balancing: Services can load-balance traffic to multiple pods.
Service Discovery: Services allow other applications to discover and communicate with your Elasticsearch cluster through a stable endpoint.
Creating a Service for Elasticsearch
Here's an example of a Service definition for Elasticsearch. This service exposes the Elasticsearch HTTP port (9200) so that other applications can communicate with the cluster.

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch
spec:
  selector:
    app: elasticsearch
  ports:
  - port: 9200
    targetPort: es-http
  type: ClusterIP
```
This YAML file creates a Service named elasticsearch, which targets pods with the label app: elasticsearch. It makes the Elasticsearch HTTP API accessible within the cluster through the cluster IP of the Service.

# 🎉 Conclusion
Deploying Elasticsearch on Kubernetes involves setting up a StatefulSet for stable pod identification and storage, configuring Persistent Volumes for data persistence, and using Services for stable network access. By following these steps, you can ensure a resilient and scalable Elasticsearch cluster within your Kubernetes environment. This guide aimed to provide a beginner-friendly introduction to deploying Elasticsearch on Kubernetes, covering the essential components and configurations.

# 🎓 What's Next?
In this lab, we will walk you through deploying Elasticsearch on Kubernetes, a powerful search and analytics engine, leveraging Kubernetes' features such as StatefulSets, Persistent Volumes (PVs), and Services for stability, data persistence, and network access.

Click on the Tasks tab to put your knowledge to the test and complete the hands-on exercises for this lab. Happy learning! 🚀
