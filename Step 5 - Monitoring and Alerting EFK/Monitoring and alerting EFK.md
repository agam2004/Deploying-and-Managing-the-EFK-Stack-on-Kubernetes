# Monitoring and Alerting EFK - Lab

## 1. Which component of the EFK stack is responsible for log aggregation?

Fluentd
Elasticseach
Kibana
Prometheus

## 2. Which of the following is a monitoring solution that allows for metrics collection and visualization when integrated with the EFK stack?

Prometheus
RabbitMQ
MySQL
MongoDB

## 3. What is the primary purpose of using Grafana with the EFK stack?

Metrics Visualization
Automated Scaling
Log Aggregation
Data Storage


## 4. Which component of the EFK stack does not natively export Prometheus metrics and requires a plugin?

Fluentd
Kibana
Prometheus
Elasticsearch





## 5. How does Prometheus Alertmanager notify teams about triggered alerts?

By updating a dashboard
By generating a PDF report
Through email, Slack, or other channels
Prometheus does not support alert notifications

## 6. What is the primary use case for Alertmanager in the context of monitoring with Prometheus?

Templating
Playlists
Snapshots
Annotations

## 7. What is the primary use case for Alertmanager in the context of monitoring with Prometheus?

Scrape intervals can be configured per job
They support only HTTP endpoints for metrics collection
They cannot be dynamically reloaded; the server must be restarted
They muster have the same scrape interval

## 8. Which type of data source does Grafana natively support for alerting?

Prometheus
Google sheets
All of the above
Elasticsearch

## 9. Which resource in Kubernetes is NOT typically monitored with Prometheus?

Secrets
Services
Deployments
Pods








## 10. Install and run the Elasticsearch exporter to scrape metrics from Elasticsearch and send them to Prometheus


## 11. Now that we have our Elasticsearch exporter running and exposing metrics, let's install prometheus to scrape these metrics.
From the Prometheus downloads page, download the prometheus binary and extract it.
Configure the prometheus.yml file and run prometheus so that it scrapes the metrics being exposed by the elasticsearch exporter at port 9114.


Download prometheus using the wget command:
wget https://github.com/prometheus/prometheus/releases/download/v2.51.1/prometheus-2.51.1.linux-amd64.tar.gz

Then extract the newly downloaded .tar.gz extension file:
tar xzf prometheus-2.51.1.linux-amd64.tar.gz -C ./

A folder titled prometheus-2.51.1.linux-amd64 will be visible under the root directory. Navigate within this folder and you will see few binaries and a prometheus.yml file.
Edit the prometheus.yml file and append the following to the scrape_configs section:
- job_name: "elasticsearch"
  static_configs:
  - targets: ["localhost:9114"]

Here, we have added a new job named elasticsearch to prometheus, to scrape metrics from port 9114 on our host.
Let's run the prometheus binary using our prometheus.yml file as the config:
nohup ./prometheus --config.file=prometheus.yml &

Ensure to run the above command in the background.
By default, prometheus runs at port 9090. Using the View Port utility on the top right of this page, access this port.
Once on the Prometheus query page, navigate to Status -> Targets. Here, you can see two jobs in the UP state:
prometheus at port 9090
elasticsearch at port 9114


## 12. Install Grafana on your Kubernetes cluster to visualize Elasticsearch metrics from Prometheus.
Use the official Grafana downloads page for the Linux platform: https://grafana.com/grafana/download?platform=linux
Extract this file and run the grafana-server binary in the background.

Default username and password for Grafana is admin. Use it to login for the first time, and then change the password to grafana_user123.
Default port for Grafana is 3000.


Solution:

Download Grafana using the wget command:
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-10.4.1.linux-amd64.tar.gz

Then extract and unzip the newly downloaded .tar.gz extension file:
tar xzf grafana-enterprise-10.4.1.linux-amd64.tar.gz -C ./

A folder titled grafana-v10.4.1 will be visible under the root directory. Navigate within this folder and inspect the bin directory. This directory contains the binary file grafana-server that we will be using to run our grafana instance.
Execute the following command from within the grafana-v10.4.1 folder:
nohup ./bin/grafana-server &

Grafana runs at port number 3000 by default. Navigate to this port using the View Port utility at the top right of this page.
Enter admin as both the username and password in the Grafana login page. Next, you will be asked to update the password. Update it to grafana_user123.
Open the menu on the left of the Grafana Welcome page under Home. Under the Connections submenu, click on Data sources -> Add data source and select Prometheus.
Under Connection, enter http://localhost:9090/ for Prometheus server URL. Scroll to the bottom of this page and click on Save and Test.




Define an alert rule in Prometheus for high Elasticsearch heap usage.
Name the alert as HighElasticsearchHeapUsage.
The alert should trigger if the heap usage is above 5% for more than 2 minutes, and its severity should be critical.
Place the alert rule file inside the extracted prometheus folder.

Refer to the Description page to view the syntax of an alert rules file.
Note: This low heap usage percentage is just for demonstration purpose. In an actual setting, you would want this to be atleast 90%.









Navigate to the prometheus-2.51.1.linux-amd64 folder and create a file named rules.yml with the following content:
groups:
- name: elasticsearch
  rules:
  - alert: HighElasticsearchHeapUsage
    expr: elasticsearch_jvm_memory_used_bytes{job="elasticsearch"} / elasticsearch_jvm_memory_max_bytes{job="elasticsearch"} > 0.05
    for: 2m
    labels:
      severity: critical
    annotations:
      summary: "High Elasticsearch heap usage (instance {{ $labels.instance }})"
      description: "Elasticsearch instance {{ $labels.instance }} has high heap usage ({{ $value }} bytes used) for more than 2 minutes."

Edit the prometheus.yml file and add the newly created rules file to it under the rule_files section:
rule_files:
   - "rules.yml"

Save and exit.
Identify the PID of the prometheus process:
ps aux | grep prometheus

and kill the process:
kill -9 <pid>

Re-start the process in the background so that it picks the updated file this time:
nohup ./prometheus --config.file=prometheus.yml &

In the Prometheus UI, navigate to the Alerts section. You will find your defined alert HighElasticsearchHeapUsage. This will remain in pending state for upto two minutes, and then go into the firing state.






