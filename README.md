# ocp-gitops
This repository installs argocd
# Install the ArgoCD operator
oc apply -f https://raw.githubusercontent.com/bh1000/ocp-gitops/refs/heads/main/argocd-operator-install.yaml

# Deploy the ArgoCD instance

helm repo add bh1000-gitops https://bh1000.github.io/ocp-gitops/
helm repo update bh1000-gitops
helm upgrade --install argocd bh1000-gitops/argocd-config --namespace openshift-gitops \
    --set global.namespace=openshift-gitops \
    --set global.clusterName=argocd \
    --set argoRollout.enabled=true \
    --set server.disableAdmin=false \
    --set global.clusterDomain=$(oc get dns.config/cluster -o jsonpath='{.spec.baseDomain}')

# Bump a new chart
helm package argocd-config
helm repo index --url https://bh1000.github.io/helm-charts
helm repo index . --url https://bh1000.github.io/helm-charts

# Commit changes