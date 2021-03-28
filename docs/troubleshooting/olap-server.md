# OLAP Server

## OLAP Server Does Not Start
### Scenario
The OLAP server pod does not start.
#### Steps

1. Run a kubectl describe on the pod that is not starting
```
kubectl describe pod -n <namespace> <pod name>
```
2. Check the events section of the output to determine the failure

3. Here are some of the reasons for failure:

* There are no resources available. Your AWS account may have reached the limit for a specific node type.
* A persistent volume failed to attach to the pod. The switchover of a persistent volume from a previous pod to a new pod can take some time.
* There was a problem with pulling down the docker image for the pod. This can occur when new nodes are added to the cluster.
* The pod is waiting for HBase region servers to start.
