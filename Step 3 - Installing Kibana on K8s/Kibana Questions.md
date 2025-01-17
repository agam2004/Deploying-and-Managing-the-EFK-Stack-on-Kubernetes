Kibana Questions
What is the main purpose of kibana?
A: Data Visualization

How can we access kibana from outside the cluster?
A: With a service

What is the default port of kibana?
A: 5601

What language is used by kibana for querying? 
A: Kibana Query Language(KQL)

Which kibana feature allows you to interactively explore your data by querying and filtering?
A: Discover

Which file format is used by kibana dashboard exports and imports?
A: .ndjson

What is an index pattern for?
A: 
An Index Pattern defines which Elasticsearch data to explore and visualize in Kibana.
Creating an Index Pattern in Kibana means specifying a pattern that matches the names of the Elasticsearch indices.
Once an index is created, Kibana analyzes the logs within it and identifies fields and their types.


How does Kibana connect to Elasticsearch?
A: Using an HTTP REST API
How would you secure Kibana access in a production environment?
A: Enabling Elasticsearch Security features.


Create a deployment for kibana in the elastic-stack namespace.
Use the following specs:
name: kibana
label: kibana
replicas: 1
image: docker.elastic.co/kibana/kibana:7.1.0
containerPort: 5601

A: 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
  namespace: elastic-stack
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: docker.elastic.co/kibana/kibana:7.1.0
        ports:
        - containerPort: 5601

Now, let’s verify that the kibana pod is running successfully.
A: kubectl get pods.

Now, Expose kibana through  Kubernetes Service of type NodePort in the elastic-stack namespace.
Name the service as kibana

*Ensure Service type is set to NodePort and the default kibana port 5601 is mapped to nodePort 30601.

A: 
apiVersion: v1
kind: Service
metadata:
  name: kibana
  namespace: elastic-stack
spec:
  type: NodePort
  selector:
    app: kibana
  ports:
  - protocol: TCP
    port: 5601
    nodePort: 30601


To explore more of kibana, refer to the following documentations:
How to create dashboards: https://www.elastic.co/guide/en/kibana/current/dashboard.html
Kibana Query Language(KQL):
https://www.elastic.co/guide/en/kibana/current/kuery-query.html

🎉 Congradulaions: 
Now we finally deployed kibana to our K8s as part of the EFK stack.

