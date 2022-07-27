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

oc label namespace spring-petclinic argocd.argoproj.io/managed-by=openshift-gitops

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

# get argocd admin password
oc get secret myargocd-cluster -n myargocd -ojsonpath='{.data.admin\.password}' | base64 -d
```

Run below clean up scripts firsly.

Deploy app with addtitional argocd instance:
```bash
# use myargocd project
oc project myargocd

# create cluster-configs application
oc create -f argo/cluster.yaml

# verify
oc get application -n myargocd
oc describe ns spring-petclinic

# create app-spring-petclinic application
oc create -f argo/app.yaml

oc label namespace spring-petclinic argocd.argoproj.io/managed-by=myargocd

# verify
oc get application -n myargocd
oc get pods -n spring-petclinic

# access the application in browser
# tips: http not https
oc get route -n spring-petclinic
```



## Clean up
```bash
oc delete application cluster-configs -n openshift-gitops
oc delete application app-spring-petclinic -n openshift-gitops
oc delete project spring-petclinic

oc delete application cluster-configs -n myargocd
oc delete application app-spring-petclinic -n myargocd
oc delete project spring-petclinic
```
