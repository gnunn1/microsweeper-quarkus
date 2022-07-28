Red Hat Microsweeper demo with Quarkus 
==========================

This demo uses a number of cloud technologies to implement a simple game from the earlier days of computing: Minesweeper!

![Screenshot](docs/microsweeper.png)

Technologies include:

* JQuery-based Minesweeper written by [Nick Arocho](http://www.nickarocho.com/) and [available on GitHub](https://github.com/nickarocho/minesweeper).
* Backend based on [Quarkus](https://quarkus.io) to persist scoreboard and provide a reactive frontend and backend connected to [Postgres](https://azure.microsoft.com/en-us/services/postgresql/).

* Technologies demonstrated here includes: [Quarkus](https://quarkus.io), [Quarkus extensions](https://quarkus.io/version/main/guides/deploying-to-kubernetes#introduction-to-the-service-binding-operator), [Quarkiverse](https://github.com/quarkiverse/quarkiverse), [Service Binding Operator](https://github.com/redhat-developer/service-binding-operator#known-bindable-operators)

-----------
```
# To run quarkus in dev mode (it will automatically use Quarkus' dev services to create a DB)
alias q='quarkus'
q dev

# access at http://localhost:8080

# To deploy and run quarkus application in an OpenShift cluster
# (Required Operators: SeviceBinding, Crunchy Postgres for Kubernetes)
oc project dev
q build --no-tests --clean -Dquarkus.kubernetes.deploy=true

# To clean up
oc delete all --selector=demo.redhat.com/name=microsweeper
oc delete postgresclusters.postgres-operator.crunchydata.com postgresql
oc delete servicebindings.binding.operators.coreos.com microsweeper-appservice-postgresql
```
* This repo has PipelineAsCode integrated. Check this [gist](https://gist.github.com/rhtevan/5d0fc387109ecba9d1f1ec24091bfe36) for details
```
# By default, a 'test' namespace/project need to be setup for PAC-based CI/CD
# To create a PAC Repository at test namespace executes the following command
oc apply -f .argocd/repository.yaml
```
* The Service Binding Operator [doc](https://redhat-developer.github.io/service-binding-operator/userguide/exposing-binding-data/rbac-requirements.html) and this [github issue](https://github.com/redhat-developer/service-binding-operator/issues/810) provide more details for the RBAC requirements.   
(Bug: Not sure why need to have a clusterrolebinding for the user if it is not in a cluster-admin role)
```
# Required for end-user deployment (e.g. user1) 
oc adm policy add-cluster-role-to-user clusterworkloadresourcemappings.servicebinding.io-v1alpha3-admin user1

# Required for system deployment (e.g. PipelineAsCode with a service account: pipeline at test project/namespace)
oc adm policy add-cluster-role-to-user clusterworkloadresourcemappings.servicebinding.io-v1alpha3-admin system:serviceaccount:test:pipeline

# Alternatively
oc apply -f .argocd/rolebinding.yaml
```

---
Date: 2022-07-28
