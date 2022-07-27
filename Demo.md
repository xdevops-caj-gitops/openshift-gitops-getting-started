# GitOps Demo

## Deploy App with default ArgoCD

```bash
# use openshift-gitops project
oc project openshift-gitops

# create cluster-configs application
oc create -f argo/cluster.yaml

# verify
oc get application -n openshift-gitops
oc describe ns spring-petclinic

# create app-spring-petclinic application
oc create -f argo/app.yaml

# verify
oc get application -n openshift-gitops
oc get pods -n spring-petclinic

# access the application in browser
# tips: http not https
oc get route -n spring-petclinic
```

## Deploy App with additional ArgoCD

Create additional ArgoCD instance:
```bash
oc new-project myargocd
oc create -f argo/argocd.yaml
```

Deploy app with addtitional argocd instance:
```bash

```



## Clean up
```bash
oc delete application cluster-configs -n openshift-gitops
oc delete application app-spring-petclinic -n openshift-gitops
oc delete project spring-petclinic
```
