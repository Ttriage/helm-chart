# TTriage Helm Chart

Version: 0.1.0

## Introduction

Helm chart for Ttriage [](https://www.ttriage.com/ttriage/index.html) 

This Helm package deploys two pods, one containing the backend of the Ttriage and the other the front end. Alongside the Ttriage app, it can be deploy a Postgresql database or you can run it with an external database.



##  Prerequisites

- Kubernetes 1.12+
- Helm 3.0+
- An external database (optional)


## Dependencies

This chart provides the dependency of Postgresql, which can be enabled, or disabled via configuration.


## Installation

Add this helm chart repo


```
  helm repo add clarolab-charts https://ttriage.github.io/helm-chart
```

It is recommended to overwrite some values before installing the helm chart to adapt the application to your enviroment. This can be done in two ways (See [helm official documentation for more info.](https://helm.sh/docs/chart_template_guide/values_files/)).

**Examples**

1. Using a custom values file

A .yaml file called *customvals* containing

```
postgresql.enabled=false
```

`helm install -f customvals.yaml my-ttriage ttriage `

2. Passing parameters with --set

`helm install my-ttriage ttriage --set postgresql.enabled=false`


## Configuration


### Ttriage Backend

By default the backend cointainer is expose using a NodePort. This is going to be used on the front end as the api URL. 
Besides this, the backend application uses two optional parameters for Google Sign-in authentication on Ttriage.

| Parameter | Description | Default |
| -------- | -------- | -------- |
| `backend.service.port`     | Service port    | `8089`    |
| `service.nodePort`     | NodePort number   | `30200`    |
| `backend.ttriageEnv.googleClientId`     | (Optional) Google Sign-in into Ttriage    | Disabled     |
| `backend.ttriageEnv.googleSecret`     | (Optional) Google Sign-in into Ttriage    | Disabled   |


### Database

As previously stated, alongside the application a PostgreSQL database is deployed with a PVC. If the PostgreSQL chart is disabled an external database configuration would be required.

External database parameters:

| Parameter | Description | Default |
| -------- | -------- | -------- |
| `externalDatabase.host`     | Database host    | `localhost`    |
| `externalDatabase.user`     | Non root Username of external db     | `postgres`   |
| `externalDatabase.password`     | Password of non-root user     | `changeme`   |
| `externalDatabase.port`     | External db port     | `5432`   |
| `externalDatabase.dbName`     | External db name    | `ttriage`   |


Postgresql is loaded as a dependency from bitnami. Configuration can be found [here](https://github.com/bitnami/charts/tree/master/bitnami/postgresql)

| Parameter | Description | Default |
| -------- | -------- | -------- |
| `postgresql.global.postgresql.postgresqlDatabase` | PostgreSQL database   | `ttriage`   |
| `postgresql.global.postgresql.postgresqlUsername` | PostgreSQL username   | `ttriage`   |
| `postgresql.global.postgresql.postgresqlPassword` | PostgreSQL password   | `ttriage`   |
| `postgresql.global.postgresql.servicePort` | PostgreSQL port  | `5432`   |
| `postgresql.persistence.enabled` | Is persistence enabled for Postgres  | `true`   |
| `postgresql.persistence.size` | PVC Storage Request for PostgreSQL volume | `8Gi`   |


### Frontend

**Service**

| Parameter | Description | Default |
| -------- | -------- | -------- |
| `frontend.service.type` | Kubernetes service type for traffic | `ClusterIP`   |
| `frontend.service.port` | Port for traffic | `8081`   |
| `frontend.ingress.enabled` | Enable ingress | `false`   |
| `frontend.ingress.hosts` | Add hosts for ingress| `ttriage.example.com`   |


The front end uses a ConfigMap to mount a config file needed to run with the following configurable parameters:


| Parameter | Description | Default |
| -------- | -------- | -------- |
| `frontend.config.backEndHost` | Backend host | `http://123.123.123.123`   |
| `frontend.config.backEndHostPort` | Backend host port. | `30200`   |
| `frontend.config.login.google` | Enable Google auth   | `false`   |
| `frontend.config.login.google` | Enable OneLogin auth   | `false`   |
| `frontend.config.login.google` | Enable Internal auth   | `true`   |



