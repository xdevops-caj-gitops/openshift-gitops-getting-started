# GitOps Demo

## Deploy App with default ArgoCD

```bash
# use openshift-gitops project
oc project openshift-gitops

# create cluster-configs application
oc create -f argo/cluster.yaml -n openshift-gitops

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

解决Login with OpenShift后在ArgoCD上没有权限操作的问题：
1. 创建`cluster-admin-group`组，将指定用户添加到该组
2. 将`cluster-admin`权限授权给`cluster-admin-group`组
```bash
oc adm policy add-cluster-role-to-group cluster-admin cluster-admin-group
```
3. 在`openshift-gitops`项目，打开Installed Operators，打开OpenShift GitOps operator
4. 打开ArgoCD，打开`openshift-gitops`, 编辑YAML，在`rbac.policy`下添加：
```yaml
g, cluster-admin-group, role:admin
```
5. 在ArgoCD上Log out再重新登录。


## Deploy App with additional ArgoCD


创建额外的ArgoCD实例：
1. 创建`myarogcd`项目
2. 在`myarogcd`项目，打开Installed Operators，打开OpenShift GitOps operator
3. 打开ArgoCD，创建ArgoCD，取名为`myargocd`
4. 在Networking / route中打开新的ArgoCD的地址
5. Login with OpenShift

此时用户还没有ArgoCD的admin权限，所以会看不到application。

下面给用户分配ArgoCD的admin权限:
1. 创建`myargocd-admin-group`组，将指定用户添加到该组
2. 将`myargocd`项目下的`admin`权限授权给`myargocd-admin-group`组
```bash
oc adm policy add-role-to-group admin myargocd-admin-group -n myargocd
```
3. 在`myarogcd`项目，打开Installed Operators，打开OpenShift GitOps operator
4. 打开ArgoCD，打开`myargocd`, 编辑YAML，在`rbac.policy`下添加：
```yaml
g, myargocd-admin-group, role:admin
```


Run below clean up scripts firsly.

Deploy app with addtitional argocd instance:
```bash
# create cluster-configs application
# cluster-scoped resources need be created under openshift-gitops
oc create -f argo/cluster.yaml -n openshift-gitops

# verify
oc get application -n openshift-gitops
oc describe ns spring-petclinic

# fix not managed error, need cluster admin right
oc label namespace spring-petclinic argocd.argoproj.io/managed-by=myargocd

# create app-spring-petclinic application
oc create -f argo/app.yaml -n myargocd

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
oc delete application app-spring-petclinic -n myargocd
oc delete project spring-petclinic
```
