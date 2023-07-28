## install the latest version 
https://github.com/open-policy-agent/gatekeeper

kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/release-3.11/deploy/gatekeeper.yaml

kubectl apply -f gatekeeper/example/templates/k8srequiredlabels_template.yaml

kubectl get constrainttemplatepodstatuses.status.gatekeeper.sh -A
