https://github.com/vmware-tanzu/velero/releases/tag/v1.10.3

download the latest vesion and ...
tar xzf velero...
ls velero-tansu
mv velero-tanzu-1.10-3../velero  /usr/local/bin

after this command you can use velero cli

install minio from this location:
/home/mojtaba/kubernetes/velero/examples/minio/00-minio-deployment.yaml

kubect  apply -f 00-minio-deployment.yaml
tips: minio password in to the manifest

by default minio in not web page . for view to web create service for web port:

kubectl -n velero logs -f minio-796cc55795-pqnz8 

WARNING: MINIO_ACCESS_KEY and MINIO_SECRET_KEY are deprecated.
         Please use MINIO_ROOT_USER and MINIO_ROOT_PASSWORD
MinIO Object Storage Server
Copyright: 2015-2023 MinIO, Inc.
License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
Version: RELEASE.2023-06-23T20-26-00Z (go1.19.10 linux/amd64)

Status:         1 Online, 0 Offline. 
S3-API: http://10.244.0.4:9000  http://127.0.0.1:9000     
Console: http://10.244.0.4:37129 http://127.0.0.1:37129   
------
apiVersion: v1
kind: Service
metadata:
  namespace: velero
  name: minio-web
  labels:
    component: minio
spec:
  type: ClusterIP
  ports:
    - port: 37129
      targetPort: 37129
      protocol: TCP
  selector:
    component: minio
------
and write ingress manifest:

-----
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minio-web
  namespace: velero
spec:
  ingressClassName: nginx
  rules:
    - host: minio.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: minio-web
                port:
                  number: 37129
-----


https://github.com/vmware-tanzu/velero/blob/main/site/content/docs/main/contributions/minio.md

1-Create a Velero-specific credentials file (credentials-velero) in your Velero directory:

[default]
aws_access_key_id = minio
aws_secret_access_key = minio123

2- Start the server and the local storage service. In the Velero directory, run:

kubectl apply -f examples/minio/00-minio-deployment.yaml


velero install \
    --provider aws \
    --plugins velero/velero-plugin-for-aws:v1.2.1 \
    --bucket velero \ #bucket name create in minio
    --secret-file ./credentials-velero \
    --use-volume-snapshots=false \
    --backup-location-config region=minio,s3ForcePathStyle="true",s3Url=http://minio.velero.svc.cluster.local:9000
 
tip: for fecth right url exec to minio
kubectl -n velero exec -it minio-796cc55795-pqnz8 -- sh
cat /etc/resolf.conf

