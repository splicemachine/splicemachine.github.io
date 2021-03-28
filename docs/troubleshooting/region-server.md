# Region Server

## Region Server Pod Restarted

### Scenario

A region server pod has restarted. A region server may restart for a few reasons:

* A node may have been removed from Kubernetes. This could be because the K8s autoscaler decided to take the node down and move the pods on the current node to another node.
* It could be that the liveness check failed and so the pod was restarted.
* It could be that there was an out of memory or unexpected error that caused the restart.
  
In the most common scenario, the pod restarting has no noticeable effect on the database.

#### Steps
There is no action required. 

#### Research
If you want to determine why the region server pod restarted follow these steps. 

1. Run the _describe_ command for the pod that restarted

```
kubectl describe pod -n <namespace> <pod name>
```

2. In the output you will see something like the following

```
  hbase-rs:
    Container ID:  docker://9e23e2c2d1f85084198e83052c39af89ebbcb5618d1a9a3a492215446ea2e688
    Image:         docker.io/splicemachine/sm_k8_hbase-3.0.0:0.0.86_AZ
    Image ID:      docker-pullable://splicemachine/sm_k8_hbase-3.0.0@sha256:a8e8553d56df521fefe7fd5eec83e46a073f9574fb08a21cb8ef7cbc2e609189
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/bash
      /usr/lib/hbase/bin/splice/start-hbase-component.sh
    State:          Running
      Started:      Fri, 22 Jan 2021 06:27:49 -0500
    Last State:     Terminated
      Reason:       Error
      Exit Code:    137
      Started:      Fri, 22 Jan 2021 01:40:49 -0500
      Finished:     Fri, 22 Jan 2021 06:27:49 -0500
```



3. For the output, you want to look at the section ‘Last State’ that will give you the reason for the restart. In the example above, the Exit Code is a 137 which is an out of memory error. In that case you want to do some searching of the logs for an out of memory error by running the following command:

```
kubectl exec -it -n <namespace> <pod name> -- bash
cd /var/log/hbase
grep ‘java.lang.OutOfMemoryError’ *
```

4. Open the matching log files and locate the out of memory error. The most likely cause is GC pressure. If you see GC pressure, then we want to enable verbose gc logs. You can do that with the following steps:

* Get the splicedbcluster and edit it
> ```
> kubectl -n splice-system edit splicedbcluster <cluster name>
> ```
* Search for the word ‘OLAP_CLASSPATH’ and add the following after that line:
> ```
> export SERVER_GC_OPTS="-verbose:gc -XX:+PrintGCTimeStamps -XX:+PrintGCDetails -XX:+PrintTenuringDistribution -XX:+PrintGCCause"
> ```
* Restart HBase
> ```
> kubectl delete po -n <namespace> --selector=app=hbase
> ```
* When the next out of memory issue occurs, send the GC logs to Splice Machine



## Region Server Pod Does Not Start
### Scenario
One or more region server pods do not start.
#### Steps

1. Run a kubectl describe on the pod that is not starting

> ```
> kubectl describe pod -n <namespace> <pod name>
> ```

2. Check the events section of the output to determine the failure

3. Here are some of the reasons for failure:

* There are no resources available. Your AWS account may have reached the limit for a specific node type.
Resolution: Submit a request to AWS to increase the limit for the specific node type
