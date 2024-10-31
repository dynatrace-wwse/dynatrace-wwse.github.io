summary: Troubleshooting Dynatrace OneAgent Operator
id: troubleshooting-dynatrace-oa-operator
categories: kubernetes, installation
tags: kubernetes, installation
status: Published
authors: sergio.hinojosa

# Troubleshooting Dynatrace OneAgent Operator


## Troubleshooting Tips and Basic commands 🩺

Nevative
: WIP, add diagrams


```bash
 kubectl get all -n dynatrace (-o wide)
```

List the basic resources of Dynatrace.
Operator deployment, Operator ReplicaSet and it's PoD
OneAgent DaemonSet definition and its PoDs. It should be one PoD per Node.
with -o wide, the output is wider and lets you verify in which node are the PoDs running.
All PoDs should be in status Running. The number of Ready and Desired should match, which by the way should always be 1 since we are deploying as a DeamonSet.

```bash
kubectl get nodes 
```
 
List all nodes of the K8s Cluster (Usefult to verify which Nodes are missing a Deployment)

```bash
 kubectl get events -n dynatrace
```
To list events in the Dynatrace namespace. Useful when to troubleshoot if a PoD can't start due resources limitations, taints or similar.
```bash
kubectl -n dynatrace logs dynatrace-oneagent-operator-[xxx]
```

```bash
kubectl -n dynatrace logs oneagent-[xxx]
```

To get the logs of a specific PoD. The logs of the operator give insights of the health of all the pods. You can of course get the logs of a oneagent PoD which gives more detailed information of a failed PoD initialation. The logs of the OneAgent PoD are not equal to the detailed logs of the OneAgent process which are found in /opt/dynatrace/oneagent/logs/
[process/network/os/memorydump/..]
Do not troubleshoot with the logs of the OneAgent described above (the logs in the node), this is only for your information to understand which logs are sent to fluentd and which are not. Also to understand where the binaries of the OneAgent reside, which is not inside the PoD but in the Node itself, Be careful with this information since might get you in trouble if not explained properly, don't bring it up if it is not really necessary. 

```bash
kubectl -n dynatrace describe pod dynatrace-oneagent-operator-[xxx]
```

```bash
kubectl -n dynatrace describe pod oneagent-[xxx]
```
You can call the describe command to get information about the definition of the PoD and the status of the PoD with its events. 

```bash
kubectl describe node [node-id]
```
If a Node has a problem you can also describe the node to see why the PoDs can't be properly scheduled/run in there.



```bash
kubectl delete --all pods -n dynatrace
```
To recycle all Dynatrace PoDs. I have never used it, but this is normally usefull to recycle the namespace of the customer so you can instrument the customer pods in that namespace. Think of it as restart of the procesess.
