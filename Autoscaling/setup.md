kubectl apply -f https://raw.githubusercontent.com/pythianarora/total-practice/master/sample-kubernetes-code/metrics-server.yaml

kubect  top pod

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: registry.k8s.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
            memory: 30Mi
          requests:
            cpu: 200m
            memory: 10Mi
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache

run load generator for test resource php pod
---
apiVersion: apps/v1
kind: Deployment
metadata:
    name: load-generator
spec:
    selector:
        matchLabels:
            run: load-generator
    replicas: 2
    template:
        metadata:
            labels:
                run: load-generator
        spec:
            containers:
            - name: load-generator
              image: busybox
              args: [/bin/sh, -c, 'while true; do wget -q -0- http://php-apache; done']
---                  /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"

---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
---

kubectl get hpa
