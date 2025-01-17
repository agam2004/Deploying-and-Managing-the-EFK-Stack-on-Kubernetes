# 📜Monitoring and alerting for the EFK Stack on Kubernetes

# 🔬 Introduction to the EFK Stack
The EFK stack is a popular logging solution for Kubernetes, consisting of Elasticsearch for storage, Fluentd for log aggregation, and Kibana for visualization. It's essential for debugging applications and monitoring cluster health.

## Why Monitor the EFK Stack?
Monitoring the EFK stack helps ensure that your logging system is performing optimally. It allows you to detect issues early, such as Elasticsearch running out of storage or Fluentd experiencing high latency.


# 🧷 Integrating Prometheus with EFK
Prometheus is an open source monitoring solution for collecting and storing metrics. Integrating prometheus allows you to monitoring and collect metrics of each component apartly, and health of each component.


## Setting Up Prometheus
First, install Prometheus in our kubernetes cluster. We can use the Prometheus Operator for easier management.


Configuring Prometheus to Monitor EFK Components

Elasticseach metrics: use the elasticsearch-exporter to expose Elasticsearch metrics to Prometheus.

   apiVersion: monitoring.coreos.com/v1
   kind: ServiceMonitor
   metadata:
     name: elasticsearch-exporter
     labels:
       team: backend
   spec:
     selector:
       matchLabels:
         app: elasticsearch-exporter
     endpoints:
     - port: http


Fluentd Metrics: Fluentd metrics can be exposed using the built-in Prometheus plugin.

Kibana Metrics: Kibana does not natively export Prometheus metrics, but you can use the kibana-prometheus-exporter plugin.



# 📽 Visualizing Metrics with Grafana
Grafana is a powerful tool for visualizing metrics. One Prometheus is collecting metric from the EFK stack, you can use Grafana to create dashboards.

Install Grafana:

kubectl create -f grafana.yaml


where the yaml file would contain the required components for deployment:

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: grafana-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: grafana
  name: grafana
spec:
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      securityContext:
        fsGroup: 472
        supplementalGroups:
          - 0
      containers:
        - name: grafana
          image: grafana/grafana:latest
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 3000
              name: http-grafana
              protocol: TCP
          readinessProbe:
            failureThreshold: 3
            httpGet:
              path: /robots.txt
              port: 3000
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 30
            successThreshold: 1
            timeoutSeconds: 2
          livenessProbe:
            failureThreshold: 3
            initialDelaySeconds: 30
            periodSeconds: 10
            successThreshold: 1
            tcpSocket:
              port: 3000
            timeoutSeconds: 1
          resources:
            requests:
              cpu: 250m
              memory: 750Mi
          volumeMounts:
            - mountPath: /var/lib/grafana
              name: grafana-pv
      volumes:
        - name: grafana-pv
          persistentVolumeClaim:
            claimName: grafana-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: grafana
spec:
  ports:
    - port: 3000
      protocol: TCP
      targetPort: http-grafana
  selector:
    app: grafana
  sessionAffinity: None
  type: LoadBalancer


Configure Grafana DataSource:
Add Prometheus as a DataSource in Grafana to start creating dashboards for your EFK metrics.



# 🕬 Configuring Alerting
Prometheus Alertmanager can manage alerts sent by Prometheus. Define alert rules based on EFK metrics.
Alerting with Prometheus Alertmanager

For example, create an alert if Elasticsearch's heap usage is too high.
   groups:
   - name: elasticsearch-alerts
     rules:
     - alert: ElasticsearchHighHeapUsage
       expr: elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes > 0.9
       for: 5m
       labels:
         severity: critical
       annotations:
         summary: "High Heap Usage (instance {{ $labels.instance }})"
         description: "Elasticsearch node {{ $labels.node }} heap usage is above 90%."


2. Configure Alertmanager:
Set up Alertmanager to send notifications through email, Slack, or other channels when alerts are triggered.

   global:
     resolve_timeout: 5m
   route:
     receiver: 'slack-notifications'
   receivers:
   - name: 'slack-notifications'
     slack_configs:
     - channel: '#alerts'

# 🎉 Conclusion
Congratulations! You've successfully deployed multiple tools to monitor the EFK stack on Kubernetes.
Monitoring and alerting are essential components of maintaining a healthy EFK stack on Kubernetes. By integrating Prometheus and Grafana, you gain visibility into the performance and health of Elasticsearch, Fluentd, and Kibana.
Configuring alerting with Prometheus Alertmanager ensures you are promptly notified of potential issues, allowing for proactive management of your logging infrastructure.



