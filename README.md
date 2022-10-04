Red Hat Microsweeper demo with Quarkus - Oct 4
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

# To access the app, hit http://localhost:8080 

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
* This repo has [OpenTelemetry](https://opentelemetry.io/docs/) and [Quarkus OpenTelemetry extension](https://quarkus.io/guides/opentelemetry) integrated to support local development using [podman play kube](https://docs.podman.io/en/stable/markdown/podman-play-kube.1.html); 
```
# The OTLP exporter can be enabled by these configuration properties
# Note: dev mode only for this demo
%dev.quarkus.opentelemetry.enabled = true
%dev.quarkus.opentelemetry.tracer.exporter.otlp.endpoint = http://localhost:4317
# Issues: need to explicitly turn on builtin sampler 
%dev.quarkus.opentelemetry.tracer.sampler=on

# To facilitate the demo, copy these yaml configuation files from
#   https://github.com/rhtevan/quick-quarkus/tree/main/.container
# Start collector and jaeger containers in the same pod
podman play kube .container/otel.yaml

# To check the pod status
podman pod ps --ctr-names --ctr-status 

POD ID        NAME        STATUS      CREATED         INFRA ID      NAMES                                                     STATUS
861d58b636a4  otel        Running     26 seconds ago  4eaddf66e0ea  861d58b636a4-infra,otel-collector,otel-jaeger-all-in-one  running,running,running

# To access the Jaeger UI, hit http://localhost:16686

# OTLP exporter is enabled for Dev mode only, and to shutdown and clean up
podman pod rm --force otel
podman pod ps --ctr-names --ctr-status 
```  
---
Date: 2022-09-28
