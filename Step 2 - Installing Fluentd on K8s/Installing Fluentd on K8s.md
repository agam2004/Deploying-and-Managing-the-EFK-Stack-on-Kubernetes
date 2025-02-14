﻿Deploying Fluentd on Kubernetes as a DaemonSet




Introduction to Fluentd and Kubernetes


What is Fluentd?
Fluentd is an open-source data collector designed for processing logs and events. It’s highly flexible, allowing you to collect data from multiple sources, transform it as needed, and send it to various destinations.


Why Kubernetes?
Kubernetes is a container orchestration platform that automates the deployment, scaling and management of containerized applications. By deploying Fluentd on Kubernetes, we can harness scalability and management features for efficient log collection.


🚀 Preparing Your Kubernetes Cluster
Ensure your kubernetes cluster is up and running by executing the following command:


kubectl get nodes






🌿 Deploying Fluentd as a DaemonSet
A DaemonSet ensures that a copy of the pod run on each node in the cluster.  This is perfect for log collection, as we want Fluentd to collect logs from every node.


Creating the Fluentd DaemonSet
create a Fluentd Configuration File
First, we’ll need to create a Fluentd configuration file, that specifies how logs should be collected and forwarded.
<source>
  @type forward
  port 24224
  bind 0.0.0.0
</source>
<match **>
  @type elasticsearch
  host elasticsearch.default.svc.cluster.local
  port 9200
  logstash_format true
</match>


Here, source and match are directives. Following is a list of all directives and the respective aspects of logging they relate to:
* source: Input sources.
* match: Output destinations.
* filter: Event processing pipelines.
* system: System-wide configuration.
* label: Group the output and filter for internal routing.
* worker: Limit to the specific workers.
* @include: Include other files.
Apart from directives, fluentd also uses various input and output plugins.
Now, let’s define the DaemonSet
Next, create a Kubernetes DaemonSet definition (say fluentd-daemonset.yaml). This file describes the Fluentd pod that will be deployed to each node.  


















Deploy the DaemonSet
Apply the DaemonSet definition to your cluster.
kubectl apply -f fluentd-daemonset.yaml


⚙️ Configuring Fluentd for Kubernetes Logs
Fluentd needs to be configured to collect logs from both Kubernetes nodes and pods. The configuration file we defined earlier in fluentd-config.conf sets Fluentd to listen for logs and forward them to Elasticsearch.


Collecting Node Logs
The DaemonSet configuration mounts the /var/log directory from the host into the Fluentd container, allowing Fluentd to access and collect system and application logs from the nodes.


Collecting Pod Logs
Similarly, mounting /var/lib/docker/container enables Fluentd to collect logs from Docker containers, including those managed by Kubernetes pods.






Forwarding Logs to Elasticsearch
The Fluentd configuration specifies Elasticsearch as the destination for logs. Ensure that Elasticsearch is running and accessible from within your Kubernetes cluster.
<match **>
  @type elasticsearch
  host elasticsearch.default.svc.cluster.local
  port 9200
  logstash_format true
</match>


The above configuration forwards all collected logs to Elasticsearch, where they can be indexed and made searchable.


This has been made possible because of the ue of output plugin fluent-plugin-elasticsearch which is used to forward data to Elasticsearch, The match section displays the parameters it uses.






🎉 Conclusion
Deploying Fluentd as a DaemonSet on Kubernetes allows for efficient log collection across all nodes and pods.
By forwarding these logs to Elasticsearch, you gain a powerful tool for log analysis and monitoring. This setup is crucial for maintaining insight into the health and performance of your Kubernetes applications.
