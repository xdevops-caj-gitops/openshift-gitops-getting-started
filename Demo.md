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

## Deploy App with additional ArgoCD (有问题)

> Error:
> Cluster level Namespace "spring-petclinic" can not be managed when in namespaced mode

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

## Deploy App with additional ArgoCD （有问题）

> 问题
> 用Login with OpenShift方式在ArgoCD界面上看不到应用

> Error:
> Cluster level Namespace "spring-petclinic" can not be managed when in namespaced mode

创建额外的ArgoCD实例：
1. 创建`myarogcd`项目
2. 在`myarogcd`项目，打开Installed Operators，打开OpenShift GitOps operator
3. 打开ArgoCD，创建ArgoCD，取名为`myargocd`
4. 在Networking / route中打开新的ArgoCD的地址
5. Login with OpenShift

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
