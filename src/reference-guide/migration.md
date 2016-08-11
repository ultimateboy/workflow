# Migrating from Deis v1

Workflow uses [`kubectl`][kubectl] and [`helmc`][helmc] to manage the cluster. These tools
are equivalent to Deis v1's [`fleetctl`][fleetctl] and [`deisctl`][deisctl]. These two tools are
used for managing the cluster's state, installing the platform and inspecting its state.

This document is a "cheat cheet" for users migrating from Deis v1 to Workflow (v2). It lists most of
the known commands administrators would use with `deisctl` and translates their usage in Workflow.

## Listing all Components

```
# Deis v1
$ deisctl list
$ fleetctl list-units

# Workflow
$ kubectl --namespace=deis get pods
$ kubectl get pods --all-namespaces
```

## Listing all Nodes

```
# Deis v1
$ fleetctl list-machines

# Workflow
$ kubectl get nodes
```

## Install the Platform

```
# Deis v1
$ deisctl install platform

# Workflow
$ helmc install workflow-v2.3.0
```

## Uninstall the Platform

```
# Deis v1
$ deisctl uninstall platform

# Workflow
$ helmc uninstall workflow-v2.3.0 -n deis
```

## Custom Configuration

```
# Deis v1
$ deisctl config controller set registrationMode=admin_only

# Workflow
$ kubectl --namespace=deis patch rc deis-controller -p '{"spec":{"containers":{"env":[{"name":"REGISTRATION_MODE","value":"admin_only"}]}}}'
$ kubectl --namespace=deis scale rc deis --replicas=0
$ kubectl --namespace=deis scale rc deis --replicas=1
```

## View Component Configuration

```
# Deis v1
$ deisctl config router get bodySize

# Workflow
$ kubectl --namespace=deis get rc deis-router -o yaml
```

## Running a Command Within a Component

```
# Deis v1
$ deisctl dock router@1

# Workflow
$ kubectl --namespace=deis get pods | grep deis-router | awk '{print $1}'
deis-router-1930478716-iz6oq
$ kubectl --namespace=deis exec -it deis-router-1930478716-iz6oq bash
```

## Follow the Logs for a Component

```
# Deis v1
$ fleetctl journal -f deis-builder

# Workflow
$ kubectl --namespace=deis get pods | grep deis-builder | awk '{print $1}'
deis-builder-1851090495-5n0sn
$ kubectl --namespace=deis logs -f deis-builder-1851090495-5n0sn
```
