# Infrastructure Plane PoC

The goal of this PoC was to give a small overview of how to use Crossplane 
to define a PostgreSQL DB ressource with multiple flavours, that could later
be used inside some service's code.

## Requirements

- Crossplane v0.12 or more installed in your k8s cluster

## Install & configure providers

To install providers you should use the:

`kubectl crossplane package install --cluster --namespace crossplane-system <your_provider:version>`
 
You should then follow their documentation to configure them (setting up credentials, etc) 


## Install definitions + compositions

To install any file in this project you can use the `kubectl apply -f` 
and precising the namespace where you want the ressource to be installed

## Architecture

- `providers` defines the needed Cloud/Infrastructure providers that will be used to create ressources
- `postgresql-instance` directory contains everything related to the postgresql database 
definition and its flavours
- `publication.yaml` is the file that defines which ressource is discoverable/usable by services

## Usage

To use the postgresql ressource in your service, you would have to add : 
```yaml
apiVersion: database.example.org/v1alpha1
kind: PostgreSQLInstanceRequirement
metadata:
  name: my-test-db
spec:
  parameters:
    storageGB: 20
    dbName: my-test-db
    username: testuser
  compositionSelector:
    matchLabels:
      tier: dev # possible values: (dev, bronze) or anything defined in compositions
  writeConnectionSecretToRef:
    name: validation-service-pg
```
