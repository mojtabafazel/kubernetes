helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update



helm install prometheus prometheus-community/prometheus --create-namespace --namespace prometheus


Prometheus web interface ingress file:
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: prometheus
  namespace: prometheus
spec:
  rules:
  - host: metrics.monlog.ir
    http:
      paths:
      - backend:
          serviceName: prometheus-server
          servicePort: 80
        path: /

for add prometheus to grafana:  in url to grafana insert like 
prometheus-service.prometheus:80
