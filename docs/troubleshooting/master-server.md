# HBase Master Server

## HMaster Pod Restarted

### Scenario
A HMaster pod has restarted. If the HMaster pod that restarted is the active HMaster, then the backup HMaster pod will become the active HMaster. If the HMaster pod that restarted is the backup HMaster, then there is essentially no effect on normal operations. In both cases another HMaster pod will be created. 
#### Steps
There is no action required.

If you want to determine why the HMaster pod restarted follow these steps:

* Log into Kibana or Cloud Manager
  * Kibana
    * Filter for the HMaster pod that was restarted.
    * Filter for “error” or “exception”
  * Cloud Manager
    * Select your time range
    * Select prebuilt search (Exception or Hbase)


## HMaster Pod Does Not Start

### Scenario
One or more of the HMaster pods do not start.One or more of the HMaster pods do not start. 
#### Steps
1. Run a kubectl describe on the pod that is not starting
>```
>kubectl describe pod -n <namespace> <pod name>
>```
2. Check the events section of the output to determine the failure

3. Here are some of the reasons for failure:

* There are no resources available. Your AWS account may have reached the limit for a specific node type.
* A persistent volume failed to attach to the pod. The switchover of a persistent volume from a previous pod to a new pod can take some time.
* There was a problem with pulling down the docker image for the pod. This can occur when new nodes are added to the cluster.
* The pod is waiting for other components such as Zookeeper and Name Node to start.