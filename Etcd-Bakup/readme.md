kubectl get cronjobs.batch -A
kubectl create job --from=cronjob/<cronjob-name> <job-name>
kubectl get jobs.batch -A
