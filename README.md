Red Hat Microsweeper demo with Quarkus 
==========================

This demo uses a number of cloud technologies to implement a simple game from the earlier days of computing: Minesweeper!

![Screenshot](docs/microsweeper.png)

Technologies include:

* JQuery-based Minesweeper written by [Nick Arocho](http://www.nickarocho.com/) and [available on GitHub](https://github.com/nickarocho/minesweeper).
* Backend based on [Quarkus](https://quarkus.io) to persist scoreboard and provide a reactive frontend and backend connected to [Postgres](https://azure.microsoft.com/en-us/services/postgresql/).

-----------
```
# To run quarkus in dev mode (it will automatically use Quarkus' dev services to create a DB)
$ alias q='quarkus'
$ q dev

# access at http://localhost:8080

# To deploy and run quarkus application in an OpenShift cluster
# (Required Operators: SeviceBinding, Crunchy Postgres for Kubernetes)
$ oc project dev
$ q build --no-tests --clean -Dquarkus.kubernetes.deploy=true

# To clean up
oc delete all --selector=demo.redhat.com/name=microsweeper
oc delete postgresclusters.postgres-operator.crunchydata.com postgresql
oc delete servicebindings.binding.operators.coreos.com microsweeper-appservice-postgresql
```

