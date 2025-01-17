# Installing Kibana on K8s

## Deploying Kibana on Kubernetes for Log Visualization
In a modern world where applications generate, logs hold a treasure trove of insight. Visualizing and analyzing these logs can be like navigating through a dense fog without a compass.

This is where Kibana, a powerful open-source visualization tool, comes into play.


So, let’s understand Kibana

Imagine Kibana as the lens that brings the details of your data into focus, allowing you to navigate through complexities with ease.

It connects to Elasticsearch, where your logs are stored, and let’s you create visualizations such as charts and graphs.
These visualization are then organized into dashboards.

# 🚀 Deploying Kibana
Deploying Kibana involves creating Deployment and a Service in Kubernetes. The Deployment ensures Kibana is running and manages replicas, while the Service makes Kibana accessible.






## Creating the Kibana Deployment
Configuration File: Start by creating a configuration file for the Kibana Deployment.



This YAML file defines a Deployment named kibana  with a single replica. It uses the official kibana Image and connects to Elasticsearch via the ELASTICSEARCH_HOSTS environment variables.

Kibana uses port 5601 by default and communicates with Elasticsearch using an HTTP REST API.

To secure kibana, the security features of Elasticsearch should be enabled.

2. Deploying Kibana: 
kubectl apply -f kibana-deployment.yaml





Exposing Kibana Through a Service
After deploying kibana, you need to make it accessible. This is done by creating a Kubernetes Service.

Service Configuration File: Create a file named kibana-service.yaml
	

This service exposes Kibana on a NodePort, making it accessible from outside the cluster.

Creating the Service: Apply the service configuration:
kubectl apply -f kibana-service.yaml








# ✨ Accessing Kibana
With Kibana deployed and exposes, we can new access its interface using the IP of any node in our cluster and the node port specified in the service configuration http://<node-ip>:30001

## To explore the raw logs shipped by Fluentd.
We can use the Kibana Query Language (KQL) to search your data.However, you will first be required to specify an index pattern in order to select the data that has to be explored.

After having explored the logs, we can create dashboards to aggregate our data from various search operations. We can also import/export dashboards(Kibana dashboard are in the .ndjson format)

# 🎉 Conclusion
Congratulations! You've successfully deployed Kibana on Kubernetes and exposed it for access. This setup allows you to visualize and analyze your logs stored in Elasticsearch, turning raw data into actionable insights. As you explore Kibana's capabilities, consider diving deeper into creating custom visualizations and dashboards to tailor the insights to your specific needs.
Remember, this guide is just the beginning. The world of Kubernetes, Elasticsearch, and Kibana offers vast possibilities for managing and understanding your application logs. Keep experimenting and learning to make the most out of your data.

